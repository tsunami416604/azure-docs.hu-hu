---
title: A rendszer által hozzárendelt felügyelt identitás használata az Azure Cosmos DB-adatok eléréséhez
description: Megtudhatja, hogyan konfigurálhat egy Azure Active Directory (Azure AD) rendszeráltal hozzárendelt felügyelt identitást (felügyelt szolgáltatásidentitást) az Azure Cosmos DB kulcsainak eléréséhez.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641235"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Az Azure Cosmos DB-adatok eléréséhez használja a rendszer által hozzárendelt felügyelt identitásokat

Ebben a cikkben egy *robusztus, kulcsos rotációs független* megoldást állíthat be az Azure Cosmos DB-kulcsok [felügyelt identitások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)használatával történő eléréséhez. Ebben a cikkben az Azure Functions például, de bármilyen szolgáltatást használhat, amely támogatja a felügyelt identitásokat. 

Megtudhatja, hogyan hozhat létre egy függvényalkalmazást, amely az Azure Cosmos DB-adatokat anélkül érheti el, hogy az Azure Cosmos DB-kulcsokat másolnia kellene. A funkció alkalmazás percenként felébred, és rögzíti az akváriumakvárium aktuális hőmérsékletét. Ha meg szeretné tudni, hogyan állíthat be egy időzítő által aktivált függvényalkalmazást, tekintse meg a [Függvény létrehozása az Azure-ban, amely egy időzítő](../azure-functions/functions-create-scheduled-function.md) cikk által kiváltott.

A forgatókönyv egyszerűsítése érdekében a [Time To Live](./time-to-live.md) beállítás már be van állítva a régebbi hőmérsékleti dokumentumok karbantartásához. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Rendszeráltal hozzárendelt felügyelt identitás hozzárendelése függvényalkalmazáshoz

Ebben a lépésben egy rendszer által hozzárendelt felügyelt identitást rendel a függvényalkalmazáshoz.

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg az **Azure Függvény** ablaktábláját, és nyissa meg a függvényalkalmazást. 

1. Nyissa meg a Platform > **identitáslapját:** **Platform features** 

   ![Képernyőkép a platform funkcióiról és a függvényalkalmazás identitásbeállításairól.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Az **Identitás** lapon kapcsolja be **a rendszeridentitás** **állapotát,** és válassza a **Mentés gombot.** **Az Identitás** ablaktáblának a következőképpen kell kinéznie:  

   ![Képernyőkép a rendszeridentitás állapotának bekapcsolva beállításával.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Hozzáférés az Azure Cosmos-fiókhoz

Ebben a lépésben szerepkört rendel a függvényalkalmazás rendszeráltal hozzárendelt felügyelt identitásához. Az Azure Cosmos DB több beépített szerepkörrel rendelkezik, amelyek et hozzárendelheti a felügyelt identitáshoz. Ehhez a megoldáshoz a következő két szerepkört fogja használni:

|Beépített szerepkör  |Leírás  |
|---------|---------|
|[DocumentDB-fiók közreműködője](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Az Azure Cosmos DB-fiókok kezelése. Lehetővé teszi az olvasási/írási kulcsok visszakeresését. |
|[Cosmos DB-fiókolvasó](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Az Azure Cosmos DB-fiók adatai olvasása. Lehetővé teszi az olvasási kulcsok visszakeresését. |

> [!IMPORTANT]
> Az Azure Cosmos DB szerepköralapú hozzáférés-vezérlésének támogatása csak a vezérlősík-műveletekre vonatkozik. Az adatsík-műveletek főkulcsokkal vagy erőforrás-jogkivonatokkal vannak biztosítva. További információ: Biztonságos hozzáférés az [adatokhoz](secure-access-to-data.md) cikkben.

> [!TIP] 
> Szerepkörök hozzárendelésekénél csak a szükséges hozzáférést rendelje hozzá. Ha a szolgáltatás csak olvasási adatokat igényel, majd rendelje hozzá a **Cosmos DB-fiókolvasó** szerepkört a felügyelt identitáshoz. A legkisebb jogosultsági hozzáférés fontosságáról a [Kiemelt jogosultságú fiókok alacsonyabb szintű felengedése](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) című cikkben olvashat bővebben.

Ebben a forgatókönyvben a függvényalkalmazás az akvárium hőmérsékletét olvassa, majd írja vissza az adatokat egy azure Cosmos DB-tárolóba. Mivel a függvényalkalmazásnak meg kell írnia az adatokat, hozzá kell rendelnie a **DocumentDB-fiók közreműködői** szerepkört. 

1. Jelentkezzen be az Azure Portalon, és nyissa meg az Azure Cosmos DB-fiókját. Nyissa meg a **Hozzáférés-vezérlés (IAM)** ablaktáblát, majd a **Szerepkör-hozzárendelések** lapot:

   ![A Hozzáférés vezérlőablaktábláját és a Szerepkör-hozzárendelések lapot ábrázoló képernyőkép.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Válassza a +**Szerepkör-hozzárendelés** **hozzáadása** > lehetőséget.

1. A **Szerepkör-hozzárendelés hozzáadása** panel jobbra nyílik:

   ![A Szerepkör-hozzárendelés hozzáadása ablaktábláról képernyőkép.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Szerepkör**: **DocumentDB-fiók közreműködőjének** kiválasztása
   * **Hozzáférés hozzárendelése:** A **Rendszer által rendelt felügyelt identitás kiválasztása** alszakaszban válassza a **Függvényalkalmazás lehetőséget.**
   * **Válassza ki**a : Az ablaktábla az előfizetésben található, **felügyelt rendszeridentitással**rendelkező összes függvényalkalmazással lesz feltöltve. Ebben az esetben válassza ki a **FishTankTemperatureService** függvényalkalmazást: 

      ![A példaokkal feltöltött Szerepkör-hozzárendelés hozzáadása ablaktábla képernyőképe.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Miután kiválasztotta a függvényalkalmazást, válassza a **Mentés**lehetőséget.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Az Azure Cosmos DB-kulcsainak programozott elérése

Most már rendelkezik egy függvényalkalmazás, amely rendelkezik egy rendszer által hozzárendelt felügyelt identitás a **DocumentDB-fiók közreműködői** szerepkör az Azure Cosmos DB engedélyeket. A következő függvényalkalmazás-kód leérkezik az Azure Cosmos DB-kulcsok, hozzon létre egy CosmosClient objektumot, az akvárium hőmérsékletét, majd mentse ezt az Azure Cosmos DB-be.

Ez a minta a [List Keys API-t](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) használja az Azure Cosmos DB-fiók kulcsainak eléréséhez.

> [!IMPORTANT] 
> Ha hozzá szeretné [rendelni a Cosmos DB-fiókolvasó](#grant-access-to-your-azure-cosmos-account) szerepkört, akkor a [Csak olvasási kulcsok listája API-t kell használnia.](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys) Ez csak az írásvédett billentyűket fogja felnagyítja.

A List Keys `DatabaseAccountListKeysResult` API visszaadja az objektumot. Ez a típus nincs definiálva a C# könyvtárakban. A következő kód ennek az osztálynak a megvalósítását mutatja:  

```csharp 
namespace Monitor 
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

A [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) könyvtár használatával lekell szereznie a rendszeráltal hozzárendelt felügyelt identitásjogkivonatot. A jogkivonat beszerzésének és a `Microsoft.Azure.Service.AppAuthentication` tárról további információkért tekintse meg a [szolgáltatás-szolgáltatás hitelesítési](../key-vault/general/service-to-service-authentication.md) cikket.


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
    public static class FishTankTemperatureService
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

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
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
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Most már készen áll [a függvényalkalmazás telepítésére.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>További lépések

* [Tanúsítványalapú hitelesítés az Azure Cosmos DB és az Azure Active Directory használatával](certificate-based-authentication.md)
* [Biztonságos Azure Cosmos DB-kulcsok az Azure Key Vault használatával](access-secrets-from-keyvault.md)
* [Az Azure Cosmos DB biztonsági alapkonfigurációja](security-baseline.md)
