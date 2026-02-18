---
name: manage-webapps
description: Manage OFBiz web application configurations, including component webapp definitions, web.xml settings, and URL mapping patterns.
---

# Manage Webapps Skill

This skill guides you through configuring and managing web applications in the OFBiz framework.

## Component Webapp Definition (`ofbiz-component.xml`)

Webapps must be registered in the `ofbiz-component.xml` file of their respective component.

### Pattern: Standard Webapp
```xml
<webapp name="mywebapp"
    title="My Web App"
    server="default-server"
    location="webapp/mywebapp"
    mount-point="/mywebapp"
    app-shortcut-screen="component://mywebapp/widget/CommonScreens.xml#ShortcutApp"
    base-permission="OFBTOOLS,MYAPP_VIEW"/>
```

- **name**: Unique identifier for the webapp.
- **title**: Display name (often used in headers/menus).
- **server**: Usually `default-server` (defined in `catalina-containers.xml`).
- **location**: Relative path from component root to the webapp directory (contains `WEB-INF`).
- **mount-point**: The context path (e.g., `/mywebapp`).
- **base-permission**: Comma-separated list of permissions required to access the webapp.

## Web Application Configuration (`web.xml`)

Located in `webapp/<webapp-name>/WEB-INF/web.xml`.

### Shared Context Parameters
```xml
<context-param>
    <param-name>entityDelegatorName</param-name>
    <param-value>default</param-value>
</context-param>
<context-param>
    <param-name>localDispatcherName</param-name>
    <param-value>mywebapp</param-value>
</context-param>
<context-param>
    <param-name>mainDecoratorLocation</param-name>
    <param-value>component://mywebapp/widget/CommonScreens.xml</param-value>
</context-param>
```

### Standard Filters
OFBiz uses a series of filters for security, caching, and context management.
- **ControlFilter**: Handles URL redirections and allowed paths.
- **ContextFilter**: Sets up the `request` and `session` with delegator and dispatcher.
- **SameSiteFilter**: Manages cookie SameSite attributes.

### Control Servlet
The entry point for most OFBiz web requests.
```xml
<servlet>
    <servlet-name>ControlServlet</servlet-name>
    <servlet-class>org.apache.ofbiz.webapp.control.ControlServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>ControlServlet</servlet-name>
    <url-pattern>/control/*</url-pattern>
</servlet-mapping>
```

## Global URL Settings (`url.properties`)

Located in `framework/webapp/config/url.properties`.

- **no.http**: If `Y`, forces HTTPS for all requests unless explicitly allowed.
- **port.http / port.https**: Define the ports the application listens on.
- **content.url.prefix**: Prefixes for static content (images, JS, CSS) - useful for CDN integration.

## Best Practices

1. **Unique Mount Points**: Ensure mount points do not overlap across components.
2. **Permission Consistency**: Align `base-permission` in `ofbiz-component.xml` with security checks in `controller.xml`.
3. **Dispatcher Naming**: Use a unique `localDispatcherName` per webapp to avoid service collision.
4. **Context Root**: Avoid hardcoding context roots in Java/Groovy; use `request.getContextPath()`.
