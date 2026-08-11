---
title: Actuators
name:  actuators
---
Convert control commands into physical actions (e.g., braking, steering). They operate in conjunction with assigned ECUs and implemented software components.
```SysML::OpenBoardnet
package Actuators { 
    private import Hardware::*;
    
    part def Actuator :> Actuator_Base;    
    
    part def SteeringActuator :> Actuator {
        attribute angle: DimensionOneValue {:>> range = -450..450 [°];}
        attribute responseTime: TimeValue {:>> range = 10..500 [ms];}
    }
}
```
