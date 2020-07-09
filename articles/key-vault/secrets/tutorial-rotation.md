---
title: A hitelesítési hitelesítő adatokkal rendelkező erőforrások rotációs oktatóanyaga
description: Ebből az oktatóanyagból megtudhatja, hogyan automatizálható a titkos kód elforgatása olyan erőforrásokhoz, amelyek a hitelesítő adatok egy készletét használják.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801444"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>A titkos kód elforgatásának automatizálása a hitelesítő adatok egy készletét használó erőforrásokhoz

Az Azure-szolgáltatásokhoz való hitelesítés legjobb módja egy [felügyelt identitás](../general/managed-identity.md)használata, de vannak olyan helyzetek, amikor ez nem lehetséges. Ezekben az esetekben a rendszer hozzáférési kulcsokat vagy titkos kódokat használ. Rendszeresen forgatni kell a hozzáférési kulcsokat vagy a titkokat.

Ez az oktatóanyag bemutatja, hogyan automatizálható a titkok rendszeres elforgatása olyan adatbázisokhoz és szolgáltatásokhoz, amelyek a hitelesítő adatok egy készletét használják. Pontosabban, ez az oktatóanyag a Azure Key Vaultokban tárolt jelszavakat SQL Server Azure Event Grid értesítés által aktivált függvény használatával forgatja el:

![Rotációs megoldás diagramja](../media/rotate1.png)

1. Harminc nappal a titok lejárati dátuma előtt Key Vault közzéteszi a "közeljövőben" eseményt Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és HTTP POST használatával hívja meg az eseményre előfizetett Function app-végpontot.
1. A Function alkalmazás fogadja a titkos adatokat, létrehoz egy új véletlenszerű jelszót, és új verziót hoz létre a titkos kulcshoz a Key Vault új jelszavával.
1. A Function alkalmazás frissíti SQL Server az új jelszóval.

> [!NOTE]
> A 3. és a 4. lépések között lehet késés. Ebben az időszakban a Key Vault titkos kulcsa nem fog tudni hitelesíteni SQL Server. A lépések bármelyikének meghibásodása esetén a Event Grid két órán keresztül próbálkozik újra.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Key Vault és SQL Server példány létrehozása

Első lépésként hozzon létre egy Key vaultot és egy SQL Server példányt és adatbázist, és tárolja Key Vault a SQL Server rendszergazdai jelszavát.

Ez az oktatóanyag egy meglévő Azure Resource Manager sablont használ az összetevők létrehozásához. A kódot itt találja: [alapszintű titkos rotációs sablon minta](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Válassza ki az Azure-sablon központi telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az **erőforráscsoport**területen válassza az **új létrehozása**lehetőséget. Nevezze el a csoport **simplerotation**.
1. Válassza a **Beszerzés** lehetőséget.

    ![Erőforráscsoport létrehozása](../media/rotate2.png)

Most már rendelkezik egy Key vaulttal, egy SQL Server példánnyal és egy SQL-adatbázissal. A telepítőt az Azure CLI-ben ellenőrizheti a következő parancs futtatásával:

```azurecli
az resource list -o table
```

Az eredmény a következő kimenettel fog kinézni:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Ezután hozzon létre egy, a rendszer által felügyelt identitással rendelkező Function alkalmazást a többi szükséges összetevőn kívül.

A Function alkalmazáshoz a következő összetevők szükségesek:
- Egy Azure App Service terv
- Egy tárfiók
- Hozzáférési szabályzat Key Vault titkos kulcsokhoz való hozzáféréshez a Function app által felügyelt identitás használatával

1. Válassza ki az Azure-sablon központi telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Az **erőforráscsoport** listában válassza a **simplerotation**lehetőséget.
1. Válassza a **Beszerzés** lehetőséget.

   ![Vásárlás kiválasztása](../media/rotate3.png)

Az előző lépések elvégzése után egy Storage-fiókkal, egy kiszolgálófarmhoz és egy Function-alkalmazással fog rendelkezni. A telepítőt az Azure CLI-ben ellenőrizheti a következő parancs futtatásával:

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

A functions-alkalmazások létrehozásáról és a felügyelt identitásnak a Key Vault eléréséhez való hozzáféréséről további információért lásd: [Function App-alkalmazás létrehozása a Azure Portal](../../azure-functions/functions-create-function-app-portal.md) és a [Key Vault hitelesítés felügyelt identitással](../general/managed-identity.md).

### <a name="rotation-function"></a>Rotációs függvény
A függvény egy eseményt használ a titkos kód elforgatásának elindításához Key Vault és az SQL-adatbázis frissítésével.

#### <a name="function-trigger-event"></a>Függvény eseményindítójának eseménye

Ez a függvény beolvassa az esemény-adatokat, és futtatja a rotációs logikát:

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
Ez a rotációs módszer beolvassa az adatbázis adatait a titkos kulcsból, létrehozza a titkos kulcs új verzióját, és frissíti az adatbázist az új titokkal:

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
A teljes kód megtalálható a [githubon](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Függvény üzembe helyezése

1. Töltse le a Function app zip-fájlját a [githubról](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Töltse fel a simplerotationsample-fn.zip fájlt a Azure Cloud Shellba.

   ![Töltse fel a fájlt](../media/rotate4.png)
1. Ezzel az Azure CLI-paranccsal telepítheti a zip-fájlt a Function alkalmazásba:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

A függvény üzembe helyezését követően két függvénynek kell megjelennie a simplerotation-FN alatt:

![SimpleRotation és SimpleRotationHttpTest függvények](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Esemény-előfizetés hozzáadása a SecretNearExpiry eseményhez

A Function alkalmazás `eventgrid_extension` kulcsának másolása:

   ![Function app-beállítások kiválasztása](../media/rotate6.png)

   ![eventgrid_extension kulcs](../media/rotate7.png)

Az `eventgrid_extension` alábbi parancsban a másolt kulcs és az előfizetés-azonosító segítségével hozzon létre egy Event Grid-előfizetést az `SecretNearExpiry` eseményekhez:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>A titok hozzáadása Key Vault
Állítsa be a hozzáférési szabályzatot, és adja meg a felhasználók számára a *titkos kulcsok kezelésére* vonatkozó engedélyeket:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Hozzon létre egy új titkot az SQL Database-adatforrást és a felhasználói azonosítót tartalmazó címkékkel. Adja meg a holnaphoz beállított lejárati dátumot.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Egy rövid lejárati dátummal rendelkező titkos kód létrehozása azonnal közzétesz egy `SecretNearExpiry` eseményt, amely viszont aktiválja a függvényt a titkos kód elforgatására.

## <a name="test-and-verify"></a>Tesztelés és ellenőrzés
Néhány perc elteltével a `sqluser` titkos kulcsot automatikusan el kell forgatni.

A titkos kód elforgatásának ellenőrzéséhez nyissa meg a **Key Vault**  >  **Secrets**:

![Ugrás a titkokra](../media/rotate8.png)

Nyissa meg a titkos **sqluser** , és tekintse meg az eredeti és az elforgatott verziót:

![A titkos sqluser megnyitása](../media/rotate9.png)

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Az SQL-alapú hitelesítő adatok ellenőrzéséhez hozzon létre egy webalkalmazást. Ennek a webalkalmazásnak a titka a Key Vault, kinyeri az SQL Database adatait és a hitelesítő adatokat a titkos kulcsból, és teszteli a SQL Server kapcsolatát.

A webalkalmazáshoz a következő összetevők szükségesek:
- Rendszer által felügyelt identitással rendelkező webalkalmazás
- Hozzáférési szabályzat a titkok eléréséhez Key Vault webalkalmazás felügyelt identitásán keresztül

1. Válassza ki az Azure-sablon központi telepítési hivatkozását:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Válassza ki a **simplerotation** erőforráscsoportot.
1. Válassza a **Beszerzés** lehetőséget.

### <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése

A webalkalmazás forráskódját a [githubon](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp)találja.

A webalkalmazás üzembe helyezéséhez hajtsa végre a következő lépéseket:

1. Töltse le a Function app zip-fájlját a [githubról](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Töltse fel a simplerotationsample-app.zip fájlt a Azure Cloud Shellba.
1. Ezzel az Azure CLI-paranccsal telepítheti a zip-fájlt a Function alkalmazásba:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>A webalkalmazás megnyitása

Nyissa meg az üzembe helyezett alkalmazást, és válassza ki az URL-címet:
 
![Válassza ki az URL-címet](../media/rotate10.png)

Amikor az alkalmazás megnyílik a böngészőben, látni fogja a **generált titkos értéket** , és egy **adatbázishoz kapcsolódó** , *igaz*értéket.

## <a name="learn-more"></a>További információ

- Áttekintés: [Key Vault figyelése Azure Event Grid (előzetes verzió)](../general/event-grid-overview.md)
- Útmutató: [e-mailek fogadása a Key Vault titkos változásairól](../general/event-grid-logicapps.md)
- [Azure Key Vault Azure Event Gridi esemény sémája (előzetes verzió)](../../event-grid/event-schema-key-vault.md)
