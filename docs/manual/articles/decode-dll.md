# Using Decode dll with ATLAS 10

## Overview

ATLAS 10 now supports an interface for passing data into ATLAS 10 client for display and storage. 
This allows custom messages generated in a customer ECU to be passed through Telemetry or over Ethernet by ATLAS and then passed into the decode dll where they are decoded. 
It also allows messages packed up as Quads from custom ECU or telemetry system to be distributed through ATLAS Data Server.

## Instructions to upgarde existing dll 

* Install the new version of Atlas 10.4.4
* Replace the files in the project for the custom decode dll with the files (TAtlasApi.cpp and TAtlasApi.h) from the new ATLAS Data Server release
* Modify the code in the project to call BeginConfig on the instance of CTAtlasApi object that you hold in the decode dll before you send the first configuration object
* Call EndConfig after you send the last configuration object. Each pair of Begin/EndConfig calls creates a SQLRace Configuration Set. If you are familiar with this concept, you can take advantage of this and create several Configuration Sets in the session
* Change the target platform to x64 (Win32 library cannot be loaded by Atlas 10 as it is a x64 application)
* Build the project
* Select the dll in Decode DLLs options page for the stream that you use
