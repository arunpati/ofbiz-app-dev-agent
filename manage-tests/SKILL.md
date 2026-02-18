---
name: manage-tests
description: Define and maintain OFBiz automated test suites using XML and JUnit.
---

# Skill: manage-tests
## Goal
Define and maintain automated test suites within the OFBiz framework to ensure code quality and regression safety.

## Triggers
**ALWAYS** read this skill when:
- Creating or modifying XML files in `testdef/` directory (e.g., `tests.xml`).
- Writing new integration tests for services or entities.

## Use when
- Adding specialized test cases for new business logic.
- Debugging failing automation suites.
- Verifying data integrity via entity tests.

## Procedure
1. **Identify Test Type**:
    - `entity-test`: For verifying entity operations and data model constraints.
    - `service-test`: For testing service execution and result maps.
    - `junit-test-suite`: For running complex Java-based JUnit tests.
2. **Define Test Suite**:
    - Use `<test-suite>` as the root element.
    - Register components in `ofbiz-component.xml` using `<test-suite loader="main" location="testdef/tests.xml"/>`.
3. **Execution**:
    - Run tests using Gradle: `./gradlew "ofbiz --test component=[component-name] --test suitename=tests"`.
4. **Asserting Results**:
    - Use built-in assertions in MiniLang or JUnit to verify object states and service outcomes.

## Guardrails
- **Isolation**: Ensure tests do not depend on external data states. Use seed or demo data provided within the component.
- **Naming**: Test case names should be descriptive of the scenario (e.g., `testCreateExample`).
- **Paths**: Use `component://` paths for all resource locations.

## Examples
**Example: Simple XML Service Test**
```xml
<test-suite xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <service-test service-name="createExample"/>
    <entity-test entity-name="Example"/>
</test-suite>
```
