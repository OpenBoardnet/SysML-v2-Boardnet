![OpenCar](Files/car.png)
# The "OpenBoardNet" as a SysML v2 library 

This is the "OpenBardnet" as a SysML v2 library for modeling automotive boardnet architectures. 
It runs with the SysML v2 tool "SysMD" and makes use of its extensions for

- interactive modeling
- analysis by constraint propagation

Note that it is not yet uploaded as we are doing some final quality assurance of the models. 

# Objective

The objective is to permit a very early analysis of performance indicators for board net architectures 
and its components. 
For this purpose, the OpenCar uses parameterized models and constraint propagation techniques. 

The library models the following aspects: 
- _Features_ and its mapping to controllers, compute units, sensors, and network components;
- _Processor_ and controller usage by the mapped features; 
- _Network_ components (gateways, wires) and its throughput constraints; 
- _Sensors_ and other data sources; 
- _Topology_ of the board net and its impact on weight, costs, and performances.

# Packages
Each package is a "notebook" that also includes documentation.
The OpenCar library is structured in the following packages: 
- [openCar](openCar.md)
- [features](features.md)
- [controllers](controllers.md)
- [network](network.md)
- [sensors](sensors.md)
- [topology](topology.md)
# Disclaimer

The development of the tools SysMD and AGILA was and is sponsored by the German BMBF within
the following projects: 
- Arrowhead Tools (concepts, SysML v2) 
- GENIAL! (tools)
- KI4BoardNet (models, advanced features)
