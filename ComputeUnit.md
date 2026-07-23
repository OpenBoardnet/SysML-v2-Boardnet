---
title: ComputeUnit
name: CU
logo: Files/car-small.png
---
# Contents
[toc]
# Control and Compute Units

Hardware components responsible for executing software and processing data. 
These include domain-specific controllers, zonal controllers (e.g., ZonalControllerFront)
and central high-performance computers (e.g., AI_ECU) with specific computational capacities.

Controllers in this model are generic processors that are characterized by:
- **Computational Load**: Defined by allocated software (via `executedBy` relationships).
- **Hardware Performance**: Specified by high-level parameters:
    - `fclk`: The clock frequency.
    - `ipc`: Average instructions per cycle.
    - `throughput`: Processing throughput.
    - `taskSize`: The size of the task per cycle.
    - `latency`: Calculated processing delay in milliseconds.
- **Costs**: Financial cost for the unit and connectors.

*(Note: The physical position is determined by the `Allocation` to a `Location` in the Technical Architecture, not defined inside the component itself.)*
```SysML::OpenBoardnet::CU
private import Hardware::*;
private import ScalarValues::*;
private import ISQ::*;
private import Quantities::*;
private import Ranges::*;
private import Safety::*;
private import NeuralNetworkModel::*;
private import BaseTypes::*;
```
# Control units
```SysML::OpenBoardnet::CU
part def ControlUnit :> ControlUnit_Base{
    attribute severity: DimensionOneValue = 3.0;
    attribute exposure: DimensionOneValue = 4.0;
    attribute controllability: DimensionOneValue = 3.0;
    attribute asilLevel: DimensionOneValue = Safety::calcASIL(severity, exposure, controllability);
    attribute fclk: FrequencyValue {:>> unit= "MHz"; :>> range= "0.1 .. 10000";} 
    attribute opsPerCyle:  IntegerInRange {:>> range default= "1..10000";}
    attribute FLOPS_Hardware: FrequencyValue =  fclk * ToReal(opsPerCyle)  {:>> unit= "GFLOPS";} 
}

part zonalControllerFront: ControlUnit;
part centralController: ControlUnit;
part gateway: ControlUnit;
part cameraController: ControlUnit;
part ultrasonicController: ControlUnit;
part radarAndLidarController: ControlUnit;
part zonalControllerRear: ControlUnit;


part def ADASController :> ControlUnit { //ARM Cortex-m7
    attribute :>> fclk = 480.0 [MHz];
    :>> opsPerCyle = 2;
    part runningModel : Yolov5n;
    attribute T : DurationValue = runningModel::FLOPsTotal / FLOPS_Hardware;

    // ADAS Hard-Deadline (ISO 26262 ASIL-B konform)
    attribute R : DurationValue = 33.0[ms]; // 30 fps, R = maxLatency
    //assert constraint { R <= T }

    // Speicher-Constraint (2 MB SRAM-Limit)
    attribute MemoryHardware : StorageCapacityValue = 2.0[MB];
    //assert constraint { runningModel::MemoryTotal <= MemoryHardware }
}

part def test :> ControlUnit { //ARM Cortex-m7
    attribute :>> fclk {:>> unit ="GHz"; :>>range="0.1..10";}
    :>> opsPerCyle = 2; //4 cores with 32 Ops per cylce
    part runningModel : Yolov5n;
    attribute T : DurationValue = runningModel::FLOPsTotal / FLOPS_Hardware {:>> unit = "ms";}

    // ADAS Hard-Deadline (ISO 26262 ASIL-B konform)
    attribute R : DurationValue = 33.0[ms]; // 30 fps, R = maxLatency
    //assert constraint { R >= T }

    // Speicher-Constraint (z.B. 2 MB SRAM-Limit)
    attribute MemoryHardware : StorageCapacityValue = 2.0[MB];
    //assert constraint { runningModel::MemoryTotal <= MemoryHardware }
}

part def macbookM4 :> ControlUnit { //ARM Cortex-m7
    attribute :>> fclk = 4.4 [GHz];
    :>> opsPerCyle = 4*32; //4 cores with 32 Ops per cylce
    part runningModel : Yolov5n;
    attribute T : DurationValue = runningModel::FLOPsTotal / FLOPS_Hardware {:>> unit = "ms";}

    // ADAS Hard-Deadline (ISO 26262 ASIL-B konform)
    attribute R : DurationValue = 33.0[ms]; // 30 fps, R = maxLatency
    //assert constraint { R <= T }

    // Speicher-Constraint (z.B. 2 MB SRAM-Limit)
    attribute MemoryHardware : StorageCapacityValue = 2.0[MB];
    //assert constraint { runningModel::MemoryTotal <= MemoryHardware }
}
```
