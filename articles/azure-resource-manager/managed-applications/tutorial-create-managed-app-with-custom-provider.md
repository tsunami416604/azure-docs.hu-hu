---
title: Oktatóanyag – egyéni műveletek & erőforrások
description: Ez az oktatóanyag ismerteti, hogyan hozhat létre egy Azure felügyelt alkalmazást egy Azure egyéni szolgáltatóval.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650078"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Oktatóanyag: Felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal

Ebben az oktatóanyagban egyéni műveletekkel és erőforrásokkal hozza létre a saját felügyelt alkalmazást. A felügyelt alkalmazás tartalmaz egy `Overview` egyéni műveletet a lapon, egy egyéni `Table of Content` erőforrástípust, amely külön menüelemként jelenik meg, valamint egy egyéni környezeti műveletet az egyéni erőforráslapon.

Ez az oktatóanyag a következő lépéseket tartalmazza:

> [!div class="checklist"]
> * Felhasználói felületdefiníciós fájl létrehozása felügyelt alkalmazáspéldány létrehozásához
> * Telepítési sablon készítése [az Azure Custom Provider,](../custom-providers/overview.md)az Azure Storage-fiók és az Azure-funkció segítségével
> * Nézetdefiníció-összetevő készítése egyéni műveletekkel és erőforrásokkal
> * Felügyelt alkalmazásdefiníció telepítése
> * Felügyelt alkalmazás példányának telepítése
> * Egyéni műveletek végrehajtása és egyéni erőforrások létrehozása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez tudnia kell:

* Felügyelt [alkalmazásdefiníció létrehozása és közzététele](publish-service-catalog-app.md).
* A Service Catalog alkalmazás telepítése az [Azure Portalon keresztül.](deploy-service-catalog-quickstart.md)
* Az [Azure Portal felhasználói felületének létrehozása a felügyelt alkalmazáshoz.](create-uidefinition-overview.md)
* [Definíciós műtermék-képességek megtekintése.](concepts-view-definition.md)
* [Az Azure egyéni szolgáltató](../custom-providers/overview.md) képességei.

## <a name="user-interface-definition"></a>Felhasználói felület definíciója

Ebben az oktatóanyagban egy felügyelt alkalmazást hoz létre, és a felügyelt erőforráscsoport egyéni szolgáltatópéldányt, tárfiókot és függvényt tartalmaz. Ebben a példában használt Azure-függvény egy API-t valósít meg, amely kezeli a műveletek és erőforrások egyéni szolgáltatói műveleteket. Az Azure Storage-fiók az egyéni szolgáltatói erőforrások alapvető tárházaként használatos.

A felügyelt alkalmazáspéldány létrehozásához szükséges `funcname` `storagename` felhasználói felület-definíció tartalmazza a bemeneti elemeket. A tárfiók nevének és függvénynevének globálisan egyedinek kell lennie. Alapértelmezés szerint a függvényfájlok a [mintafüggvénycsomagból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)lesznek telepítve, de a *createUIDefinition.json*fájlban egy csomaghivatkozás bemeneti elemének hozzáadásával módosíthatók:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

és kimenet *createUIDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

A teljes *createUIDefinition.json* minta megtalálható [a Reference: Felhasználói felület elemeinek összetevői](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Sablon egyéni szolgáltatóval

Ha egyéni szolgáltatóval szeretne felügyelt alkalmazáspéldányt létrehozni, meg kell határoznia **a nyilvános** nevű egyéni szolgáltatói erőforrást, és be kell írnia a **Microsoft.CustomProviders/resourceProviders** parancsot a **mainTemplate.json mezőbe.** Ebben az erőforrásban megadhatja a szolgáltatás erőforrástípusait és műveleteket. Az Azure Function és az Azure Storage-fiók `Microsoft.Storage/storageAccounts` példányainak üzembe helyezéséhez definiáljon típusú `Microsoft.Web/sites` erőforrásokat.

Ebben az oktatóanyagban `users` egy erőforrástípust, `ping` `users/contextAction` egyéni műveletet és egyéni műveletet `users` hoz létre, amelyet egy egyéni erőforrás környezetében fog végrehajtani. Minden erőforrástípushoz és művelethez adjon meg egy végpontot, amely a [createUIDefinition.json](#user-interface-definition)mezőben megadott névvel rendelkező függvényre mutat. Adja meg az `Proxy,Cache` `Proxy` **útválasztási típust** erőforrástípusokhoz és műveletekhez:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

A teljes *mainTemplate.json* minta megtalálható [a Referencia: Telepítési sablon összetevő](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

Egyéni műveleteket és egyéni erőforrásokat a felügyelt alkalmazásban egyéni műveleteket és egyéni erőforrásokat tartalmazó felhasználói felület meghatározásához létre kell adnia **a viewDefinition.json** összetevőt. A view definition összetevőről további információt az [Azure Felügyelt alkalmazások definíciós összetevőjének megtekintése című témakörben talál.](concepts-view-definition.md)

Ebben az oktatóanyagban a következőket határozza meg:
* Eszköztárgombbal ellátott *áttekintő* lap, `TestAction` amely egyszerű szövegbevitellel rendelkező egyéni műveletet jelöl.
* Egyéni *Users* erőforrástípust jelölő Felhasználók `users`lap .
* Egyéni erőforrásművelet `users/contextAction` a *Felhasználók* lapon, amely et egyéni típusú `users`környezetben hajt végre.

A következő példa egy "Áttekintés" lap nézetkonfigurációját mutatja be:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Az alábbi példa a "Felhasználók" erőforrások lap konfigurációját tartalmazza egyéni erőforrás-művelettel:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

A teljes *viewDefinition.json* minta megtalálható [a Reference: View definition összetevő](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Felügyelt alkalmazásdefiníció

Csomagolja be a következő felügyelt alkalmazásösszetevőket a zip archívumba, és töltse fel a tárolóba:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Minden fájlnak gyökérszinten kell lennie. Az összetevőkkel rendelkező csomag tárolható bármely tárolóban, például a GitHub blobban vagy az Azure Storage-fiók blobjában. Itt van egy szkript feltölteni az alkalmazáscsomagot a tárfiókba: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Futtassa az alábbi Azure CLI-parancsfájlt, vagy kövesse az Azure Portal lépéseit a Service Catalog felügyelt alkalmazásdefiníciójának üzembe helyezéséhez:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájában írja be és válassza a **Felügyelt alkalmazások központ lehetőséget.**
2. A **Felügyelt alkalmazások központban**válassza a **Szolgáltatáskatalógus alkalmazásdefiníció lehetőséget,** és kattintson a **Hozzáadás**gombra. 
    
    ![Szolgáltatáskatalógus hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Adja meg a szolgáltatáskatalógus-definíció létrehozásának értékeit:

    * Adjon meg egyedi **nevet** a szolgáltatáskatalógus-definícióhoz, **a Megjelenítendő név** és a Leírás *nevet*(nem kötelező).
    * Válassza ki az **Előfizetés**, **Erőforrás csoport**és a **Hely lehetőséget,** ahol az alkalmazásdefiníció létrejön. Használhatja ugyanazt az erőforráscsoportot, amelyet a zip csomaghoz használ, vagy létrehozhat egy új erőforráscsoportot.
    * Az **Uri csomagfájl**esetén adja meg az előző lépésben létrehozott zip-fájl elérési útját.

    ![Értékek megadására](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Amikor a Hitelesítési és zárolási szint szakaszhoz, válassza az **Engedélyezés hozzáadása**lehetőséget.

    ![Engedélyezés hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Az erőforrások kezeléséhez válasszon ki egy Azure Active Directory-csoportot, és kattintson az **OK gombra.**

   ![Engedélyezési csoport hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Miután megadta az összes értéket, válassza a **Létrehozás gombot.**

   ![Felügyelt alkalmazásdefiníció létrehozása](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Felügyelt alkalmazáspéldány

A felügyelt alkalmazásdefiníció telepítésekor futtassa az alábbi parancsfájlt, vagy kövesse az Azure Portal on a felügyelt alkalmazáspéldány egyéni szolgáltatóval történő üzembe helyezéséhez szükséges lépéseket:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájában írja be és válassza a **Felügyelt alkalmazások központ lehetőséget.**
2. A **Felügyelt alkalmazások központban**válassza a **Szolgáltatáskatalógus-alkalmazások** lehetőséget, és kattintson a **Hozzáadás**gombra. 

    ![Felügyelt alkalmazás hozzáadása](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. A **Szolgáltatáskatalógus-alkalmazások** lapon a Service Catalog definition megjelenítendő neve szerepel a keresőmezőben. Jelölje ki az előző lépésben létrehozott definíciót, és kattintson a **Létrehozás gombra.**

    ![Szolgáltatáskatalógus kiválasztása](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Adjon meg értékeket felügyelt alkalmazáspéldány létrehozásához a Service Catalog-definícióból:

    * Válassza ki az **Előfizetés**, **Erőforrás csoport**és **a Hely,** ahol az alkalmazáspéldány létre jön.
    * Adjon meg egy egyedi Azure-függvénynevet és az Azure Storage-fiók nevét.

    ![Alkalmazásbeállítások](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Az érvényesítés sikeres elfogadásakor kattintson az **OK** gombra egy felügyelt alkalmazás példányának központi telepítéséhez. 
    
    ![Felügyelt alkalmazás telepítése](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Egyéni műveletek és erőforrások

A szolgáltatáskatalógus-alkalmazáspéldány üzembe helyezése után két új erőforráscsoporttal rendelkezik. Az első `applicationGroup` erőforráscsoport a felügyelt alkalmazás egy `managedResourceGroup` példányát tartalmazza, a második erőforráscsoport pedig a felügyelt alkalmazás erőforrásait tartalmazza, beleértve az **egyéni szolgáltatót**is.

![Alkalmazáserőforrás-csoportok](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

A felügyelt alkalmazáspéldányt megtekintheti, és **egyéni műveleteket** hajthat végre az "Áttekintés" lapon, egyéni erőforrást hozhat létre **a felhasználóknak** a "Felhasználók" lapon, és **egyéni környezetműveletet** futtathat egyéni erőforráson.

* Lépjen az "Áttekintés" oldalra, és kattintson a "Pingművelet" gombra:

![Egyéni művelet végrehajtása](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Lépjen a "Felhasználók" oldalra, és kattintson a "Hozzáadás" gombra. Adja meg az erőforrások létrehozásához és az űrlap elküldéséhez a bemeneteket:

![Egyéni erőforrás létrehozása](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Lépjen a "Felhasználók" lapra, jelöljön ki egy "felhasználók" erőforrást, és kattintson az "Egyéni környezeti művelet" gombra:

![Egyéni erőforrás létrehozása](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Segítségre van szüksége

Ha kérdése van az Azure felügyelt alkalmazásokkal kapcsolatban, próbálja meg feltenni a [Stack Overflow-t.](https://stackoverflow.com/questions/tagged/azure-managedapps) Lehet, hogy egy hasonló kérdést már feltettek és megválaszoltak, ezért először ellenőrizze a feladás előtt. Add hozzá `azure-managedapps` a címkét, hogy gyors választ kapj!

## <a name="next-steps"></a>További lépések

Ha közzétenné felügyelt alkalmazását az Azure Marketplace-en, tekintse meg az [Azure-beli felügyelt alkalmazások a Marketplace piactéren](publish-marketplace-app.md) című témakört.

További információ az [Azure egyéni szolgáltatókról.](../custom-providers/overview.md)
