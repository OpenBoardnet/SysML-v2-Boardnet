---
title: Features and Functions
name:  FeaturesFunctions
---
# Contents
[toc]

Translate requirements into specific system capabilities (features) and operationalize these through executable functional building blocks. They form the logical architecture layer between requirements and technical implementation.
# Features
```SysML::OpenBoardnet
package Features {
    private import ScalarValues::*;
    private import BaseTypes::*;

    part def CruiseControlFeature :> Feature {
        attribute isActive: Boolean;
    }

    part def ObstacleDetection :> Feature {
        attribute isObjectDetected: Boolean;
    }
    
    part def EmergencyBrakeAssist :> Feature {
        attribute isBraking: Boolean;
    }
    
    part def LaneKeepAssist :> Feature {
        attribute isSteeringIntervention: Boolean;
    }

    part def AdaptiveCruiseControl :> Feature {
        attribute isFollowing: Boolean;
    }
    
    part def ParkingAssist :> Feature {
        attribute isParking: Boolean;
    }
}
```
# Functions
```SysML::OpenBoardnet
package Functions {
    private import BaseTypes::*; 
    private import ISQ::*;

    part def SpeedAcquisition :> Function {
        out attribute vehicleSpeed: SpeedValue {:>> unit = "km/h"; :>> range = "-20..200";}
    }
    part def SpeedControl :> Function {
        in attribute speedInput: SpeedValue {:>> unit = "km/h"; :>> range = "-20..250";}
    }
}
```
