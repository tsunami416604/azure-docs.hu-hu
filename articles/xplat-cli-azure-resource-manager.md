<properties
    pageTitle="Azure CLI 搭配資源管理員 | Microsoft Azure"
    description="使用 Mac、Linux 和 Windows 適用的 Azure CLI 將多個資源做為資源群組部署。"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2015"
    ms.author="danlep"/>

# 搭配使用 Mac、Linux 和 Windows 適用的 Azure CLI 與 Azure 資源管理員

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



此文描述如何在 Azure 資源管理員模式中使用 Mac、Linux 和 Windows 適用的 Azure 命令列介面 (CLI)，建立及管理 Azure 資源。

>[AZURE.NOTE] 若要建立和管理 Azure 命令列上的資源，您將需要 Azure 帳戶 ([免費試用版](http://azure.microsoft.com/pricing/free-trial/))。 您也必須 [安裝 Azure CLI](xplat-cli-install.md), ，以及 [登入您的帳戶相關聯的 Azure 資源](xplat-cli-connect.md)。 如果上述事項都已完成，您就能開始進行了。

## Azure 資源

使用 Azure 資源管理員來建立和管理一群 _資源_ （使用者管理的實體，例如虛擬機器、 資料庫伺服器、 資料庫或網站） 作為單一邏輯單元或 _資源群組_。

Azure 資源管理員的優點之一是，您可以建立 Azure 資源 _宣告式_ 方式 ︰ 您描述的結構和部署的 JSON 中的資源群組的關聯性 *範本*。 範本會識別可在執行命令時內嵌的參數，或儲存於個別 JSON azuredeploy-parameters.json 檔案中的參數。 如此一來，您只要提供不同的參數，就能使用相同的範本輕易建立新資源。 例如，建立網站的範本將具有網站名稱的參數、網站將放置的區域，以及其他共同設定。

若要修改或建立群組時，使用範本 _部署_ 建立時，接著將其套用至群組。 如需 Azure 資源管理員的詳細資訊，請瀏覽 [Azure 資源管理員概觀](../resource-group-overview.md)。

建立部署之後，您可以在命令列上以命令管理個別的資源，就如同在傳統 (服務管理) 部署模型中一樣。 例如，使用 Azure 資源管理員 CLI 命令來啟動、 停止或刪除資源，例如 [Azure 資源管理員的虛擬機器](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md)。

## 驗證

若要透過 Azure CLI 使用 Azure 資源管理員，您必須使用公司或學校帳戶 (組織帳戶) 或 Microsoft 帳戶 (從 CLI 0.9.10 版開始) 讓 Microsoft Azure 進行驗證。 透過 .publishsettings 檔案安裝的憑證無法在此模式中進行驗證。

如需有關如何對 Microsoft Azure 進行驗證的詳細資訊，請參閱 [從 Azure CLI 連接至 Azure 訂用帳戶](xplat-cli-connect.md)。

>[AZURE.NOTE] 當您使用工作或學校帳戶由 Azure Active Directory 管理-您也可以使用 azure 角色式存取控制 (RBAC) 來存取與使用 Azure 資源管理。 如需詳細資訊，請參閱 [管理和稽核資源存取權](resource-group-rbac.md)。

## 設定 Azure 資源管理員模式

因為預設不會啟用 Azure 資源管理員模式，請使用下列命令來啟用 Azure CLI 資源管理員命令。

    azure config mode arm

>[AZURE.NOTE] Azure 資源管理員模式與 Azure 服務管理模式是互斥。 亦即，任一模式所建立的資源，將無法由另一種模式來管理。

## 尋找位置

大部分的 Azure 資源管理員命令需要有可用來建立或尋找資源的有效位置。 您可以使用下列命令來尋找不同 Azure 資源的所有可用位置。

    azure location list

這會列出 Azure 資源以及適用的 Azure 區域，例如「美國西部」、「美國東部」等等。

## 建立資源群組

資源群組是網路、儲存體和其他資源的邏輯群組。 Azure 資源管理員模式中幾乎所有命令都需要資源群組。 您可以建立資源群組名稱 _testRG_, ，例如，使用下列命令。

    azure group create -n "testRG" -l "West US"

在此之後，您可以開始將資源新增到此群組中，並用它來設定資源 (例如新的虛擬機器)。


## 使用資源群組範本

### 尋找和設定資源群組範本

當使用範本，您可以 [建立您自己](resource-group-authoring-templates.md), ，或是使用其中一個範本 [範本庫](https://azure.microsoft.com/documentation/templates/), ，也會用於 [GitHub](https://github.com/Azure/azure-quickstart-templates)。

建立新的範本已超出本文的範圍，因此一開始我們要使用 _101 簡單 vm-從-映像_ 範本可從 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm)。 根據預設，這會在美國西部區域中具有單一子網路的新虛擬網路中建立單一 Ubuntu 4.04.2-LTS 虛擬機器。 您只需要指定下列幾個參數即可使用此範本：

* 唯一的儲存體帳戶名稱
* VM 的系統管理員使用者名稱
* 密碼
* VM 的網域名稱

>[AZURE.TIP] 下列步驟將示範一個方法可使用 Azure cli 的 VM 範本。 如需其他範例，請參閱 [部署和管理虛擬機器使用 Azure 資源管理員範本和 Azure CLI](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md)。

1. 下載檔案 azuredeploy.json 和從 azuredeploy.parameters.json [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm) 到本機電腦上的工作資料夾。

2. 在文字編輯器開啟 azuredeploy.parameters.json 檔案，且輸入參數值適用於您的環境 (離開 **ubuntuOSVersion** 值未變更)。

        {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "newStorageAccountName": {
              "value": "MyStorageAccount"
            },
            "adminUsername": {
              "value": "MyUserName"
            },
            "adminPassword": {
              "value": "MyPassword"
            },
            "dnsNameForPublicIP": {
              "value": "MyDomainName"
            },
            "ubuntuOSVersion": {
              "value": "14.04.2-LTS"
            }
          }
        }
    ```
3. After saving the azuredeploy.parameters.json file, use the following command to create a new resource group based on the template. The `-e` option specifies the azuredeploy.parameters.json file that you modified in the previous step. Replace the *testRG* with the group name you wish to use, and *testDeploy* with a deployment name of your choice. The location should be same as the one specified in your template parameter file.

        azure group create "testRG" "West US" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json

    This command will return OK after the deployment is uploaded, but before the deployment is applied to resources in the group.

4. To check the status of the deployment, use the following command.

        azure group deployment show "testRG" "testDeploy"

    The **ProvisioningState** shows the status of the deployment.

    If your deployment is successful, you will see output similar to the following.

        azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
        info:    Executing command group deployment show
        + Getting deployments
        + Getting deployments
        data:    DeploymentName     : testDeploy
        data:    ResourceGroupName  : testRG
        data:    ProvisioningState  : Running
        data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
        data:    Mode               : Incremental
        data:    Name                   Type          Value
        data:    ---------------------  ------------  ---------------------
        data:    newStorageAccountName  String        MyStorageAccount
        data:    adminUsername          String        MyUserName
        data:    adminPassword          SecureString  undefined
        data:    dnsNameForPublicIP     String        MyDomainName
        data:    ubuntuOSVersion        String        14.04.2-LTS
        info:    group deployment show command OK

    >[AZURE.NOTE] If you realize that your configuration isn't correct, and need to stop a long-running deployment, use the following command.
    >
    > `azure group deployment stop "testRG" "testDeploy"`
    >
    > If you don't provide a deployment name, one is created automatically based on the name of the template file. It is returned as part of the output of the `azure group create` command.

    Now you can SSH to the VM, using the domain name you specified. When connnecting to the VM, you need to use a fully qualified domain name of the form `<domainName>.<region>.cloudapp.azure.com`, such as `MyDomainName.westus.cloudapp.azure.com`.

5. To view the group, use the following command.

        azure group show "testRG"

    This command returns information about the resources in the group. If you have multiple groups, use the `azure group list` command to retrieve a list of group names, and then use `azure group show` to view details of a specific group.

You can also use a template directly from [GitHub](https://github.com/Azure/azure-quickstart-templates), instead of downloading one to your computer. To do this, pass the URL to the azuredeploy.json file for the template in your command by using the **--template-url** option. To get the URL, open azuredeploy.json on GitHub in _raw_ mode, and copy the URL that appears in the browser's address bar. You can then use this URL directly to create a deployment by using a command similar to the following.

    azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
You are prompted to enter the necessary template parameters.

> [AZURE.NOTE] It is important to open the JSON template in _raw_ mode. The URL that appears in the browser's address bar is different from the one that appears in regular mode. To open the file in _raw_ mode when viewing the file on GitHub, in the upper-right corner click **Raw**.

## Working with resources

While templates allow you to declare group-wide changes in configuration, sometimes you need to work with just a specific resource. You can do this using the `azure resource` commands.

> [AZURE.NOTE] When using the `azure resource` commands other than the `list` command, you must specify the API version of the resource you are working with using the `-o` parameter. If you are unsure about the API version to use, consult the template file and find the **apiVersion** field for the resource.

1. To list all resources in a group, use the following command.

        azure resource list "testRG"

2. To view an individual resource within the group, use a command like the following.

        azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

    Notice the **Microsoft.Compute/virtualMachines** parameter. This indicates the type of the resource you are requesting information on. If you look at the template file downloaded earlier, you will notice that this same value is used to define the type of the virtual machine resource described in the template.

    This command returns information related to the virtual machine.

3. When viewing details on a resource, it is often useful to use the `--json` parameter. This makes the output more readable as some values are nested structures, or collections. The following example demonstrates returning the results of the **show** command as a JSON document.

        azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

    >[AZURE.NOTE] You can save the JSON data to file by using the &gt; character to pipe the output to file. For example:
    >
    > `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. To delete an existing resource, use a command like the following.

        azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Logging

To view logged information on operations performed on a group, use the `azure group log show` command. By default, this will list the last operation performed on the group. To view all operations, use the optional `--all` parameter. For the last deployment, use `--last-deployment`. For a specific deployment, use `--deployment` and specify the deployment name. The following example returns a log of all operations performed on the group *MyGroup*.

    azure group log show MyGroup --all

## Next steps

* For information on working with Azure Resource Manager using Azure PowerShell, see [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md).
* For information on working with Azure Resource Manager from the Azure portal, see [Using resource groups to manage your Azure resources][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760


