---
name: manage-api-integration
description: Define and integrate REST and SOAP APIs in OFBiz.
---

# Manage API Integration

Integration patterns for exposing OFBiz services via REST and SOAP, and handling JSON responses.

## REST API (rest-api plugin)

OFBiz uses the `rest-api` plugin (based on Jersey) to declaratively expose services.

### Defining REST Endpoints
REST endpoints are defined in `*.rest.xml` files within the `ofbiz-component.xml` of a plugin.

```xml
<rest-api name="ExampleResource" title="Example API" description="API for Example entities">
    <resource path="/examples">
        <operation method="GET" service="getExample" name="getExample">
            <description>Get an example by ID</description>
            <parameter name="exampleId" type="path" required="true"/>
        </operation>
        <operation method="POST" service="createExample" name="createExample">
            <description>Create a new example</description>
        </operation>
    </resource>
</rest-api>
```

- **Path Parameters**: Use `type="path"` in `<parameter>`.
- **Query Parameters**: Use `type="query"` in `<parameter>`.
- **Body**: The request body is automatically mapped to service IN parameters if they match the JSON keys.

### Core Components
- `OFBizApiConfig.java`: Scans and registers REST definitions.
- `ServiceRequestHandler.java`: Dispatches REST requests to the OFBiz Service Dispatcher.

## JSON Responses

For standard controller-based AJAX/API calls, use the `json` response type.

### Controller Configuration
In `controller.xml`:
```xml
<request-map uri="getJsonData">
    <event type="service" invoke="someService"/>
    <response name="success" type="request" value="json"/>
    <response name="error" type="request" value="json"/>
</request-map>
```

### Response Handling
`CommonEvents.jsonResponseFromRequestAttributes` (or `CommonEvents.jsonResponseFromServiceResults` in some versions) processes the request attributes and writes a JSON object to the response.
- **Security**: A `&&&START&&&` prefix is added to GET responses to prevent JSON hijacking (XSSI).

## SOAP Integration

OFBiz provides built-in SOAP support via the `SOAPEventHandler`.

### Exposing a Service as SOAP
1. Set `export="true"` in the service definition.
2. Ensure the `soap` handler is enabled in `handlers-controller.xml`.
3. Access the WSDL at `https://[host]/[webapp]/control/SOAPService/[ServiceName]?wsdl`.

### Invoking a Remote SOAP Service
Use the `soap-engine` or `SOAPClientEngine`.
```xml
<service name="remoteSoapCall" engine="soap" location="http://remote-host/control/SOAPService">
    <implements service="localServiceTemplate"/>
</service>
```

## Security & Data Mapping

- **Authentication**: REST API typically uses JWT or API Keys (configured in `rest-api` common filters).
- **Permissions**: Every service invoked should have proper `permission-service` or `check-permission` logic.
- **Data Mapping**: JSON/XML fields must precisely match service IN attributes for automatic mapping. Use `ServiceUtil.getAndValidateParameters` pattern in custom Java events if manual mapping is needed.
