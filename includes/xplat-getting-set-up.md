
## Using Azure CLI
The following steps help you use Azure CLI easily with the most recent version and the proper subscription. If you need to install Azure CLI and connect it to your account first, see the [Azure Command-Line Interface (Azure CLI)](/documentation/articles/cli-install-nodejs/).

### Step 1: Update Azure CLI version
To use Azure CLI for imperative commands with service management mode, you should have a recent version if possible. To verify your version, type `azure --version`. You should see something like:

    $ azure --version
    0.8.17 (node: 0.10.25)

If you want to update your version of Azure CLI, see [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### Step 2: Set the Azure account and subscription
Once you have connected your Azure CLI with the account you want to use, you may have more than one subscription. If you do, you should review the subscriptions available for your account by typing `azure account list`, and then select the subscription you want to use by typing `azure account set <subscription id or name> true` where *subscription id or name* is either the subscription id or the subscription name that you would like to work with in the current session. You should see something like the following:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE]
> If you don't already have an Azure account but you do have a subscription to MSDN subscription, you can use the Trial. Either will work for Azure access.
> 
> 

