# Versioning

This page documents the syntax for the legacy compiler. The documentation for the current foreseeti compiler can be found [here](https://github.com/mal-lang/mal-documentation/wiki/MAL-Syntax).

# Language Constituents

The following are commonly used keywords in writing MAL specification:
* **category** Similar to a package in Java. A category consists of one or more assets. The category does not bear semantics, it is only there to enable structure for the language developer. 

* **asset** Similar to a class in Java. An asset could represent physical objects (e.g., a network router) or logical objects (e.g., Credentials, or a Network Service). When the MAL compiler generates the Java code from the MAL specifications, an 
asset is translated into a java class.

* **abstractAsset** Similar to asset, however, it refers to an asset which need not have a class but its properties are inherited by its children. So, in Java generated code, an abstract asset will not have a class, and its properties are included in each child asset.

* **extends** Similar to inheritance in Java. A extended asset inherits attack steps and defense steps from its parent asset. Also the asset associations are inherited by its extended assets (e.g. we don't need to create the association for an extended asset X1 to another asset X2, if its parent asset X is already associated with X2.).

* **info** This keyword is used in MAL to provide information for users (not MAL writers) about the asset and/or the associated attack step and defense mechanism.

* **rationale** This keyword is used by developers of MAL specification. It tells MAL writers why the attack step is used and other related information.

* **association** Association is similar to UML association. It is a connection between two assets, and the name of the association is written near the asset. The multiplicity of a composite association can be [1..1], [1..*] or [*..*] on the composite end.

# Basic MAL Example

MAL, like other languages, has its own syntax and alphabet of symbols. Each asset may have attack or defense steps, and can have associations between each other. The MAL symbols can be seen below. The  associations between assets follow the structure of UML class diagrams, where associations are bound to types with cardinalities.

```
category User {
    asset UserAccount {
        | impersonate
            -> compromise
        | compromise 
            -> computer.stealSecret
    }
}
category Hardware {
    asset Computer {
        & compromise
            -> communicate,
                folder.subFolder+.accessFolder
        | communicate
            -> connect
        | authenticate
            -> compromise
        | vulnerability
            -> compromise
        & access
            -> compromise
        | connect
            -> access
        | stealSecret
            -> compromise
        | firewallBypass
            -> firewall.bypass
        E firewall
            <- firewall
            -> access
    }
    asset Firewall {
        & bypass [probability]
            -> computer.access
        # protected
            -> bypass
    }
    asset Folder {
        | accessFolder
            -> findCredentials
        | findCredentials
    }
}

associations {
    Computer [studentComputer] 1-* <-- Use --> 1 [student] UserAccount
    Computer [teacherComputer] 1 <-- Use --> 1 [teacher] UserAccount
    Computer [computer] 1-* <-- Use --> 1-* [person] UserAccount
    Computer [computer] * <-- Protect --> 1 [firewall] Firewall
    Computer [computer] * <-- Contains --> * [folder] Folder
    Folder [folder] 1 <-- Contains --> * [subFolder] Folder
}
```

# MAL Symbols

| Symbol | Meaning | Description |
| --- | --- | --- |
| --> | Leads to | The successful compromise of this attack step allows the attacker to consequently compromise further attack steps. |
| \| | OR | An OR attack step A can be reached if any of the attack steps which refers to A is reached.
| & | AND | An AND attack step A can be reached only if all of the attack steps which refer to A are reached.
| \# | Defense | As opposed to attack steps defences are boolean. A defense step represent the countermeasure of an attack step (e.g., passwordBruteForce attack step can be defended by twoFactorAuthentications defense step). While declaring an asset, it is possible to either enable or disable a defense step by setting the defense step to either TRUE or FALSE.
| E | Existence | This symbol is related to $<$-- operator. It is used when the existence of a connected/associated asset must be checked. It acts like a defense but the boolean value is automatically assigned based on the existence of the asset. The example can be seen in Appendix, the firewallProtected attack step under \texttt{Computer} asset.
| 3 | Non-existence | It is the same as above but this time a check for non-existence is being made (i.e. when the associated asset does not exist, the boolean value will be true).
| [ ]  | TypeOf | Reaching an attack step that traverses a set of assets can now be further specified by child asset if a parent asset has been extended. For example, if Dataflow has children assets Inbound and Outbound, we specify as dataflows[Outbound] 
| \\/ | Union | Union operations of same-typed sets. 
|  /\\ | Intersection | Intersection operations on same-typed sets.
| X.A | Collect operator | Attack step A of the asset X is referenced. Other asset X is referenced to reach the attack step A.
| X+.A | Transitive operator | It can be used to refer many attack steps without writing all of them.
|  +> | Add operator | This happens to the child asset. One attack step (e.g. access) under the child asset leads to its specific attack steps besides its corresponding attack step of the parent asset.
| TTC | Time-To-Compromise | TTC is a probability distribution reflects an attacker's ability of compromising an asset. Attack steps can have a TTC to execute. the MAL simulations calculate/aggregate TTC for models/scenarios.

# Probability Distributions
Some attack steps have probability distributions to express their uncertainties. MAL has four probability distributions to choose from:
* ExponentialDistribution
* UniformDistribution
* GammaDistribution
* BernoulliDistribution

# MAL Coding Standard
MAL specification follows a coding standard similar to Java. Please consider the following coding standard when creating your MAL language.
* Asset names start with a capital letter
* Attack steps are following camelCase
* Role names are lower case
* Comments in MAL files can be introduced by "//"
* It is recommended that asset names are not repeated in the attack step
