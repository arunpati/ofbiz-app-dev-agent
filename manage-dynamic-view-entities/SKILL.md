# manage-dynamic-view-entities

Programmatically build complex queries using the `DynamicViewEntity` API. This is essential when query structures (joins, aliases) depend on runtime parameters that cannot be statically defined in `entitymodel.xml`.

## Triggers
- Dynamic search screens with optional joins based on user input.
- Reporting tools that allow selecting arbitrary fields or grouping criteria.
- Scenarios requiring joins on the fly without modifying entity definitions.

## Use When
- You need to join entities dynamically at runtime.
- You need to add aliases or relations based on complex business logic.
- You want to avoid creating a permanent `<view-entity>` in XML for a one-off or highly variable query.

## Procedure

### 1. Initialize DynamicViewEntity
Create a new instance of `DynamicViewEntity`.

```java
DynamicViewEntity dve = new DynamicViewEntity();
```

### 2. Add Member Entities
Define the base entities involved in the query. Use unique aliases for each.

```java
dve.addMemberEntity("OH", "OrderHeader");
dve.addMemberEntity("OI", "OrderItem");
```

### 3. Add Aliases
Select fields to be returned in the result set. You can alias all fields or specific ones.

```java
// Alias all fields from OrderHeader with no prefix
dve.addAliasAll("OH", "", null);

// Alias a specific field and add a function
dve.addAlias("OI", "totalQuantity", "quantity", null, null, null, "sum");
```

### 4. Create View Links (Joins)
Define how the member entities are joined.

```java
dve.addViewLink("OH", "OI", Boolean.FALSE, UtilMisc.toList(new ModelKeyMap("orderId", "orderId")));
```

### 5. Add Relations (Optional)
If you need to join with entities that have a defined relationship but aren't member entities.

```java
dve.addRelation("one-nofk", "", "StatusItem", UtilMisc.toList(new ModelKeyMap("statusId", "statusId")));
```

### 6. Execute Query
Use `EntityQuery` with the `DynamicViewEntity`.

```java
EntityListIterator eli = EntityQuery.use(delegator)
    .from(dve)
    .where(conditions)
    .orderBy("orderDate DESC")
    .queryIterator();
```

## Guardrails
- **Performance**: Dynamic views can be slower than static `<view-entity>` because OFBiz must generate the SQL structure at runtime. Use them only when necessary.
- **Resource Management**: Always close `EntityListIterator` in a `finally` block or use try-with-resources.
- **Complexity**: Keep dynamic joins manageable. If a join is very complex and frequently used, consider a static `<view-entity>`.

## Examples

### Dynamic Order Search with Item Details
```java
DynamicViewEntity dve = new DynamicViewEntity();
dve.addMemberEntity("OH", "OrderHeader");
dve.addAliasAll("OH", "", null);

if (UtilValidate.isNotEmpty(productId)) {
    dve.addMemberEntity("OI", "OrderItem");
    dve.addAlias("OI", "productId");
    dve.addViewLink("OH", "OI", Boolean.FALSE, UtilMisc.toList(new ModelKeyMap("orderId", "orderId")));
}

// Perform find
List<GenericValue> orders = EntityQuery.use(delegator)
    .from(dve)
    .where(EntityCondition.makeCondition("productId", productId))
    .queryList();
```
