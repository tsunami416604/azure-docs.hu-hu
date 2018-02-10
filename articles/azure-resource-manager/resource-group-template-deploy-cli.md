---
title: "Erőforrások az Azure CLI és a sablon telepítése |} Microsoft Docs"
description: "Azure Resource Manager és az Azure parancssori felület használatával egy erőforrások telepítése az Azure-bA. Az erőforrások egy Resource Manager-sablonban vannak meghatározva."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: 9c8b352194e3a624097a48b5d312356a0ead4276
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével

Ez a cikk ismerteti az erőforrások telepítése Azure Resource Manager-sablonok Azure CLI 2.0 használata. Ha nem ismeri a telepítésével kapcsolatos alapfogalmakat és kezelése az Azure megoldások, lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).  

A Resource Manager-sablon, azok a helyi fájl a számítógépre telepít, vagy egy külső egy például a GitHub-tárházban található fájl. Ez a cikk központi telepítését a sablon érhető el a [mintasablon](#sample-template) szakasz, vagy a regisztrációja, mivel egy [tárolási fiók sablon a Githubon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Ha nincs telepítve az Azure parancssori felület, használhatja a [felhő rendszerhéj](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Ha erőforrásokat üzembe helyezi az Azure-ba, hogy:

1. Jelentkezzen be az Azure-fiókjával
2. Hozzon létre egy erőforráscsoportot, amely a telepített erőforrások tárolójaként szolgál. Az erőforráscsoport neve csak tartalmazhatnak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet ponttal.
3. Telepítse az erőforráscsoport a sablon, amely meghatározza az erőforrás létrehozásához.

A sablon tartalmazhat, amelyek segítségével testre szabhatja a központi telepítési paramétereit. Biztosíthatja például is lefednek értékeket (például a fejlesztői, tesztelési és éles) egy adott környezetben. A minta sablon meghatározza a tárfiók SKU paraméter. 

Az alábbi példa létrehoz egy erőforráscsoport, és egy sablon, a helyi számítógépen telepíti:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. A Befejezés után megjelenik egy üzenet, amely tartalmazza az eredmény:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Külső sablon üzembe helyezése

Helyett Resource Manager-sablonok a helyi gépén, célszerű lehet külső helyen tárolja őket. A verziókövetési tárházat (például a Githubon) sablonok tárolhat. Vagy tárolhatja őket egy Azure storage-fiók megosztott eléréséhez a szervezetében.

Egy külső sablon történő üzembe helyezéséhez használjon a **sablon-uri** paraméter. A példában az URI segítségével telepítheti a minta-sablont a Githubból.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

Az előző példában a sablont, amely a legtöbb környezetben működik, mivel a sablon nem tartalmaznia kell a bizalmas adatok nyilvánosan elérhető URI igényel. Meg kell adnia a bizalmas adatok (például egy rendszergazdai jelszó), ha egy biztonságos paraméterben adja át ezt az értéket. Azonban ha nem szeretné, hogy a sablon a nyilvánosan hozzáférhető, megvédheti azokat a személyes tárolót tárolja őket. A sablont, amely közös hozzáférésű jogosultságkód (SAS) jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

A felhő rendszerhéj az alábbi parancsokat használja:

   ```azurecli-interactive
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageAccountType=Standard_GRS
   ```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Egynél több erőforráscsoportba vagy előfizetésbe történő központi telepítése

Általában központi telepítését az erőforrásokat a sablonhoz, amelyekkel egyetlen erőforráscsoportként működnek. Vannak azonban forgatókönyvek, ahol szeretne erőforráscsoport telepítsen együtt, de másik erőforráscsoport-sablonok és előfizetések helyezze el őket. Egy központi telepítésnél csak öt erőforráscsoportok telepítene. További információkért lásd: [telepítése Azure erőforrások több mint egy előfizetéshez vagy erőforráscsoporthoz](resource-manager-cross-resource-group-deployment.md).

## <a name="parameter-files"></a>A paraméter fájlok

Ahelyett, hogy a parancsfájl beágyazott értékeiként paraméterek átadása, előfordulhat, hogy ez egyszerűbbé teszi a paraméterek értékeit tartalmazó JSON-fájl használatára. A paraméterfájl a következő formátumúnak kell lennie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Figyelje meg, hogy a Paraméterek szakaszban tartalmazza-e a paraméter neve, amely megfelel a sablonban (storageAccountType) meghatározott paraméter. A paraméterfájl a paraméter értékét tartalmazza. Ezt az értéket automatikusan kerülnek a sablon üzembe helyezése során. Hozzon létre különböző telepítési forgatókönyvek esetén több paraméter fájlt, és akkor továbbítja a megfelelő paraméter fájlban. 

Másolja át az előző példában, és mentse a fájlt `storage.parameters.json`.

A helyi paraméterfájl továbbítani, használja `@` storage.parameters.json nevű helyi fájl megadását.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>A sablon üzemelő példány tesztelése

Minden olyan erőforrásnál tényleges telepítése nélkül a sablonnal és paraméterfájlokkal értékek teszteléséhez [az csoport központi telepítésének ellenőrzése](/cli/azure/group/deployment#az_group_deployment_validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Ha nincsenek hibák, a parancs a teszttelepítés információt ad vissza. Különösen figyelje meg, hogy a **hiba** értéke null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Ha a rendszer hibát észlel, a parancs hibaüzenetet ad vissza. Például a tárfiók SKU, helytelen értéket átadni próbált a következő hibaüzenet:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs nem tudta elemezni a sablon jelző hiba adja vissza. Az üzenet azt jelzi, a sor számának megjelenítése és elhelyezése az elemzési hiba.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Teljes módot használja, használja a `mode` paraméter:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Minta sablon

Ebben a cikkben szereplő példák a következő sablon használható. Másolja ki és mentse azt egy storage.json nevű fájlba. Ez a sablon létrehozása ismertetése: [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>További lépések
* Ebben a cikkben szereplő példák erőforrások telepítése az alapértelmezett előfizetésében az erőforráscsoporthoz. Használjon másik előfizetést, lásd: [több Azure-előfizetések kezeléséhez](/cli/azure/manage-azure-subscriptions-azure-cli).
* Egy teljes parancsfájlt, amely telepít egy sablon, lásd: [Resource Manager sablon üzembe helyezési parancsfájl](resource-manager-samples-cli-deploy.md).
* Szeretné megtudni, hogyan adhat meg a paramétereket a sablonban, lásd: [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A sablont, amely a SAS-jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-cli-sas-token.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
