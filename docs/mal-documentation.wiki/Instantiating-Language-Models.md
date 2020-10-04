_For instruction on how to run tests, see [Compiling](https://github.com/mal-lang/mal-documentation/wiki/Compiling)._

# Table of Contents
[Overview](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#overview)

[The Test File](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#the-test-file)

[- Setting up](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#setting-up)

[-- Complete Test File Skeleton](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#complete-test-file-skeleton)

[-- Dissecting the Test File Skeleton](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#dissecting-the-test-file-skeleton)

[--- Package Definition](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#package-definition)

[--- Import Statements](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#import-statements)

[--- Test Class Declaration](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#test-class-declaration)

[--- Test Case Definition](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#test-case-definition)

[- Writing Test Cases](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#writing-test-cases)

[-- Complete Test Case Example](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#complete-test-case-example)

[-- Dissecting the Test Case Example](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#dissecting-the-test-case-example)

[--- Instantiating Assets](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#instantiating-assets)

[--- Define Associations](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#define-associations)

[--- Specify Attacker Starting Point(s)](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#specify-attacker-starting-points)

[--- Run the Simulation and Test Assertions](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#run-the-simulation-and-test-assertions)

[- Assertion Methods](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#assertion-methods)

[- Test Case Styles](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#test-case-styles)

[-- Waterfall](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#waterfall)

[--- Waterfall Example](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#waterfall-example)

[-- Model-based](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#model-based)

[--- Model-based Example](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#model-based-example)

# Overview
After the MAL specifications are created, it is time to instantiate models and verify the domain-specific language. A model is an instantiation of some assets and the connections between them. Each model created is considered one test case. In the case of MAL, models are instantiated using Java and tests are executed with JUnit. Test case files must, therefore, be [structured accordingly](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#complete-test-file-skeleton). Additionally, test case files must be placed under the `<project>/src/test/java/<Defined>/<Project>/<ID>` directory in accordance with the [Apache Maven project structure](https://github.com/mal-lang/exampleLang#examplelang). Finally, test case filenames must always begin with `Test`, e.g., `TestExampleLang.java`.

[MAL test cases](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#complete-test-case-example) conventionally follow a 4-part structure:
1. [Instantiate assets as Java objects](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#instantiating-assets).
2. [Define associations](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#define-associations).
3. [Specify the attacker's starting point(s)](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#specify-attacker-starting-points).
4. [Run the simulation and test assertions](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#run-the-simulation-and-test-assertions).

There are two distinct styles of test case definitions, [waterfall](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#waterfall) and [model-based](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#model-based). The waterfall style follows the 4-part structure for every test case. They are easy to write and follow, making it good for beginners. The waterfall style, however, has a lot of redundancy and takes longer to write. The model-based style is more streamlined but potentially more difficult to follow when starting out.

# The Test File
## Setting Up
### Complete Test File Skeleton
```
package <SameAsLanguageID>.test
import core.Attacker;
import core.Asset;
import core.AttackStep;
import core.Defense;
import core.AttackStepMax;
import core.AttackStepMin;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.DisplayName;

public class Test<SameAsFileName> {

    @Test
    @DisplayName("An example test case")
    public void testExampleCase() {
        //Test code here...
    }

    @AfterEach
    public void deleteModel() {
        // Clean up before each test
        Asset.allAssets.clear();
        AttackStep.allAttackSteps.clear();
        Defense.allDefenses.clear();
    }
}

```
### Dissecting the Test File Skeleton
#### Package Definition
```
// Generic syntax
//package <SameAsLanguageID>.test;

// Example
package com.lang.example.test;
```
The first line in each file should be the package definition. This is the same as the project ID for the MAL project excecpt `.test` is appended.

#### Import Statements
```
import core.*;
```
Every test file typically begins by importing fundamental MAL components. These are `Attacker`, `AttackStep`, `Asset`, `Defense`, `AttackStepMin`, and `AttackStepMax`. Importing all of them is not necessary. Only import the ones actually used.

```
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.DisplayName;
```
The appropriate JUnit components must be imported to run tests. The `Test` component is necessary for defining tests. `AfterEach` is typically used to remove lingering objects before each test case. `DisplayName` allows for setting custom display names, commonly shown in IDEs. Any JUnit components may be imported but the three mentioned are the most common.

#### Test Class Declaration
```
// Generic syntax
// public class Test<SameAsFileName> {...}

public class TestExampleLang {...}
```
Defines the class containing test case definitions. The class name must be identical to the file name, excluding the file extension.

#### Test Case Definition
```    
@Test
@DisplayName("An example test case")
public void testExampleCase() {
    //Test code here...
}
```
`@Test` designates the function as a test case. `@DisplayName` sets a custom name for the case. JUnit test cases must always be declared as `public` functions without return types (`void`). The test code is regular java code following the 4-part MAL test case structure. Test cases can be defined in any order since JUnit randomizes the order each time tests are run.

```    
@AfterEach
public void deleteModel() {
    Asset.allAssets.clear();
    AttackStep.allAttackSteps.clear();
    Defense.allDefenses.clear();
}
```
This function is executed after each test case. The code above clears out all lingering assets between each test.

## Writing Test Cases
### Complete Test Case Example
```
@Test
@DisplayName("An example test case")
public void testExampleCase() {
    // Create assets
    Computer computer = new Computer("Linux", false); // Disable some defense
    // Computer computer = new Computer("Linux", true); // Enable some defense
    User alice = new User("Alice");

    // Create associations
    Computer.addUsers(alice);

    // Create attacker
    Attacker attacker = new Attacker();
    attacker.addAttackPoint(computer.attemptToHack);

    // Run simulation and test assertions
    attacker.attack();

    computer.compromised.assertCompromisedInstantaneously();
    alice.hacked.assertCompromisedInstantaneously();
}
```
### Dissecting the Test Case Example
#### Instantiating Assets
```
// Generic syntax
// MyAsset assetName = new MyAsset("NameForTestPrintOuts", defense1, [...], defensen);

// Example
Computer computer = new Computer("Linux", false);
// Computer computer = new Computer("Linux", true);
```
Each non-abstract `Asset` defined with MAL has its own Java class definition. They are instantiated like regular Java objects. The string `NameForTestPrintOuts` represents the object instance in test log printouts.

#### Define Associations
```
Computer computer = new Computer("Linux");
User alice = new User("Alice");

// Generic syntax
// asset1.add<Role>(asset2);


// Example
Computer.addUsers(alice);
// alice.addComputers(computer); // Same association, reverse direction.
```

Every asset has an automatically generated add function for each association. The function names are determined by the [roles](https://github.com/mal-lang/mal-documentation/wiki/MAL-Code-Examples#association) defined in the MAL specifications. Add functions always have the form `add<Role>`, where the first letter of the role name is _always capitalized_.

#### Specify Attacker Starting Point(s)
```
Attacker attacker = new Attacker();
attacker.addAttackPoint(computer.attemptToHack);
```
**One** attacker object must be instantiated for **every test case**. The `addAttackPoint` function is used to specify which attack steps are compromised upon starting the simulation. In this example, `attemptToHack` is an attack step for an instantiated Computer asset.

#### Run the Simulation and Test Assertions
```
Attacker attacker = new Attacker();
attacker.addAttackPoint(computer.attemptToHack);
attacker.attack();

computer.hacked.assertCompromisedInstantaneously();
alice.hacked.assertCompromisedInstantaneously();
```
Calling `attack()` for the Attacker object without parameters starts the simulation. **Nothing will happen unless this is called**. The last step is to write [assertion clauses](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#assertion-methods). These check whether the specified attack steps were reachable or unreachable. The test succeeds if all results conformed to the assertions. The test fails if at least one contradiction is produced. In this example, the attacker starts by attempting to hack a computer. The assertion checks if the corresponding `compromised` step was reached as expected. Then, it checks whether the user, Alice, was `hacked` as a result.

## Assertion Methods
| Assertion method | Description
| --- | --- |
| assertUncompromised() | Check for unsuccessful compromise of the attack step.
| assertUncompromisedFrom(parent) | Check for unsuccessful compromise of the attack step from the specified parent attack step.
| assertCompromisedInstantaneously() | Check for successful and immediate compromise of the attack step.
| assertCompromisedWithEffort() | Check for successful compromise of the attack step but only after some effort/time is spent.
| assertCompromisedInstantaneouslyFrom() | Same as assertCompromisedInstantaneously from specified parent attack step.
| assertCompromisedWithEffortFrom() | Same as assertCompromisedWithEffort from specified parent attack step.

## Test Case Styles
### Waterfall
The waterfall style is when the 4-step structure is repeated for every test case. It is also the same one demonstrated on this page. Waterfall test cases are easy to write and follow. However, they tend to be lengthy and contain a lot of redundancy. The waterfall style is, therefore, only recommended for practicing the basics. Other styles, such as the [model-based style](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models#model-based), should be used eventually.

#### Waterfall Example
```
@Test
@DisplayName("A waterfall example")
public void testExampleCase() {
    // Create assets
    Computer computer = new Computer("Linux", false);
    User alice = new User("Alice");

    // Create associations
    Computer.addUsers(alice);

    // Create attacker
    Attacker attacker = new Attacker();
    attacker.addAttackPoint(computer.attemptToHack);

    // Run simulation and test assertions
    attacker.attack();

    computer.compromised.assertCompromisedInstantaneously();
    alice.hacked.assertCompromisedInstantaneously();
}
```

### Model-based
The model-based, or object-oriented, style creates additional private classes within the test class. These are referred to as models. Models can, for example, instantiate and associate any number of pre-defined assets according to parameters sent to constructors. This style is very efficient when many test cases require similar, and possibly complex, setups. The drawback with this style is that test cases can become more difficult to follow, especially when handed to other developers.

#### Model-based Example
```
private class ComputerModel{
    // Basic assets + associations model
    public final User alice = new User("Alice");
    public Computer computer;

    public ComputerModel(boolean isDefended){
        computer = new Computer("Linux", isDefended)
        computer.addUsers(alice);
    }
}

@Test
@DisplayName("A model-based example")
public void testExampleCase() {
    // Create model (assets + associations)
    ComputerModel model = new ComputerModel(false);

    // Create attacker
    Attacker attacker = new Attacker();
    attacker.addAttackPoint(model.computer.attemptToHack);

    // Run simulation and test assertions (models can also include attackers and assertions!)
    attacker.attack();

    model.computer.compromised.assertCompromisedInstantaneously();
    model.alice.hacked.assertCompromisedInstantaneously();
}
```
