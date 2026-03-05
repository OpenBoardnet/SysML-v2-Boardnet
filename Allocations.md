---
title: Allocations
name:  allocations
---
# Contents
[toc]

In the OpenBoardnet Framework, allocations define mapping relationships between system elements. 
This file serves as the central integration layer that bridges the Logical and Technical architectures. 
It ensures that behaviors, resources, and constraints are properly assigned by mapping logical features to technical hardware 
and verifying that system requirements are satisfied by the combined architecture.
```SysML::OpenBoardnet::Allocations
    private import LogicalArchitecture::*;
    private import Requirements::*;
    private import LocationsAndSpaces::*;
    private import Software::*;
    private import Sensors::*;
    private import CU::*;
    
    part zonalLogic : ZonalArchitecture;                  
    part domainLogic : DomainArchitecture;
    part req_Speed : SpeedAccuracyRequirement;
    part req_Brake : EmergencyBrakeAssistRequirement;
```
# Requirement -> Feature Allocations
## Zonal Architecture Requirements
```SysML::OpenBoardnet::Allocations
    allocation alloc_sat_1 allocate req_Speed to zonalLogic.centralFeatures.laneKeep; 
    allocation alloc_sat_2 allocate req_Brake to zonalLogic.frontZoneFeatures.obstacleDet;
```
## Domain Architecture Requirements
```SysML::OpenBoardnet::Allocations
    allocation alloc_sat_3 allocate req_Speed to domainLogic.visionDomain.laneKeep;
    allocation alloc_sat_4 allocate req_Brake to domainLogic.radarDomain.obstacleDet;
```
# Software -> Controller Allocations
## Zonal Software -> Zonal Controllers
```SysML::OpenBoardnet::Allocations
    allocation alloc_exec_1 allocate zonalLogic.frontZoneFeatures to CU::zonalControllerFront;
    allocation alloc_exec_2 allocate zonalLogic.rearZoneFeatures to CU::zonalControllerRear;
    allocation alloc_exec_3 allocate zonalLogic.centralFeatures to CU::centralController;
```
## Domain Software -> Domain Controllers
```SysML::OpenBoardnet::Allocations
    allocation alloc_domain_exec_1 allocate domainLogic.visionDomain.laneKeep to CU::cameraController;
    allocation alloc_domain_exec_2 allocate domainLogic.radarDomain.acc to CU::radarAndLidarController;
    allocation alloc_domain_exec_3 allocate domainLogic.radarDomain.obstacleDet to CU::radarAndLidarController;
    allocation alloc_domain_exec_4 allocate domainLogic.ultrasonicDomain.parkAssist to CU::ultrasonicController;
```
# Hardware -> Location allocations
## Sensors -> Locations
```SysML::OpenBoardnet::Allocations
    allocation alloc_topViewLeft_loc allocate Sensors::topViewLeft to LocationsAndSpaces::topViewLeftLoc;
    allocation alloc_topViewRight_loc allocate Sensors::topViewRight to LocationsAndSpaces::topViewRightLoc;
    allocation alloc_topViewFront_loc allocate Sensors::topViewFront to LocationsAndSpaces::topViewFrontLoc;
    allocation alloc_topViewRear_loc allocate Sensors::topViewRear to LocationsAndSpaces::topViewRearLoc;
    allocation alloc_roofCamera_loc allocate Sensors::roofCamera to LocationsAndSpaces::roofCameraLoc;
    allocation alloc_lidar_loc allocate Sensors::lidar to LocationsAndSpaces::lidarLoc;
    allocation alloc_midRangeRadar_loc allocate Sensors::midRangeRadar to LocationsAndSpaces::midRangeRadarLoc;
    allocation alloc_ultrasonicsensorFront_loc allocate Sensors::ultrasonicsensorFront to LocationsAndSpaces::ultrasonicsensorFrontLoc;
    allocation alloc_ultrasonicsensorRear_loc allocate Sensors::ultrasonicsensorRear to LocationsAndSpaces::ultrasonicsensorRearLoc;
```
## Controllers -> Locations
### Zonal Controllers -> Locations
```SysML::OpenBoardnet::Allocations
    allocation alloc_centralController_loc allocate CU::centralController to LocationsAndSpaces::centralControllerLoc;
    allocation alloc_zonalControllerFront_loc allocate CU::zonalControllerFront to LocationsAndSpaces::zonalControllerFrontLoc;
    allocation alloc_zonalControllerRear_loc allocate CU::zonalControllerRear to LocationsAndSpaces::zonalControllerRearLoc;
```
### Domain Controllers -> Locations
```SysML::OpenBoardnet::Allocations
    allocation alloc_gateway_loc allocate CU::gateway to LocationsAndSpaces::gatewayLoc;
    allocation alloc_cameraController_loc allocate CU::cameraController to LocationsAndSpaces::cameraControllerLoc;
    allocation alloc_ultrasonicController_loc allocate CU::ultrasonicController to LocationsAndSpaces::ultrasonicControllerLoc;
    allocation alloc_radarAndLidarController_loc allocate CU::radarAndLidarController to LocationsAndSpaces::radarAndLidarControllerLoc;
```
