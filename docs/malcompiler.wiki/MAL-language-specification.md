# Grammar

The grammar of MAL can be found at <https://github.com/mal-lang/malcompiler/blob/master/grammar>. The grammar is not written to be machine readable, but instead to be human readable yet correct.

# Details

A MAL specification is a text file consisting of **`define`**, **`include`**, **`category`**, and **`associations`** declarations. These declarations can appear in the file in any order and in any number.

## Comments

Comments can appear anywhere in the MAL specification file. There are two types of comments, line comments and block comments.

A line comment starts with `//` and ends at the end of that line. For example:
```
#id: "com.example.lang" // This is the unique identifier of the language
#version: "1.2.3"       // This is the version of the language
```

A block comment starts with `/*` and ends with `*/`, and do not nest. For example:
```
/**
 * These are the required project coordinates of the language
 */
#id: "com.example.lang"
#version: "1.2.3"
```

Comments should not be used for documentation of the language. For documentation, use **`meta`** declarations instead.

## Define

**`define`** declarations look like this:
```
#key: "value"
```
For example:
```
#id: "com.example.lang"
#version: "1.2.3"
#foo: "bar"
```

Every key must be unique across all MAL specifications in a project, and the keys `#id` and `#version` must be defined in every MAL project.

`#id` can be defined as any string, but by convention it should use [reverse domain name notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation) to uniquely identify the MAL project.

`#version` must be defined as a sequence of three numbers separated by dots. The convention is to use [semantic versioning](https://semver.org/) without additional labels.

## Include

**`include`** declarations look like this:
```
include "file"
```
For example:
```
include "other1.mal"
include "subdir/other2.mal"
include "../other3.mal"
```

Including an other MAL specification is equivalent to replacing the **`include`** declaration with the contents of the included file. If the path of the **`include`** declaration is relative, it will be evaluated as relative to the MAL specification containing the **`include`** declaration.

## Category

**`category`** declarations look like this:
```
category <id>
  <meta>*
{
  <asset>*
}
```

A category is a group for assets. The category name does not have to be unique to a **`category`** declaration.

## Asset

_TODO_

## Associations

**`associations`** declarations look like this:
```
associations {
  <association>*
}
```

See **`association`** for more details.

## Association

**`association`** declarations look like this:
```
<id> [<id>] <mult> <-- <id> --> <mult> [<id>] <id>
  <meta>*
```

For example:
```
Network [networks] * <-- NetworkAccess --> * [hosts] Host
```

These declarations define an association between two assets. In the example above, the association is called `NetworkAccess`. A `Host` can be connected to zero or more `Network`s through the field `networks` and a `Network` can be connected to zero or more `Host`s through the field `hosts`.

The identifiers in brackets, `[networks]` and `[hosts]` above, are called fields. These can be used in attack steps to specify what can be reached. For example:
```
category Example {
  asset A {
    | compromise
      -> b.compromise
  }
  asset B {
    | compromise
  }
}
associations {
  A [a] * <-- L --> * [b] B
}
```
Because an association is defined between assets `A` and `B`, the field `b` can be used in the attack step `compromise` in the asset `A` to specify that if an asset `A` is compromised, all associated assets `B` are also compromised.

The identifiers between arrows, e.g. `<-- NetworkAccess -->`, are called link names. These names describe the association symmetrically, and gives each association a proper name.

Multiplicities in association specify how the association shall be used in models. The following table describes all valid multiplicities in associations:

| Association                    | Meaning                                                                     |
| ------------------------------ | --------------------------------------------------------------------------- |
| `A [a] * <-- L --> 1 [b] B`    | Asset `A` must be connected to exactly one asset `B` through the field `b`  |
| `A [a] * <-- L --> * [b] B`    | Asset `A` can be connected to zero or more assets `B` through the field `b` |
| `A [a] * <-- L --> 0..1 [b] B` | Asset `A` can be connected to zero or one asset `B` through the field `b`   |
| `A [a] * <-- L --> 0..* [b] B` | Same as `A [a] * <-- L --> * [b] B`                                         |
| `A [a] * <-- L --> 1..1 [b] B` | Same as `A [a] * <-- L --> 1 [b] B`                                         |
| `A [a] * <-- L --> 1..* [b] B` | Asset `A` must be connected to one or more assets `B` through the field `b` |

In all associations in the table above, the asset `B` can be connected to zero or more assets `A` through the field `a`.

## ID

An **`id`** must start with an upper case or lower case letter or an underscore (`[A-Za-z_]`) and can be followed by zero or more letters, underscores, or digits (`[A-Za-z_0-9]*`). Examples: `Host`, `networkAccess`, `network_access_1`

By convention, categories and assets should start with an upper case letter. Attack steps, variables, fields, and defines should start with a lower case letter. All identifiers should use [camel case](https://en.wikipedia.org/wiki/Camel_case) and underscores are discouraged.

## Meta

**`meta`** declarations attach different types of information to **`category`**, **`asset`**, **`attackstep`**, and **`association`** declarations.

Valid meta declarations are:
```
user info: "Information for users"
developer info: "Information for language developers"
modeler info: "Information for modelers"
```
These declarations should be used to document the language.

Example:
```
category Example
  user info: "The 'Example' category contains example assets"
{
  asset Asset1
    modeler info: "Connect the attacker to this asset"
  {
    | attack
      modeler info: "This should be the entry point for the attacker"
      developer info: "This attack step is used to reach compromise on all sub assets"
      -> subAsset*.compromise
    | compromise
      user info: "The attacker has full access on this asset"
  }
}

associations {
  Asset1 [superAsset] 0..1 <-- Hierarchy --> 0..1 [subAsset] Asset1
    user info: "Assets can be connected in a hierarchy"
}
```