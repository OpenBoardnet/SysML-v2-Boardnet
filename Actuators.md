---
title: Actuators
name:  actuators
---
Convert control commands into physical actions (e.g., braking, steering). They operate in conjunction with assigned ECUs and implemented software components.
```SysML::OpenBoardnet
package Actuators { 
    private import Hardware::*;
    private import ISQ::*;
    
    part def Actuator :> Actuator_Base;    
    
    part def SteeringActuator :> Actuator {
        attribute angle: DimensionOneValue {:>> unit = "°"; :>> range = "-450..450";}
        attribute responseTime: TimeValue {:>> unit = "ms"; :>> range = "10..500";}
    }
}
```
