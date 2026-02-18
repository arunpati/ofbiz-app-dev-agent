---
name: manage-email-services
description: Configure and implement email services in OFBiz.
---

# Manage Email Services

OFBiz provides robust support for sending emails, including template-based and screen-rendered notifications.

## Core Services

- `sendMail`: Basic service to send a single-part email (text or HTML).
- `sendMailMultiPart`: Service for sending emails with multiple body parts (e.g., attachments).
- `sendMailFromScreen`: Renders an email body from a Screen Widget URI.
- `sendMailFromTemplateSetting`: Highest-level service that uses the `EmailTemplateSetting` entity to configure and send an email.

## Configuration

Email settings are primarily managed in `framework/common/config/general.properties`.

### SMTP Settings
```properties
mail.notifications.enabled=Y
mail.smtp.relay.host=localhost
mail.smtp.port=465
mail.smtp.auth.user=user@example.com
mail.smtp.auth.password=password
mail.smtp.starttls.enable=true
```

### Testing & Debugging
- `mail.notifications.redirectTo`: Redirects all outgoing emails to a specific address for testing.
- `mail.debug.on`: Enables detailed SMTP logging in the console.

## Email Templates

OFBiz uses the `EmailTemplateSetting` entity to define email metadata (subject, from address) and the screen to render.

### Creating a Template (XML Data)
```xml
<EmailTemplateSetting emailTemplateSettingId="MY_EMAIL_TEMPLATE"
    subject="Action Required: ${orderId}"
    fromAddress="support@example.com"
    bodyScreenLocation="component://myplugin/widget/EmailScreens.xml#MyEmailBody"/>
```

### Sending with a Template
```groovy
run service: 'sendMailFromTemplateSetting', with: [
    emailTemplateSettingId: 'MY_EMAIL_TEMPLATE',
    sendTo: 'customer@customer.com',
    bodyParameters: [orderId: '10000']
]
```

## Screen Rendering for Emails

Emails are rendered using standard Screen Widgets. Ensure the screen uses a decorator suitable for emails (minimal HTML, inline styles).

```xml
<screen name="MyEmailBody">
    <section>
        <actions>
            <set field="title" value="Notification"/>
        </actions>
        <widgets>
            <platform-specific>
                <html><html-template location="component://myplugin/template/email/MyEmailBody.ftl"/></html>
            </platform-specific>
        </widgets>
    </section>
</screen>
```

- **Attachments**: Use `xslfoAttachScreenLocation` in `sendMailFromScreen` to render and attach a PDF (using FOP).
