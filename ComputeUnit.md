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
    :>> fclk {::> range ="1..100"; ::> unit ="MHz";} 
    :>> opsPerInstruction = 4;
    :>> ipc = 2;         
    part runningModel: LeNeT5;
    attribute FLOPsLeNeT5: Integer = runningModel::totalFLOPs;
    attribute executionTimeLeNet5: DurationValue = ToReal(FLOPsLeNeT5) / FLOPS {:>> unit = "ms";}
    attribute maximumExecutionTime: DurationValue = 2.0 [ms];
    assert constraint {executionTimeLeNet5 <= maximumExecutionTime}
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
    attribute :>> FLOPs = 2 * inputNeurons * outputNeurons;

    // Memory calculation
    attribute :>> Memory = ToReal(inputNeurons * outputNeurons + outputNeurons) * precision::size {:>> unit = "kB";}
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
    // Output shape calculation
    attribute outputHeight: Integer = (inputHeight - kernelSize + 2 * padding) / stride + 1;
    attribute outputWidth: Integer = (inputWidth - kernelSize + 2 * padding) / stride + 1;
    // FLOPS calculation
    attribute :>> FLOPs = 2 * kernelSize^2 * numChannels * numFilters * outputHeight * outputWidth;  
    // Memory calculation
    attribute :>> Memory = ToReal(kernelSize^2 * numChannels * numFilters + numFilters) * precision::size {:>> unit = "kB";}
}
```
### Pooling Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def PoolingLayer :> NeuralNetworkLayer {
    attribute kernelSize: Integer;
    attribute inputHeight: Integer;
    attribute inputWidth: Integer;
    attribute numChannels: Integer;
        
    // FLOPS calculation
    attribute :>> FLOPs = (kernelSize^2 - 1) * inputHeight * inputWidth * numChannels;

    // Memory requirement is negligible
    attribute :>> Memory = 0.0 [B];
}
```
## Batch Normalization Layer
```SysML::OpenBoardnet::NeuralNetworkModel
part def BatchNormLayer :> NeuralNetworkLayer {
    attribute numNeurons: Integer;
        
    // FLOPS calculation
    attribute :>> FLOPs = 2 * numNeurons;

    // Memory calculation
    attribute :>> Memory = ToReal(4 * numNeurons) * precision::size {:>> unit = "kB";}
}
```
## LeNeT5 Architecture
```SysML::OpenBoardnet::NeuralNetworkModel
// Define a Neural Network with example layers
part def LeNeT5 {
    part layer1 : ConvLayer {
        attribute :>> kernelSize : ScalarValues::Integer = 5;
        attribute :>> numFilters : ScalarValues::Integer = 6;
        attribute :>> inputHeight  = 32;
        attribute :>> inputWidth  = 32;
        attribute :>> numChannels  = 1;
        attribute :>> stride  = 1;
        attribute :>> padding  = 0;
        part precision : BaseTypes::PrecisionTypes::Float16;
    }
    part layer2 : PoolingLayer {
        attribute :>> kernelSize = 2;
        attribute :>> inputHeight  = 28;
        attribute :>> inputWidth  = 28;
        attribute :>> numChannels  = 6;
    }
    part layer3 : ConvLayer {
        attribute :>> kernelSize = 5;
        :>> numFilters : ScalarValues::Integer = 16;
        attribute :>> inputHeight  = 14;
        attribute :>> inputWidth  = 14;
        attribute :>> numChannels  = 6;
        attribute :>> stride  = 1;
        attribute :>> padding  = 0;
        part precision : BaseTypes::PrecisionTypes::Float16;
    }
    part layer4 : PoolingLayer {
        attribute :>> kernelSize = 2;
        attribute :>> inputHeight  = 10;
        attribute :>> inputWidth  = 10;
        attribute :>> numChannels  = 16;
    }
    part layer5 : DenseLayer {
        attribute :>> inputNeurons  = 400;
        attribute :>> outputNeurons  = 120;
        part precision : BaseTypes::PrecisionTypes::Float16;
    }
    part layer6 : DenseLayer {
        attribute :>> inputNeurons  = 120;
        attribute :>> outputNeurons  = 84;
        part precision : BaseTypes::PrecisionTypes::Float16;
    }

    part layer7 : DenseLayer {
        attribute :>> inputNeurons  = 84;
        attribute :>> outputNeurons  = 10;
        part precision : BaseTypes::PrecisionTypes::Float16;
    }

    // Compute total FLOPS and memory for the entire network
    attribute totalFLOPs : ScalarValues::Integer = sumOverParts(FLOPs);
    attribute totalMemory : StorageCapacityValue = sumOverParts(Memory) {:>> unit = "kB";}
}
```
