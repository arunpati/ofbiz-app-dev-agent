---
name: manage-component
description: Maintain OFBiz component configuration, dependencies, and boundaries. Use when registering new resources (services/entities) or managing build dependencies.
---

# Skill: manage-component
## Goal
Configure and maintain the structure, resources, and dependencies of an OFBiz component (plugin).

## Triggers
**ALWAYS** read this skill when:
- Creating a new component or modifying `ofbiz-component.xml`.
- Adding new service or entity resource files.
- Managing project dependencies in `build.gradle`.
- Troubleshooting component loading or classpath issues.

## Use when
- Registering business resources (entities, services, data).
- Defining web applications and mount points.
- Configuring inter-component dependencies.
- Managing external Java libraries.

## Procedure
1. **Resource Registration**:
    - **Entities**: `<entity-resource type="model" reader-name="main" loader="main" location="entitydef/entitymodel.xml"/>`
    - **Services**: `<service-resource type="model" loader="main" location="servicedef/services.xml"/>`
    - **Webapp**: Define `<webapp name="..." title="..." server="..." location="webapp/..." mount-point="/..."/>`.
2. **Dependency Management**:
    - **Internal**: Use `component://` URIs for cross-component resource referencing.
    - **External**: Add `dependencies { implementation 'group:name:version' }` to `build.gradle`.
3. **Component Properties**:
    - Define component-specific settings in `config/[Component]UiLabels.xml` or custom properties files.

## Guardrails
- **Structure**: Always maintain standard OFBiz directory structure (e.g., `src`, `webapp`, `entitydef`, `servicedef`, `data`).
- **URIs**: Prefer `component://[componentName]/path/to/file` for all resource locations to ensure portability.
- **Portability**: Avoid absolute file system paths.
- **Mount Points**: Ensure `mount-point` in `<webapp>` does not collide with other components.

## Examples
**Example: Complete ofbiz-component.xml snippet**
```xml
<ofbiz-component name="myplugin" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ofbiz.apache.org/dtds/ofbiz-component.xsd">
    <resource-loader name="main" loader="org.apache.ofbiz.base.container.ComponentResourceLoader"/>
    
    <entity-resource type="model" reader-name="main" loader="main" location="entitydef/entitymodel.xml"/>
    <service-resource type="model" loader="main" location="servicedef/services.xml"/>
    <entity-resource type="data" reader-name="seed" loader="main" location="data/MyPluginTypeData.xml"/>
    
    <webapp name="myplugin" title="My Plugin" server="default-server" location="webapp/myplugin" mount-point="/myplugin" app-bar-display="true"/>
</ofbiz-component>
```
