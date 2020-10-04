_This is the developers' guide to MAL. For technical details, see the [MAL compiler wiki](https://github.com/mal-lang/malcompiler/wiki/)._

# Versioning

This page documents the latest changes based on the foreseeti MAL compiler. The syntax for the legacy compiler can be found [here](https://github.com/mal-lang/mal-documentation/wiki/MAL-Syntax-(legacy)).

# Language Constituents

The following are commonly used keywords in writing MAL specification:
* **[define](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#define)** Defines information for entire MAL projects. The syntax is '\#key: "value"', for example '\#version: "1.0.0". The keys \#id and \#version must be present in every project. For more information, see the [MAL compiler wiki](https://github.com/mal-lang/malcompiler/wiki/MAL-language-specification#define).

* **[include](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#include) "path/to/specification.mal"** Imports another MAL specification. Enables references to assets and elements defined in separate specifications. Paths are relative to the specification with the include statement.

* **[category](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#category)** Similar to a package in Java. A category consists of one or more assets. The category does not bear semantics, it is only there to enable structure for the language developer. 

* **[asset](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#asset)** Similar to a class in Java. An asset could represent physical objects (e.g., a network router) or logical objects (e.g., Credentials, or a Network Service). When the MAL compiler generates the Java code from the MAL specifications, an 
asset is translated into a java class.

* **[extends](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#extends)** Similar to inheritance in Java. A child asset inherits attack steps and defense mechanisms from its parent. Child steps and mechanisms override their parent counterparts by default if the same names are used. Let expressions and associations are also inherited by extended assets (e.g. we don't need to create the association for an extended asset Linux to another asset Application, if its parent asset Machine is already associated with Application.).

* **[abstract asset](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#abstract-asset)** Similar to asset except it cannot be instantiated, only extended. An abstract asset need not have a class but its properties are inherited by its children. So, in Java generated code, an abstract asset will not have a class, and its properties are included in each child asset.

* **[user info](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#user-info)** This keyword is used in MAL to provide information for end users about the asset and/or the associated attack step and defense mechanism.

* **[developer info](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#developer-info)** This keyword is used by developers of MAL specifications. It tells other MAL writers why the attack step is used and other related information.

* **[modeler info](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#modeler-info)** This keyword is used to provide information to modellers or parser developers. It can be used to communicate assumptions or parsing requirements, which might otherwise be ambiguous.

* **[let](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#let)** This keyword is used to associate a given expression with a specific reusable name. The syntax is 'let \<nameHere\> = \<MAL expression\>'. Let expressions can be defined within the scope of entire assets, specific attack steps, and defense mechanisms. Let expressions can be referred to across separate assets like attack steps, assuming appropriate associations exist.

* **[association](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#association)** Association is analogous to UML class diagram associations. It denotes a potential bidirectional structural relationship (physical or logical) between two assets. The syntax is 'Asset1 [role1] \<multiplicity1\> \<-- AssociationName --\> \<multiplicity2\> [role2] Asset2'. The multiplicity of a composite association can be [1], [1..*] or [\*] on the composite end. Role names determine the directional references to other assets used in code. For example, Asset1 references Asset2 as _role2.A_, where A is an attack step. Conversely, Asset2 references Asset1 as _role1.A_. 

# MAL Symbols

| Symbol | Meaning | Description |
| --- | --- | --- |
| -> | [Leads to](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#leads-to--) | The successful compromise of this attack step allows the attacker to consequently compromise further attack steps. Also specifies steps affected by defenses and existence checks. |
|  +> | [Append](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#append-) | Child assets only. When parent and child assets have overlapping elements, e.g. _\| access_, the expressions defined for the child _access_ are appended to those of the parent _access_. The child expressions will otherwise override those of the parent.
| \| | [OR](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#or-step-) | An OR attack step A can be reached if any of the attack steps which refers to A is reached.
| & | [AND](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#and-step-) | An AND attack step A can be reached only if all of the attack steps which refer to A are reached.
| \# | [Defense](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#defense-) | As opposed to attack steps defenses are boolean. A defense step represents the countermeasure of an attack step (e.g., passwordBruteForce attack step can be defended by twoFactorAuthentications defense step). While declaring an asset, it is possible to either enable or disable a defense step by setting the defense step to either TRUE or FALSE. It is also possible to assign a Bernoulli distribution to govern the activation.
| E | [Existence](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#existence-e) | It is used when the existence of connected/associated assets, given by <-, must be checked. The specified attack steps are reachable when at least one designated asset exist.
| !E | [Non-existence](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#non-existence-e) | Same as Existence, except the specified attack steps are reachable when at least one designated asset does NOT exist.
| <\- | [Require](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#require--) | Denotes which associated assets are required by the current expression. Used to specify preconditions for Existence and Non-existence. | 
| [ ]  | [TypeOf](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#typeof-) | Reaching an attack step that traverses a set of assets can now be further specified by child asset if a parent asset has been extended. For example, if Dataflow has children assets Inbound and Outbound, we specify as dataflows[Outbound] 
| \\/ | [Union](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#union-) | Union operations of same-typed sets. (Backslash + forward slash)
|  /\\ | [Intersection](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#intersection-) | Intersection operations on same-typed sets. (Forward slash + backslash)
| - | [Difference](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#difference--) | Difference operations on same-typed sets.
| X.A | [Collect operator](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#collect-operator-xa) | Attack step A of the asset X is referenced. Other asset X is referenced to reach the attack step A. 
| X*.A | [Transitive operator](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#transitive-operator-xa) | Recursively follow X until the end is reached, then, perform A. For example, _folder.subFolder.subFolder.subFolder.access_ = _folder.subFolder*.access_. Hanging transition are allowed for let expression, e.g., _let allFolders = folders.subFolder*_.
| X().A | [Let substitution](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#let-substitution-xa) | The parentheses after X denote that it refers to the let expression labeled 'X'. A is performed on the assets given by X.
| @hidden | [Hidden step](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#hidden-step) | (securiCAD only) Attack steps tagged as hidden remain functional but will not be shown in attack paths.
| @debug | [Debug step](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#debug-step) | (securiCAD only) Attack steps tagged as debug exist while testing but are removed from the language while exporting for securiCAD.
| @trace | [Trace origin](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#trace-origin) | (securiCAD only) Attack steps tagged for tracing force securiCAD into reporting their root cause. The typical use case is highlighting which intermediary assets were involved in a set expression evaluation. For example, which intermediary security policy assets were reached between _userAccount.assume->action.perform_?
| AS {C, I, A} | [Risk type](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#risk-type) | (securiCAD only) Attack steps (AS) can have specific types of risks associated with them. It can be any combination of Confidentiality (C), Integrity (I), and Availability (A). Risk types are used by securiCAD together with probability distributions to calculate the total risk.
| TTC | Time-To-Compromise | TTC is a probability distribution reflects an attacker's ability of compromising an asset. Attack steps can have a TTC to execute. the MAL simulations calculate/aggregate TTC for models/scenarios.

# Probability Distributions

_For detailed information regarding distributions, see [Supported distribution functions (MAL compiler)](https://github.com/mal-lang/malcompiler/wiki/Supported-distribution-functions)._

Attack steps may have probability distributions set to express their uncertainties. MAL has the following to choose from:
* Bernoulli(p)
* Binomial(n, p)
* Exponential(λ)
* Gamma(k, θ)
* LogNormal(μ, σ)
* Pareto(x, α)
* TruncatedNormal(μ, σ^2)
* Uniform(a, b)

# MAL Coding Standard
MAL specification follows a coding standard similar to Java. Please consider the following coding standard when creating your MAL language.
* Asset names are PascalCased.
* Attack steps, defense mechanims, and role names are camelCased.
* The attack step definition syntax is '<\type> nameHere @tag [ProbabilityDistribution(params)]'. Tags and probability distributions are optional.
* Multiple expressions can be written under both -> and <- operators. They should be delimited by a comma and line break. Furthermore, the first expression should be on the same line as the operator itself.
* The defense mechanism definition syntax is '\# nameHere @tag [Bernoulli(p)]'. The tags and Bernoulli distribution are optional.
* Comments in MAL files can be introduced by "//"
* It is recommended that asset names are not repeated in the attack step
* Associations are defined at the end of each MAL specification.
* Inline documentation using the three info elements is preferred over other formats.