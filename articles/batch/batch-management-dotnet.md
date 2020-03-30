---
title: A Kötegkezelés .NET függvénytár használata fiókerőforrások kezeléséhez
description: Hozzon létre, töröljön és módosítson Azure Batch-fiókerőforrásokat a Batch Management .NET kódtárral.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 79916d769ad8a7228aec8db965c29506ccd78ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023684"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Kötegfiókok és kvóták kezelése a kötegelt kezelés ügyféltárral .

> [!div class="op_single_selector"]
> * [Azure-portál](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Az Azure Batch-alkalmazások karbantartási terhelését a [Batch Management .NET][api_mgmt_net] függvénytár használatával csökkentheti a Batch-fiók létrehozásának, törlésének, kulcskezelésének és kvótafelderítésének automatizálásához.

* **Kötegfiókok létrehozása és törlése** bármely régióban. Ha például független szoftverszállítóként (ISV) olyan szolgáltatást nyújt az ügyfeleknek, amelyben mindegyik számlázási célokra külön Batch-fiókot kap, fióklétrehozási és -törlési lehetőségeket adhat hozzá az ügyfélportálhoz.
* **A batch-fiókok programozott módon való lekérése és újragenerálása.** Ez segíthet a fiókkulcsok időszakos átütemezését vagy lejártát kikényszerítő biztonsági házirendeknek való megfelelésben. Ha több Batch-fiókkal rendelkezik különböző Azure-régiókban, a görgetési folyamat automatizálása növeli a megoldás hatékonyságát.
* **Ellenőrizze a fiókkvótákat,** és vegye ki a próba-és hibatalálgatásot annak meghatározásából, hogy mely Batch-fiókok milyen korlátokkal rendelkeznek. Ha a feladatok megkezdése előtt ellenőrzi a fiókkvótákat, készleteket hoz létre, vagy számítási csomópontokat ad hozzá, proaktív módon módosíthatja, hogy hol és mikor jönnek létre ezek a számítási erőforrások. Meghatározhatja, hogy mely fiókok igényelnek kvótanövelést, mielőtt további erőforrásokat osztana ki ezekben a fiókokban.
* **Más Azure-szolgáltatások funkcióit kombinálva** teljes körű felügyeleti élményt nyújt – a Batch Management .NET, az [Azure Active Directory][aad_about]és az Azure Resource [Manager][resman_overview] együttes használatával ugyanabban az alkalmazásban. Ezekkel a szolgáltatásokkal és API-khasználatával zökkenőmentes hitelesítési élményt biztosíthat, lehetővé teheti az erőforráscsoportok létrehozását és törlését, valamint a fent leírt képességeket egy végpontok közötti felügyeleti megoldáshoz.

> [!NOTE]
> Ez a cikk a Batch-fiókok, kulcsok és kvóták programozott felügyeletére összpontosít, számos ilyen tevékenységet hajthat végre az [Azure Portal][azure_portal]használatával. További információ: [Azure Batch-fiók létrehozása az Azure Portalhasználatával](batch-account-create-portal.md) és [az Azure Batch-szolgáltatás kvótái és korlátozásai](batch-quota-limit.md)című témakörben talál.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Kötegfiókok létrehozása és törlése
Mint említettük, a Batch Management API egyik elsődleges funkciója a Batch-fiókok létrehozása és törlése egy Azure-régióban. Ehhez használja [a BatchManagementClient.Account.CreateAsync][net_create] és [DeleteAsync][net_delete]fájlt, vagy azok szinkron megfelelőit.

A következő kódrészlet létrehoz egy fiókot, beszerzi az újonnan létrehozott fiókot a Batch szolgáltatásból, majd törli azt. Ebben a kódrészletben és a `batchManagementClient` cikkben szereplő többi példány a [BatchManagementClient][net_mgmt_client]teljesen inicializált példánya.

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
> A Batch Management .NET függvénytárat és annak BatchManagementClient osztályát használó alkalmazások kezeléséhez **a szolgáltatás rendszergazdájának** vagy **társadminisztrátorának** hozzáférést kell biztosítani a Batch-fiók kalkulátorához. További információkért tekintse meg az Azure Active Directory szakaszés az [AccountManagement-kód][acct_mgmt_sample] minta.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Fiókkulcsok beolvasása és újragenerálása
Az elsődleges és másodlagos fiókkulcsokat az előfizetés bármely Batch-fiókjából a [ListKeysAsync][net_list_keys]használatával szerezheti be. Ezeket a kulcsokat a [RegenerateKeyAsync][net_regenerate_keys]segítségével újragenerálhatja.

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
> Egyszerűsített kapcsolati munkafolyamatot hozhat létre a felügyeleti alkalmazásokhoz. Először szerezzen be egy fiókkulcsot a [ListKeysAsync][net_list_keys]segítségével kezelni kívánt Batch-fiókhoz. Ezt követően használja ezt a kulcsot a Batch .NET függvénytár [BatchSharedKeyCredentials][net_sharedkeycred] osztályának inicializálásakor, amely a BatchClient inicializálásakor [használatos.][net_batch_client]
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Az Azure-előfizetési és Batch-fiókkvóták ellenőrzése
Az Azure-előfizetések és az egyes Azure-szolgáltatások, például a Batch mind alapértelmezett kvótákkal rendelkeznek, amelyek korlátozzák a bennük lévő bizonyos entitások számát. Az Azure-előfizetések alapértelmezett kvótáiról az [Azure-előfizetések és szolgáltatáskorlátok, kvóták és korlátozások című témakörben olvashat.](../azure-resource-manager/management/azure-subscription-service-limits.md) A Batch szolgáltatás alapértelmezett kvótáiról a [Kvóták és az Azure Batch szolgáltatás korlátozásai című](batch-quota-limit.md)témakörben olvashat. A Kötegkezelés .NET kódtár használatával ellenőrizheti ezeket a kvótákat az alkalmazásokban. Ez lehetővé teszi, hogy felosztási döntéseket hozzon, mielőtt fiókokat vagy erőforrásokat, például készleteket és számítási csomópontokat ad hozzá.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Azure-előfizetés ellenőrzése kötegelt fiókkvótákhoz
Mielőtt létrehozna egy Batch-fiókot egy régióban, ellenőrizheti az Azure-előfizetését, hogy megállapíthatja, hogy hozzá tud-e adni egy fiókot az adott régióban.

Az alábbi kódrészletben először a [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] használatával kapjuk meg az előfizetésen belüli összes Batch-fiók gyűjteményét. Miután beszereztük ezt a gyűjteményt, meghatároztuk, hogy hány fiók van a célrégióban. Ezután [a BatchManagementClient.Subscriptions][net_mgmt_subscriptions] használatával kapjuk meg a Batch-fiók kvótáját, és meghatározzuk, hogy hány fiók hozható létre (ha van ilyen) az adott régióban.

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

A fenti kódrészletben `creds` a [TokenCloudCredentials példánya][azure_tokencreds]található. Az objektum létrehozásának példáját tekintse meg a [AccountManagement-kód][acct_mgmt_sample] minta a GitHubon.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Batch-fiók ellenőrzése számítási erőforráskvótákat keresve
Mielőtt növelné a számítási erőforrásokat a Batch-megoldásban, ellenőrizheti, hogy a lefoglalni kívánt erőforrások nem lépik-e túl a fiók kvótáit. Az alábbi kódrészletben kinyomtatjuk a Batch fiók `mybatchaccount`kvótaadatait. A saját alkalmazásában ezeket az információkat használhatja annak meghatározására, hogy a fiók képes-e kezelni a létrehozandó további erőforrásokat.

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
> Bár az Azure-előfizetések és -szolgáltatások alapértelmezett kvótái vannak érvényben, ezek közül a korlátok közül sok az [Azure Portalon][azure_portal]történő kérelem kiadásával emelhető ki. Például [lásd: Kvóták és korlátok az Azure Batch szolgáltatás](batch-quota-limit.md) a Batch-fiók kvóták növelésével kapcsolatos utasításokat.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Az Azure AD használata kötegelt kezeléssel .NET

A Batch Management .NET függvénytár egy Azure-erőforrás-szolgáltató ügyfél, és az [Azure Resource Manager][resman_overview] segítségével kezeli a fiók erőforrásait programozott módon. Az Azure AD-nek hitelesítenie kell az Azure erőforrás-szolgáltató ügyfélen keresztül, beleértve a Batch Management .NET függvénytárat és az [Azure Resource Manageren][resman_overview]keresztül érkező kérelmeket. Az Azure AD batch management .NET-kódtárral való használatáról az [Azure Active Directory használata kötegelt megoldások hitelesítéséhez című](batch-aad-auth.md)témakörben talál további információt. 

## <a name="sample-project-on-github"></a>Mintaprojekt a GitHubon

A Batch Management .NET működés közbeni megtekintéséhez tekintse meg a [AccountManagement][acct_mgmt_sample] mintaprojektet a GitHubon. Az AccountManagement mintaalkalmazás a következő műveleteket mutatja be:

1. Biztonsági jogkivonat beszerzése az Azure AD-ből az [ADAL][aad_adal]használatával. Ha a felhasználó még nincs bejelentkezve, a rendszer kéri az Azure-hitelesítő adatait.
2. Az Azure AD-től kapott biztonsági jogkivonattal hozzon létre egy [SubscriptionClient-t,][resman_subclient] amely lekérdezi az Azure-t a fiókhoz társított előfizetések listájához. A felhasználó kiválaszthat egy előfizetést a listából, ha egynél több előfizetést tartalmaz.
3. A kijelölt előfizetéshez társított hitelesítő adatok beszereznie.
4. Hozzon létre egy [ResourceManagementClient][resman_client] objektumot a hitelesítő adatok használatával.
5. Erőforráscsoport létrehozásához használjon [ResourceManagementClient][resman_client] objektumot.
6. [BatchManagementClient][net_mgmt_client] objektum használatával több Batch-fiókműveletet hajtson végre:
   * Batch-fiók létrehozása az új erőforráscsoportban.
   * Az újonnan létrehozott fiók bekéselése a Batch szolgáltatásból.
   * Az új fiók fiókkulcsainak nyomtatása.
   * A fiók új elsődleges kulcsának újragenerálása.
   * A fiók kvótaadatainak nyomtatása.
   * Az előfizetés kvótaadatainak nyomtatása.
   * Az előfizetésen belüli összes fiók nyomtatása.
   * Az újonnan létrehozott fiók törlése.
7. Az erőforráscsoport törlése.

Az újonnan létrehozott Batch-fiók és erőforráscsoport törlése előtt megtekintheti őket az [Azure Portalon:][azure_portal]

A mintaalkalmazás sikeres futtatásához először regisztrálnia kell azt az Azure AD-bérlővel az Azure Portalon, és engedélyeket kell adnia az Azure Resource Manager API-nak. Kövesse a [Kötegkezelési megoldások hitelesítése](batch-aad-auth-management.md)az Active Directoryval című részben leírt lépéseket.


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Az Azure AD hitelesítési forgatókönyvei"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Alkalmazások integrálása az Azure Active Directoryval"
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
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
