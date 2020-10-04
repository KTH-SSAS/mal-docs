# Installation

Instructions regarding the MAL compiler and the MAL project structure can be found in the [exampleLang README](https://github.com/mal-lang/exampleLang/blob/master/README.md).

# Compilation primer
_For detailed instructions, see [building exampleLang and running unit tests (exampleLang)](https://github.com/mal-lang/exampleLang/blob/master/README.md#building-examplelang-and-running-the-unit-tests)._

_For instructions regarding how to write tests, see [Instantiating Language Models](https://github.com/mal-lang/mal-documentation/wiki/Instantiating-Language-Models)._

## Compile and run unit tests
```
mvn test
```
Generates `.java` files from the MAL specifications and outputs them into `<project>/target/generated-test-sources`. Also compiles the language source files together with any unit test code found. Finally runs the unit tests.

## Compile and run a specific test file
```
mvn test -Dtest=TestMyLanguage
```
Same as `mvn test` except only the test cases defined in `TestMyLanguage.java` are run.

## Compile and run a specific test case
```
mvn test -Dtest=TestMyLanguage#myTestCase
```
Same as `mvn test` except only the test case `myTestCase` defined in `TestMyLanguage.java` is run.

## Build for securiCAD
```
mvn clean package -PsecuriCAD
```
The result will be a `.jar` file found in `<project>/target`. To skip running tests, add `-Dmaven.test.skip=true`.

Building a securiCAD package requires access to foreseeti's Maven repository. Contact [support@foreseeti.com](mailto:support@foreseeti.com) to request access.

## Generate Java source
```
mvn generate-test-sources
```
This solely compiles MAL specifications into `.java` source code files. The files are found under `<project>/target/generated-test-sources`.

## Generate and compile Java source
```
mvn test-compile
```
This compiles MAL specifications into `.java` source code files. Also compiles those into `.class` files together with any unit test files found. The Java source files are found under `<project>/target/generated-test-sources` and the class files under `<project>/target/test-classes`. No tests are run with this command.
