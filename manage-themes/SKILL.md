---
name: manage-themes
description: Manage OFBiz Visual Themes. Use when defining theme resources (CSS/JS), creating theme variations, or managing LESS files.
---

# Skill: manage-themes
## Goal
Configure and customize the visual appearance of OFBiz applications using the Visual Theme system.

## Triggers
**ALWAYS** read this skill when:
- Creating a new theme component.
- Modifying CSS, JS, or LESS resources for a specific theme.
- Adding theme variations (e.g., color schemes).

## Use when
- Defining `VisualTheme` and `VisualThemeResource` entities.
- Registering theme data in `ofbiz-component.xml`.
- Managing template locations and styling assets.

## Procedure
1. **Define VisualTheme**:
    - The root record for a theme.
    - Fields: `visualThemeId`, `visualThemeSetId` (e.g., `BACKOFFICE`, `ECOMMERCE`), `displayName`.
2. **Define Theme Resources**:
    - Use `VisualThemeResource` to attach assets (CSS, JS, Favicons) to the theme.
    - Fields: `visualThemeId`, `resourceTypeEnumId` (e.g., `VT_STYLESHEET`, `VT_SCRIPT`), `sequenceId`, `resourceValue`.
3. **LESS Compilation (if applicable)**:
    - OFBiz themes often use LESS. Link the compiled CSS or the variable files in the resource records.
4. **Register in Component Context**:
    - Ensure the theme data file is registered in `ofbiz-component.xml` via `<entity-resource type="data" .../>`.

## Guardrails
- **Resource Ordering**: Use `sequenceId` in `VisualThemeResource` to ensure correct loading order of CSS/JS.
- **Paths**: Use standard webapp paths or component-relative paths for assets.

## Examples
**Example: Defining a Theme and CSS Resource**
```xml
<entity-engine-xml>
    <VisualTheme visualThemeId="MY_MODERN_THEME" visualThemeSetId="BACKOFFICE" displayName="My Modern Theme"/>
    
    <!-- Link Global Stylesheet -->
    <VisualThemeResource visualThemeId="MY_MODERN_THEME" resourceTypeEnumId="VT_STYLESHEET" 
                         sequenceId="01" resourceValue="/my-assets/css/main-style.css"/>
                         
    <!-- Link Theme-specific JS -->
    <VisualThemeResource visualThemeId="MY_MODERN_THEME" resourceTypeEnumId="VT_SCRIPT" 
                         sequenceId="01" resourceValue="/my-assets/js/theme-init.js"/>
</entity-engine-xml>
```

**Example: Theme Variation (Sub-Theme)**
```xml
<entity-engine-xml>
    <VisualTheme visualThemeId="MY_MODERN_DARK" visualThemeSetId="BACKOFFICE" displayName="Modern Dark"/>
    <VisualThemeResource visualThemeId="MY_MODERN_DARK" resourceTypeEnumId="VT_SCREEN_SHOT" 
                         resourceValue="/my-assets/images/dark-preview.png"/>
</entity-engine-xml>
```
