<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## 使用 Azure CLI

下列步驟可協助您利用最新版本和正確的訂用帳戶，輕鬆使用 Azure CLI。 如果您需要安裝 Azure CLI 並先將它連接到您的帳戶，請參閱 [Azure 命令列介面 (Azure CLI)](xplat-cli-install.md)。

### 步驟 1：更新 Azure CLI 版本

若要利用服務管理模式，針對命令式指令使用 Azure CLI，您應該盡可能使用最新版本。 若要確認您的版本，請輸入 `azure --version`。 您應該會看到如下的內容：

    $ azure --version
    0.8.17 (node: 0.10.25)

如果您想要更新您的 Azure CLI 版本，請參閱 [Azure CLI](https://github.com/Azure/azure-xplat-cli)。

### 步驟 2：設定 Azure 帳戶和訂用帳戶

一旦將 Azure CLI 與您想要使用的帳戶連線之後，您可能需要一個以上的訂用帳戶。 如果您這樣做，您應該檢閱可用的訂閱您的帳戶輸入 `azure account list`, ，然後選取您想要使用輸入的訂閱 `azure account set <subscription id or name> true` 其中 _訂用帳戶識別碼或名稱_ 訂用帳戶 id 或您想要在目前工作階段中使用的訂閱名稱。 您應該會看到如下的內容：

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] 如果您還沒有 Azure 帳戶，但您沒有 MSDN 訂閱的訂閱，您可以取得免費的 Azure 點數藉由啟用您 [MSDN 訂閱者權益這裡](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -您可以使用免費帳戶。 這其中一種方式將可用來存取 Azure。


