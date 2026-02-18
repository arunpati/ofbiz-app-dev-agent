---
name: manage-minilang
description: Orchestrate service workflows using OFBiz MiniLang (XML). Use when coordinating standard flows or declarative logic.
---

# Skill: manage-minilang
## Goal
Maintain and troubleshoot legacy XML-based business logic (Simple Methods) in OFBiz.

## Triggers
**ALWAYS** read this skill when:
- Modifying files in `minilang/` directories.
- Debugging services with `engine="simple"`.
- Converting legacy MiniLang logic to Groovy (recommended for new work).

## Use when
- Maintaining existing OFBiz core logic.
- Implementing very simple CRUD sequences (if required by project conventions).

## Procedure
1. **Method Definition**:
    - Defined inside `<simple-methods>`.
    - Use `<simple-method method-name="...">`.
2. **Context Manipulation**:
    - Use `<set field="..." from-field="..."/>`.
    - Use `<property-to-field .../>` to read properties.
3. **Entity Operations**:
    - Use `<make-value entity-name="..." value-field="..."/>`.
    - Use `<create-value value-field="..."/>` or `<store-value value-field="..."/>`.
    - Use `<set-nonpk-fields map="..." value-field="..."/>`.
4. **Service Calls**:
    - Use `<call-service service-name="..." in-map-name="...">`.
5. **Control Flow**:
    - Use `<if-empty>`, `<if-compare>`, `<iterate>`.

## Guardrails
- **Complexity**: Do NOT write new complex logic in MiniLang. Prefer Groovy.
- **Validation**: Ensure all tags follow the `simple-methods.xsd`.
- **Error Handling**: Use `<add-error>`, `<check-errors>`, and `<return/>`.

## Examples
**Example: Simple Status Update**
```xml
<simple-method method-name="updateExampleStatus">
    <entity-one entity-name="Example" value-field="example"/>
    <if-not-empty field="example">
        <set field="example.statusId" from-field="parameters.statusId"/>
        <store-value value-field="example"/>
    </if-not-empty>
</simple-method>
```
