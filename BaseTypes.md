---
title: BaseTypes
name:  BaseTypes
---
Defines the fundamental data types, units of measure (SI units), and value types used across the entire model. 
It ensures semantic consistency for all attributes like length, mass, or speed throughout the project.
```SysML::OpenBoardnet
package BaseTypes {  

    private import Reliability::*;
    private import Safety::*;
    
    
    // An abstract 'Feature' that other features will specialize.
    abstract part def Feature;

    // An abstract 'Component' for software components.
    abstract part def Component;
    
    // An abstract 'Function' for system functions.
    abstract part def Function;
    
    
}
```
