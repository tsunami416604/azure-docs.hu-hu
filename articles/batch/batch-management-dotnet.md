<properties
    pageTitle="使用 Batch Management .NET 進行帳戶管理 | Microsoft Azure"
    description="使用 Batch Management .NET 程式庫在您的應用程式中建立、刪除和修改 Azure Batch 帳戶。"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="11/10/2015"
    ms.author="v-marsma"/>


# 使用 Batch Management .NET 管理 Azure Batch 帳戶和配額

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)


藉由降低維護您的 Azure 批次應用程式中的額外負荷 [批次管理.NET ][api_mgmt_net] 自動化批次帳戶的建立、 刪除、 金鑰管理和配額探索程式庫。

- 在任何區域內**建立和刪除 Batch 帳戶**。 比方說，身為獨立軟體廠商 (ISV)，您為每個獲派不同 Batch 帳戶的客戶針對計費目的提供服務，您可以將帳戶建立和刪除功能加入至客戶入口網站。
- 以程式設計的方式為您的任何 Batch 帳戶**擷取和重新產生帳戶金鑰**。 這尤其有利於保持符合可能會對帳戶金鑰強制執行定期變換或過期的安全性原則。 當您在各種不同的 Azure 區域中有許多 Batch 帳戶時，將此變換程序自動化將增加解決方案的效率。
- **檢查帳戶配額**並採取反復試驗的猜測，判斷哪一個 Batch 帳戶有哪些限制。 藉由開始作業、建立集區或加入計算節點之前檢查您的帳戶配額，您可以主動地調整建立計算資源的位置或時機。 您可以決定在帳戶中配置其他資源之前，哪些帳戶需要增加配額。
- **將其他 Azure 服務的功能結合** 功能完整的管理體驗利用批次管理.NET， [Azure Active Directory ][aad_about], ，而 [Azure 資源管理員 ][resman_overview] 一起放在同一個應用程式。 使用這些功能和其 API，您可以提供順暢的驗證體驗、建立和刪除資源群組，以及上面所述的功能，以獲得端對端管理解決方案。

> [AZURE.NOTE] 雖然本文著重於以程式設計方式管理的批次帳戶、 金鑰和配額，您可以執行許多這些活動的使用 [Azure 入口網站 ][azure_portal]。 請參閱 [建立和管理 Azure 入口網站中的 Azure 批次帳戶](batch-account-create-portal.md) 和 [配額和限制的 Azure 批次服務](batch-quota-limit.md) 如需詳細資訊。

## 建立和刪除 Batch 帳戶

如上所述，Batch Management API 的主要功能之一就是在 Azure 區域內建立和刪除 Batch 帳戶。 若要這樣做，您將使用 [BatchManagementClient.Accounts.CreateAsync][net_create] 和 [DeleteAsync ][net_delete], ，或與其同步對應項目。

下列程式碼片段會建立一個帳戶、從 Batch 服務取得新建立的帳戶，然後將它刪除。 這和在本文中的其他程式碼片段中 `batchManagementClient` 完整初始化的執行個體 [BatchManagementClient ][net_mgmt_client]。

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] 使用 Batch Management .NET 程式庫和其 BatchManagementClient 的應用程式需要 **服務管理員**或**共同管理員**存取權，以使用擁有要管理的 Batch 帳戶的訂用帳戶。 請參閱 [Azure Active Directory](#aad) 下一節和 [AccountManagement ][acct_mgmt_sample] 如需詳細資訊的程式碼範例。

## 擷取和重新產生帳戶金鑰

使用從您的訂閱內的任何批次帳戶取得主要和次要帳戶金鑰 [ListKeysAsync ][net_list_keys], ，並重新產生這些金鑰， [RegenerateKeyAsync ][net_regenerate_keys]。

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
    "MyResourceGroup",
    "mybatchaccount",
    new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP] 您可以為您的管理應用程式建立簡化的連線工作流程。 首先，取得您想要使用管理的批次帳戶的帳戶金鑰 [ListKeysAsync ][net_list_keys], ，當初始化批次.NET 程式庫，接著使用此金鑰 [BatchSharedKeyCredentials ][net_sharedkeycred] 初始化時使用 [BatchClient ][net_batch_client]。

## 檢查 Azure 訂用帳戶和 Batch 帳戶配額

Azure 訂用帳戶和 Batch 之類的個別 Azure 服務均具有預設配額，限制其中特定實體的數目。 Azure 訂用帳戶的預設配額可在 [Azure 訂用帳戶和服務限制、 配額和條件約束](./../azure-subscription-service-limits.md), ，並為批次服務可在 [配額和限制的 Azure 批次服務](batch-quota-limit.md)。 Batch Management .NET 程式庫提供在應用程式內檢查這些配額的能力，讓您在加入帳戶或計算資源 (如集區和計算節點) 之前進行配置決策。

### 檢查 Azure 訂用帳戶和 Batch 帳戶配額

在區域中建立 Batch 帳戶之前，您可以檢查您的 Azure 訂用帳戶，以查看您是否能夠將帳戶加入該區域中。

在下列程式碼片段，我們會先使用 [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] 以取得訂閱內的所有批次帳戶的集合。 在我們已取得此集合後，我們判斷多少帳戶位於目標的區域，然後使用 [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] 取得批次帳戶配額，並判斷該地區中可建立多少個帳戶 (如果有的話)。

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

在上述程式碼片段 `認證` 的執行個體 [TokenCloudCredentials ][azure_tokencreds]。 建立此物件的範例，請參閱 [AccountManagement ][acct_mgmt_sample] GitHub 上的程式碼範例。

### 檢查 Batch 帳戶的計算資源配額

在增加 Batch 解決方案內的計算資源之前，您可以檢查以確定您想要配置的資源不會超出目前既有的帳戶配額。 下列程式碼片段中，我們只要列印批次帳戶的配額資訊 `mybatchaccount`, ，但在自己的應用程式，您可以使用這類資訊來判斷帳戶是否可以處理您想要建立的其他資源。

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Azure 訂閱和服務的預設配額時，許多這些限制可能會引發發出中的要求 [Azure 入口網站 ][azure_portal]。 例如，請參閱 [配額和限制的 Azure 批次服務](batch-quota-limit.md) 如需增加您的批次帳戶配額。

## Batch Management .NET、AAD 和資源管理員

當使用批次管理.NET 程式庫，您通常會利用兩個功能 [Azure Active Directory ][aad_about] (AAD) 和 [Azure 資源管理員 ][resman_overview]。 下面討論的範例專案，在示範 Batch Management .NET API 時同時運用 Azure Active Directory 和資源管理員。

### <a name="aad"></a>Azure Active Directory

Azure 本身會使用 Azure Active Directory (AAD) 來驗證其客戶、服務管理員和組織的使用者。 在 Batch Management .NET 內容中，您將使用它來驗證的訂用帳戶管理員或共同管理員，然後允許管理程式庫查詢 Batch 服務並執行本文中所述的作業。

範例專案中所討論，Azure [Active Directory 驗證程式庫 ][aad_adal] (ADAL) 用來提示使用者輸入他們的 Microsoft ID 認證。 提供服務管理員或共同管理員認證時，這可讓應用程式查詢 Azure 訂用帳戶的清單，以及建立和刪除資源群組和 Batch 帳戶。

### 資源管理員

當使用批次管理.NET 程式庫建立批次帳戶，您通常要建立這些內 [資源群組 ][resman_overview]。 您可以建立資源群組，以程式設計方式使用 [ResourceManagementClient ][resman_client] 內找到 [資源管理員.NET ][resman_api] 媒體櫃，或者您可以將帳戶新增至現有的資源群組，您已建立先前使用 [Azure 入口網站 ][azure_portal]。

## <a name="sample"></a>在 GitHub 上的範例專案

簽出 [AccountManagment ][acct_mgmt_sample] 上以查看作用中的批次管理.NET 程式庫的 GitHub 範例專案。 此主控台應用程式顯示建立和使用情形 [BatchManagementClient ][net_mgmt_client] 和 [ResourceManagementClient ][resman_client], ，以及示範如何使用 Azure [Active Directory 驗證程式庫 ][aad_adal] (ADAL) 所需的兩個用戶端。
> [AZURE.IMPORTANT] 若要成功執行範例應用程式，您必須先使用 Azure 管理入口網站向 Azure Active Directory 登錄它。 簽出 **新增應用程式** 中 [整合的應用程式與 Azure Active Directory ][aad_integrate], ，然後依照 [文件，以註冊您自己的帳戶中的範例應用程式中的步驟。

範例應用程式會示範下列作業：

1. 取得安全性權杖，從 Azure Active Directory (AAD) 使用 [ADAL ][aad_adal]。 如果使用者尚未登入，將提示使用者輸入其 Azure 認證。
2. 使用從 AAD，取得安全性權杖建立 [SubscriptionClient ][resman_subclient] 查詢 Azure 訂用帳戶與帳戶相關聯的清單，讓使用者能夠選取其中一個，如果有多個找到。
3. 建立與選取的訂用帳戶相關聯的新認證物件
4. 建立 [ResourceManagementClient ][resman_client] 使用新的認證
5. 使用 [ResourceManagementClient ][resman_client] 來建立新的資源群組
6. 使用 [BatchManagementClient ][net_mgmt_client] 執行批次帳戶作業的數量:
  - 在新建立的資源群組中建立新 Batch 帳戶
  - 從 Batch 服務取得新建立的帳戶
  - 列印新帳戶的帳戶金鑰
  - 重新產生帳戶的新主要金鑰
  - 列印帳戶的配額資訊
  - 列印訂用帳戶的配額資訊
  - 列印訂用帳戶內的所有帳戶
  - 刪除新建立的帳戶
7. 刪除資源群組

在刪除之前的新建立的批次帳戶和資源群組，您可以檢查在 [Azure 入口網站 ][azure_portal]:

![顯示資源群組和 Batch 帳戶的 Azure 入口網站][1]
<br />
*顯示新的資源群組和批次帳戶的 azure 入口網站*


[aad_about]: ../active-directory/active-directory-whatis.md "What is Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md 
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Authentication Scenarios for Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrating Applications with Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement 
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx 
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx 
[azure_portal]: http://portal.azure.com 
[azure_storage]: https://azure.microsoft.com/services/storage/ 
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx 
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer 
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx 
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx 
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx 
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx 
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx 
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx 
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx 
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx 
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx 
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx 
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx 
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx 
[resman_overview]: ../resource-group-overview.md 
[1]: ./media/batch-management-dotnet/portal-01.png 

