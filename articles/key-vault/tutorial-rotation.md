---
title: Egyfelhasználós/jelszóelforgatási oktatóanyag
description: Az oktatóanyag használata az egyfelhasználós/jelszó elforgatásának automatizálására
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239370"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Az erőforrások titkos kulcsának elforgatásának automatizálása egyfelhasználós/jelszó-hitelesítéssel

Bár a legjobb módja annak, hogy hitelesítse az Azure-szolgáltatások használatával [felügyelt identitás,](managed-identity.md)vannak olyan forgatókönyvek, ahol ez nem lehetséges. Ezekben az esetekben a hozzáférési kulcsok vagy titkos kulcsok vannak használatban. A hozzáférési kulcsokat vagy titkos kulcsokat rendszeres időközönként el kell forgatni.

Ez az oktatóanyag bemutatja, hogyan automatizálhatja az adatbázisok és szolgáltatások titkos kulcsainak időszakos rotációját egyfelhasználós/jelszó-hitelesítéssel. Ez a forgatókönyv pontosabban elforgatja a key vaultban tárolt SQL-kiszolgálói jelszavakat az Event Grid értesítés által aktivált függvény használatával:

![Elforgatási diagram](./media/rotate1.png)

1. Harminc nappal egy titkos kulcs lejárati dátuma előtt a Key Vault közzéteszi a "majdnem lejárati" eseményt az Event Gridben.
1. Event Grid ellenőrzi az esemény-előfizetések, és a http post használatával meghívja a Függvény alkalmazás végpont előfizetett erre az eseményre.
1. A függvény alkalmazás megkapja a titkos információkat, létrehoz egy új véletlenszerű jelszót, és létrehoz egy új verziót a titkos kulcs egy új jelszót a Key Vaultban.
1. Az alkalmazás új jelszóval frissíti az SQL-t.

> [!NOTE]
> Lehet, hogy a 3. Hiba esetén bármelyik lépésben Az Event Grid 2 órán keresztül újrapróbálkozik.

## <a name="setup"></a>Telepítés

## <a name="create-a-key-vault-and-sql-server"></a>Kulcstartó és SQL-kiszolgáló létrehozása

Mielőtt elkezdenénk, létre kell hoznunk egy Key Vaultot, létre kell hoznunk egy SQL Server t és egy adatbázist, és tárolnunk kell az SQL Server rendszergazdai jelszavát a Key Vaultban.

Ez az oktatóanyag egy előre létrehozott Azure Resource Manager-sablont használ az összetevők létrehozásához. A teljes kódot itt találja: [Basic Secret Rotation Template Sample](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Kattintson az Azure-sablon telepítési hivatkozására:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az "Erőforráscsoport" területen válassza az "Új létrehozása" lehetőséget, és adja meg az "simplerotation" nevet.
1. Válassza a "Vásárlás" lehetőséget.

    ![Új erőforráscsoport létrehozása](./media/rotate2.png)

A lépések végrehajtása után lesz egy key vault, egy SQL-kiszolgáló és egy SQL-adatbázis. Ezt egy Azure CLI-terminálon ellenőrizheti a következő futtatásával:

```azurecli
az resource list -o table
```

Az eredmények fog kinézni valami ez:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Függvényalkalmazás létrehozása

Hozzon létre egy függvényalkalmazást rendszeráltal felügyelt identitással, valamint további szükséges összetevőkkel: 

A Függvényalkalmazás az alábbi összetevőket és konfigurációt igényli:
- App Service-csomag
- Tárfiók
- A Kulcstár ban lévő titkos kulcsok elérésére vonatkozó szabályzat a Függvényalkalmazás felügyelt identitásának használatával

1. Kattintson az Azure-sablon telepítési hivatkozására:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az "Erőforráscsoport" (Erőforráscsoport) területen válassza az "egyszerű rotáció" lehetőséget.
1. Válassza a "Vásárlás" lehetőséget.

   ![Vásárlási képernyő](./media/rotate3.png)

A fenti lépések végrehajtása után lesz egy tárfiók, egy kiszolgálófarm és egy függvényalkalmazás.  Ezt egy Azure CLI-terminálon ellenőrizheti a következő futtatásával:

```azurecli
az resource list -o table
```

Az eredmények fog kinézni valami ez:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

A Függvényalkalmazás létrehozásáról és a Key Vault eléréséhez a Felügyelt identitás használatával című [témaköra: Függvényalkalmazás létrehozása az Azure Portalról](../azure-functions/functions-create-function-app-portal.md) és [Key Vault-hitelesítés biztosítása felügyelt identitással](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Rotációs funkció és üzembe helyezés
A függvény eseményindítóként használja az eseményt, és egy titkos kulcstároló és SQL-adatbázis elforgatását hajtja végre.

#### <a name="function-event-trigger-handler"></a>Függvény eseményindító-kezelője

A Függvény alatt beolvassa az eseményadatokat, és elforgatási logikát hajt végre

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
Ez az elforgatási módszer beolvassa az adatbázis adatait a titkos adatközül, létrehozza a titkos adatikat, és frissíti az adatbázist egy új titkos adattal.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
A teljes kódot itt találja:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Függvény központi telepítése

1. Letöltés funkció app zip fájl:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Töltsön fel egyszerűenrotációsminta-fn.zip fájlt az Azure Cloud Shellbe.
 
1. A CLI parancs alatt a zip file függvényalkalmazásba való telepítéséhez használja:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Vásárlási képernyő](./media/rotate4.png)

A telepítés után két függvényt kell észlelni a simplerotation-fn alatt:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Esemény-előfizetés hozzáadása a "SecretNearExpiry" eseményhez

Másolja a függvényalkalmazást eventgrid_extension kulcsba.

![Azure Cloud Shell](./media/rotate6.png)

![Tesztelés és ellenőrzés](./media/rotate7.png)

A másolt eventgrid bővítménykulcs és az alábbi parancsban az előfizetés-azonosító használatával hozzon létre egy eseményrács-előfizetést a SecretNearExpiry eseményekhez.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz
Állítsa be a hozzáférési szabályzatot úgy, hogy "titokkezelési" engedélyt adjon a felhasználóknak.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Most hozzon létre egy új titkos kulcsot az SQL adatbázis adatforrását és felhasználói azonosítóját tartalmazó címkékkel, a holnapi lejárati dátummal.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Egy titkos titok rövid lejárati dátummal történő létrehozása azonnal közzéteszi a SecretNearExpiry eseményt, amely viszont elindítja a funkciót a titkos titok elforgatásához.

### <a name="test-and-verify"></a>Tesztelés és ellenőrzés
Néhány perc múlva az sqluser titkos fájl automatikusan elfordul.

A titkos rotáció ellenőrzésének ellenőrzéséhez nyissa meg a Key Vault > Secrets

![Tesztelés és ellenőrzés](./media/rotate8.png)

Nyissa meg az "sqluser" titkos kulcsot, és tekintse meg az eredeti és elforgatott verziót

![Tesztelés és ellenőrzés](./media/rotate9.png)

## <a name="create-web-app"></a>Webalkalmazás létrehozása

Az SQL-hitelesítő adatok ellenőrzéséhez hozzon létre egy webalkalmazást. Ez a webalkalmazás lenyeri a titkos kulcsot a key vaultból, kinyeri az SQL-adatbázis adatait és hitelesítő adatait a titkos kulcsból, és teszteli a kapcsolatot az sql-hez.

A webalkalmazás alábbi összetevőket és konfigurációt igényel:
- Webalkalmazás rendszeráltal felügyelt identitással
- A Kulcstár ban lévő titkos kulcsok elérésére vonatkozó házirend elérése a Web App felügyelt identitáshasználatával

1. Kattintson az Azure-sablon telepítési hivatkozására:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. A **simplerotation** erőforráscsoport kijelölése
1. Kattintson a Beszerzés gombra.

### <a name="deploy-web-app"></a>Webalkalmazás üzembe helyezése

A webalkalmazás forráskódja a https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp Webalkalmazás telepítéséhez a következő kbanasza:

1. Töltse le a funkcióalkalmazás zip fájljáthttps://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Töltse fel `simplerotationsample-app.zip` a fájlt az Azure Cloud Shellbe.
1. Ezzel az Azure CLI paranccsal telepítheti a zip-fájlt a függvényalkalmazásba:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Webes alkalmazás megnyitása

Nyissa meg az üzembe helyezett alkalmazást, és kattintson az "URL" gombra:
 
![Tesztelés és ellenőrzés](./media/rotate10.png)

A létrehozott titkos értéket úgy kell megjeleníteni, hogy az adatbázis-csatlakoztatva igaz legyen.

## <a name="learn-more"></a>További információ:

- Áttekintés: [Key Vault figyelése az Azure Event Griddel (előzetes verzió)](event-grid-overview.md)
- Útmutató: [E-mailek fogadása, ha egy kulcstartó titkos titkára megváltozik](event-grid-logicapps.md)
- [Azure Event Grid eseménysémája az Azure Key Vaulthoz (előzetes verzió)](../event-grid/event-schema-key-vault.md)
