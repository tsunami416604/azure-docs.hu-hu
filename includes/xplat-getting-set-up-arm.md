<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />


## 搭配使用 Azure CLI 與 Azure 資源管理員 (ARM)

您必須具有 Azure 的帳戶，才能搭配使用 Azure CLI 與資源管理員命令和範本，使用資源群組來部署 Azure 資源和工作負載 (理所當然)。 如果您沒有帳戶，您可以取得 [免費 Azure 試用版](http://azure.microsoft.com/pricing/free-trial/)。
> [AZURE.NOTE] 如果您還沒有 Azure 帳戶，但您沒有 MSDN 訂閱的訂閱，您可以取得免費的 Azure 點數藉由啟用您 [MSDN 訂閱者權益這裡](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -您可以使用免費帳戶。 這其中一種方式將可用來存取 Azure。

### 步驟 1：確認 Azure CLI 版本

若要針對命令式指令和 ARM 範本使用 Azure CLI，您至少必須擁有版本 0.8.17。 若要確認您的版本，請輸入 `azure 版本`。 您應該會看到如下的內容：

    $ azure --version
    0.8.17 (node: 0.10.25)

如果您需要更新您的 Azure CLI 版本，請參閱 [Azure CLI](https://github.com/Azure/azure-xplat-cli)。

### 步驟 2： 確認您和 Azure 搭配使用的是工作或學校身分識別

如果您使用，您可以只使用 ARM 命令模式 [Azure Active Directory 租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) 或 [服務主要名稱](https://msdn.microsoft.com/library/azure/dn132633.aspx)。 (這些也稱為*組織識別碼*)。

若要查看您是否擁有一個，來登入輸入 `azure 登入` 並使用公司或學校使用者名稱和密碼，當系統提示您。 當您確實擁有一個時，應該會看到如下的內容：

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

如果您未看到此內容，就必須使用您的 Microsoft 帳戶身分識別來建立新的租用戶 (或服務主體) (這通常是個人 MSDN 訂閱或免費試用版訂閱的情況)。 若要建立工作或學校識別碼，從您使用 Microsoft 識別碼建立的 Azure 帳戶，請參閱 [Azure AD 目錄關聯新的 Azure 訂閱](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)。 如果您認為應該已經擁有組織識別碼，則您可能需要洽詢為您建立帳戶的人員。

### 步驟 3：選擇您的 Azure 訂用帳戶

如果您在 Azure 帳戶中只擁有一個訂用帳戶，Azure CLI 預設會將其本身關聯至該訂用帳戶。如果您有多個訂閱，您必須選取您想要使用輸入的訂閱 `azure 帳戶 < 訂用帳戶識別碼或名稱 > 組 true` _subscription 識別碼或 name_ 所在的訂用帳戶識別碼或您想要在目前工作階段中使用的訂閱名稱。

您應該會看到如下的內容：

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### 步驟 4：使 Azure CLI 處於 ARM 模式

若要使用 Azure 資源管理 (ARM) 模式和 Azure CLI，輸入 `azure 組態模式 arm`。 您應該會看到如下的內容：

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] 您可以切換回使用 Azure 服務管理命令輸入 `azure 組態模式 asm`。





