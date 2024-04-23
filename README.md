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
