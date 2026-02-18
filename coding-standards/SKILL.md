---
name: coding-standards
description: Guidelines for writing production-quality code, focusing on commenting style, cleanliness, and professionalism.
---

# Skill: coding-standards
## Goal
Ensure all OFBiz contributions meet production-grade standards for clarity, maintainability, and architectural alignment.

## Triggers
**ALWAYS** read this skill when:
- Writing or refactoring any code (Java, Groovy, XML, FTL).
- A task requires "clean code" or "production quality".
- Preparing a pull request or implementing new features.

## General Rules
1. **Explain the WHY**: Code shows *what* happens. Comments must explain *why* (e.g., explaining a workaround for a legacy constraint).
2. **No Internal Monologue**: Delete "thinking out loud" or "maybe this works" comments.
3. **Guard against Technical Debt**: Avoid `// TODO` without a tracking reference.
4. **Remove Debug Code**: Clean up `System.out.println`, `println`, or excessive `Debug.logInfo` before completion.

## OFBiz Specific Standards
1. **XML Actions**: Keep logic flat. If it requires complex loops or nesting, move to Groovy.
2. **Service Definitions**:
    - Always include `<description>` tags.
    - Use clear, descriptive attribute names.
3. **Java/Groovy**:
    - Use `MODULE` string for logging.
    - Use `.filterByDate()` for all date-effective entities (e.g., `ProductPrice`, `StatusItem`).
    - Prefer `EntityQuery` over `delegator.findOne(...)`.
4. **Internationalization (i18n)**:
    - Never hardcode user-facing strings. Use `uiLabelMap.LabelKey`.

## Guardrails
- **Consistency**: Follow the existing indentation and naming conventions of the component you are editing.
- **Transactional Integrity**: Respect OFBiz service boundaries; don't commit transactions manually.

## Examples
**Example: Clean documentation**
```java
// Check if the product is active based on current system time.
// We use filterByDate to ensure we only get valid price records.
GenericValue price = EntityQuery.use(delegator).from("ProductPrice")
        .where("productId", productId)
        .filterByDate()
        .queryFirst();
```
