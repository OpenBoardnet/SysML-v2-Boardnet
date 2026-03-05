![OpenCar](Files/car.png)
# The "OpenBoardNet" as a SysML v2 library

This is the "OpenBoardnet" as a SysML v2 library for modeling automotive boardnet architectures. It runs with the SysML v2 tool "SysMD", for example. The SysML v2 library provides an overall framework for creating instances of boardnet architectures. For this purpose, we consider the use cases:

- **Interactive modeling of an instance of a in-vehicle network.** For this case, the SysML v2 library provides the definitions of views, parts, requirements, etc.
- **Analysis of instances of in-vehicle networks.** In this case, the library provides definitions of calculations that permit checking the consistency. Such calculations are done by the tool SysMD, for example.

# Objective

The objective is to permit a very early analysis of performance indicators for board net architectures and its components. For this purpose, the OpenCar uses parameterized models and constraint propagation techniques.

The library models the following aspects:
- **Features** and its mapping to controllers, compute units, sensors, and network components;
- **Processor** and controller usage by the mapped features;
- **Network** components (gateways, wires) and its throughput constraints;
- **Sensors** and other data sources;
- **Topology** of the board net and its impact on weight, costs, and performances.
# Packages & Files structure
Each package is a "notebook" that includes documentation and code.
The OpenBoardnet library is structured as follows:

- **[OpenBoardnet (Main)](OpenBoardnet.md)**: The central entry point definition.
- **[BaseTypes](BaseTypes.md)**: Fundamental definitions and types.
- **[Requirements](Requirements.md)**: System requirements.
- **[Features & Functions](FeaturesFunctions.md)**: Logical features.
- **[Logical Architecture](LogicalArchitecture.md)**: Abstract architecture.
- **[Technical Architecture](TechnicalArchitecture.md)**: Physical realization.
- **[Allocations](Allocations.md)**: Mapping logic to hardware/location.
- **[Compute Units (CU)](CU.md)**: Controllers and ECUs.
- **[Network](Network.md)**: Wires, buses (CAN, Ethernet) and connection definitions.
- **[Sensors](Sensors.md)**: Sensor definitions.
- **[Actuators](Actuators.md)**: Actuator definitions.
- **[Locations & Spaces](LocationsAndSpaces.md)**: 3D coordinates and installation spaces.
- **[Topology](Topology.md)**: Connectivity graph.
- **[Safety](Safety.md)**: ASIL and safety concepts.
- **[Reliability](Reliability.md)**: MTBF, MTTR, metrics.
# Disclaimer

The development of the tools SysMD and AGILA was and is sponsored by the German BMBF within the following projects: 
- Arrowhead Tools (concepts, SysML v2) 
- GENIAL! (tools)
- KI4BoardNet (models, advanced features)
