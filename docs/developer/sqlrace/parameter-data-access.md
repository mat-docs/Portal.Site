# Parameter Data Access

**ParameterDataAccess objects (PDA)** are used to defines how parameter values are 
sampled or interpolated via SQL Race.

PDA can be created by the
`Session.CreateParameterDataAccess(parameterIdentifier)`
method and is associated with both the Session and a Parameter. 
More than one PDA can exist within a session as each can used to retrieve values from a
different parameter.

Methods on the PDA object allow you to:

* Move to specific point in time
* Set the sample time
* Read sub-sampled data
* Read interpolated data
* Read the measured sample values

!!! info "Best practice"
    
    * Opening a PDA uses up memory until the session is closed and disposed. It is best to open a PDA for a parameter once and cache it. Then you can reuse it throughout the session. An example of when this is particularly useful is when iterating through laps. It is better to open each PDA once than to open and dispose of them inside the lap iteration.
    * Calls to a PDA are not thread safe. If you are wanting to access data using the same Parameter in different threads, it is better to consider opening a small pool of PDAs for the threads to use. You have to be extra careful when using method that require the PDA's current position to ensure that it is only being used by one thread.

## Samples and Data
Within the context of PDA methods, *samples* refers to the values as logged by the 
datalogger, whereas *data* refers to sub-sampled, or interpolated values at a predefined
fixed frequency.

## Sample Modes
Sample modes define the behaviour when sub-sampling. 

| Sample Mode | Behaviour |
|-------------|-----------|
| First | Return the first sample value in the range. |
| Maximum | Return the maximum sample value in the range. |
| Mean | Return the average sample value across the range. |
| Minimum | Return the minimum sample value in the range. |

## Data Status
`ParameterValues.DataStatus` provides additional information about the values returned 
in `ParameterValues.Data`.

When retrieving values via a PDA, the arrays within `ParameterValues` will always be the
length of the requested sample size, regardless of the number of values requested. 

As an example, when there are only 100 samples left between the cursor and the end of 
the session, and the sample size requested is larger than 100. The first 100 
data status will be `Sample`, and the remaining `DataStatus` will be `AfterEnd`. 

The property `ParameterValues.SampleCount` can be used to check the number of valid 
samples within the returned array.

* Samples that represents values as measured by the data logger will have data status `Sample`.
* Data that are interpolated will have data status `Interpolated`.

## Examples

### Getting Samples from a Historic Session
=== "C#"

    ``` csharp
    // Local database connection string and session key for data already recorded
    string connectionString = @"DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;";
    SessionKey sessionKey = SessionKey.Parse("7DD05707-EAA2-4A36-BB8A-E2327AA52BFC");

    // Initialise SQLRace API
    Core.Initialize();
    var sessionManager = SessionManager.CreateSessionManager();

    // Load existing session
    using (var clientSession = sessionManager.Load(sessionKey, connectionString))
    {
        // Obtain the session
        var session = clientSession.Session;
        
        // Use PDA to get logged data across all channels in parameter vCar
        using (ParameterDataAccessBase pda = session.CreateParameterDataAccess("vCar:Chassis"))
        {
            // Go to the start of the session
            pda.GoTo(session.StartTime);

            // Get 10 samples
            var parameterValues = pda.GetNextSamples(10, StepDirection.Forward);

            // Print data and status to console window
            for (int i = 0; i < 10; i++)
            {
                Console.WriteLine("Data: {0}, Timestamp: {1}, Status: {2}", parameterValues.Data[i], parameterValues.Timestamp[i], parameterValues.DataStatus[i]);            }
            }
        }
    }
    ```

=== "Python"

    ``` python
    # Local database connection string and session key for data already recorded
    connection_string = r"DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;"
    sessionKey = SessionKey.Parse("7DD05707-EAA2-4A36-BB8A-E2327AA52BFC")
    
    # Initialise SQLRace API
    Core.Initialize()
    session_manager = SessionManager.CreateSessionManager()
    
    # Load existing session
    client_session = session_manager.Load(sessionKey, connection_string)
    
    # Obtain the session
    session = client_session.Session
    
    # Create a PDA to get logged data across all channels in parameter vCar
    pda = session.CreateParameterDataAccess("vCar:Chassis")
    
    # Go to the start of the session
    pda.GoTo(session.StartTime)

    # Get 10 samples
    parameter_values = pda.GetNextSamples(10, StepDirection.Forward)
        
    # Print data and status to console window
    for i in range(10):
        print(f"Data: {parameter_values.Data[i]}, TimeStamp: {parameter_values.Timestamp[i]} Status: {parameter_values.DataStatus[i]}")
        
    # Dispose objects once we are finish with it
    pda.Dispose()
    client_session.Close()
    ```

=== "MATLAB"

    ``` matlab
    % Local database connection string and session key for data already recorded
    connectionString = "DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;";
    sessionKey = SessionKey.Parse("7DD05707-EAA2-4A36-BB8A-E2327AA52BFC");
    
    % Initialise SQLRace API
    Core.Initialize()
    sessionManager = SessionManager.CreateSessionManager();
    
    % Load existing session
    clientSession = sessionManager.Load(sessionKey, connectionString);
    
    % Obtain the session
    session = clientSession.Session;
    
    % Create a PDA to get logged data across all channels in parameter vCar
    pda = session.CreateParameterDataAccess("vCar:Chassis");
    
    %  Go to the start of the session
    pda.GoTo(session.StartTime);
    
    % Get 10 samples
    parameterValues = pda.GetNextSamples(10, StepDirection.Forward);
    
    % Print data and status to console window
    for i=1:10
        fprintf("Data: %f, Timestamp: %i, Status: %s\n",parameterValues.Data(i),parameterValues.Timestamp(i),parameterValues.DataStatus(i))
    end

    % Dispose objects once we are finish with it
    pda.Dispose()
    clientSession.Close()
    ```

### Getting Samples from a Live Session
PDA contains the property `CurrentTime`, which keeps track of the
location of the cursor. This cursor is specific to each instance of PDA.

This can be used in combination with `GetNextSamples` to get new samples on the leading 
edge during a live session. The `CurrentTime` gets updated to 1ns after the last sample 
retrieved.



=== "C#"

    ``` csharp
    string connectionString = @"DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;";
    
    // Initialise SQLRace API
    Core.Initialize();
    var sessionManager = SessionManager.CreateSessionManager();
        
    // Find the latest live session
    var sessionSummaries = sessionManager.FindBySessionState(SessionState.Live, connectionString);
    var liveSessionSummary = sessionSummaries.OrderBy(x => x.TimeOfRecording).Last();
    
    // Load the live session
    using (var clientSession = sessionManager.Load(liveSessionSummary.Key, liveSessionSummary.GetConnectionString()))
    {
        // Obtain the session
        var session = clientSession.Session;
    
        // Use PDA to get logged data across all channels in parameter vCar
        using (ParameterDataAccessBase pda = session.CreateParameterDataAccess("vCar:Chassis"))
        {
            // Go to the start of the session
            pda.GoTo(session.StartTime);
            while (session.State == SessionState.Live)
            {
                // Get 10 samples
                var parameterValues = pda.GetNextSamples(10, StepDirection.Forward);
    
                // Print data and status to console window
                for (int i = 0; i < parameterValues.SampleCount; i++)
                {
                    Console.WriteLine("Data: {0}, Timestamp: {1}, Status: {2}", parameterValues.Data[i], parameterValues.Timestamp[i], parameterValues.DataStatus[i]);
                }
                Console.WriteLine($"New Cursor Position: {pda.CurrentTime}");
    
                // Wait for a second
                Thread.Sleep(1000);
            }
        }
    }
    }
    ```

=== "Python"

    ```python
    connection_string = r"DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;"

    # Initialise SQLRace API
    Core.Initialize()
    session_manager = SessionManager.CreateSessionManager()
    
    # Find the latest live session
    session_summaries = session_manager.FindBySessionState(SessionState.Live, connection_string)
    latest_live_session = max(session_summaries, key=lambda x: x.TimeOfRecording)
    client_session = session_manager.Load(latest_live_session.Key, latest_live_session.GetConnectionString())
    session = client_session.Session
    
    # Create a PDA to get logged data across all channels in parameter vCar
    pda = session.CreateParameterDataAccess("vCar:Chassis")
    
    # Go to the start of the session
    pda.GoTo(session.StartTime)
    
    while session.State == SessionState.Live:
        # Get 10 samples
        parameter_values = pda.GetNextSamples(10, StepDirection.Forward)
    
        # Print data and status to console window
        for i in range(parameter_values.SampleCount):
            print(
                f"Data: {parameter_values.Data[i]}, TimeStamp: {parameter_values.Timestamp[i]} Status: {parameter_values.DataStatus[i]}")
        print(f"New Cursor Position: {pda.CurrentTime}")
    
        # Wait for a second
        time.sleep(1)
    
    # Dispose objects once we are finish with it
    pda.Dispose()
    client_session.Close()
    ```

=== "MATLAB"

    ```matlab
    connectionString = "DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;";
    
    % Initialise SQLRace API
    Core.Initialize()
    sessionManager = SessionManager.CreateSessionManager();
    
    % Find the latest live session
    sessionSummaries = sessionManager.FindBySessionState(SessionState.Live, connectionString);
    latestLiveSession = sessionSummaries.Item(0);
    %  Array formation and indexing are not allowed on .NET objects in MATLAB
    for i = 0:sessionSummaries.Count-1 
        disp(sessionSummaries.Item(i).Key.ToString())
        if (sessionSummaries.Item(i).TimeOfRecording > latestLiveSession.TimeOfRecording)
            latestLiveSession = sessionSummaries.Item(i);
        end
    end
    
    clientSession = sessionManager.Load(latestLiveSession.Key, latestLiveSession.GetConnectionString());
    
    % Obtain the session
    session = clientSession.Session;
    
    % Create a PDA to get subampled data across all channels in parameter vCar
    pda = session.CreateParameterDataAccess("vCar:Chassis");
    
    %  Go to the start of the session
    pda.GoTo(session.StartTime);
    
    while (session.State == SessionState.Live)
        % Get 10 samples
        parameterValues = pda.GetNextSamples(10, StepDirection.Forward);
        
        % Print data and status to console window
        for i=1:10
            fprintf("Data: %f, Timestamp: %i, Status: %s\n",parameterValues.Data(i),parameterValues.Timestamp(i),parameterValues.DataStatus(i))
        end
        fprintf("New Cursor Position: %i\n", pda.CurrentTime.Value)
    
        % Wait for a second
        pause(1)
    end
    
    % Dispose objects once we are finish with it
    pda.Dispose()
    clientSession.Close()
    ```

<!---
## Getting multiple parameters at the same time
-->


<!---
TODO: BUG for pda.GetNextData ADO:85594
### Getting Interpolated Data

=== "C#" 

    ``` csharp hl_lines="25-29"
    // Local database connection string and session key for data already recorded
    string connectionString = @"DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;";
    SessionKey sessionKey = SessionKey.Parse("7DD05707-EAA2-4A36-BB8A-E2327AA52BFC");

    // Initialise SQLRace API
    Core.Initialize();
    SessionManager sessionManager = SessionManager.CreateSessionManager();

    // Create storage for output values
    double[] data = new double[10];
    DataStatusType[] status = new DataStatusType[10];

    // Load existing session
    using (IClientSession clientSession = sessionManager.Load(sessionKey, connectionString))
    {
        // Obtain the session
        Session session = clientSession.Session;
        
        // Use PDA to get subsampled data across all channels in parameter vCar
        using (ParameterDataAccessBase pda = session.CreateParameterDataAccess("vCar:Chassis"))
        {
            // Go to the start of the session
            pda.GoTo(session.StartTime);

            // Set sample time to 500Hz ~ 2000000ns 
            pda.SampleTime = 2000000;

            # Get 10 interpolated samples
            var parameterValues = pda.GetNextData(10, SampleModeType.Mean);

            // Print data and status to console window
            for (int i = 0; i < 10; i++)
            {
                Console.WriteLine("Data: {0}, Timestamp: {1}, Status: {2}", parameterValues.Data[i], parameterValues.Timestamp[i], parameterValues.DataStatus[i]);            }
            }
        }
    }
    ```

=== "Python"

    ``` python hl_lines="21-25"
    # Local database connection string and session key for data already recorded
    connection_string = r"DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;"
    sessionKey = SessionKey.Parse("7DD05707-EAA2-4A36-BB8A-E2327AA52BFC")
    
    # Initialise SQLRace API
    Core.Initialize()
    sessionManager = SessionManager.CreateSessionManager()
    
    # Load existing session
    clientSession = sessionManager.Load(sessionKey, connection_string)
    
    # Obtain the session
    session = clientSession.Session
    
    # Create a PDA to get subsampled data across all channels in parameter vCar
    pda = session.CreateParameterDataAccess("vCar:Chassis")
    
    # Go to the start of the session
    pda.GoTo(session.StartTime)
    
    # Set sample time to 500Hz ~ 2000000ns
    pda.SampleTime = 2000000
    
    # Get 10 interpolated samples
    parameterValues = pda.GetNextData(10, SampleModeType.Mean)
        
    # Print data and status to console window
    for i in range(10):
        print(f"Data: {parameterValues.Data[i]}, TimeStamp: {parameterValues.Timestamp[i]} Status: {parameterValues.DataStatus[i]}")
        
    # Dispose objects once we are finish with it
    pda.Dispose()
    clientSession.Close()
    ```

=== "MATLAB"

    ``` matlab hl_lines="21-25"
    % Local database connection string and session key for data already recorded
    connection_string = "DbEngine=SQLite;Data Source=C:\session01.ssndb;Pooling=false;";
    sessionKey = SessionKey.Parse("7DD05707-EAA2-4A36-BB8A-E2327AA52BFC");
    
    % Initialise SQLRace API
    Core.Initialize()
    sessionManager = SessionManager.CreateSessionManager();
    
    % Load existing session
    clientSession = sessionManager.Load(sessionKey, connection_string);
    
    % Obtain the session
    session = clientSession.Session;
    
    % Create a PDA to get subsampled data across all channels in parameter vCar
    pda = session.CreateParameterDataAccess("vCar:Chassis");
    
    % Go to the start of the session
    pda.GoTo(session.StartTime);
    
    % Set sample time to 500Hz ~ 2000000ns
    pda.SampleTime = 2000000;
    
    % Get 10 interpolated samples
    parameterValues = pda.GetNextData(10, SampleModeType.Mean);
    
    % Print data and status to console window
    for i=1:10
        fprintf("Data: %f, Timestamp: %i, Status: %s\n",parameterValues.Data(i),parameterValues.Timestamp(i),parameterValues.DataStatus(i))
    end

    % Dispose objects once we are finish with it
    pda.Dispose()
    clientSession.Close()
    ```
-->





