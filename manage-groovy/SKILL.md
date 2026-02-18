---
name: manage-groovy
description: Write Groovy logic for OFBiz services and scripts. Use when creating/debugging Groovy services or implementing entity logic.
---

# Skill: manage-groovy
## Goal
Write concise, effective Groovy logic for data preparation, UI scripting, and business orchestration.

## Triggers
**ALWAYS** read this skill when:
- Creating files in `src/main/groovy` or using `engine="groovy"` in services.
- Writing `<actions>` scripts for screens or forms.
- Designing data adaptors or simple service sequences.

## Use when
- Preparing data for screens (filtering, tree building).
- Implementing services that primarily manipulate maps and lists.
- Rapidly prototyping business logic.
- Using OFBiz DSL features (e.g., `from(...)`, `select(...)`).

## Procedure
1. **Service vs. Script**:
    - **Service**: Methods like `def myService() { ... }` that return a Map.
    - **Script**: Flat logic for UI data preparation (returns context variables).
2. **OFBiz Groovy DSL**:
    - Use `from("Entity").where("field", value).queryOne()` for data.
    - Use `runService("serviceName", [param: val])` for orchestration.
3. **Data Manipulation**:
    - Leverage Groovy's power: `list.collect { it.name }`, `map.subMap(['key1', 'key2'])`.
4. **Context Access**:
    - Services access `parameters`. Scripts access global bindings (e.g., `context`).
5. **Success/Error**:
    - Use `return success([key: val])` or `return error("Message")`.

## Guardrails
- **Method Scope**: Avoid complex logic in the root of the script; wrap in methods for services.
- **Service Validation**: ALWAYS check `ServiceUtil.isError(result)` when calling `runService`.
- **Null Handling**: Use the safe navigation operator `?.` (e.g., `userLogin?.userLoginId`).
- **Complexity**: If logic exceeds 200 lines or requires deep inheritance, consider Java.

## Examples
**Example: Orchestration Service**
```groovy
def processOrderAndNotify() {
    // 1. Run a service
    Map result = runService("processOrder", [orderId: parameters.orderId])
    if (ServiceUtil.isError(result)) return result
    
    // 2. Query data
    GenericValue order = from("OrderHeader").where("orderId", parameters.orderId).queryOne()
    
    // 3. Notify
    runService("sendNotification", [to: order.createdByUserLogin, message: "Order processed"])
    
    return success("Order ${parameters.orderId} processed successfully")
}
```

**Example: Screen Data Preparation**
```groovy
// In a .groovy file referenced by <actions>
exampleList = from("Example").where("statusId", "EXST_APPROVED").queryList()
context.exampleList = exampleList
```
