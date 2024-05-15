### Azure Active Directory

Azure Active Directory (Azure AD) is Microsoft's cloud-based identity and access management service. It helps your employees sign in and access resources in:

- External resources, such as Microsoft Office 365, the Azure portal, and thousands of other SaaS applications.
- Internal resources, such as apps on your corporate network and intranet, along with any cloud apps developed by your own organization.

Azure AD provides features such as authentication, single sign-on (SSO), application management, multi-factor authentication (MFA), device management, user and group management, role-based access control (RBAC), security monitoring and alerts, and more. It's used to grant users secure access to these resources and protect the organization's data.

Here's a simplified text representation of Azure Active Directory (Azure AD):

```
Azure AD
|
|--- Users
|     |
|     |--- User 1
|     |--- User 2
|     |--- ...
|
|--- Groups
|     |
|     |--- Group 1
|     |--- Group 2
|     |--- ...
|
|--- Applications
|     |
|     |--- Application 1
|     |--- Application 2
|     |--- ...
|
|--- Enterprise Applications
|     |
|     |--- Enterprise Application 1
|     |--- Enterprise Application 2
|     |--- ...
|
|--- App Registrations
|     |
|     |--- App Registration 1
|     |--- App Registration 2
|     |--- ...
|
|--- Devices
|     |
|     |--- Device 1
|     |--- Device 2
|     |--- ...
|
|--- Roles
|     |
|     |--- Role 1
|     |--- Role 2
|     |--- ...
|
|--- Policies
      |
      |--- Policy 1
      |--- Policy 2
      |--- ...
```

In this diagram:

- **Users** are the individual users in your organization.
- **Groups** are collections of users.
- **Applications** are the apps that are registered in your Azure AD.
- **Enterprise Applications** are the apps that your organization uses and that are integrated with Azure AD.
- **App Registrations** are the Azure AD representations of applications that are registered with Azure AD for identity services.
- **Devices** are the devices that are registered in your Azure AD.
- **Roles** are the roles that can be assigned to users, groups, and service principals.
- **Policies** are the rules that govern the behavior of users and devices.

This is a simplified view of Azure AD. Azure AD includes many other features and components.

### Azure Active Directory Built-In roles

Azure Active Directory (Azure AD) provides several built-in roles for managing resources in your organization. Here are some of the most common ones:

1. **Global Administrator**: Has access to all administrative features in Azure AD. The person who signs up for the Azure AD subscription becomes a Global Administrator.

2. **User Administrator**: Can manage all aspects of users and groups, including resetting passwords for limited admins.

3. **Billing Administrator**: Makes purchases, manages subscriptions, manages support tickets, and monitors service health.

4. **Password Administrator**: Can reset passwords, manage service requests, and monitor service health. Password administrators can reset passwords only for users and other password administrators.

5. **Security Administrator**: Manages security features in Azure AD and configures security settings.

6. **Exchange Administrator**: Manages mailboxes in Exchange Online and performs tasks such as setting email properties for users.

7. **SharePoint Administrator**: Manages SharePoint Online and performs tasks such as setting SharePoint properties for users.

8. **Teams Administrator**: Manages settings for Teams in your organization.

9. **Conditional Access Administrator**: Manages and creates conditional access policies.

10. **Security Reader**: Can read security information and reports in Azure AD.

Remember, these are just a few examples. Azure AD provides many more built-in roles, and you can also create custom roles if the built-in roles do not meet your needs. Always assign the least privilege necessary to perform a task to follow best security practices.

### Terraform Script to create a User in Azure Active Directory

To create a user in Azure Active Directory using Terraform, you can use the `azuread_user` resource. Here's an example:

```hcl
provider "azuread" {
  version = "=0.11.0"
}

resource "azuread_user" "example" {
  user_principal_name = "jdoe@example.com"
  display_name        = "John Doe"
  password            = "SuperSecretPassword123!"
  force_password_change = false
}
```

This script creates a user with the username `jdoe@example.com`, display name `John Doe`, and a predefined password. The `force_password_change` is set to `false` which means the user will not be forced to change their password at next login.

Please replace the values as per your requirements. Also, ensure that you have the necessary permissions to create users in Azure AD.

Remember to initialize the Terraform provider before running the script using the `terraform init` command, and apply the configuration with `terraform apply`.

Note: Storing passwords in plaintext in your scripts is not recommended for security reasons. Consider using a secrets manager or a similar service to handle your passwords securely.

### Terraform script to assign Azure AD roles to a user

Terraform currently does not support assigning Azure AD roles to a user directly. The Azure AD provider for Terraform does not have this functionality at the moment.

However, you can use a workaround by using the `local-exec` provisioner in Terraform to run Azure CLI commands to assign the role. Here's an example:

```hcl
provider "azurerm" {
  features {}
}

data "azurerm_client_config" "current" {}

resource "null_resource" "assign_role" {
  provisioner "local-exec" {
    command = "az ad user update --id ${data.azurerm_client_config.current.object_id} --append assignedroles='User Administrator'"
  }
}
```

This script uses the `az ad user update` command to assign the "User Administrator" role to the user. Replace `${data.azurerm_client_config.current.object_id}` with the object ID of the user to whom you want to assign the role.

Please note that this is a workaround and may not be ideal for all situations. Always refer to the official Terraform and Azure AD documentation for the most accurate information.

Also, ensure that you have the Azure CLI installed and configured correctly on your machine, and that you have the necessary permissions to assign roles in Azure AD.


### Create an Azure AD application using Terraform

Creating an Azure Active Directory (Azure AD) application using Terraform involves using the `azuread_application` resource. Here's an example:

```hcl
provider "azuread" {
  version = "=0.11.0"
}

resource "azuread_application" "example" {
  name                       = "example-app"
  homepage                   = "https://homepage"
  identifier_uris            = ["https://identifier"]
  reply_urls                 = ["https://replyurl"]
  available_to_other_tenants = false
  oauth2_allow_implicit_flow = true
}
```

This script creates an Azure AD application with the name `example-app`. The `homepage`, `identifier_uris`, and `reply_urls` are set to example URLs - you should replace these with your actual URLs. The `available_to_other_tenants` is set to `false`, meaning the application is not available to other tenants. The `oauth2_allow_implicit_flow` is set to `true`, allowing the OAuth 2.0 implicit flow.

Please replace the values as per your requirements. Also, ensure that you have the necessary permissions to create applications in Azure AD.

Remember to initialize the Terraform provider before running the script using the `terraform init` command, and apply the configuration with `terraform apply`.

### Assign permissions to an Azure AD application using Terraform

Assigning permissions to an Azure AD application using Terraform involves using the `azuread_application_app_role` resource for application permissions and the `azuread_application_oauth2_permission_scope` resource for delegated permissions. Here's an example:

```hcl
provider "azuread" {
  version = "=0.11.0"
}

resource "azuread_application" "example" {
  name = "example-app"
}

resource "azuread_application_app_role" "example" {
  application_object_id = azuread_application.example.object_id
  allowed_member_types  = ["User"]
  description           = "Admins can manage roles and perform all task actions"
  display_name          = "Admin"
  is_enabled            = true
  value                 = "Admin"
}

resource "azuread_application_oauth2_permission_scope" "example" {
  application_object_id = azuread_application.example.object_id
  admin_consent_description  = "Allow the application to access example-app on behalf of the signed-in user."
  admin_consent_display_name = "Access example-app"
  enabled                    = true
  value                      = "user_impersonation"
}
```

In this script, an Azure AD application is created first. Then, an application role named "Admin" is created and assigned to the application. Finally, a delegated permission named "user_impersonation" is created and assigned to the application.

Please replace the values as per your requirements. Also, ensure that you have the necessary permissions to create applications and assign permissions in Azure AD.

Remember to initialize the Terraform provider before running the script using the `terraform init` command, and apply the configuration with `terraform apply`.

### Configure Single Sign-On (SSO) for an Azure AD application using Terraform

Configuring Single Sign-On (SSO) for an Azure AD application using Terraform involves using the `azuread_application` and `azuread_service_principal` resources. Here's an example:

```hcl
provider "azuread" {
  version = "=0.11.0"
}

resource "azuread_application" "example" {
  name = "example-app"
  reply_urls = ["https://example-app.com/sso/redirect"]
}

resource "azuread_service_principal" "example" {
  application_id = azuread_application.example.application_id
}
```

In this script, an Azure AD application is created first with a reply URL, which is where Azure AD will send the SSO tokens. Then, a service principal is created for the application, which is necessary for enabling SSO.

Please replace the values as per your requirements. Also, ensure that you have the necessary permissions to create applications and service principals in Azure AD.

Remember to initialize the Terraform provider before running the script using the `terraform init` command, and apply the configuration with `terraform apply`.

Please note that this script only sets up the basic configuration for SSO. Depending on your application and the type of SSO you want to implement (SAML, OIDC, etc.), you may need to perform additional configuration steps, which may not be fully supported by Terraform. Always refer to the official Azure AD and Terraform documentation for the most accurate information.


### Configure Azure AD authentication for an Azure App Service using Terraform

To configure Azure AD authentication for an Azure App Service using Terraform, you can use the `azurerm_app_service_auth_settings` resource. Here's an example:

```hcl
provider "azurerm" {
  features {}
}

data "azurerm_client_config" "current" {}

resource "azurerm_app_service" "example" {
  # ... existing configuration ...
}

resource "azurerm_app_service_auth_settings" "example" {
  app_service_id = azurerm_app_service.example.id

  enabled = true

  active_directory {
    client_id = data.azurerm_client_config.current.client_id
    client_secret = "client_secret"
    allowed_audiences = ["https://example-app.com"]
  }

  default_provider = "AzureActiveDirectory"
}
```

In this script, Azure AD authentication is enabled for an existing Azure App Service. The `client_id` and `client_secret` are set to the Azure AD application's ID and secret, respectively. The `allowed_audiences` is set to the URL of the app service.

Please replace the values as per your requirements. Also, ensure that you have the necessary permissions to configure app services and Azure AD authentication in Azure.

Remember to initialize the Terraform provider before running the script using the `terraform init` command, and apply the configuration with `terraform apply`.

Please note that this script assumes that you have already created an Azure AD application and obtained its ID and secret. If you haven't done so, you will need to create the Azure AD application first.




