---
name: manage-strategies
description: General development strategies and preferences for OFBiz. Use when deciding between multiple implementation options (e.g., XML vs Groovy services).
---

# Skill: manage-strategies
## Goal
Provide clear guidance on preferred implementation patterns and architecture decisions when multiple options exist in OFBiz.

## Triggers
**ALWAYS** read this skill when:
- Designing a new service, screen, or logic component.
- Choosing between XML, Groovy, or Java for implementation.
- Reviewing code for architectural alignment.

## Core Preferences
### 1. Services: XML vs. Java/Groovy
- **XML (MiniLang/Simple Method)**: **DEPRECATED**. Do not use for new logic.
- **Java**: Preferred for complex, performance-critical, or core domain logic.
- **Groovy**: Preferred for orchestration, business rules, and script-based services.
- **Preference Order**: Groovy > Java > XML (Legacy only).

### 2. Logic Placement
- **Prefer Groovy/Java**: For service implementation.
- **Avoid XML Actions**: While `controller.xml` or screens allow some XML actions, keep these to a minimum. Move logic to Groovy scripts.

## Dos and Don'ts
| Area | DO | DON'T |
| :--- | :--- | :--- |
| **Services** | Implement in **Groovy** or **Java**. | Implement in **MiniLang (XML)**. |
| **Logic** | Keep logic in `.groovy` or `.java` files. | Embed complex logic in `<actions>` tags of XML files. |
| **UI** | Use Freemarker (FTL) for custom layout logic. | Use complex inline Java in FTL files. |
| **Data** | Use `EntityQuery` for all data retrieval. | Use `delegator.findByAnd` or legacy low-level methods. |
| **Extensions** | Use `<extend-entity>` and plugin overrides. | Modify core framework files directly. |

## Guardrails
- **Modern First**: Always choose the modern (Java/Groovy/FTL) approach over the legacy XML-heavy approach.
- **Service Engine**: Use `engine="groovy"` or `engine="java"` in your service definitions.

## Examples
**Example: Preferred Service Definition**
```xml
<!-- Proper service definition using Groovy engine -->
<service name="myCustomService" engine="groovy" 
         location="component://myplugin/src/main/groovy/org/apache/ofbiz/myplugin/MyServices.groovy" invoke="myServiceLogic">
    <description>My preferred modern service implementation</description>
    <attribute name="id" mode="IN" type="String"/>
</service>
```
