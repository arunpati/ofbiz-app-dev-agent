---
name: manage-java
description: Implement OFBiz services in Java. Use when writing core domain logic, strict typing, or performance-critical code.
---

# Skill: manage-java
## Goal
Implement robust, transaction-aware business logic in Java for OFBiz services.

## Triggers
**ALWAYS** read this skill when:
- Creating or modifying Java files in `src/main/java`.
- Implementing complex calculations or high-performance integrations.
- Debugging compilation or runtime reflection errors.

## Use when
- Core business invariants and domain logic.
- Performance is a primary concern.
- Deep integration with 3rd-party Java libraries is required.
- Strict typing and compile-time safety are preferred.

## Procedure
1. **Class Structure**:
    - Create classes in `src/main/java/[package]/[Name]Services.java`.
    - Define `public static final String MODULE = [Name]Services.class.getName();`.
2. **Service Signature**:
    - Methods must be `public static Map<String, Object> method(DispatchContext dctx, Map<String, ? extends Object> context)`.
3. **Core Tools**:
    - `Delegator delegator = dctx.getDelegator();`
    - `LocalDispatcher dispatcher = dctx.getDispatcher();`
    - `GenericValue userLogin = (GenericValue) context.get("userLogin");`
4. **Data Access (EntityQuery)**:
    - Use `EntityQuery.use(delegator).from("EntityName").where(...).queryOne()`.
    - Use `.filterByDate()` for date-effective data.
    - Use `.orderBy(...)` for lists.
5. **Execution & Errors**:
    - Wrap logic in `try-catch`.
    - Log errors using `Debug.logError(e, MODULE)`.
    - Return `ServiceUtil.returnSuccess()` or `ServiceUtil.returnError()`.

## Guardrails
- **Reflection**: Ensure the method name and parameters match exactly what is defined in `services.xml`.
- **Boilerplate**: Avoid Java for simple CRUD; use `entity-auto` engine instead.
- **Transactions**: Avoid manual JDBC/SQL; use the `delegator` to ensure transaction integrity.
- **Resource Management**: Properly close any external resources (streams, connections) in a `finally` block.

## Examples
**Example: Service with Entity Query and Field Update**
```java
public static Map<String, Object> updateExampleStatus(DispatchContext dctx, Map<String, ? extends Object> context) {
    Delegator delegator = dctx.getDelegator();
    String exampleId = (String) context.get("exampleId");
    String statusId = (String) context.get("statusId");
    
    try {
        GenericValue example = EntityQuery.use(delegator).from("Example")
                .where("exampleId", exampleId).queryOne();
        if (example == null) {
            return ServiceUtil.returnError("Example not found: " + exampleId);
        }
        
        example.set("statusId", statusId);
        example.store();
        
        return ServiceUtil.returnSuccess();
    } catch (GenericEntityException e) {
        Debug.logError(e, MODULE);
        return ServiceUtil.returnError(e.getMessage());
    }
}
```
