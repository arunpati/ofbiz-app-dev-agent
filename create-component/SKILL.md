---
name: create-component
description: Scaffold a new OFBiz component (plugin) using Gradle. Use when starting a new feature set or isolating custom code.
---

# Skill: create-component
## Goal
Create a new, isolated OFBiz component with the correct directory structure and configuration.

## Triggers
**ALWAYS** read this skill when:
- Creating a new plugin or component.
- Isolating a large feature set into a separate module.

## Use when
- Starting a brand new feature (e.g., `my-custom-module`).
- Developing an integration that should not impact core components.

## Procedure
1. **Gradle Command**:
    - Run `./gradlew createPlugin -PpluginId=my-plugin-name`.
    - Optional flags: `-PwebappName=my-app`, `-PbasePermission=MY_PERM`.
2. **Base Structure**:
    - Ensure the following folders are present: `src`, `webapp`, `entitydef`, `servicedef`, `data`, `widget`.
3. **Initial Configuration**:
    - Update `ofbiz-component.xml` with proper titles and descriptions.
    - Add basic security permissions in `data/[Component]SecurityData.xml`.
4. **Registration**:
    - Run `./gradlew loadAll` (or just start OFBiz) to register the component in the system.

## Guardrails
- **Naming**: Use lowercase, kebab-case (e.g., `order-management`). Avoid spaces or special characters.
- **Location**: All custom components MUST reside in the `plugins/` directory.
- **Cleanliness**: Delete unused scaffolded files (e.g., placeholder entities) after creation.

## Examples
**Example: Creating a new inventory plugin**
```bash
./gradlew createPlugin -PpluginId=inventory-custom -PwebappName=inventoryApp
./gradlew loadAll
```
Then, verify the folder `plugins/inventory-custom` exists.
