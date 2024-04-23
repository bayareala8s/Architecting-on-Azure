# Architecting-on-Azure

### Install Azure CLI on macOS

You can install Azure CLI on macOS using the Homebrew package manager. Here are the steps:

1. If you haven't installed Homebrew yet, you can install it by running the following command in your terminal:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. Once Homebrew is installed, you can install Azure CLI by running:

```bash
brew update
brew install azure-cli
```

3. After the installation is complete, you can verify the installation by checking the version of Azure CLI:

```bash
az --version
```

This should display the version of Azure CLI that you installed.

#### List all the Azure Region locations

You can use the Azure CLI to list all the Azure region locations. Here is the command:

```bash
az account list-locations --query "[].{Region:name}" --out table
```

This command will list all the Azure regions in a table format. The `--query "[].{Region:name}"` argument formats the output to only show the region names. The `--out table` argument formats the output as a table. You can replace `table` with `json`, `jsonc`, `yaml`, `tsv` if you prefer a different output format.

#### Best Practices for Azure Resource Group Name

Azure Resource Group is a container that holds related resources for an Azure solution. Here are some best practices for naming Azure Resource Groups:

1. **Use a Consistent Naming Convention**: It's important to use a consistent naming convention for your resource groups and other Azure resources. This makes it easier to manage your resources.

2. **Include Relevant Information**: The name should include information about the resource group's purpose, the environment (like prod, dev, test), and the location if applicable. For example, `prod-weu-networking-rg` could be a resource group for networking resources in a production environment in West Europe.

3. **Avoid Special Characters**: Only alphanumeric characters, periods, underscores, hyphens, and parenthesis are allowed. Other special characters are not allowed.

4. **Use Lowercase Letters**: Although Azure is not case sensitive, it's a good practice to use lowercase letters to avoid confusion.

5. **Keep it Short but Descriptive**: The name can be up to 90 characters long, but it's a good practice to keep it short while still being descriptive.

6. **Avoid Using Reserved Words**: Certain words are reserved in Azure and cannot be used in the name of a resource group. You can find a list of these words in the Azure documentation.

Remember, once a resource group is created, you can't change its name. You would have to delete and recreate it, which would also delete all resources contained in it. So, choose your names carefully.

#### Best Practices for DataBricks workspace name

When naming an Azure Databricks workspace, you should follow these best practices:

1. **Use a Consistent Naming Convention**: It's important to use a consistent naming convention for your Databricks workspaces and other Azure resources. This makes it easier to manage your resources.

2. **Include Relevant Information**: The name should include information about the workspace's purpose, the environment (like prod, dev, test), and the location if applicable. For example, `prod-weu-databricks-ws` could be a workspace for a production environment in West Europe.

3. **Avoid Special Characters**: Only alphanumeric characters, periods, underscores, hyphens, and parenthesis are allowed. Other special characters are not allowed.

4. **Use Lowercase Letters**: Although Azure is not case sensitive, it's a good practice to use lowercase letters to avoid confusion.

5. **Keep it Short but Descriptive**: The name can be up to 90 characters long, but it's a good practice to keep it short while still being descriptive.

6. **Avoid Using Reserved Words**: Certain words are reserved in Azure and cannot be used in the name of a Databricks workspace. You can find a list of these words in the Azure documentation.

Remember, once a Databricks workspace is created, you can't change its name. You would have to delete and recreate it, which would also delete all resources contained in it. So, choose your names carefully.


#### Best Practices to name a Service Principal

When naming a Service Principal in Azure, it's important to follow some best practices to ensure clarity, organization, and security. Here are some guidelines:

1. **Descriptive**: The name should clearly indicate the purpose of the Service Principal. This makes it easier to manage and identify the Service Principal in the future. For example, if the Service Principal is used for a specific application, you might include the application's name in the Service Principal's name.

2. **Include Environment Information**: If you have multiple environments (like development, testing, staging, production), it can be helpful to include the environment in the Service Principal's name. This can help prevent confusion and mistakes.

3. **Consistent Naming Convention**: Use a consistent naming convention across all your Service Principals. This can make it easier to manage them, especially if you have a large number of Service Principals.

4. **Avoid Sensitive Information**: Do not include sensitive information in the Service Principal's name. The name might appear in logs or other output, so it should not reveal any sensitive information.

5. **Avoid Special Characters**: Some special characters might cause problems with certain operations or scripts. It's generally safest to stick with alphanumeric characters and hyphens.

Here's an example of a Service Principal name following these best practices: `myapp-dev-sp`. This name indicates that the Service Principal is used for an application named "myapp" in the development environment.

#### Bash Script to login to Azure without Browse


Yes, you can use a Service Principal to authenticate to Azure from a bash script without needing a browser. A Service Principal is an identity created for use with applications, hosted services, and automated tools to access Azure resources.

Here's an example of how you can do it:

```bash
#!/bin/bash

# Variables
subscriptionId="your-subscription-id"
tenantId="your-tenant-id"
appId="your-app-id"
password="your-password"

# Login to Azure
az login --service-principal --username $appId --password $password --tenant $tenantId

# Set the subscription
az account set --subscription $subscriptionId
```

In this script, replace `"your-subscription-id"`, `"your-tenant-id"`, `"your-app-id"`, and `"your-password"` with your actual Azure subscription ID, tenant ID, app ID, and password respectively.

Please note that this method requires you to store your credentials in the script, which can be a security risk. Make sure to secure your script appropriately, and consider using Azure Key Vault or another secure method to store your credentials.

#### Bash Script to create an Azure Service Principal

Sure, you can create an Azure Service Principal using a bash script. Here's an example:

```bash
#!/bin/bash

# Variables
name="your-service-principal-name"

# Login to Azure
az login

# Create a Service Principal
sp=$(az ad sp create-for-rbac --name $name --query "{appId: appId, password: password, tenant: tenant}" --output tsv)

# Parse the output
appId=$(echo $sp | cut -f1)
password=$(echo $sp | cut -f2)
tenant=$(echo $sp | cut -f3)

# Print the Service Principal's details
echo "App ID: $appId"
echo "Password: $password"
echo "Tenant: $tenant"
```

In this script, replace `"your-service-principal-name"` with the name you want to give to your Service Principal. The script logs into Azure, creates a Service Principal, and then prints the Service Principal's `appId`, `password`, and `tenant`. Make sure to save these values, especially the `password`, as you won't be able to retrieve it later.



