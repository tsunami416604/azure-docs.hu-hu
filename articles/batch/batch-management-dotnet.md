---
title: A fiók erőforrásainak kezelése a .NET-hez készült ügyféloldali kódtár használatával – Azure Batch | Microsoft Docs
description: Azure Batch fiók-erőforrások létrehozása, törlése és módosítása a Batch Management .NET-könyvtárral.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f7554993e2e3d8d2f6bce71db57a746a4392ce1a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095074"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batch-fiókok és kvóták kezelése a Batch Management ügyféloldali kódtáraval a .NET-hez

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

A Batch-fiókok létrehozásával, törlésével, a kulcskezelő szolgáltatással és a kvóta-felderítéssel automatizálható [a Azure batch][api_mgmt_net] -alkalmazások terhelésének csökkentése.

* **Hozzon létre és töröljön batch-fiókokat** bármely régión belül. Ha például egy független szoftvergyártó (ISV) olyan szolgáltatást biztosít ügyfeleinek, amelyben a számlázáshoz külön batch-fiókot rendelnek hozzá, a fiókok létrehozására és törlésére vonatkozó képességeket adhat az ügyfél-portálhoz.
* A **fiók kulcsainak programozott módon történő beolvasása és újragenerálása** bármely batch-fiókhoz. Ez segíthet a biztonsági szabályzatok betartásában, amelyek kényszerítik az ismétlődő átütemezést vagy a fiók kulcsainak lejáratát. Ha több batch-fiókkal rendelkezik különböző Azure-régiókban, akkor ez a rollover-folyamat automatizálása növeli a megoldás hatékonyságát.
* **Tekintse meg a fiók kvótáit** , és végezze el a próba-és hibaüzenetet, és határozza meg, hogy melyik batch-fiókoknak milyen korlátai vannak. A fiók kvótáinak a feladatok elindítása, készletek létrehozása vagy számítási csomópontok hozzáadása előtt történő ellenőrzésével proaktív módon módosíthatja a számítási erőforrások létrehozásának helyét vagy időpontját. Megadhatja, hogy mely fiókoknál van szükség kvóta növelésére a fiókok további erőforrásainak lefoglalása előtt.
* **Más Azure-szolgáltatások funkcióinak kombinálása** teljes funkcionalitású felügyeleti funkciókkal – a Batch Management .net, a [Azure Active Directory][aad_about]és a [Azure Resource Manager][resman_overview] együttes használata ugyanabban az alkalmazásban. Ezekkel a szolgáltatásokkal és API-kkal a zökkenőmentes hitelesítési élmény, az erőforráscsoportok létrehozására és törlésére, valamint a fent ismertetett képességek teljes körű felügyeleti megoldásra való képességét biztosíthatja.

> [!NOTE]
> Ez a cikk a Batch-fiókok, a kulcsok és a kvóták programozott felügyeletére összpontosít, és a [Azure Portal][azure_portal]használatával számos tevékenységet végrehajthat. További információ: [Azure batch fiók létrehozása a](batch-account-create-portal.md) [Azure batch szolgáltatás Azure Portal és kvótái és korlátai](batch-quota-limit.md)alapján.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Batch-fiókok létrehozása és törlése
Ahogy említettük, a Batch Management API egyik elsődleges funkciója a Batch-fiókok létrehozása és törlése egy Azure-régióban. Ehhez használja a [BatchManagementClient. Account. CreateAsync][net_create] és a [DeleteAsync][net_delete], illetve a szinkron társaikat.

A következő kódrészlet létrehoz egy fiókot, beolvassa az újonnan létrehozott fiókot a Batch szolgáltatásból, majd törli azt. Ebben a kódrészletben és a cikk `batchManagementClient` többi részében a [BatchManagementClient][net_mgmt_client]teljes mértékben inicializált példánya.

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> A Batch Management .NET-függvénytárat és annak BatchManagementClient osztályt használó alkalmazásokhoz a **szolgáltatás-rendszergazda** **vagy a** felügyelni kívánt batch-fiókot birtokló előfizetés szükséges. További információ: Azure Active Directory szakasz és a [AccountManagement][acct_mgmt_sample] -kód minta.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Fiókok kulcsainak beolvasása és újragenerálása
Szerezze be az elsődleges és a másodlagos fiókok kulcsait az előfizetésben található összes batch-fiókból a [ListKeysAsync][net_list_keys]használatával. Ezeket a kulcsokat a [RegenerateKeyAsync][net_regenerate_keys]használatával lehet újragenerálni.

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Létrehozhat egy egyszerűsített munkafolyamatot a felügyeleti alkalmazásaihoz. Először szerezze be a [ListKeysAsync][net_list_keys]-mel felügyelni kívánt batch-fiókhoz tartozó fiók kulcsát. Ezt követően ezt a kulcsot használja a Batch .NET-függvénytár [BatchSharedKeyCredentials][net_sharedkeycred] osztályának inicializálásához, amelyet a rendszer a [BatchClient][net_batch_client]inicializálásakor használ.
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Az Azure-előfizetés és a Batch-fiók kvótáinak megtekintése
Az Azure-előfizetések és az egyes Azure-szolgáltatások (például a Batch) minden alapértelmezett kvótával rendelkeznek, amelyek korlátozzák a bennük található egyes entitások számát. Az Azure-előfizetések alapértelmezett kvótái az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../azure-subscription-service-limits.md)című részben olvashatók. A Batch szolgáltatás alapértelmezett kvótái esetében lásd: [kvóták és korlátozások a Azure batch szolgáltatáshoz](batch-quota-limit.md). A Batch Management .NET-kódtár használatával megtekintheti ezeket a kvótákat az alkalmazásaiban. Ez lehetővé teszi a kiosztási döntések meghozatalát a fiókok vagy számítási erőforrások, például a készletek és a számítási csomópontok hozzáadása előtt.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>A Batch-fiókokra vonatkozó kvóták Azure-előfizetésének keresése
Mielőtt létrehoz egy batch-fiókot egy régióban, megtekintheti az Azure-előfizetését, és megtekintheti, hogy hozzá tud-e adni egy fiókot az adott régióban.

Az alábbi kódrészletben először a [BatchManagementClient. Account. ListAsync][net_mgmt_listaccounts] használjuk az előfizetésen belüli összes batch-fiók gyűjteményének beolvasásához. A gyűjtemény beszerzését követően megállapítjuk, hogy hány fiók van a célként megadott régióban. Ezt követően a [BatchManagementClient. Subscriptions][net_mgmt_subscriptions] használatával szerezheti be a Batch-fiók kvótáját, és meghatározhatja, hogy hány fiók (ha van ilyen) hozható létre ebben a régióban.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

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

A fenti `creds` kódrészletben a [TokenCloudCredentials][azure_tokencreds]egy példánya. Ha meg szeretné tekinteni az objektum létrehozásának példáját, tekintse meg a [AccountManagement][acct_mgmt_sample] -kód mintát a githubon.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Batch-fiók beadása számítási erőforrások kvótái számára
A Batch-megoldás számítási erőforrásainak növelése előtt ellenőrizheti, hogy a lefoglalni kívánt erőforrások nem lépik túl a fiók kvótáit. Az alábbi kódrészletben kinyomtatjuk a nevű `mybatchaccount`batch-fiók kvótájának adatait. A saját alkalmazásban az ilyen információk segítségével meghatározhatja, hogy a fiók képes-e kezelni a létrehozandó további erőforrásokat.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Habár az Azure-előfizetések és-szolgáltatások esetében alapértelmezett kvóták vannak, ezek a korlátozások nagy része egy kérelemnek a [Azure Portal][azure_portal]való kiállításával állítható elő. A Batch-fiók kvótáinak növelésével kapcsolatos utasításokért tekintse meg például [a Azure batch szolgáltatás kvótáit és korlátait](batch-quota-limit.md) .
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Az Azure AD használata a Batch Management .NET használatával

A Batch Management .NET-függvénytár egy Azure erőforrás-szolgáltatói ügyfél, és a [Azure Resource Manager][resman_overview] együtt használható a fiók erőforrásainak programozott kezeléséhez. Az Azure AD-nek szüksége van az Azure erőforrás-szolgáltatói ügyfélen keresztül küldött kérések hitelesítésére, beleértve a Batch Management .NET könyvtárat és a [Azure Resource Manager][resman_overview]. További információ az Azure AD és a Batch Management .NET könyvtár használatáról: a [Batch-megoldások hitelesítésének Azure Active Directory használata](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Minta projekt a GitHubon

A Batch Management .NET működés közbeni megtekintéséhez tekintse meg a [AccountManagement][acct_mgmt_sample] -minta projektet a githubon. A AccountManagement minta alkalmazás a következő műveleteket mutatja be:

1. Biztonsági jogkivonat beszerzése az Azure AD-ből a [ADAL][aad_adal]használatával. Ha a felhasználó még nincs bejelentkezve, a rendszer kérni fogja az Azure-beli hitelesítő adataikat.
2. Az Azure AD-től kapott biztonsági jogkivonat segítségével hozzon létre egy [SubscriptionClient][resman_subclient] az Azure lekérdezéséhez a fiókhoz társított előfizetések listájának megjelenítéséhez. A felhasználó kiválaszthat egy előfizetést a listából, ha több előfizetést is tartalmaz.
3. A kiválasztott előfizetéshez társított hitelesítő adatok beolvasása.
4. Hozzon létre egy [ResourceManagementClient][resman_client] objektumot a hitelesítő adatok használatával.
5. Hozzon létre egy erőforráscsoportot egy [ResourceManagementClient][resman_client] objektum használatával.
6. [BatchManagementClient][net_mgmt_client] -objektum használata több batch-fiók műveleteinek elvégzéséhez:
   * Hozzon létre egy batch-fiókot az új erőforráscsoporthoz.
   * Szerezze be az újonnan létrehozott fiókot a Batch szolgáltatásból.
   * Nyomtassa ki az új fiókhoz tartozó fiók kulcsait.
   * Új elsődleges kulcs újralétrehozása a fiókhoz.
   * Nyomtassa ki a fiók kvótájának adatait.
   * Nyomtassa ki az előfizetés kvótájának adatait.
   * Az előfizetésen belüli összes fiók nyomtatása.
   * Az újonnan létrehozott fiók törlése.
7. Törölje az erőforráscsoportot.

Az újonnan létrehozott batch-fiók és az erőforráscsoport törlése előtt megtekintheti őket a [Azure Portalban][azure_portal]:

A minta alkalmazás sikeres futtatásához először regisztrálnia kell a Azure Portal Azure AD-Bérlővel, és engedélyt kell adnia a Azure Resource Manager API-nak. Kövesse a [Batch-felügyeleti megoldások Active Directory használatával végzett hitelesítésének](batch-aad-auth-management.md)lépéseit.


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Hitelesítési forgatókönyvek az Azure AD-hez"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Alkalmazások integrálása a Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
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
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
