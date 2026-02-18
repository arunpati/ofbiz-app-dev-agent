---
name: manage-data
description: Create and manage OFBiz data files (Entity Engine XML). Use when adding Seed/Demo data, fixing foreign keys, or configuring entity resources.
---

# Skill: manage-data
## Goal
Populate and manage the OFBiz database using XML data files for seed, type, and demo data.

## Triggers
**ALWAYS** read this skill when:
- Creating new XML data files in `data/`.
- Registering data resources in `ofbiz-component.xml`.
- Fixing data loading or foreign key constraint errors during startup.

## Use when
- **Seed Data**: Mandatory data required for the application to function (e.g., system settings).
- **Type/Metadata Data**: Common enums, status items, and type definitions.
- **Demo/Test Data**: Optional data for development and testing environments.
- **Job Scheduling**: Background tasks using `JobSandbox` and `RecurrenceInfo`.
- **System Properties**: Component or system-level configuration.

## Procedure
1. **Data File Creation**:
    - Create files like `data/[Component]TypeData.xml`.
    - Use `<entity-engine-xml>` as the root tag.
2. **Entity Record Definition**:
    - Define records using the entity name as the tag: `<EntityName field1="val1" field2="val2"/>`.
3. **Registration (Critical)**:
    - Add `<entity-resource>` to `ofbiz-component.xml`.
    - **Attributes**:
        - `type="data"`
        - `reader-name`: `seed`, `seed-initial`, or `demo`.
        - `location`: Path to the data file.
4. **Loading Patterns**:
    - Use `reader-name="seed"` for foundational data.
    - Use `reader-name="demo"` for example data (loaded with `./gradlew loadAll`).
5. **Scheduled Jobs**:
    - Use `JobSandbox` for async/scheduled services.
    - Link to `RecurrenceInfo` for non-temp-expression repeating jobs.
    - Use `TemporalExpression` (e.g., `MIDNIGHT_DAILY`) for standard intervals.
6. **Configuration**:
    - Use `SystemProperty` for persistent application settings that can be changed via UI/DB without code redeploy.

## Guardrails
- **Foreign Keys**: Records must be ordered so that referenced records (parents) come before referencing records (children) if they are in the same file.
- **Component.xml**: Data files MUST be registered in `ofbiz-component.xml` to be loaded.
- **Formatting**: Use double quotes for attribute values and self-closing tags `<Entity .../>`.

## Examples
**Example: Registering Seed and Demo Data**
```xml
<!-- in ofbiz-component.xml -->
<entity-resource type="data" reader-name="seed" loader="main" location="data/MyComponentTypeData.xml"/>
<entity-resource type="data" reader-name="demo" loader="main" location="data/MyComponentDemoData.xml"/>
```

**Example: Enumeration and Status Data**
```xml
<entity-engine-xml>
    <EnumerationType description="My App Types" enumTypeId="MYAPP_TYPE" hasTable="N"/>
    <Enumeration description="Type Alpha" enumCode="ALPHA" enumId="MYAPP_ALPHA" enumTypeId="MYAPP_TYPE" sequenceId="01"/>
    
    <StatusType description="My App Status" hasTable="N" statusTypeId="MYAPP_STATUS"/>
    <StatusItem description="Active" sequenceId="01" statusCode="ACTIVE" statusId="MYAPP_ACTIVE" statusTypeId="MYAPP_STATUS"/>
</entity-engine-xml>
```

**Example: Scheduled Service (JobSandbox)**
```xml
<entity-engine-xml>
    <!-- Daily check at midnight using temporary expression -->
    <JobSandbox jobId="8000" jobName="BackOrder Notification" runTime="2000-01-01 00:00:00.000" 
                serviceName="checkInventoryAvailability" poolId="pool" runAsUser="system" 
                tempExprId="MIDNIGHT_DAILY" maxRecurrenceCount="-1"/>
</entity-engine-xml>
```

**Example: System Property Definition**
```xml
<entity-engine-xml>
    <SystemProperty systemResourceId="mycomponent" systemPropertyId="enable.feature.x" 
                    systemPropertyValue="Y" description="Enable special feature X in my plugin"/>
</entity-engine-xml>
```
