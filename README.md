# ivy-publish and test-fixtures issue reproducer
This repo serves to reproduce a bug in the interoperation of the ivy-publish and java-test-fixtures
plugins.

## Setup
In /ivy-program/ivysettings.xml, set the `ivy.repository` (at line 3) property to the absolute path to the project on your computer.
Then, you may proceed with the reproduction steps.

*Note: This bug is most easily reproducible in IntelliJ with the IvyDEA plugin*

## How to reproduce
1) First, have a gradle project with the following plugins:
```groovy
plugins {
    id 'java-library'
    id 'ivy-publish'
    id 'java-test-fixtures'
}
```
2) In that project, create a **testFixtures** resource set in /src 
and a test in the **test** resource set that uses a fixture.
3) Publish the gradle project with `gradle publish`.
4) Try and resolve the ivy-program's dependencies. You will get the following error:

```
:: resolving dependencies :: com.sb#ivy-program;working@SPECTRE
	confs: [default]
	found com.sb#ivy-publish-test-fixtures;1.0.0 in internal

:: problems summary ::
:::: ERRORS
	a module is not authorized to depend on itself: com.sb#ivy-publish-test-fixtures;1.0.0


:: USE VERBOSE OR DEBUG MESSAGE LEVEL FOR MORE DETAILS

```

Indeed, if you look at the ivy-1.0.0.xml of ivy-publish-test-fixtures, you will see the following lines:
```xml
  <dependencies>
    <dependency org="com.sb" name="ivy-publish-test-fixtures" rev="1.0.0" conf="testFixturesApiElements-&gt;default"/>
    <dependency org="com.sb" name="ivy-publish-test-fixtures" rev="1.0.0" conf="testFixturesRuntimeElements-&gt;default"/>
  </dependencies>
  ```