# Versioning
_This page documents instructions for the legacy compiler. The documentation for the current foreseeti compiler can be found [here](https://github.com/mal-lang/mal-documentation/wiki/Compiling)._

***

The compiler generates Java code from the MAL specification. More details regarding compilation can be found in the MAL Compiler User Guide and in the [GIT repository](https://github.com/mal-lang). MAL is compiled with the following commands:

| Assertion method | Description
| --- | --- |
| assertUncompromised() | Check for unsuccessful compromise of the attack step.
| assertUncompromisedFrom(parent) | Check for unsuccessful compromise of the attack step from the specified parent attack step.
| assertCompromisedInstantaneously() | Check for successful and immediate compromise of the attack step.
| assertCompromisedWithEffort() | Check for successful compromise of the attack step but only after some effort/time is spent.
| assertCompromisedInstantaneouslyFrom() | Same as assertCompromisedInstantaneously from specified parent attack step.
| assertCompromisedWithEffortFrom() | Same as assertCompromisedWithEffort from specified parent attack step.

```
java -cp mal-compiler-0.0.X.jar se.kth.mal.Master -i <input_path>  
-o <ouput_path> -p <package_name> -t <tests_path>


usage: 
 X to be replaced by the version number. 
 -f,--foreseeti       flag to use foreseeti backend
 -i,--input <arg>     input mal file path
 -o,--output <arg>    output folder path for generated code
 -p,--package <arg>   package name of generated code
 -t,--tests <arg>     output folder path for generated test code
 -v,--visual <arg>    icons for visualization
```

The output of a successful compilation will look like:
```
>>> Using MAL Compiler <<<
Reading from <input>.malinc
...
Attempting to write the TTC Config File...
Complete
```

Several files will be created in the specified output folder. One of these is the html file, which can be opened in a web browser to visually represent the attack graph. The attack graph's large circles are the assets, the small circles are the attack steps and the squares are defences.
