# Using Decode dll with ATLAS 10.4.4

## Overview

ATLAS 10.4.4 now supports an interface for passing data into ATLAS client for display and storage. 
This allows custom messages generated in a customer ECU to be passed through Telemetry or over Ethernet by ATLAS and then passed into the decode dll where they are decoded. 
It also allows messages packed up as Quads from custom ECU or telemetry system to be distributed through ATLAS Data Server.

## Instructions to upgrade existing dll 

* Install the new version of Atlas 10.4.4
* Replace the files (TAtlasApi.cpp and TAtlasApi.h) in the project for the custom decode dll with the files from [AtlasAPI](https://github.com/mat-docs/AtlasAPI)
* Modify the code in the project to call BeginConfig on the instance of CTAtlasApi object that you hold in the decode dll before you send the first configuration object
* Call EndConfig after you send the last configuration object. 
* Change the target platform to x64 (Win32 library cannot be loaded by Atlas 10 as it is a x64 application)
* Build the project
* Select the dll in Decode DLLs options page for the stream that you use
