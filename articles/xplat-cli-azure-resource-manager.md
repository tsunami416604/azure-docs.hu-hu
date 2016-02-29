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

使用 Azure 資源管理員來建立和管理一群 _資源_ (使用者管理的實體，例如虛擬機器、 資料庫伺服器、 資料庫或網站) 作為單一邏輯單元或 _資源群組_。

Azure 資源管理員的優點之一是，您可以建立 Azure 資源 _宣告式_ 方式: 您描述的結構和部署的 JSON 中的資源群組的關聯性 *範本*。 範本會識別可在執行命令時內嵌的參數，或儲存於個別 JSON azuredeploy-parameters.json 檔案中的參數。 如此一來，您只要提供不同的參數，就能使用相同的範本輕易建立新資源。 例如，建立網站的範本將具有網站名稱的參數、網站將放置的區域，以及其他共同設定。

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
3. 儲存 azuredeploy.parameters.json 檔案後，請使用下列命令以根據範本建立新資源群組。 `-e` 選項會指定您在上一個步驟中修改的 azuredeploy.parameters.json 檔案。 取代 *testRG* 與您想要使用時，群組的名稱和 *testDeploy* 與您所選擇的部署名稱。 位置應與您在範本參數檔案中指定的位置相同。

        azure group create "testRG" "West US" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json

    上傳部署之後及將部署套用至群組中的資源之前，此命令會傳回 [確定]。

4. 若要檢查部署的狀態，請使用下列命令。

        azure group deployment show "testRG" "testDeploy"

     **ProvisioningState** 顯示部署的狀態。

    如果部署成功，您會看到類似下列的輸出。

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

    >[AZURE.NOTE] 如果您認為您的設定不正確，並需要停止長期執行部署，請使用下列命令。
    >
    > `azure group deployment stop "testRG" "testDeploy"`
    >
    > 若未提供部署名稱，則會根據範本檔案的名稱自動建立一個名稱。 `azure group create` 命令的輸出會傳回該名稱。

    現在您可以使用您指定的網域名稱，透過 SSH 連接到 VM。 連接到 VM 時，您需要使用格式為 `<domainName>.<region>.cloudapp.azure.com` 的完整網域名稱，例如 `MyDomainName.westus.cloudapp.azure.com`。

5. 若要檢視群組，請使用下列命令。

        azure group show "testRG"

    此命令會傳回群組中資源的相關資訊。 如果您有多個群組，可使用 `azure group list` 命令來擷取群組名稱清單，然後使用 `azure group show` 來檢視特定群組的詳細資料。

您也可以使用範本，以直接從 [GitHub](https://github.com/Azure/azure-quickstart-templates), ，而不是下載到您的電腦的其中一個。 若要這樣做，請將 URL 傳給您的命令中的範本的 azuredeploy.json 檔案使用 **-範本 url** 選項。 若要取得此 URL，開啟 [在 GitHub 上的 azuredeploy.json _原始_ 模式中，然後複製瀏覽器的網址列中的 URL。 接著，您可以使用類似下列的命令，直接使用此 URL 建立部署。

    azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
系統會提示您輸入必要的範本參數。

> [AZURE.NOTE] 請務必開啟 JSON 範本 _原始_ 模式。 瀏覽器網址列中出現的 URL，與一般模式中出現的不同。 若要開啟的檔案中 _原始_ 模式時檢視 GitHub 上的檔案在右上角按一下 **Raw**。

## 使用資源

雖然範本可讓您宣告設定中整個群組的變更，但有時您只需要使用特定的資源。 您可以使用 `azure resource` 命令執行此工作。

> [AZURE.NOTE] 當使用 `azure resource` 命令以外 `list` 命令，您必須指定您搭配使用的資源的 API 版本 `-o` 參數。 如果您不確定要使用的 API 版本，請參閱範本檔，並尋找 **apiVersion** 資源欄位。

1. 若要列出群組中的所有資源，請使用下列命令。

        azure resource list "testRG"

2. 若要檢視群組內的個別資源，請使用如下所示的命令。

        azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

    請注意 **microsoft.compute/virtualmachines** 參數。 這表示您正在要求哪類資源的資訊。 如果您查看稍早下載的範本檔案，您會發現此相同的值可用來定義範本中說明的虛擬機器資源類型。

    此命令會傳回虛擬機器的相關資訊。

3. 檢視資源的詳細資料時，建議使用 `--json` 參數，更加實用。 此參數可製作更具可讀性的輸出，其中部分值是巢狀結構或集合。 下列範例示範的結果傳回 **顯示** 命令做為 JSON 文件。

        azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

    >[AZURE.NOTE] 您也可以使用檔案儲存的 JSON 資料 (& s) gt;將輸出導向至檔案的字元。 例如：
    >
    > `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. 若要刪除現有的資源，請使用如下所示的命令。

        azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## 記錄

若要檢視群組上執行之作業的記錄資訊，請使用 `azure group log show` 命令。 依預設，這會列出在群組上執行的最後一個作業。 若要檢視所有作業，請使用選用的 `--all` 參數。 針對前次部署，使用 `--last-deployment`。 針對特定部署，使用 `--deployment` 並指定部署名稱。 下列範例會傳回的群組上執行的所有作業記錄 *MyGroup*。

    azure group log show MyGroup --all

## 後續步驟

* 如需使用 Azure 資源管理員使用 Azure PowerShell 資訊，請參閱 [使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。
* 如需使用搭配 Azure 資源管理員從 Azure 入口網站的資訊，請參閱 [使用資源群組來管理您的 Azure 資源][psrm]。

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

