_This page contains MAL source code examples. For a syntax overview, see [MAL Syntax](https://github.com/mal-lang/mal-documentation/wiki/MAL-Syntax#MAL-Symbols)._

# Table of contents
[Basic MAL Example](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#basic-mal-example)

[MAL Demonstrations](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#mal-demonstrations)

[- Language Constituents](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#language-constituents)

[-- Define](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#define)

[-- Include](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#include)

[-- Category](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#category)

[-- Asset](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#asset)

[-- Extends](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#extends)

[-- Abstract asset](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#abstract-asset)

[-- User info](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#user-info)

[-- Developer info](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#developer-info)

[-- Modeler info](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#modeler-info)

[-- Let](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#let)

[-- Association](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#association)



[- MAL Symbols](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#mal-symbols)

[-- Leads to (->)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#leads-to--)

[-- Append (+>)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#append-)

[-- OR-step (|)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#or-step-)

[-- AND-step (&)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#and-step)

[-- Defense (\#)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#defense-)

[-- Existence (E)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#existence-e)

[-- Non-existence (!E)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#non-existence-e)

[-- Require (<-)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#require--)

[-- TypeOf ([])](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#typeof-)

[-- Union (\\/)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#union)

[-- Intersection (/\\)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#intersection-)

[-- Difference (-)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#difference-)

[-- Collect operator (X.A)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#collect-operator-xa)

[-- Transitive operator (X*.A)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#transitive-operator-xa)

[-- Let substitution (X().A)](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#let-substitution-xa)

[-- Hidden step](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#hidden-step)

[-- Debug step](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#-debug-step)

[-- Trace origin](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#trace-origin)

[-- Risk type](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#risk-type)


# Basic MAL Example

MAL, like other languages, has its own syntax and alphabet of symbols. Each asset may have attack or defense steps, and can have associations between each other. The MAL symbols can be seen below. The associations between assets follow the structure of UML class diagrams, where associations are bound to types with cardinalities.

```
category System {
    asset Computer {
        let allFolders = folder.subFolder*        

        | connect
            -> access
        E firewallExists
            <- firewall
            -> firewall.bypass
        E! noFirewall
            <- firewall
            -> firewallBypassed
        | firewallBypassed @hidden
            -> access
        | vulnerability
            -> compromise
        & access
            -> compromise
	& compromise
            ->  allFolders().accessFolder
                // Let substitution
    }
    asset Folder {
        | accessFolder
            -> stealSecrets
        | stealSecrets
    }
}

category Security {
    asset Firewall {
        & bypass [Bernoulli(0.2)]
            -> computer.firewallBypassed
        # hardened
            -> bypass
    }
}

associations {
    Computer [computers] * <-- Protect --> 0..1 [firewall] Firewall
    Computer [computer] * <-- Contains --> * [folder] Folder
    Folder [folder] 1 <-- Contains --> * [subFolder] Folder
}
```

# MAL Demonstrations
## Language Constituents
### Define
```
// Generic syntax
#key1: "value"

// Intuition
The ID "key1" contains the project-wide information "value"

// Example
#id: "com.code.example.MAL"
#version: "1.0.0"

//Interpretation
The name of the MAL project is MAL.example.code.com (reverse domain name notation). The current version of the language is 1.0.0.
```


### Include
```
// Simplified syntax
include "./my/spec.mal"

// Intuition
Include the source code of spec.mal in the current specification.

// Example
include "networking.mal"
include "systems/linux.mal

//Interpretation
Includes the source code from networking into the current specification. Additionally includes the source code in linux.mal found under the systems directory relative to the current specification. It is now possible to reference assets and attack steps of both networking.mal and linux.mal.
```


### Category
```
// Simplified syntax
category foo
{
[...]
}

// Intuition
Assets defined within the curly braces are categorized as foo.

// Example
category System
{
[...]
}

category Security
{
[...]
}

//Interpretation
Asset defined within the first category are classified as System assets. Assets defined within the second are classified as Security assets.
```

### Asset
```
// Simplified syntax
asset Foo
{
[...]
}

// Intuition
The MAL representation of a physical or logical object Foo.

// Example
asset Computer
{
[...]
}

asset Software
{
[...]
}

//Interpretation
Computer is the MAL representation of a physical computer system. Software is the MAL representation of executable applications.
```

### Extends
```
// Simplified syntax
asset Parent
{
[parent logic]
}

asset Child extends Parent
{
[parent + child logic]
}

// Intuition
The asset Child is a sub-class of Parent. Child additionally inherits the logic specified for its parent.

// Example
asset OperatingSystem
{
[OperatingSystem logic]
}

asset Linux
{
[OperatingSystem + Linux logic]
}

//Interpretation
Linux is a type of OperatingSystem. Therefore, what applies for generic OperatingSystems also applies to all instances of Linux systems in the language.
```

### Abstract asset
```
// Simplified syntax
abstract asset Foo
{
[Foo logic]
}

asset Bar extends Foo
{
[Foo + Bar logic]
}

// Intuition
The MAL representation of the object Foo, which cannot be instantiated. However, its sub-class Bar may be instantiated.

// Example
abstract asset OperatingSystem
{
[OperatingSystem logic]
}

asset Linux
{
[OperatingSystem + Linux logic]
}

//Interpretation
The language contains logic for generic operating systems. However, only concrete sub-classes, like Linux, may be instantiated.
```

### User info
```
// Simplified syntax
asset Foo
  user info: "Hello"
{
  | bar
    user info: "World"
}

// Intuition
"Hello" is information for end users regarding Foo. "World" is end user information regarding the attack step bar in asset Foo.

// Example
asset Software
  user info: Software represents application running on your computer systems.
{
  | attack
    user info: Software can be attacked in multiple ways, which leads to undesirable consequences.
}

//Interpretation
Asset level user information can provide a description of what the asset corresponds to in reality. Logic level user information can explain what the step means.
```

### Developer info
```
// Simplified syntax
asset Foo
  developer info: "Hello"
{
  | bar
    developer info: "World"
}

// Intuition
"Hello" is information for other MAL developers regarding Foo. "World" is developer information regarding the attack step bar in asset Foo.

// Example
asset Software
  developer info: We do not model specific software at this point in time. Instantiate as needed.
{
  | attack
    developer info: Stub logic until a vulnerability specification is developed.
}

//Interpretation
Asset level developer information can, for example, explain why an asset is needed or if other assumptions have been made. Logic level developer information can, for example, explain why a step is necessary when it is not apparent.
```

### Modeler info
```
// Simplified syntax
asset Foo
  modeler info: "Hello"
{
  | bar
    modeler info: "World"
}

// Intuition
"Hello" is information for parser developers or modelers regarding Foo. "World" is parser/modeler information regarding the attack step bar in asset Foo.

// Example
asset Software
  developer info: Give appropriate names during parsing.
{
  | attack
    developer info: Reached from associated vulnerabilities. Attach to Software during parsing based on vulnerability scanner output.
}

//Interpretation
Asset level modeler information can, for example, explain how the asset should be treated during the parsing process. Logic level developer information can, for example, explain assumptions made by the MAL developers regarding parsing contra language responsibilities.
```

### Let
```
// Simplified syntax
let name  = foo \/ bar

// Intuition
The union of all assets foo and bar is given the label name.

// Example
asset Computer
{
  let components = software \/ hardware
}

//Interpretation
The name components can be used to refer to the union of software and hardware assets associated with any given instance of Computer.
```

### Association
```
// Simplified syntax
Asset1 [foo] * <-- connects --> * [bar] Asset2

// Intuition
Any number of Asset1 instantiations can be connected to any number of Asset2 instantiations. Inline references from Asset1 to Asset2 use the name bar. Conversely, Asset2 refers to Asset1 with the name foo.

// Example
Computer [host] 1 <-- runs --> 1-* [os] OperatingSystem

//Interpretation
One Computer must be associated with at least one type of operating system. References from Computer assets to its associated OperatingSystem assets use the format 'os.attackStep'. Reverse references from OperatingSystem assets to the underling Computer use the format 'host.attackStep'.
```



## MAL Symbols
### Leads to (->)
```
// Simplified syntax
| step1
   -> step2,
      step3

// Intuition
If step 1 is reached, then, this leads to the subsequent steps 2 and 3.

// Example
| authenticate
  -> readData,
     writeData,
     deleteData

//Interpretation
If the attacker gains authenticated access, they can now attempt to read, write, and delete data as a result.
```

### Append (+>)
```
// Simplified syntax
asset Parent
{
  | step1
    -> foo
}

asset Child extends Parent
{
  | step1
    +> [foo,] // 'foo,' is implicit.
       bar
}
// Intuition
The element step1 is defined multiple times in the inheritance hierarchy. Rather than overriding the Parent logic, add any new logic from Child to the pre-existing logic.

// Example
asset OperatingSystem
{
  | spyware
    -> logKeystrokes
}

asset Linux
{
  | spyware
    +> readBashHistory
}

//Interpretation
If a system is infected with spyware, the user's keystrokes may be logged. Additionally, it is assumed that spyware might both log keystrokes and read the Bash history file on Linux systems.
```

### OR-step (|)
```
// Simplified syntax
| step1
  -> step3
| step2
  -> step3
| step3

// Intuition
Reaching either step1 or step2 is enough to reach step3.

// Example
| guessPassword
  -> authenticate
| stealPassword
  -> authenticate
| authenticate

//Interpretation
An attacker may either guess or steal a password to obtain authenticated access to some asset.
```

### AND-step (&)
```
// Simplified syntax
| step1
  -> step3
| step2
  -> step3
& step3

// Intuition
Reaching both step1 or step2 is required to reach step3.

// Example
| obtainPassword
  -> authenticate
| obtainMFAToken
  -> authenticate
& authenticate

//Interpretation
Attacking an asset with multi-factor authentication requires the attacker to obtain both the password and the additional code, e.g., produced by a separate authenticator or sent via text messages to a user.
```

### Defense (\#)
```
// Simplified syntax
# step1
 -> step2

// Intuition
If "step1" is done (enabled, implemented; imp(step1) = 1), then "step2" is not reachable by the attacker

// Example
# encrypted
 -> read

// Interpretation
If data is encrypted, then it cannot be read.
```

### Existence (E)
```
//Simplified syntax
E existenceCheck
 <- preconditions
 -> step1

// Intuition
If some preconditions for the existenceCheck are satisfied, then "step1" becomes reachable.

// Example
E hasCamera
 <- hardware[Camera]
 -> hijackCamera

// Interpretation
If the system, e.g. a laptop, has a built-in camera, then the camera asset be attacked.
```

### Non-existence (!E)
```
// Simplified syntax
!E nonExistenceCheck
 <- preconditions
 -> step1

// Intuition
If any preconditions for the nonExistenceCheck are NOT satisfied, then "step1" becomes reachable.

// Example:
!E noAntiMalwareInstalled
 <- applications[AntiMalware]
 -> infectWithMalware

// Interpretation:
If anti-malware software not present on a host system, then it can be infected with malware.
```

### Require (<-)
```
// Simplified syntax
E existenceCheck
 <- precondition1,
    precondition2
 -> step1
!E nonExistenceCheck
 <- preconditionA,
    preconditionB
 -> step2

// Intuition
Specifies all preconditions to check for existence and non-existence checks. Either precondition1 or precondition2 must exist to reach step1. Similarly, either preconditionA or preconditionB must NOT exist to reach step2.

// Example:
TODO

// Interpretation:
TODO
```

### TypeOf ([])
```
// Simplified syntax
| step1
  -> assets[Foo].bar

// Intuition
Designates a sub-class Foo of some associated asset to perform operations on.

// Example:
| attackLinux
  -> computers[LinuxMachine].attack

// Interpretation:
The step attackLinux only affects associated computers of the LinuxMachine sub-class.
```

### Union (\\/)
```
// Simplified syntax
foo \/ bar

// Intuition
Designates the union of the sets of associated assets designated by the roles foo and bar.

// Example:
| attackWindowsOrLinux
  -> computers[WindowsMachine] \/ computers[LinuxMachine]

// Interpretation
The step attackWindowsOrLinux affects all associated Windows and Linux machines.
```

### Intersection (/\\)
```
// Simplified syntax
foo /\ bar

// Intuition
Designates the intersection of the sets of associated assets designated by the roles foo and bar.

// Example
| attackWindowsAndLinux
  -> computers.operatingSystems[Windows] /\ computers.operatingSystems[Linux]

// Interpretation
The step attackWindowsAndLinux affects only the set of associated computers which has both Windows and Linux installed.
```

### Difference (-)
```
// Simplified syntax
foo - bar

// Intuition
Designates the intersection of the sets of associated assets designated by the roles foo and bar.

// Example
| attackWindowsNotLinux
  -> computers.operatingSystems[Windows] - computers.operatingSystems[Linux]

// Interpretation
The step attackWindowsNotLinux affects only the set of associated computers which has Windows, but not Linux, installed.
```

### Collect operator (X.A)
```
// Simplified syntax
foo.bar

// Intuition
Designates the all associated assets foo, then, the assets or elements bar.

// Example
Asset Computer
{
  operatingSystems.hack
}
// Interpretation
Refers to the attack step 'hack' for all operating systems associated with Computer assets.
```

### Transitive operator (X*.A)
```
// Simplified syntax
foo*.bar

// Intuition
Recursively follows foo until the end is reached, then, performs bar.

// Example
Asset Computer
{
  folders.subFolder*.access
}
// Interpretation
Refers to all folders associated with computer assets. Recursively follows all subFolder associations as far as possible, then, performs access.
```

### Let substitution (X().A)
```
// Simplified syntax
let foo = bar,
foo().step

// Intuition
Substitutes foo() with the contents of the corresponding let expression, i.e., foo().step = bar.step.

// Example
Asset Computer
{
  let allFolders = folders.subFolder*,
  allFolders().access
}
// Interpretation
Refers to all folders associated with computer assets. Recursively follows all subFolder associations as far as possible, then, performs access.
```

### Hidden step
```
// Simplified syntax
| foo @hidden
  -> bar
| bar

// Intuition
The step foo is functional but will not be visualized by securiCAD. The step bar appears normally.

// Example
Asset Password
{
  | guess
    -> accounts.passwordObtained
}

Asset MFAToken
{
  | steal
    -> accounts.tokenObtained
}

Asset Account
{
  | passwordObtained @hidden
    -> compromise
  | tokenObtained @hidden
    -> compromise
  & compromise
}
// Interpretation
Compromising the Account requires both a password and multi-factor authentication token. Guessing the password, stealing the token, and compromising the account will be reported. However, the "helpers" passwordObtained and tokenObtained are informative end users and are, therefore, not visualized.
```

### Debug step
```
// Simplified syntax
asset HelloWorld
{
| step 
  -> let someAssets = foo /\ bar,
     someAssets[Foo].test
}

asset Foo
{
| test @debug
}
// Intuition
The step test can be called to control which Foo assets were included after the intersection operation during development. Both someAssets[Foo].test and the test step itself will be removed upon packaging a production version.

// Example
Asset Computer
{
  | findWindowsAndLinux
    -> let allTargets = computers.operatingSystems[Windows] /\ computers.operatingSystems[Linux],
       allTargets[Windows].foundWindows
}

Asset Windows extends OperatingSystem
{
  | foundWindows @debug
}

// Interpretation
The let expression attempts to find all associated computers with both Windows and Linux installed. Before writing more logic, the foundWindows step is used to test if all Windows hosts were found as expected while testing the language. Since foundWindows has no value outside of testing, it can be omitted when shipping for securiCAD.
```

### Trace origin
```
// Simplified syntax
asset Gamma
{
  & activate @trace
  | calledByAlpha
    -> activate
  | calledByBeta
    -> activate
}

asset Alpha
{
  | goToBeta
    -> let allBeta = allGamma.allBeta,
       let someBeta = myBeta /\ allBeta,
       someBeta.calledByAlpha,
       someBeta.foo
}

asset Beta
  | foo
    -> myGamma.calledByBeta

// Intuition
Alpha can only reach Beta through Gamma. However, the path resulting from someBeta would only display Alpha->Beta. The involved Gamma assets would be omitted, even with the activate step in place. However, by tracing activate, securiCAD will show all relationships between Alpha, Beta, and Gamma.

// Example

asset Policy {
  & satisfy @trace
  | calledByUser
    -> satisfy
  | calledByHost
    -> satisfy
}

asset User {
  let effectivePolicies = policies
  let policyReachableHosts = policies.hosts

  | compromise
    -> effectivePolicies.calledByUser,
       policyReachableHosts.connect
}

asset Host {
  let effectivePolicies = policies
  | connect
    -> effectivePolicies.calledByHost

}
// Interpretation
The security Policy determines which Host machines any User may communicate with. However, policyReachableHosts.connect only provides the path User->Host and omits the offending Policy. Therefore, the satisfy step is traced in the Policy asset.
```

### Risk type
```
// Simplified syntax
| stepC {C}
| stepCI {C, I}
| stepCIA {C, I, A}

// Intuition
Reaching step C harms confidentiality. Reaching step CI harms confidentiality and integrity. Step CIA harms confidentiality, integrity, and availability.

// Example

asset Data {
  | readOnly {C}
  | readAndAppend {C, I}
  | fullAccess {C, I, A}

// Interpretation
Unauthorized reads harms confidentiality. Being able to append information harms the integrity of the data. Having full access potentially harms all security properties.
```