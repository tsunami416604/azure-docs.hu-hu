---
title: Kezelheti a Batch-fiók erőforrásokat, az ügyféloldali kódtára a .NET-keretrendszerhez – Azure |} A Microsoft Docs
description: Létrehozása, törlése és módosítása a Batch Management .NET könyvtár az Azure Batch-fiók erőforrásokat.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 65f8eb0752a181eda312515e557bb733c091e2e5
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505385"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batch-fiókok és kvóták kezelése a a Batch Management ügyféloldali kódtára a .NET-hez

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Csökkentheti karbantartási terhelés az Azure Batch-alkalmazások használatával a [Batch Management .NET használatával] [ api_mgmt_net] kódtár Batch-fiók létrehozása, törlés, kulcskezelés és kvóta felderítési automatizálásához.

* **Hozzon létre vagy töröljön a Batch-fiókok** belül bármely régióba. Ha egy független szoftverszállító (ISV), például megad egy szolgáltatás, amelyben minden egyes hozzárendelnek egy külön a Batch-fiók számlázási célokra az ügyfelek, a fiók létrehozását és törlését képességek a felhasználói portálra is hozzáadhat.
* **Kérje le, és a fiókkulcsok ismételt létrehozása** programozott módon bármelyik a Batch-fiókok esetében. Ez segíthet felel meg, amelyeket rendszeres helyettesítő vagy fiókkulcsok lejártát biztonsági házirendeknek. Ha különböző Azure-régiókban található számos Batch-fiókok, a helyettesítő folyamat automation növeli a megoldás hatékonyságát.
* **Ellenőrizze a fiókra vonatkozó kvóták** és a találgatást próbaverzió és a hiba meghatározása, hogy melyik Batch-fiókok milyen korlátokkal rendelkeznek. A fiókra vonatkozó kvóták ellenőrzésével feladatok megkezdése előtt, készletek létrehozását, vagy a számítási csomópontok hozzáadása, hol proaktív módon módosíthatja, vagy amikor ezek a számítási erőforrás jön létre. Megadhatja, hogy melyik fiók szükséges kvóta növeli a ezeket a fiókokat a további erőforrások kiosztása előtt.
* **Más Azure-szolgáltatások funkciói egyesítése** egy teljes körű megoldást – Batch Management .NET használatával az [Azure Active Directory][aad_about], és a [Azure Erőforrás-kezelő] [ resman_overview] ugyanazzal az alkalmazással együtt. Ezek a funkciók és saját API-k használatával megadhat egy zökkenőmentes hitelesítési módszer, erőforráscsoportok és a egy teljes körű megoldás a fentiekben leírt funkciók létrehozását és törlését teszi.

> [!NOTE]
> Amíg ez a cikk a Batch-fiókok, a kulcsok és a kvóták szoftveres kezelését összpontosít, is elvégezheti ezeket a tevékenységeket számos használatával a [az Azure portal][azure_portal]. További információkért lásd: [hozzon létre egy Azure Batch-fiókot az Azure portal használatával](batch-account-create-portal.md) és [kvóták és korlátozások az Azure Batch szolgáltatás](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Hozzon létre vagy töröljön a Batch-fiókok
Ahogy említettük, az elsődleges funkcióit a Batch Management API egyik létrehozására és törlésére a Batch-fiókok egy Azure-régióban. Ehhez használja [BatchManagementClient.Account.CreateAsync] [ net_create] és [DeleteAsync][net_delete], vagy mint a szinkron.

Az alábbi kódrészlet létrehoz egy fiókot, beolvassa az újonnan létrehozott fiókhoz a Batch szolgáltatás és törli őket. Ez a kódrészlet és a többi ebben a cikkben `batchManagementClient` egy teljesen inicializálva példánya [BatchManagementClient][net_mgmt_client].

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
> A Batch Management .NET könyvtár és annak BatchManagementClient osztályt használó alkalmazásoknak **szolgáltatás-rendszergazda** vagy **coadministrator** hozzáférést az előfizetéshez, amely a Batch tulajdonosa fiók kezelését. További információkért lásd: a [Azure Active Directory](#azure-active-directory) szakaszt, és a [AccountManagement] [ acct_mgmt_sample] kódmintát.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Kérje le, és a fiókkulcsok ismételt létrehozása
Az elsődleges és másodlagos kulcsainak beszerzése az adott előfizetéshez tartozó minden Batch-fiókból használatával [ListKeysAsync][net_list_keys]. Ezeknek a kulcsoknak használatával létrehozhatja [RegenerateKeyAsync][net_regenerate_keys].

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
> Létrehozhat egy zökkenőmentes kapcsolat a munkafolyamat felügyeleti alkalmazásai számára. Először szerezze be a Batch-fiók a kezelni kívánt fiók kulcsára [ListKeysAsync][net_list_keys]. Ezután használja ezt a kulcsot, a Batch .NET-kódtár inicializálása közben [BatchSharedKeyCredentials] [ net_sharedkeycred] osztály, amely inicializálása közben használatos [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Ellenőrizze az Azure-előfizetés és a Batch-fiókra vonatkozó kvóták
Az Azure-előfizetések és az egyes Azure-szolgáltatások például a kötegelt összes rendelkezik alapértelmezett őket bizonyos entitások számát korlátozó kvótákat. Az Azure-előfizetések alapértelmezett kvótái, lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md). Az alapértelmezett kvótákat a Batch szolgáltatás, lásd: [kvóták és korlátozások az Azure Batch szolgáltatás](batch-quota-limit.md). A Batch Management .NET könyvtár használatával ellenőrizheti az alkalmazások kvóta. Ez lehetővé teszi, hogy foglalási döntéseket, mielőtt fiókok hozzáadása, vagy a számítási erőforrások, például a készletek és számítási csomópontokon.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Ellenőrizze a Batch-fiókra vonatkozó kvóták az Azure-előfizetés
Mielőtt létrehozná a Batch-fiók egy régióban, ellenőrizheti, hogy vannak-e fiókkal az adott régióban adhat hozzá Azure-előfizetéséhez.

Az alábbi kódrészlet először használjuk [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] gyűjteménye, amelyek egy előfizetésen belül az összes Batch-fiókok beolvasásához. Amint azt korábban beszerzett ebben a gyűjteményben, hogy állapítsa meg, hány fiókok a célrégióban. Majd ezzel [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] szerezze be a Batch-fiókkvótája, és meghatározhatja, hány fiókok (ha vannak) az adott régióban hozható létre.

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

A fenti kódrészletben `creds` példánya [TokenCloudCredentials][azure_tokencreds]. Ez az objektum létrehozása egy példát, olvassa el a [AccountManagement] [ acct_mgmt_sample] kód példa a Githubon.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Ellenőrizze a számítási erőforrás kvótákat a Batch-fiók
Növelje a számítási erőforrásokat a Batch-megoldás, mielőtt ellenőrizheti annak biztosítása érdekében a hozzárendelni kívánt erőforrások nem haladhatja meg a fiókra vonatkozó kvóták. Az alábbi kódrészlet azt nyomtassa ki a kvótákra vonatkozó információk nevű Batch-fiók `mybatchaccount`. A saját alkalmazásában az ilyen információk segítségével határozza meg, hogy a fiók képes kezelni a további erőforrások létrehozása.

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
> Nincsenek alapértelmezett kvóták az Azure-előfizetések és-szolgáltatások, míg számos ilyen korlátozás megemelhetők a kérelem elküldése a [az Azure portal][azure_portal]. Lásd a [kvóták és korlátozások az Azure Batch szolgáltatás](batch-quota-limit.md) vonatkozó utasításokat a Batch-fiókra vonatkozó kvóták növelését.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Az Azure AD a Batch Management .NET használata

A Batch Management .NET könyvtár egy Azure-erőforrás-szolgáltató ügyfél, és együtt használatos [Azure Resource Manager] [ resman_overview] programozott módon kezelheti a fiók erőforrásokat. Az Azure AD bármely Azure-erőforrás szolgáltató ügyfél, például a Batch Management .NET könyvtár és révén-kérelmek hitelesítéséhez szükséges [Azure Resource Manager][resman_overview]. A Batch Management .NET könyvtár az Azure AD-vel kapcsolatos további információkért lásd: [használata az Azure Active Directory hitelesítést Batch-megoldások](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Minta-projektet a Githubon

Tekintse meg a Batch Management .NET használatával működés közben, tekintse át a [AccountManagment] [ acct_mgmt_sample] mintaprojektet a Githubon. A AccountManagment mintaalkalmazás bemutatja a következő műveleteket:

1. Az Azure AD-ből biztonsági jogkivonat beszerzése [ADAL][aad_adal]. Ha a felhasználó még nem jelentkezett be, bekapcsolják a Azure hitelesítő adatait.
2. Az Azure ad-ből kapott biztonsági jogkivonat, hozzon létre egy [SubscriptionClient] [ resman_subclient] Azure lekérdezni a fiókhoz tartozó előfizetések listáját. A felhasználó is válasszon egy előfizetést a listából, ha több előfizetéssel tartalmazza.
3. A kiválasztott előfizetéshez társított hitelesítő adatainak lekérése.
4. Hozzon létre egy [ResourceManagementClient] [ resman_client] objektum a hitelesítő adatok használatával.
5. Használja a [ResourceManagementClient] [ resman_client] objektumot hozzon létre egy erőforráscsoportot.
6. Használja a [BatchManagementClient] [ net_mgmt_client] objektum több Batch-fiók műveletek végrehajtásához:
   * Batch-fiók létrehozása az új erőforráscsoportban.
   * Az újonnan létrehozott fiókhoz le a Batch szolgáltatásban.
   * Nyomtassa ki a fiókkulcsok az új fiókhoz.
   * A fiók egy új elsődleges kulcs újragenerálása.
   * Nyomtassa ki a kvóta a fiók adatai.
   * Az előfizetéshez tartozó kvóta információt kinyomtatni.
   * Nyomtassa ki az előfizetésen belüli összes fiókot.
   * Törölje az újonnan létrehozott fiókhoz.
7. Törölje az erőforráscsoportot.

Mielőtt az újonnan létrehozott Batch-fiók és az erőforrás csoportot töröl, megtekintheti őket a [az Azure portal][azure_portal]:

A mintaalkalmazás sikeresen futtatni, először regisztrálja az Azure Portalon az Azure AD-bérlő, és engedélyek megadása az Azure Resource Manager API-hoz. Az itt ismertetett lépéseket követve [az Active Directory hitelesítést Batch Management solutions](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Hitelesítési forgatókönyvek az Azure ad-ben"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Alkalmazások integrálása az Azure Active Directoryval"
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
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
