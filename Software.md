---
title: Software
name:  sw
---
Implements logical functions on compute units. Through allocation relationships (executedBy), it is assigned to specific hardware components, e.g., AbsSoftware on CentralECU.
```SysML::OpenBoardnet
package Software {
    private import BaseTypes::*;

    part def SpeedControlSW :> Component {
        attribute inputSpeed: SpeedValue {:>> range = -20..200 [km/h];}
        attribute controlSignal: Boolean;
        attribute processingLatency: DurationValue {:>> unit = "ms";}
    }

    part def ObstacleAvoidanceSW :> Component {
        in attribute distance: LengthValue {:>> unit = "m";}
        out attribute brakeCommand: Boolean;
        attribute detectionConfidence: RealInRange {:>> range = 0.0..1.0;}
    }
    
    part def NeuralNetworkInferenceSW :> Component {
        attribute modelSize: StorageCapacityValue {:>> unit = "MB";}
        attribute inferenceTime: DurationValue {:>> unit = "ms";}
        attribute accuracy: RealInRange {:>> range = 0.0..1.0;}
    }
}
```
