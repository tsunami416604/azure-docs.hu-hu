---
title: Azure-beli felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal
description: Ez az oktatóanyag azt ismerteti, hogyan hozható létre Azure-beli felügyelt alkalmazás egy egyéni Azure-szolgáltatóval.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: 3dd0887114156956b55f554d0265e3ca2b9b10ab
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335998"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Oktatóanyag: Felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal

Ebben az oktatóanyagban saját felügyelt alkalmazást hoz létre egyéni műveletekkel és erőforrásokkal. A felügyelt alkalmazás egy egyéni műveletet fog tartalmazni `Overview` az oldalon, egy egyéni erőforrástípust, amely külön menüelemként `Table of Content` jelenik meg az egyéni erőforrás lapon, valamint egy egyéni környezeti művelet.

Ez az oktatóanyag a következő lépéseket tartalmazza:

> [!div class="checklist"]
> * Felhasználói felület definíciós fájljának szerzője felügyelt alkalmazás példányának létrehozásához
> * Telepítési sablon készítése az Azure egyéni szolgáltatóval, az Azure Storage-fiókkal és az Azure-függvénnyel
> * Szerzői nézet definíciós összetevője egyéni műveletekkel és erőforrásokkal
> * Felügyelt alkalmazás definíciójának telepítése
> * Felügyelt alkalmazás példányának üzembe helyezése
> * Egyéni műveletek végrehajtása és egyéni erőforrások létrehozása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez ismernie kell a következőket:

* Felügyelt [alkalmazás definíciójának létrehozása és közzététele](publish-service-catalog-app.md).
* A [Service Catalog alkalmazás üzembe helyezése Azure Portal használatával](deploy-service-catalog-quickstart.md).
* [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz](create-uidefinition-overview.md).
* [](concepts-view-definition.md) Megtekintheti a definíciós összetevők képességeit.
* Az [Azure egyéni szolgáltatói](custom-providers-overview.md) képességei.

## <a name="user-interface-definition"></a>Felhasználói felület definíciója

Ebben az oktatóanyagban egy felügyelt alkalmazást hoz létre, és a felügyelt erőforráscsoport egyéni szolgáltatói példányt, Storage-fiókot és-függvényt fog tartalmazni. Az ebben a példában használt Azure-függvény olyan API-t valósít meg, amely kezeli az egyéni szolgáltatói műveleteket a műveletekhez és az erőforrásokhoz. Az Azure Storage-fiók alapszintű tárolóként használható az egyéni szolgáltatói erőforrások számára.

A felügyelt alkalmazás példányának létrehozásához szükséges felhasználói felület `funcname` definíciója tartalmazza a és `storagename` a bemeneti elemeket. A Storage-fiók nevének és a függvény nevének globálisan egyedinek kell lennie. Alapértelmezés szerint a függvény fájljai a [minta Function csomagból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)lesznek telepítve, de a *createUIDefinition. JSON*fájlban lévő csomaghoz tartozó beviteli elem hozzáadásával módosíthatja azt:

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

és kimenet a *createUIDefinition. JSON*fájlban:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

A teljes *createUIDefinition. JSON* minta a következő címen [érhető el: A felhasználói felület elemei összetevők](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Sablon egyéni szolgáltatóval

Az egyéni szolgáltatóval felügyelt alkalmazási példány létrehozásához meg kell határoznia az egyéni szolgáltatói erőforrást a **nyilvános** névvel, és be kell írnia a **Microsoft. CustomProviders/ResourceProviders** értéket a **mainTemplate. JSON**fájlban. Ebben az erőforrásban meg kell határoznia a szolgáltatáshoz tartozó erőforrás-típusokat és műveleteket. Az Azure Function és az Azure Storage-fiók példányainak üzembe helyezéséhez `Microsoft.Storage/storageAccounts` adja meg a Type és a típusú `Microsoft.Web/sites` erőforrásokat.

Ebben az oktatóanyagban létre fog hozni egy `users` erőforrás-típust, `ping` egy egyéni `users/contextAction` műveletet és egy egyéni műveletet, amelyet egy `users` egyéni erőforrás kontextusában fog végrehajtani. Minden erőforrástípus és művelet esetében adjon meg egy végpontot, amely a [createUIDefinition. JSON](#user-interface-definition)fájlban megadott nevű függvényre mutat. A **routingType** `Proxy,Cache` megadása az erőforrástípusok és `Proxy` a műveletek esetében:

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

A teljes *mainTemplate. JSON* minta a következő címen [érhető el: Telepítési sablon](reference-main-template-artifact.md)összetevő.

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

A felügyelt alkalmazásban egyéni műveleteket és egyéni erőforrásokat tartalmazó felhasználói felület definiálásához **viewDefinition. JSON** -összetevőt kell létrehoznia. A definíciós összetevők megtekintésével kapcsolatos további információkért lásd: definíciós összetevő [megtekintése Azure Managed Applicationsban](concepts-view-definition.md).

Ebben az oktatóanyagban az alábbiakat határozza meg:
* Az ** alapszintű szövegbevitelt használó egyéni műveletet `TestAction` jelképező eszköztár gombbal rendelkező áttekintő lap.
* Egy ** egyéni erőforrástípust `users`képviselő felhasználók lap.
* Egyéni erőforrás-művelet `users/contextAction` a *felhasználók* lapon, amelyet a rendszer a típusú `users`egyéni erőforrás kontextusában hajt végre.

Az alábbi példa egy "áttekintés" oldal konfigurációjának megtekintését szemlélteti:

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

Az alábbi példa a "felhasználók" erőforrások oldal konfigurációját tartalmazza egyéni erőforrás-művelettel:

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

A teljes *viewDefinition. JSON* minta a következő címen [érhető el: Definíciós](reference-view-definition-artifact.md)összetevő megtekintése

## <a name="managed-application-definition"></a>Felügyelt alkalmazás definíciója

Csomagolja a következő felügyelt alkalmazási összetevőket a zip Archive-be, majd töltse fel a Storage-ba:

* createUiDefinition.json
* mainTemplate. JSON
* viewDefinition. JSON

Minden fájlnak legfelső szintűnek kell lennie. Az összetevőkkel rendelkező csomag bármilyen tárolóban tárolható, például a GitHub blob vagy az Azure Storage-fiók blobja. Itt látható egy parancsfájl az alkalmazáscsomag Storage-fiókba való feltöltéséhez: 

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

Futtassa az alábbi Azure CLI-szkriptet, vagy kövesse a Azure Portal a Service Catalog felügyelt alkalmazás-definíciójának üzembe helyezése című témakör lépéseit:

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

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

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájában írja be és válassza a **felügyelt alkalmazások központ**elemet.
2. A **felügyelt alkalmazások központban**válassza a **Service Catalog alkalmazás definíciója** elemet, majd kattintson a **Hozzáadás**gombra. 
    
    ![Szolgáltatás-katalógus hozzáadása](./media/managed-application-with-custom-providers/service-catalog-managed-application.png)

3. Adja meg a Service Catalog-definíciók létrehozásához szükséges értékeket:

    * Adja meg a szolgáltatás-katalógus definíciójának egyedi **nevét** , a **megjelenítendő nevet** és a *leírást*(nem kötelező).
    * Válassza ki **** azt az előfizetést, **erőforráscsoportot**és **helyet** , ahová az alkalmazás-definíciót létre kívánja hozni. Használhatja ugyanazt az erőforráscsoportot, amelyet a zip-csomaghoz használ, vagy létrehozhat egy új erőforráscsoportot.
    * A **Package file URI**esetében adja meg az előző lépésben létrehozott zip-fájl elérési útját.

    ![Értékek megadása](./media/managed-application-with-custom-providers/add-service-catalog-managed-application.png)

4. A hitelesítési és a zárolási szint szakaszban válassza az **Engedélyezés hozzáadása**lehetőséget.

    ![Engedélyezés hozzáadása](./media/managed-application-with-custom-providers/add-authorization.png)

5. Válasszon ki egy Azure Active Directory csoportot az erőforrások kezeléséhez, majd kattintson **az OK gombra**.

   ![Engedélyezési csoport hozzáadása](./media/managed-application-with-custom-providers/add-auth-group.png)

6. Ha megadta az összes értéket, válassza a **Létrehozás**lehetőséget.

   ![Felügyelt alkalmazás definíciójának létrehozása](./media/managed-application-with-custom-providers/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Felügyelt alkalmazás példánya

A felügyelt alkalmazás definíciójának telepítésekor futtassa az alábbi parancsfájlt, vagy kövesse a Azure Portal a felügyelt alkalmazás-példány egyéni szolgáltatóval történő üzembe helyezéséhez szükséges lépéseket:

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

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

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. Az erőforrások listájában írja be és válassza a **felügyelt alkalmazások központ**elemet.
2. A **felügyelt alkalmazások központban**válassza a **Service Catalog-alkalmazások** elemet, majd kattintson a **Hozzáadás**gombra. 

    ![Felügyelt alkalmazás hozzáadása](./media/managed-application-with-custom-providers/add-managed-application.png)

3. A **Service Catalog-alkalmazások** lapon írja be a Service Catalog-definíció megjelenítendő neve kifejezést a keresőmezőbe. Válassza ki az előző lépésben létrehozott definíciót, és kattintson a **Létrehozás**gombra.

    ![Szolgáltatáskatalógus kiválasztása](./media/managed-application-with-custom-providers/select-service-catalog-definition.png)

4. Adja meg a felügyelt alkalmazás példányának a Service Catalog-definícióból való létrehozásához szükséges értékeket:

    * Válassza ki **** azt az előfizetést, **erőforráscsoportot**és **helyet** , ahová az alkalmazás-példányt létre kívánja hozni.
    * Adjon meg egy egyedi Azure-függvény nevét és az Azure Storage-fiók nevét.

    ![Alkalmazásbeállítások](./media/managed-application-with-custom-providers/application-settings.png)

5. Az érvényesítés után kattintson **az OK** gombra a felügyelt alkalmazás példányának telepítéséhez. 
    
    ![Felügyelt alkalmazás telepítése](./media/managed-application-with-custom-providers/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Egyéni műveletek és erőforrások

A Service Catalog alkalmazás példányának telepítése után két új erőforráscsoport van. Az első erőforráscsoport `applicationGroup` a felügyelt alkalmazás egy példányát tartalmazza, a második `managedResourceGroup` erőforráscsoport tárolja a felügyelt alkalmazás erőforrásait, beleértve az **egyéni szolgáltatót**is.

![Alkalmazás-erőforráscsoportok](./media/managed-application-with-custom-providers/application-resource-groups.png)

Megtekintheti a felügyelt alkalmazás példányát, és **egyéni műveleteket** hajthat végre az "áttekintés" lapon a **felhasználók** egyéni erőforrás létrehozása a "felhasználók" lapon, valamint egyéni **környezeti művelet** futtatása egyéni erőforráson.

* Nyissa meg az "áttekintés" lapot, és kattintson a "művelet pingelése" gombra:

![Egyéni művelet végrehajtása](./media/managed-application-with-custom-providers/perform-custom-action.png)

* Nyissa meg a "felhasználók" lapot, és kattintson a "Hozzáadás" gombra. Adja meg a bemeneteket az erőforrások létrehozásához és az űrlap elküldéséhez:

![Egyéni erőforrás létrehozása](./media/managed-application-with-custom-providers/create-custom-resource.png)

* Nyissa meg a "felhasználók" lapot, válassza ki a "felhasználók" erőforrást, és kattintson az "egyéni környezet művelet" lehetőségre:

![Egyéni erőforrás létrehozása](./media/managed-application-with-custom-providers/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van a Azure Managed Applicationsával kapcsolatban, próbálja meg megkérdezni a [stack overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke `azure-managedapps` hozzáadásával gyors választ kaphat!

## <a name="next-steps"></a>További lépések

Ha közzétenné felügyelt alkalmazását az Azure Marketplace-en, tekintse meg az [Azure-beli felügyelt alkalmazások a Marketplace piactéren](publish-marketplace-app.md) című témakört.
