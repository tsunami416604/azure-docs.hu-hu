---
title: Egyetlen felhasználó/jelszó elforgatásának oktatóanyaga
description: Ez az oktatóanyag az egyetlen felhasználó/jelszó rotációjának automatizálására használható.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239370"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Egy titkos kód elforgatásának automatizálása egyetlen felhasználói/jelszó-hitelesítéssel rendelkező erőforrások esetében

Habár az Azure-szolgáltatásokhoz való hitelesítés legjobb módja egy [felügyelt identitás](managed-identity.md)használata, bizonyos esetekben ez nem lehetséges. Ezekben az esetekben hozzáférési kulcsokat vagy titkos kódokat használ a rendszer. A hozzáférési kulcsokat vagy titkos kódokat időnként el kell forgatni.

Ez az oktatóanyag azt mutatja be, hogyan automatizálható az adatbázisok és szolgáltatások titkos kulcsainak rendszeres elforgatása egyetlen felhasználói/jelszó-hitelesítéssel. Pontosabban, ez a forgatókönyv a Key vaultban tárolt SQL Server-jelszavakat Event Grid értesítés által aktivált függvény használatával forgatja:

![Rotációs diagram](./media/rotate1.png)

1. A titkos kód lejárati dátumát megelőző harminc nappal Key Vault tegye közzé a "közel lejárati" eseményt Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és a http post használatával meghívja az eseményre előfizetett függvényalkalmazás végpontot.
1. A Function alkalmazás fogadja a titkos adatokat, létrehoz egy új véletlenszerű jelszót, és új verziót hoz létre a titok számára egy új jelszóval Key Vaultban.
1. A Function alkalmazás az SQLot új jelszóval frissíti.

> [!NOTE]
> Előfordulhat, hogy a 3. és 4. lépés közötti késés és a Key Vaulti titok nem érvényes az SQL-hitelesítésre. Ha hiba történt valamelyik lépésben, Event Grid újrapróbálkozik 2 órára.

## <a name="setup"></a>Beállítás

## <a name="create-a-key-vault-and-sql-server"></a>Key Vault és SQL Server létrehozása

Mielőtt elkezdené, létre kell hoznia egy Key Vault, létre kell hoznia egy SQL Server és egy adatbázist, és tárolnia kell a SQL Server rendszergazdai jelszavát Key Vault.

Ez az oktatóanyag egy előre létrehozott Azure Resource Manager sablont használ az összetevők létrehozásához. Itt megtalálhatja a teljes kódot: [alapszintű titkos rotációs sablon minta](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Kattintson az Azure-sablon központi telepítési hivatkozás elemre:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az "erőforráscsoport" területen válassza az "új létrehozása" lehetőséget, és adja meg a "simplerotation" nevet.
1. Válassza a vásárlás lehetőséget.

    ![Új erőforráscsoport létrehozása](./media/rotate2.png)

A lépések elvégzése után egy kulcstartót, egy SQL-kiszolgálót és egy SQL-adatbázist fog tartalmazni. Ezt egy Azure CLI-terminálon ellenőrizheti, ha futtatja a következőt:

```azurecli
az resource list -o table
```

Az eredmények a következőket fogják kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>függvényalkalmazás létrehozása

Hozzon létre egy függvényalkalmazás rendszer által felügyelt identitással, valamint a további szükséges összetevőket: 

A Function alkalmazáshoz az alábbi összetevők és konfigurációk szükségesek:
- App Service-csomag
- Tárfiók
- Hozzáférési szabályzat a Key Vault titkos kódok eléréséhez függvényalkalmazás felügyelt identitás használatával

1. Kattintson az Azure-sablon központi telepítési hivatkozás elemre:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az "erőforráscsoport" területen válassza a "simplerotation" lehetőséget.
1. Válassza a vásárlás lehetőséget.

   ![Vásárlás képernyő](./media/rotate3.png)

A fenti lépések elvégzése után egy Storage-fiók, egy kiszolgálófarm és egy függvényalkalmazás fog rendelkezni.  Ezt egy Azure CLI-terminálon ellenőrizheti, ha futtatja a következőt:

```azurecli
az resource list -o table
```

Az eredmények a következőket fogják kinézni:

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

További információ a függvényalkalmazás létrehozásáról és a felügyelt identitásnak a Key Vault eléréséről: [Function-alkalmazás létrehozása a Azure Portal](../azure-functions/functions-create-function-app-portal.md) és [Key Vault hitelesítés felügyelete felügyelt identitás](managed-identity.md) használatával

### <a name="rotation-function-and-deployment"></a>Rotációs függvény és üzembe helyezés
A függvény az eseményt eseményindítóként használja, és elvégzi a titkos Key Vault és az SQL Database szolgáltatás frissítésének elforgatását.

#### <a name="function-event-trigger-handler"></a>Function esemény eseményindító-kezelője

Az alábbi függvény beolvassa az események adatát, és elforgatási logikát hajt

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
Ez a rotációs módszer beolvassa az adatbázis adatait a titkos kulcsból, létrehozza a titkos kulcs új verzióját, és új titkos kulccsal frissíti az adatbázist.

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
A teljes kódot itt találja: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Függvény üzembe helyezése

1. A Function app zip-fájl letöltése: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Töltse fel a simplerotationsample-FN. zip fájlt a Azure Cloud Shellba.
 
1. A következő CLI-parancs használatával telepítheti a zip-fájlt a Function alkalmazásba:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Vásárlás képernyő](./media/rotate4.png)

Az üzembe helyezést követően két függvényt láthat a simplerotation-FN alatt:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Esemény-előfizetés hozzáadása a "SecretNearExpiry" eseményhez

Másolja a Function app eventgrid_extension kulcsot.

![Azure Cloud Shell](./media/rotate6.png)

![Tesztelés és ellenőrzés](./media/rotate7.png)

Az alábbi parancsban a másolt eventgrid-kiterjesztési kulcsot és az előfizetés-azonosítót használva hozzon létre egy Event Grid-előfizetést a SecretNearExpiry eseményekhez.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Titok hozzáadása a Key Vaulthoz
Állítsa be a hozzáférési szabályzatot, hogy a "titkok kezelése" engedélyt adjon a felhasználóknak.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Most hozzon létre egy új titkot az SQL Database-adatforrást és a felhasználói azonosítót tartalmazó címkékkel, a holnap lejárati dátumával.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Egy rövid lejárati dátummal rendelkező titkos kód létrehozása azonnal közzétesz egy SecretNearExpiry eseményt, amely viszont aktiválja a függvényt a titkos kód elforgatására.

### <a name="test-and-verify"></a>Tesztelés és ellenőrzés
Néhány perc elteltével automatikusan el kell forgatni a sqluser titkos kulcsát.

A titkos elforgatás ellenőrzésének ellenőrzéséhez lépjen a Key Vault > Secrets elemre.

![Tesztelés és ellenőrzés](./media/rotate8.png)

Nyissa meg a "sqluser" titkot, és tekintse meg az eredeti és az elforgatott verziót

![Tesztelés és ellenőrzés](./media/rotate9.png)

## <a name="create-web-app"></a>Webalkalmazás létrehozása

Az SQL-hitelesítő adatok ellenőrzéséhez hozzon létre egy webalkalmazást. Ez a webalkalmazás megkapja a titkos kulcsot a Key vaultból, kinyeri az SQL Database-adatokat és hitelesítő adatokat a titkos kulcsból, és teszteli a kapcsolódást az SQL-hez.

A webalkalmazáshoz a következő összetevők és konfigurációk szükségesek:
- Webalkalmazás rendszerfelügyelt identitással
- Hozzáférési szabályzat a Key Vault titkos kódok eléréséhez a webalkalmazás által felügyelt identitás használatával

1. Kattintson az Azure-sablon központi telepítési hivatkozás elemre:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Válassza ki a **simplerotation** erőforráscsoportot
1. Kattintson a Vásárlás elemre.

### <a name="deploy-web-app"></a>Webalkalmazás üzembe helyezése

A webalkalmazás forráskódja https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp a webalkalmazás telepítéséhez a következő műveleteket hajthatja végre:

1. Töltse le a Function app zip-fájlját https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Töltse fel `simplerotationsample-app.zip` fájlt a Azure Cloud Shellba.
1. Ezzel az Azure CLI-paranccsal telepítheti a zip-fájlt a Function alkalmazásba:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Webalkalmazás megnyitása

Nyissa meg az üzembe helyezett alkalmazást, és kattintson az "URL" elemre:
 
![Tesztelés és ellenőrzés](./media/rotate10.png)

A generált titkos értéket meg kell jeleníteni az igaz értékkel rendelkező adatbázissal.

## <a name="learn-more"></a>További információ:

- Áttekintés: [Key Vault figyelése Azure Event Grid (előzetes verzió)](event-grid-overview.md)
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)](../event-grid/event-schema-key-vault.md)
