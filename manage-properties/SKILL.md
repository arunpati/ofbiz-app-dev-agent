---
name: manage-properties
description: Manage OFBiz application properties. Use for static .properties files or dynamic SystemProperty entity configurations.
---

# Skill: manage-properties
## Goal
Define and access configuration settings in OFBiz using static property files or dynamic database records.

## Triggers
**ALWAYS** read this skill when:
- Adding new configuration keys to a component.
- Overriding core OFBiz settings.
- Accessing properties from Java, Groovy, or XML.

## Use when
- Defining static defaults in `config/` directory.
- Creating runtime-configurable settings in `SystemProperty` entity.
- Using `UtilProperties` to retrieve values.

## Procedure
1. **Static Properties (`.properties` files)**:
    - Create/Edit files in component's `config/` directory.
    - Path: `plugins/mycomponent/config/mycomponent.properties`.
    - Access via Java: `UtilProperties.getPropertyValue("mycomponent", "my.key")`.
2. **Dynamic Properties (`SystemProperty` entity)**:
    - Define in data XML files.
    - Fields: `systemResourceId` (matches file basename), `systemPropertyId` (key), `systemPropertyValue` (value).
    - Advantage: Can be updated via DB/UI without restart.
3. **Overriding Core Properties**:
    - Avoid editing core framework property files.
    - Use `SystemProperty` records to override static values if the logic checks DB first (standard OFBiz practice).

## Guardrails
- **Uniqueness**: Prefix property keys with component name to avoid collisions (e.g., `shop.order.limit`).
- **Basename**: `systemResourceId` must match the `.properties` file name if providing dynamic overrides for static defaults.

## Examples
**Example: Static Property File (`myplugin.properties`)**
```properties
# Cache setting for the plugin
myplugin.cache.enabled=true
myplugin.max.items=50
```

**Example: Dynamic Overrides (Data XML)**
```xml
<entity-engine-xml>
    <!-- Override the static default at runtime -->
    <SystemProperty systemResourceId="myplugin" systemPropertyId="myplugin.max.items" 
                    systemPropertyValue="100" description="Maximum items override"/>
</entity-engine-xml>
```

**Example: Accessing in Java**
```java
import org.apache.ofbiz.base.util.UtilProperties;

// Retrieves from DB if exists, else from myplugin.properties
String maxItems = UtilProperties.getPropertyValue("myplugin", "myplugin.max.items", "50");
```
