---
title: Egyfelhasználós/egyjelszós elforgatási oktatóanyag
description: Az oktatóanyagból megtudhatja, hogyan automatizálhatja az egyfelhasználós/egyjelszós hitelesítést használó erőforrások titkos kulcsának elforgatását.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 70eb2449c5c54750831c30ff7d5c948173a38594
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423304"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Az egyfelhasználós/egypasswordos hitelesítést használó erőforrások titkos kulcsának elforgatásának automatizálása

Az Azure-szolgáltatások hitelesítésének legjobb módja egy [felügyelt identitás](../general/managed-identity.md)használata, de vannak olyan esetek, amikor ez nem lehetséges. Ezekben az esetekben a hozzáférési kulcsok vagy titkos kulcsok használata. Rendszeresen forgassa el a hozzáférési kulcsokat vagy titkos kulcsokat.

Ez az oktatóanyag bemutatja, hogyan automatizálhatja az egyfelhasználós/egyjelszós hitelesítést használó adatbázisok és szolgáltatások titkos kulcsainak időszakos rotációját. Ez az oktatóanyag konkrétan az Azure Key Vaultban tárolt SQL Server-jelszavakat forgatja el az Azure Event Grid értesítésáltal aktivált függvény használatával:

![Rotációs megoldás diagramja](../media/rotate1.png)

1. Harminc nappal egy titkos kulcs lejárati dátuma előtt a Key Vault közzéteszi a "majdnem lejárati" eseményt az Event Gridben.
1. Event Grid ellenőrzi az esemény-előfizetések, és http post használatával hívja meg a függvény alkalmazás végpont előfizetett az eseményre.
1. A függvényalkalmazás megkapja a titkos információkat, új véletlenszerű jelszót hoz létre, és létrehoz egy új verziót a titkos kulcshoz a Key Vault új jelszavával.
1. A függvényalkalmazás frissíti az SQL Server t az új jelszóval.

> [!NOTE]
> A 3. Ez idő alatt a key vault titkos kulcsa nem lesz képes hitelesíteni az SQL Server. A lépések bármelyikének meghibásodása esetén az Event Grid két órán keresztül újrapróbálkozik.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Kulcstartó és SQL Server-példány létrehozása

Az első lépés egy kulcstartó és egy SQL Server-példány és adatbázis létrehozása, valamint az SQL Server rendszergazdai jelszavának a Key Vaultban való tárolása.

Ez az oktatóanyag egy meglévő Azure Resource Manager-sablont használ az összetevők létrehozásához. A kódot itt találja: [Basic Secret Rotation Template Sample](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Válassza ki az Azure-sablon telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az **Erőforráscsoport**csoportban válassza **az Új létrehozása lehetőséget.** Nevezze el a csoport **simplerotation**.
1. Válassza a **Beszerzés** lehetőséget.

    ![Erőforráscsoport létrehozása](../media/rotate2.png)

Most antól lesz egy key vault, egy SQL Server-példány és egy SQL-adatbázis. Ezt a beállítást az Azure CLI-ben a következő parancs futtatásával ellenőrizheti:

```azurecli
az resource list -o table
```

Az eredmény fog kinézni valami a következő kimenet:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Ezután hozzon létre egy függvényalkalmazást egy rendszer által felügyelt identitással, a többi szükséges összetevő mellett.

A függvényalkalmazás nak szüksége van a következő összetevőkre:
- Egy Azure App Service-csomag
- Egy tárfiók
- Hozzáférési szabályzat a Kulcstároló titkos kulcsainak eléréséhez a függvényalkalmazás által felügyelt identitáson keresztül

1. Válassza ki az Azure-sablon telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az **Erőforráscsoport** listában válassza a **simplerotation**lehetőséget.
1. Válassza a **Beszerzés** lehetőséget.

   ![Beszerzési lehetőséget válassza ki](../media/rotate3.png)

Az előző lépések elvégzése után egy tárfiókkal, egy kiszolgálófarmpal és egy függvényalkalmazással fog rendelkezni. Ezt a beállítást az Azure CLI-ben a következő parancs futtatásával ellenőrizheti:

```azurecli
az resource list -o table
```

Az eredmény a következő kimenethez hasonlóan fog kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

A függvényalkalmazás létrehozásáról és a felügyelt identitás használatáról a Key Vault eléréséhez című témakörben [talál: Függvényalkalmazás létrehozása az Azure Portalról,](../../azure-functions/functions-create-function-app-portal.md) és [key vault-hitelesítés biztosítása felügyelt identitással.](../general/managed-identity.md)

### <a name="rotation-function"></a>Elforgatás függvény
A függvény egy eseményt használ egy titkos kulcs elforgatásának elindításához a Key Vault és az SQL-adatbázis frissítésével.

#### <a name="function-trigger-event"></a>Függvény eseményindító eseménye

Ez a függvény beolvassa az eseményadatokat, és futtatja a rotációs logikát:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Titkos rotációs logika
Ez az elforgatási módszer beolvassa az adatbázis adatait a titkos adatból, létrehozza a titkos adatikat egy új verzióját, és frissíti az adatbázist az új titkos adatikkal:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
A teljes kódot a [GitHubon](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function)találja.

#### <a name="function-deployment"></a>Függvény központi telepítése

1. Töltse le a függvényalkalmazás zip fájlját a [GitHubról.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip)

1. Töltse fel a simplerotationsample-fn.zip fájlt az Azure Cloud Shellbe.

   ![A fájl feltöltése](../media/rotate4.png)
1. Ezzel az Azure CLI paranccsal telepítheti a zip-fájlt a függvényalkalmazásba:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

A függvény üzembe helyezése után két függvénynek kell lennie a simplerotation-fn alatt:

![Egyszerű elforgatás és egyszerű elforgatáshttpTeszt függvények](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Esemény-előfizetés hozzáadása a SecretNearExpiry eseményhez

Másolja a függvényalkalmazás `eventgrid_extension` kulcsát:

   ![A függvényalkalmazás beállításainak kiválasztása](../media/rotate6.png)

   ![eventgrid_extension kulcs](../media/rotate7.png)

A következő `eventgrid_extension` parancsban a másolt kulcs és az előfizetés-azonosító `SecretNearExpiry` használatával hozzon létre eseményrács-előfizetést eseményekhez:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>A titkos kulcs hozzáadása a Key Vaulthoz
Állítsa be a hozzáférési szabályzatot úgy, hogy *a felhasználóknak titoktartási* engedélyeket adjon:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Hozzon létre egy új titkos kulcsot az SQL adatbázis adatforrását és a felhasználói azonosítót tartalmazó címkékkel. A holnapra beállított lejárati dátumot is mellékelje.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Egy titkos titok rövid lejárati dátummal történő létrehozása azonnal közzétesz egy `SecretNearExpiry` eseményt, amely viszont elindítja a funkciót a titok elforgatásához.

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés
Néhány perc múlva a `sqluser` titok automatikusan elfordul.

Annak ellenőrzéséhez, hogy a titkos kulcs elfordult-e, nyissa meg a **Key Vault** > **Secrets**:

![Ugrás a titkokra](../media/rotate8.png)

Nyissa meg az **sqluser** titkos kulcsot, és tekintse meg az eredeti és az elforgatott verziókat:

![Az sqluser titkos fájl megnyitása](../media/rotate9.png)

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Az SQL-hitelesítő adatok ellenőrzéséhez hozzon létre egy webalkalmazást. Ez a webalkalmazás lenyeri a titkos kulcsot a Key Vaultból, kinyeri az SQL-adatbázis adatait és hitelesítő adatait a titkos kulcsból, és teszteli az SQL Serverrel való kapcsolatot.

A webalkalmazásnak szüksége van ezekre az összetevőkre:
- Rendszer által felügyelt identitással rendelkező webalkalmazás
- Hozzáférési szabályzat a Kulcstároló ban a webalkalmazás által felügyelt identitáson keresztül idiktatitkok eléréséhez

1. Válassza ki az Azure-sablon telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Válassza ki a **simplerotation** erőforráscsoportot.
1. Válassza a **Beszerzés** lehetőséget.

### <a name="deploy-the-web-app"></a>A webalkalmazás telepítése

A webalkalmazás forráskódját a [GitHubon](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp)találja.

A webalkalmazás üzembe helyezéséhez hajtsa végre az alábbi lépéseket:

1. Töltse le a függvényalkalmazás zip fájlját a [GitHubról.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip)
1. Töltse fel a simplerotationsample-app.zip fájlt az Azure Cloud Shellbe.
1. Ezzel az Azure CLI paranccsal telepítheti a zip-fájlt a függvényalkalmazásba:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>A webalkalmazás megnyitása

Nyissa meg az üzembe helyezett alkalmazást, és válassza ki az URL-címet:
 
![Az URL-cím kiválasztása](../media/rotate10.png)

Meg kell jelennie a létrehozott titkos érték egy adatbázishoz csatlakoztatott értéke igaz.

## <a name="learn-more"></a>Részletek

- Áttekintés: [Key Vault figyelése az Azure Event Griddel (előzetes verzió)](../general/event-grid-overview.md)
- Útmutató: [E-mailek fogadása, ha egy kulcstartó titkos titkára megváltozik](../general/event-grid-logicapps.md)
- [Azure Event Grid eseménysémája az Azure Key Vaulthoz (előzetes verzió)](../../event-grid/event-schema-key-vault.md)