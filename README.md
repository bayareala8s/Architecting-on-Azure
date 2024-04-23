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
