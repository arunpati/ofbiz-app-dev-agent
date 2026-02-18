---
name: manage-templates
description: Define and maintain OFBiz Freemarker (FTL) templates for UI widgets.
---

# Skill: manage-templates
## Goal
Define and maintain complex OFBiz Freemarker (FTL) templates, ensuring correct use of OFBiz directives and context variables.

## Triggers
**ALWAYS** read this skill when:
- Creating or modifying `.ftl` files in `template/` directories.
- Using `<platform-specific><html><html-template ...>` in screens.
- Implementing email templates or custom PDF layouts using FTL.

## Use when
- Creating dynamic HTML components that require logic beyond XML widgets.
- Building custom dashboards or specialized storefront pages.
- Developing macro libraries for reusable UI elements.

## Procedure
1. **Variable Setup & Safety**:
    - Use `<#assign variable = contextVar! />` to safely assign context variables with default values.
    - **Null Safety**: ALWAYS use the `!` operator (e.g., `user.name!`) to prevent template crashes if a variable is missing.
2. **OFBiz Directives (Critical)**:
    - **Links**: Use `<@ofbizUrl>requestName</@ofbizUrl>` for internal links.
    - **Content**: Use `<@ofbizContentUrl>/images/logo.png</@ofbizContentUrl>`.
    - **Currency**: Use `<@ofbizCurrency amount=price isoCode=currency />`.
    - **Labels**: Use `${uiLabelMap.LabelKey}` for internationalization.
3. **Control Flow**:
    - **Lists**: Use `<#list listVar as item> ... <#else> ... </#list>` for iterations.
    - **Conditionals**: Use `<#if condition> ... <#elseif ...> ... <#else> ... </#if>`.
4. **Context Access**:
    - Access `parameters.xyz` for request parameters.
    - Access `requestAttributes.xyz` or `sessionAttributes.xyz` for backend data.
    - Use `Static["class.path"].method()` for Java utility calls (use sparingly).
5. **Encoding & Security**:
    - Use `?html` or `?js_string` when outputting variables to prevent XSS.

## Guardrails
- **Logic Placement**: Keep complex business logic in Groovy/Java; FTL should primarly focus on presentation logic.
- **Paths**: Reference templates using `component://` in screen XML, but keep FTL internal paths relative to the component's `template` root where possible.
- **Macros**: Define reusable UI blocks as `<#macro name param> ... </#macro>` to avoid duplication.

## Examples
**Example: Advanced Product Listing Snippet**
```ftl
<#-- Initialize data -->
<#assign productList = productList! />

<div class="product-grid">
  <#if productList?has_content>
    <ul>
      <#list productList as product>
        <#assign price = productPriceMap[product.productId]! />
        <li>
          <h3>${product.productName!product.productId}</h3>
          <p>
            ${uiLabelMap.ProductPrice}: 
            <#if price?has_content>
              <@ofbizCurrency amount=price.price isoCode=price.currencyUsed />
            <#else>
              ${uiLabelMap.ProductNoPriceAvailable}
            </#if>
          </p>
          <a href="<@ofbizUrl>product?product_id=${product.productId}</@ofbizUrl>" class="button">
            ${uiLabelMap.CommonViewDetails}
          </a>
        </li>
      </#list>
    </ul>
  <#else>
    <p>${uiLabelMap.ProductNoProductsFound}</p>
  </#if>
</div>
```
