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
private import NeuralNetworkModel::*;
private import BaseTypes::*;
private import Safety::*;
```
# Control units
```SysML::OpenBoardnet::CU
part def ControlUnit :> ControlUnit_Base{
    attribute severity: DimensionOneValue = 3.0;
    attribute exposure: DimensionOneValue = 4.0;
    attribute controllability: DimensionOneValue = 3.0;
    attribute asilLevel: DimensionOneValue = Safety::calcASIL(severity, exposure, controllability);
    attribute fclk: FrequencyValue {:>> range = 0.1..100000 [MHz];} 
    attribute opsPerCyle:  IntegerInRange {:>> range default = 1..1000;}
    attribute FLOPS_Hardware: FrequencyValue =  fclk * ToReal(opsPerCyle)  {:>> unit= "GFLOPS";} 
    attribute Memory_Hardware : StorageCapacityValue {:>> range = 1.0 .. 100000.0 [MB];}
}
```
```SysML::OpenBoardnet::CU
part zonalControllerFront: ControlUnit;
part centralController: ControlUnit;
part gateway: ControlUnit;
part cameraController: ControlUnit;
part ultrasonicController: ControlUnit;
part radarAndLidarController: ControlUnit;
part zonalControllerRear: ControlUnit;
```
## ADAS Controller running the Yolov5n model
```SysML::OpenBoardnet::CU
part def ADASController :> ControlUnit { 
    part runningModel : Yolov5n;
    attribute FLOPs_Total: DimensionOneValue = runningModel::FLOPsTotal {:>> unit = "GFLOPs";}
    attribute Memory_Total: StorageCapacityValue = runningModel::MemoryTotal {:>> unit = "MB";}
    attribute T : DurationValue = FLOPs_Total / FLOPS_Hardware {:>> unit = "ms";}
    attribute R : DurationValue = 33.0[ms] {:>> unit = "ms";} // 30 fps, R = maxLatency   
    assert constraint TimeConstraint { R >= T }
    assert constraint MemoryConstraint { Memory_Hardware >= Memory_Total }
}
```
## ARM Cortex-m7
```SysML::OpenBoardnet::CU
part def ARMCortex :> ADASController { //ARM Cortex-m7
    :>> fclk {:>> range = 240.0 [MHz];}
    :>> opsPerCyle = 2; //4 cores with 32 Ops per cylce
    :>> Memory_Hardware = 2.0 [MB];
}
```
## MacBook M4
```SysML::OpenBoardnet::CU
part def macbookM4 :> ADASController { 
    :>> fclk = 4.4 [GHz];
    :>> opsPerCyle = 4*32; //4 cores with 32 Ops per cylce
    :>> Memory_Hardware = 16000.0[MB];
}
```
