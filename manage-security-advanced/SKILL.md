# manage-security-advanced

Manage OFBiz security permissions, groups, and programmatic authorization. OFBiz uses a granular permission-based security model.

## Triggers
- Creating new applications or features that require access control.
- Defining new user roles or security groups.
- Implementing custom logic that checks for specific permissions.

## Use When
- You need to restrict access to a service, screen, or request.
- You are defining new `SecurityPermission` or `SecurityGroup` data.
- You need to perform complex permission checks in Java/Groovy code.

## Procedure

### 1. Define Security Permissions
Permissions are usually defined in `seed` or `demo` data.

```xml
<SecurityPermission description="View Order" securityPermissionId="ORDERMGR_VIEW"/>
<SecurityPermission description="Create/Update Order" securityPermissionId="ORDERMGR_CREATE"/>
```

### 2. Group Permissions
Associate individual permissions with security groups.

```xml
<SecurityGroupPermission securityGroupId="FULLADMIN" securityPermissionId="ORDERMGR_ADMIN"/>
<SecurityGroupPermission securityGroupId="ORDER_VIEWER" securityPermissionId="ORDERMGR_VIEW"/>
```

### 3. Programmatic Checks (Java/Groovy)
Use the `security` object available in most OFBiz contexts.

```java
// Check for a specific permission
if (security.hasPermission("ORDERMGR_VIEW", userLogin)) {
    // ... logic
}

// Check with target context (e.g., entity name and action)
if (security.hasEntityPermission("ORDERMGR", "_VIEW", userLogin)) {
    // ... logic
}
```

### 4. Link Groups to Users
Assign users to security groups.

```xml
<UserLoginSecurityGroup userLoginId="admin" securityGroupId="FULLADMIN" fromDate="2001-01-01 00:00:00.0"/>
```

## Guardrails
- **Granularity**: Avoid overly broad permissions (e.g., `SUPERADMIN`). Use specific permissions for specific actions.
- **Naming**: Follow the `MODULE_ACTION` convention (e.g., `PARTYMGR_CREATE`).
- **Data over Code**: Prefer defining permissions and groups in XML data rather than hardcoding checks where possible.

## Examples

### Restricting a Service
```xml
<service name="createOrder" engine="java" ...>
    <permission-service service-name="orderManagerPermissionCheck" main-action="CREATE"/>
    ...
</service>
```

### Restricting a Screen
```xml
<screen name="ViewOrder">
    <section>
        <condition>
            <if-has-permission permission="ORDERMGR" action="_VIEW"/>
        </condition>
        <widgets>
            ...
        </widgets>
    </section>
</screen>
```
