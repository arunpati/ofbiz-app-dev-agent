---
name: manage-eca
description: Manage Service Event Condition Actions (SECA) and Entity Event Condition Actions (EECA) to implement event-driven logic in OFBiz.
---

# Manage Service and Entity ECAs (Service/Entity Event Condition Actions)

Event Condition Actions (ECAs) are the primary way to implement event-driven logic in OFBiz. They allow you to trigger services automatically when specific service events or entity operations occur.

## Triggers
- Implementing cross-cutting concerns (e.g., clearing caches, indexing).
- Triggering side effects (e.g., sending emails after order completion).
- Enforcing business rules that depend on data changes.
- Syncing data between different systems or components.

## Procedures

### 1. Service ECAs (SECAs)
SECAs trigger a service when another service reaches a specific life-cycle event.

- **Location**: Typically defined in `secas.xml` files within a component's `servicedef` directory.
- **Registration**: Register the SECA file in `ofbiz-component.xml`:
  ```xml
  <service-resource type="eca" loader="main" location="servicedef/secas.xml"/>
  ```

#### SECA Pattern
```xml
<eca service="serviceName" event="event-name">
    <!-- Optional Conditions -->
    <condition field-name="statusId" operator="equals" value="ORDER_COMPLETED"/>
    <!-- Actions -->
    <action service="triggerService" mode="sync|async" persist="true|false"/>
</eca>
```

**Common Events**:
- `auth`: Before authentication.
- `in-validate`: Before input validation.
- `invoke`: Just before the service logic runs.
- `out-validate`: Before output validation.
- `return`: After the service logic runs (most common).
- `commit`: After the transaction is committed.

---

### 2. Entity ECAs (EECAs)
EECAs trigger a service when a database operation occurs on a specific entity.

- **Location**: Typically defined in `eecas.xml` files within a component's `entitydef` directory.
- **Registration**: Register the EECA file in `ofbiz-component.xml`:
  ```xml
  <entity-resource type="eca" loader="main" location="entitydef/eecas.xml"/>
  ```

#### EECA Pattern
```xml
<eca entity="EntityName" operation="create|store|remove|find" event="validate|run|return|status-change">
    <condition field-name="fieldName" operator="not-equals" value="N"/>
    <action service="triggerService" mode="sync" value-attr="currentValue"/>
</eca>
```

**Common Operations**: `create`, `store` (update), `remove`.
**Common Events**: `run` (before), `return` (after).

---

## Guardrails
- **Avoid Recursion**: Be extremely careful not to trigger a SECA/EECA that eventually calls the original service/entity operation, leading to an infinite loop.
- **Transactional Impact**: Synchronous (`mode="sync"`) actions run in the same transaction. If the action fails, the main operation fails.
- **Mode Selection**: Use `mode="async"` for heavy tasks like email sending or complex integrations to avoid blocking the user.
- **Condition Precision**: Always use conditions to ensure the action only triggers when strictly necessary.

## Examples

### Order Completion SECA
Triggering an email and resetting totals after an order is stored.
```xml
<eca service="storeOrder" event="return">
    <condition field-name="orderTypeId" operator="equals" value="SALES_ORDER"/>
    <action service="resetOrderGrandTotal" mode="sync"/>
    <action service="sendOrderConfirmationEmail" mode="async" persist="true"/>
</eca>
```

### Product Keyword Indexing EECA
Automatically indexing keywords whenever a Product is created or updated.
```xml
<eca entity="Product" operation="create" event="return">
    <action service="indexProductKeywords" mode="sync" value-attr="productInstance"/>
</eca>
<eca entity="Product" operation="store" event="return">
    <action service="indexProductKeywords" mode="sync" value-attr="productInstance"/>
</eca>
```
