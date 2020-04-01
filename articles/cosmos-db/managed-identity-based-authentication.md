---
title: A rendszer által hozzárendelt felügyelt identitás használata az Azure Cosmos DB-adatok eléréséhez
description: Ismerje meg, hogyan konfigurálhat egy Azure AD rendszeráltal hozzárendelt felügyelt identitást az Azure Cosmos DB kulcsainak eléréséhez. msi, felügyelt szolgáltatásidentitás, aad, azure active directory, identitás
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 102efba5d028eef621f392ef1739ea9ebeca0b44
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80417232"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>A rendszer által hozzárendelt felügyelt identitás használata az Azure Cosmos DB-adatok eléréséhez

Ebben a cikkben egy **robusztus, kulcsrotációs független megoldást** állít be az Azure Cosmos DB-kulcsok eléréséhez a felügyelt [identitások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)kihasználásával. Ebben a cikkben szereplő példa egy Azure-függvényt használ. Ezt a megoldást azonban bármely olyan szolgáltatás használatával elérheti, amely támogatja a felügyelt identitásokat. 

Megtudhatja, hogyan hozhat létre egy Azure-függvényt, amely anélkül érheti el az Azure Cosmos DB-t, hogy az Azure Cosmos DB-kulcsait másolnia kellene. A funkció minden percben felébred, és rögzíti az akváriumi akvárium jelenlegi hőmérsékletét. Ha meg szeretné tudni, hogyan állíthat be egy időzítő aktivált Azure-függvény tekintse meg a [hozzon létre egy függvényt az Azure-ban, amely egy időzítő](../azure-functions/functions-create-scheduled-function.md) cikk által kiváltott.

A forgatókönyv egyszerűsítése érdekében a régebbi hőmérsékleti dokumentumok karbantartását egy már konfigurált [Time To Live](./time-to-live.md) beállítás kezeli. 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Rendszeráltal hozzárendelt felügyelt identitás hozzárendelése Azure-függvényhez

Ebben a lépésben egy rendszer által hozzárendelt felügyelt identitást rendel az Azure-függvényhez.

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg az **Azure Függvény** ablaktábláját, és keresse meg a függvényalkalmazást. 

1. Nyissa meg a Platform > **identitáslapját:** **Platform features** 

   ![Identitás lap](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Az **Identitás** lapon kapcsolja be **a** **Rendszeridentitás** állapotát. Győződjön meg a **Rról,** hogy válassza a Mentés lehetőséget, és ellenőrizze, hogy be szeretné-e kapcsolni a rendszeridentitást. A **rendszeridentitás** ablaktáblájának végül a következőképpen kell kinéznie:  

   ![A rendszeridentitás be van kapcsolva](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>A felügyelt identitás-hozzáférés megadása az Azure Cosmos-fiókhoz

Ebben a lépésben egy szerepkört rendel az Azure Függvény rendszeráltal hozzárendelt felügyelt identitásához. Az Azure Cosmos DB több beépített szerepkörrel rendelkezik, amelyek et hozzárendelheti a felügyelt identitáshoz. Ehhez a megoldáshoz a következő két szerepkört fogja használni:

|Beépített szerepkör  |Leírás  |
|---------|---------|
|[DocumentDB-fiók közreműködője](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Az Azure Cosmos DB-fiókok kezelése. Lehetővé teszi az olvasási/írási kulcsok visszakeresését. |
|[Cosmos DB-fiókolvasó](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Az Azure Cosmos DB-fiók adatai olvasása. Lehetővé teszi az olvasási kulcsok visszakeresését. |

> [!IMPORTANT]
> Az RBAC-támogatás az Azure Cosmos DB-ben csak a síkműveletek vezérlésére alkalmazható. Az adatsík-műveletek főkulcsokkal vagy erőforrás-jogkivonatokkal vannak biztosítva. További információ: Biztonságos hozzáférés az [adatokhoz](secure-access-to-data.md) cikkben.

> [!TIP] 
> Szerepkörök hozzárendelésekénekénkén csak a szükséges hozzáférést rendelje hozzá. Ha a szolgáltatás csak olvasási adatokat igényel, majd rendelje hozzá a felügyelt identitást a **Cosmos DB-fiókolvasó** szerepkörhöz. A legkisebb jogosultsági hozzáférés fontosságáról a [kiemelt jogosultságú fiókok alacsonyabb szintű kitettsége](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) című cikkben olvashat bővebben.

A forgatókönyv ben olvassa el a hőmérsékletet, majd írja vissza az adatokat egy tárolóba az Azure Cosmos DB-ben. Mivel meg kell írnia az adatokat, a **DocumentDB-fiók közreműködői** szerepkört fogja használni. 

1. Jelentkezzen be az Azure Portalon, és keresse meg az Azure Cosmos DB-fiókját. Nyissa meg a **Hozzáférés-kezelés (IAM) ablaktáblát,** majd a **Szerepkör-hozzárendelések** lapot:

   ![IAM ablaktábla](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Jelölje ki a **+ Hozzáadás** gombot, majd **adja hozzá a szerepkör-hozzárendelést**.

1. A **Szerepkör-hozzárendelés hozzáadása** panel jobbra nyílik:

   ![Szerepkör hozzáadása](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Szerepkör** – **DocumentDB-fiók közreműködőjének** kiválasztása
   * **Hozzáférés hozzárendelése** a – A **Rendszer által rendelt felügyelt identitás** kiválasztása alszakaszban válassza a **Függvényalkalmazás**lehetőséget.
   * **Válassza ki** – Az ablaktábla fel lesz töltve az összes függvényalkalmazással az előfizetésben, amelyek **felügyelt rendszeridentitással**rendelkeznek. A mi esetünkben kiválasztom az **SummaryService** függvény alkalmazást: 

      ![Hozzárendelés kiválasztása](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Miután a függvényalkalmazás identitásának kiválasztása, kattintson a **Mentés gombra.**

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Az Azure Cosmos DB-kulcsainak programozott elérése az Azure Függvényből

Most már rendelkezik egy függvényalkalmazás, amely egy rendszer által hozzárendelt felügyelt identitás. Ez az identitás az Azure Cosmos DB-engedélyekben megkapja a **DocumentDB-fiók közreműködői** szerepkörét. A következő függvényalkalmazás-kód lefoglalja az Azure Cosmos DB-kulcsokat, hozzon létre egy CosmosClient objektumot, levegye a hőmérsékletet, majd mentse ezt a Cosmos DB-be.

Ez a minta a [List Keys API-t](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) használja az Azure Cosmos DB-fiók kulcsainak eléréséhez.

> [!IMPORTANT] 
> Ha hozzá szeretné [rendelni a **Cosmos DB-fiókolvasó** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) szerepkört, akkor az írásvédett [listakulcsok api-t kell használnia.](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys) Ez csak az írásvédett kulcsokat foglalja be.

A List Keys `DatabaseAccountListKeysResult` API visszaadja az objektumot. Ez a típus nincs definiálva a C# könyvtárakban. A következő kód ennek az osztálynak a megvalósítását mutatja:  

```csharp 
namespace SummarizationService 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

A példa egy "TemperatureRecord" nevű egyszerű dokumentumot is használ, amely a következőképpen határozható meg:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

A [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) könyvtár használatával lekell szereznie a rendszeráltal hozzárendelt felügyelt identitásjogkivonatot. A jogkivonat beszerzésének és a `Microsoft.Azure.Service.AppAuthentication` tárról további információkért tekintse meg a [Szolgáltatás-szolgáltatás hitelesítése](../key-vault/service-to-service-authentication.md) című cikket.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class TemperatureMonitor
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Most már készen áll [az Azure-függvény üzembe helyezésére.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>További lépések

* [Tanúsítványalapú hitelesítés az Azure Cosmos DB és az Active Directory használatával](certificate-based-authentication.md)
* [Azure Cosmos-kulcsok védelme az Azure Key Vaulttal](access-secrets-from-keyvault.md)
* [Az Azure Cosmos DB biztonsági alapkonfigurációja](security-baseline.md)
