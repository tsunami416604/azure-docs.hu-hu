---
title: "A .NET - Azure Batch fiók erőforrások az ügyféloldali kódtár kezelése |} Microsoft Docs"
description: "Létrehozása, törlése és módosítása az Azure Batch erőforrásainak a Batch Management .NET kódtárral könyvtárhoz."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eafde9258222a2ab09ade2e366f9cc595a303dec
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batch fiókjainak és kvótáinak a Batch Management ügyféloldali kódtára a .NET-keretrendszerhez készült kezelése

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Csökkenthető karbantartási terhet az Azure Batch-alkalmazások használatával a [Batch Management .NET kódtárral] [ api_mgmt_net] automatizálhatja a Batch-fiók létrehozása, törlés, kulcskezelés és kvóta felderítési könyvtár.

* **Hozzon létre vagy töröljön a Batch-fiókok** bármely régióban. Ha egy független szoftverszállító (ISV), például ad meg, amelyben mindegyik tartozik egy külön Batch-fiók számlázási okokból az ügyfelek a szolgáltatás, a fiók létrehozását és törlését képességek a felhasználói portálra is hozzáadhat.
* **Beolvasni, majd újra létrehozza a kulcsait** programozott módon az összes, a Batch-fiókok. Ez segít felel meg a biztonsági házirendek, amelyeket rendszeres helyettesítő vagy kulcsait lejártát. Ha több Batch-fiókok Azure különböző régiókban, a helyettesítő folyamat automation növeli az a megoldás hatékonyságát.
* **Ellenőrizze a fiók kvóták** hajtanak végre a próba-és-hiba munka bizonytalanságát kívül meghatározása, hogy mely Batch-fiókok milyen korlátokkal rendelkeznek. A fiók kvótákat feladatok megkezdése előtt ellenőrzésével készletek létrehozása, vagy a számítási csomópontok hozzáadása, hol proaktív módosíthatja, vagy ha ezek a számítási erőforrások jönnek létre. Azt is meghatározhatja, hogy mely fiókok szükséges kvóta növeli az említett további erőforrások hozzárendelése előtt.
* **Más Azure-szolgáltatások jellemzői egyesítése** teljes körű felügyeleti élmény--a Batch Management .NET kódtárral [Azure Active Directory][aad_about], és a [Azure Erőforrás-kezelő] [ resman_overview] együtt ugyanabban az alkalmazásban. Ezeket a szolgáltatásokat és az API-k segítségével megadhatja a frictionless felhasználói hitelesítés, létrehozása és törlése az erőforrás-csoportok és végpont kezelési megoldás a fentiekben leírt képességeit.

> [!NOTE]
> Amíg ez a cikk foglalkozik a programozott felügyeleti a kötegelt fiókok, a kulcsok és a kvóták, hajthat végre ezeket a tevékenységeket számos használatával a [Azure-portálon][azure_portal]. További információkért lásd: [az Azure portál használata az Azure Batch-fiók létrehozása](batch-account-create-portal.md) és [kvótái és korlátai az Azure Batch szolgáltatás](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Hozzon létre vagy töröljön a Batch-fiókok
Ahogy azt korábban említettük, a kötegelt API elsődleges szolgáltatásai egyik hozzon létre vagy töröljön a Batch-fiókok Azure-régióban. Ehhez használja [BatchManagementClient.Account.CreateAsync] [ net_create] és [DeleteAsync][net_delete], vagy mint a szinkron.

A következő kódrészletet fiókot hoz létre, az újonnan létrehozott fiókhoz kapja a Batch szolgáltatás, és törli őket. Ezt a kódrészletet, és a többi ebben a cikkben `batchManagementClient` teljesen inicializált példánya [BatchManagementClient][net_mgmt_client].

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
> A Batch Management .NET kódtárral és BatchManagementClient osztálya használó alkalmazásoknak **szolgáltatás-rendszergazda** vagy **coadministrator** hozzáférése az előfizetéshez, amely a kötegelt tulajdonosa fiókot felügyelhető. További információkért lásd: a [Azure Active Directory](#azure-active-directory) szakasz és a [AccountManagement] [ acct_mgmt_sample] kódminta.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Kérje le, majd fiók kulcsok újragenerálása
Elsődleges és másodlagos kulcsok beszerzése az előfizetésen belül a Batch-fiókból való használatával [ListKeysAsync][net_list_keys]. Ezeknek a kulcsoknak használatával állíthatja helyre [RegenerateKeyAsync][net_regenerate_keys].

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
> A felügyeleti alkalmazások zökkenőmentes kapcsolódási munkafolyamatot hozhat létre. Először szerezze be a Batch-fiókhoz a felügyelni kívánt fiókkulcs [ListKeysAsync][net_list_keys]. Ezt követően használja ezt a kulcsot, a Batch .NET kódtár inicializálása közben [BatchSharedKeyCredentials] [ net_sharedkeycred] osztály, amely a inicializálása [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Ellenőrizze az Azure-előfizetés és a Batch-fiók kvóták
Azure-előfizetések és az egyes Azure-szolgáltatásokkal, mint a Batch-összes rendelkezik alapértelmezett kvótákat, amelyek a bennük lévő egyes entitások számának korlátozásához. További Azure-előfizetések alapértelmezett kvótái: [Azure-előfizetés és szolgáltatási korlátok, kvóták és megkötések](../azure-subscription-service-limits.md). A Batch szolgáltatás alapértelmezett kvóták, lásd: [kvótái és korlátai az Azure Batch szolgáltatás](batch-quota-limit.md). A Batch Management .NET kódtárral könyvtár használatával ellenőrizheti az alkalmazások ezek mely százalékértékénél kéri. Ez lehetővé teszi, hogy foglalási döntéseket fiókok hozzáadása, vagy a számítási erőforrásokhoz, mint a gyűjtők és számítási csomópontok előtt.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Ellenőrizze a kötegelt fiók kvóták az Azure-előfizetés
Batch-fiók létrehozása a régióban, előtt ellenőrizheti, hogy meg vannak-e fiók hozzáadása az adott régióban tudni az Azure-előfizetéshez.

Az alábbi kódrészletet, először használjuk [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] gyűjteménye, amelyek egy előfizetésen belüli összes Batch-fiókok eléréséhez. Amint azt korábban beszerzett ehhez a gyűjteményhez, azt határozza meg, hány fiókhoz cél területen vannak. Akkor használjuk [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] beszerzése a Batch-fiókkvótája, és határozza meg, hány fiókhoz (ha vannak) az adott régióban is létrehozható.

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

A fenti, a részlet `creds` példánya [TokenCloudCredentials][azure_tokencreds]. Ez az objektum létrehozására láthat példát, olvassa el a [AccountManagement] [ acct_mgmt_sample] kód mintát a Githubon.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Ellenőrizze a számítási erőforrás kvóták Batch-fiók
A kötegelt megoldásban számítási erőforrások növelése, előtt ellenőrizheti a lefoglalni kívánt erőforrásokat biztosításához nem haladhatja meg a fiók a kvóták. Az alábbi kódrészletben, azt nyomtassa ki a Batch-fiók a kvótaadatok `mybatchaccount`. A saját alkalmazásban használhatja ezeket az információkat annak meghatározásához, hogy a fiók kezelni tud létrehozni a további erőforrások.

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
> Miközben alapértelmezett kvóták Azure-előfizetések és a szolgáltatások, a működés felső korlátjának számos küld a emelhető a [Azure-portálon][azure_portal]. Lásd például: [kvótái és korlátai az Azure Batch szolgáltatás](batch-quota-limit.md) kapcsolatos utasításokat a Batch-fiók kvótákat növelését.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Használhatja az Azure Active Directory Batch Management .NET

A Batch Management .NET könyvtár az Azure-erőforrás-szolgáltató ügyfél, és együtt használni [Azure Resource Manager] [ resman_overview] programozott módon fiók-erőforrások kezeléséhez. Az Azure AD bármely Azure-erőforrás szolgáltató ügyfél, beleértve a Batch Management .NET kódtár és révén kérelmek hitelesítéséhez szükséges [Azure Resource Manager][resman_overview]. A Batch Management .NET könyvtár az Azure AD használatával kapcsolatban további információkért lásd: [használata Azure Active Directory kötegelt megoldások hitelesítéséhez](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Mintaprojektet a Githubon

A művelet a Batch Management .NET kódtárral megtekintéséhez tekintse meg a [AccountManagment] [ acct_mgmt_sample] mintaprojektet a Githubon. A AccountManagment mintaalkalmazás mutatja be a következő műveleteket:

1. Szerezzen be egy biztonsági jogkivonatot az Azure AD használatával [ADAL][aad_adal]. Ha a felhasználó már nem jelentkezett be, meg kell a Azure hitelesítő adatait.
2. Az az Azure AD-ből kapott biztonsági jogkivonatot, hozzon létre egy [SubscriptionClient] [ resman_subclient] Azure lekérdezni a fiókhoz tartozó előfizetések listáját. A felhasználó is kiválaszthat a listából, ha egynél több előfizetésnek tartalmaz.
3. A kijelölt előfizetéshez tartozó hitelesítő adatokat lekérni.
4. Hozzon létre egy [ResourceManagementClient] [ resman_client] objektum a hitelesítő adatok használatával.
5. Használja a [ResourceManagementClient] [ resman_client] objektumot hozzon létre egy erőforráscsoportot.
6. Használja a [BatchManagementClient] [ net_mgmt_client] objektum több Batch-fiók műveletek végrehajtásához:
   * Batch-fiók létrehozása az új erőforráscsoportban.
   * Az újonnan létrehozott fiókhoz beszerzése a Batch szolgáltatás.
   * Nyomtassa ki az új fiók a kulcsait.
   * A fiók egy új elsődleges kulcs újragenerálása.
   * A fiók kvótájának nyomtatása.
   * Az előfizetéshez tartozó kvóta információt kinyomtatni.
   * Nyomtassa ki az előfizetésen belüli összes fiók.
   * Törölje az újonnan létrehozott fiók.
7. Törölje a csoportot.

Az újonnan létrehozott kötegelt fiók- és erőforrás csoportot töröl, mielőtt megtekinthetné őket a a [Azure-portálon][azure_portal]:

A mintaalkalmazás sikeresen futtatni, először regisztrálja az Azure AD-bérlő az Azure portálon, és a hozzáférési jogot az Azure Resource Manager API-t. Kövesse az itt ismertetett lépéseket: [hitelesítéséhez kötegelt megoldásokat az Active Directory](batch-aad-auth-management.md).


[aad_about]: ../active-directory/active-directory-whatis.md "Mi az Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Az Azure Active Directory hitelesítési forgatókönyvei"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Alkalmazások integrálása az Azure Active Directoryval"
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
