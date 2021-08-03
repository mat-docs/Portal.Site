# Function DLLs

ATLAS 10 supports function DLLs written in .NET Framework.

## Code Structure

Function classes:

* Implement `MESL.SqlRace.Domain.Functions.DotNet.IDotNetFunction`
* Are decorated with:
    *   `System.Serializable`
    *   `System.ComponentModel.Composition.ExportAttribute`

!!! example

    ```c#
    [Serializable]
    [Export(typeof(IDotNetFunction))]
    public class VCarMultipliedByThree : IDotNetFunction
    {
        public void Initialize(IFunctionManager functionManager)
        {
            // ...
        }

        public void Execute(IExecutionContext executionContext)
        {
            // ...
        }
    }
    ```

### Initialize

`Initalize` is called at startup.

!!! example

    ```c#
    var functionDefinition = functionManager .CreateFunctionDefinition(DotNetFunctionConstants.UniqueId);

    functionDefinition.CalculateOverWholeSession = false;
    functionDefinition.FunctionMode = FunctionMode.Instantaneous;
    functionDefinition.InputParameterIdentifiers.Add("vCar:Chassis");
    functionDefinition.InterpolateBetweenSamples = true;
    functionDefinition.JoinGapsAroundNull = true;
    functionDefinition.Name = @"vCarTimesThree";
    this.Name = @"vCarTimesThree";

    var implementationDefintion = (DotNetFunctionImplementationDefinition)functionDefinition.ImplementationDefinition;
    implementationDefintion.Function = this;

    functionDefinition.OutputParameterDefinitions.Add(
        new FunctionOutputParameterDefinition
        {
            ApplicationName = "Functions",
            Identifier = "vCarTimesThree",
            Name = "vCarTimesThree",
            BitShift = 0x00,
            ByteOrder = ByteOrderType.BigEndian,
            DataBitMask = 0x00,
            Description = @"Multiply vCar three times",
            FormatOverride = @"%5.1f",
            MaximumValue = 900,
            OffsetValue = 0x00,
            Units = "kph"
        });

    functionDefinition.StoreInSession = true;
    functionManager.Build(functionDefinition);
    ```

Multiple input identifiers and output parameter definitions can be defined.

### Execute

`Execute` is called with a context providing function input and output arrays, and other configuration.

!!! example

    ```c#
    // find input
    int vCarIndex = executionContext.FunctionInput.InputParameterIndexes["vCar:Chassis"];
    double[] vCarInputValues = executionContext.FunctionInput.Values[vCarIndex];

    // find output
    int vCarTripledIndex = functionOutput.OutputParameterIndexes["vCarTripled:FunctionParameters"];
    double[] vCarTripledOutputValues = functionOutput.OutputParametersValues[vCarTripledIndex];

    // compute
    for (var i = 0; i < executionContext.FunctionInput.Timestamps.Length; i++)
    {
        vCarTripledOutputValues[i] = vCarInputValues[i] * 3;
    }
    ```

* The function execution framework will sample the input data based on each function’s properties.
* You must provide an output for each input timestamp:  
  function input and output arrays will be the same length as `FunctionInput.Timestamps.Length`.
* Return `double.NaN` for timestamps with no value.

### Constants

A function class can access constants through a two-step process:

1. Specify the identifiers for required constants during `Initialize`;
2. Access constant values through the `IExecutionContext` during `Execute`.

This enables ATLAS to disable a function if the requirements are not met.

## Discovery

Locate function DLLs in:  
`%USERPROFILE%\Documents\McLaren Electronic Systems\SQL Race\Functions`

ATLAS checks this directory at startup.

When a function DLL is loaded, it is available in the Parameter Browser under _Functions_.  
Function output parameters will also be available to use as inputs to other functions.   
In FDL, they can be referenced like: `$rSlipRatioFL:FunctionParameters`
