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
    attribute ipc:  IntegerInRange {:>> range default= "1..10000";}
    attribute opsPerInstruction: IntegerInRange {:>> range = "1..10000";}
    attribute FLOPS: FrequencyValue =  fclk * ToReal(opsPerInstruction) * ToReal(ipc) ;
}

part zonalControllerFront: ControlUnit;
part centralController: ControlUnit;
part gateway: ControlUnit;
part cameraController: ControlUnit;
part ultrasonicController: ControlUnit;
part radarAndLidarController: ControlUnit;
part zonalControllerRear: ControlUnit;

part def testCPU :> ControlUnit {
    :>> fclk {:>> range ="1..100"; :>> unit ="MHz";} 
    :>> opsPerInstruction = 4;
    :>> ipc = 2;         
    part runningModel: LeNeT5;
    attribute FLOPsLeNeT5: Integer = runningModel::totalFLOPs;
    attribute executionTimeLeNet5: DurationValue = ToReal(FLOPsLeNeT5) / FLOPS {:>> unit = "ms";}
    attribute maximumExecutionTime: DurationValue = 2.0 [ms];
    assert constraint {executionTimeLeNet5 <= maximumExecutionTime}
} 
part def ADASController :> ControlUnit {
    attribute :>> fclk = 100.0 [MHz];
    :>> opsPerInstruction = 4;
    :>> ipc = 2;     
    part runningModel : Yolov5n;
    attribute executionTime : DurationValue = ToReal(runningModel::totalFLOPs) / FLOPs {:>> unit = "ms";}

    // ADAS Hard-Deadline (ISO 26262 ASIL-B konform)
    attribute maxLatency : DurationValue = 33.0[ms]; // 30 fps
    //assert constraint { executionTime <= maxLatency }

    // Speicher-Constraint (z.B. 2 MB SRAM-Limit)
    attribute maxMemory : StorageCapacityValue = 2.0[MB];
    assert constraint { runningModel::totalMemory <= maxMemory }
}
```
# Neural Network Model
## Neural Network Layer Base
```SysML::OpenBoardnet
package NeuralNetworkModel {
    private import BaseTypes::*;
    
    // Abstract base type for all layers
    part def NeuralNetworkLayer {
        attribute FLOPs: Integer;
        attribute Memory: StorageCapacityValue;
        part precision: PrecisionTypes::Precision;
    }
}
```
## Layer Definitions
### Dense Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def DenseLayer :> NeuralNetworkLayer {
    attribute inputNeurons: Integer;
    attribute outputNeurons: Integer;
        
    // FLOPS calculation
    :>> FLOPs = 2 * inputNeurons * outputNeurons;

    // Memory calculation
    :>> Memory = ToReal(inputNeurons * outputNeurons + outputNeurons) * precision::size {:>> unit = "kB";}
}
```
### Convolution Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def ConvLayer :> NeuralNetworkLayer {
    attribute kernelSize: Integer;
    attribute numFilters: Integer;
    attribute inputHeight: Integer;
    attribute inputWidth: Integer;
    attribute numChannels: Integer;
    attribute stride: Integer;
    attribute padding: Integer;   
    attribute outputHeight: Integer = floor((inputHeight - kernelSize + 2 * padding) / stride) + 1;
    attribute outputWidth: Integer = floor((inputWidth - kernelSize + 2 * padding) / stride) + 1;
    
    :>> FLOPs = 2 * kernelSize^2 * numChannels * numFilters * outputHeight * outputWidth;  
    :>> Memory = ToReal(kernelSize^2 * numChannels * numFilters + numFilters) * precision::size {:>> unit = "kB";}
}
```
### Pooling Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def PoolingLayer :> NeuralNetworkLayer {
    attribute kernelSize: Integer;
    attribute inputHeight: Integer;
    attribute inputWidth: Integer;
    attribute numChannels: Integer;
        
    :>> FLOPs = (kernelSize^2 - 1) * inputHeight * inputWidth * numChannels;
    // Memory requirement is negligible
    :>> Memory = 0.0 [B];
}
```
## Batch Normalization Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def BatchNormLayer :> NeuralNetworkLayer {
    attribute numNeurons: Integer;
        
    :>> FLOPs = 2 * numNeurons;
    :>> Memory = ToReal(4 * numNeurons) * precision::size {:>> unit = "kB";}
}
```
## SiLu Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def SiLULayer :> NeuralNetworkLayer {
    attribute numChannels : Integer;
    attribute inputHeight : Integer;
    attribute inputWidth  : Integer;

    :>> FLOPs  = 4 * numChannels * inputHeight * inputWidth;
    :>> Memory = 0.0[B];
}
```
## LeNeT5 Architecture
```SysML::OpenBoardnet::NeuralNetworkModel
// Define a Neural Network with example layers
part def LeNeT5 {
    part layer1 : ConvLayer {
        :>> kernelSize = 5;
        :>> numFilters = 6;
        :>> inputHeight = 32;
        :>> inputWidth = 32;
        :>> numChannels = 1;
        :>> stride = 1;
        :>> padding = 0;
        part precision: BaseTypes::PrecisionTypes::Float16;
    }
    part layer2 : PoolingLayer {
        :>> kernelSize = 2;
        :>> inputHeight = 28;
        :>> inputWidth = 28;
        :>> numChannels = 6;
    }
    part layer3 : ConvLayer {
        :>> kernelSize = 5;
        :>> numFilters = 16;
        :>> inputHeight = 14;
        :>> inputWidth = 14;
        :>> numChannels = 6;
        :>> stride = 1;
        :>> padding = 0;
        part precision: BaseTypes::PrecisionTypes::Float16;
    }
    part layer4 : PoolingLayer {
        :>> kernelSize = 2;
        :>> inputHeight = 10;
        :>> inputWidth = 10;
        :>> numChannels = 16;
    }
    part layer5 : DenseLayer {
        :>> inputNeurons = 400;
        :>> outputNeurons = 120;
        part precision: BaseTypes::PrecisionTypes::Float16;
    }
    part layer6 : DenseLayer {
        :>> inputNeurons = 120;
        :>> outputNeurons = 84;
        part precision: BaseTypes::PrecisionTypes::Float16;
    }

    part layer7 : DenseLayer {
        :>> inputNeurons = 84;
        :>> outputNeurons = 10;
        part precision: BaseTypes::PrecisionTypes::Float16;
    }

    // Compute total FLOPS and memory for the entire network
    attribute totalFLOPs : ScalarValues::Integer = sumOverParts(FLOPs);
    attribute totalMemory : StorageCapacityValue = sumOverParts(Memory) {:>> unit = "kB";}
}
```
