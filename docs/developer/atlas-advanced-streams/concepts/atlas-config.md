# Atlas configuration
A tree-like structure that defines the display structure of your data, using the following levels in the tree:

```python
AtlasConfiguration({"config_name":
        ApplicationGroup(groups={"group_name":
            ParameterGroup(parameters={"parameter_name":
                Parameter(name="parameter_display_name")})})})
```
Of course there can be multiple of each, hence the dictionary based tree-like structure.
Atlas configuration needs to be set and put to the dependency service only if you want to display your data in Atlas10.

#### AtlasConfiguration
Represents one or more ATLAS-compatible configuration sets. The key of an item should correspond to the app identifier/name (e.g. "Chassis").

#### ApplicationGroup
Represents all configuration for an application - equivalent to the ATLAS concept.

 - Conversions
 
 Conversion functions to translate samples into engineering values. Useful when injecting uncalibrated data into a stream, but any data sourced from a time-series store (like SQL Race) can usually be expected to be pre-calibrated, requiring no conversion. The dictionary key must correspond to the Conversion Id. This should be unique and of the form 'foo:{appname}'. For example: "SomeParamConv:MyApp". It is case-sensitive."
 
  - Conversion
  
  Conversion function - from a raw sample to an engineering value.
  
 - Events
 
 Definitions of events - which can happen at any particular moment, not aligned to a sample interval. The dictionary key must correspond to the Event Definition Id. This should be unique and of the form '{id:4}:{appname}'. For example: "02D4:MyApp". It is case-sensitive.

  - EventDefinition
  
  Defines an event - something that can happen independently of any sampling. Each Event can carry sample values, for context.
  There are three supported types of conversion function: RationalConversion, TableConversion, TextConversion
  
  - EventPriority
  
  Event priority. This concept is similar to common logging frameworks. Options are High, Medium, Low, Debug
  
- ApplicationId

Unique application id. Optional.

- RdaOwnerId

Unique team id, for RDA (Reduced Data Access). Optional.

#### ParameterGroup

Node in a tree of parameters. Contains both child groups and child parameters (leaf nodes).
ATLAS actually binds together group identifiers by concatenating group names together with underscore prefixes. We interpret that as meaning there isn't really a Description attribute as such - it's all just names. Needless to say the parameters are bound to the groups by the concatenated identifier

#### Parameter

Parameter within a ParameterGroup
