---
title: Requirements
name:  requirements
---
Define mandatory system requirements as the starting point of the development process. They specify functional and non-functional conditions that must be fulfilled by features and functions.
```SysML::OpenBoardnet
package Requirements {
    private import ISQ::*;
    private import ScalarValues::*;

    // --- Cruise Control Subject & Req ---
    part def CruiseControl {
        attribute actualSpeed: SpeedValue {:>> unit ="km/h"; :>> range = "-20..200";}
        attribute targetSpeed: SpeedValue {:>> unit = "km/h"; :>> range = "0..200";}
    }

    requirement def SpeedAccuracyRequirement {
        subject cc: CruiseControl;
        attribute maxDeviation: SpeedValue {:>> unit = "km/h"; :>> range = "0..5";}
        constraint deviationOK {
            abs(cc::actualSpeed - cc::targetSpeed) <= maxDeviation
        }
    }

    // --- Global Vehicle Subject & Reqs ---
    abstract part def Vehicle {
        attribute totalLength: LengthValue {:>> unit = "m";}
        attribute totalWeight: MassValue {:>> unit = "kg";}
        attribute totalLengthofCable: LengthValue {:>> unit = "m";}
        attribute totalCosts: AmountOfMoneyValue; 
    }

    requirement def VehicleWeightRequirement {
        subject v: Vehicle;
        attribute maxWeight: MassValue {:>> unit = "kg"; :>> range = "1000..3500";}
        constraint { v::totalWeight <= maxWeight }
    }
    
    requirement def VehicleLengthRequirement {
        subject v: Vehicle;
        attribute maxLength: LengthValue {:>> unit = "m"; :>> range = "3.0..6.0";}
        constraint { v::totalLength <= maxLength }
    }
    
    requirement def VehicleCostRequirement {
        subject v: Vehicle;
        attribute maxCost: AmountOfMoneyValue {:>> unit = "EUR"; :>> range = "10000..100000";}
        constraint { v::totalCosts <= maxCost }
    }
    
    requirement def VehicleCableLengthRequirement {
        subject v: Vehicle;
        attribute maxCableLength: LengthValue {:>> unit = "m"; :>> range = "100..5000";}
        constraint { v::totalLengthofCable <= maxCableLength }
    }

    // --- EBA Subject & Req ---
    part def EBA_System {
        attribute minDetectionDistance: LengthValue {:>> unit = "m";}
        attribute timeToCollision: TimeValue {:>> unit = "s";}
    }
    
    requirement def EmergencyBrakeAssistRequirement {
        subject eba: EBA_System;
        attribute requiredDistance: LengthValue {:>> unit = "m"; :>> range = "1..200";}
        constraint { eba::minDetectionDistance >= requiredDistance }
    }
}
```
