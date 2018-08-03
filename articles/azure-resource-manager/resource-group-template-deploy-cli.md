---
title: Erőforrások üzembe helyezése az Azure CLI és a sablon |} A Microsoft Docs
description: Azure Resource Manager és az Azure CLI használatával helyezze üzembe az Azure-erőforrásokat. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: 510cba0c2e27ab56ea26a476258fd7480b80e0d2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420403"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével

Ez a cikk ismerteti az Azure CLI használata a Resource Manager-sablonok, az erőforrások üzembe helyezése az Azure. Ha Ön nem ismeri a fogalmait, üzembe helyezése és kezelése az Azure-megoldások, lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).  

A Resource Manager-sablon telepít egy helyi fájlt a gépén, vagy egy külső amilyen a GitHub-tárházban található fájl lehet. A sablon üzembe helyezi az ebben a cikkben érhető el a [mintasablon](#sample-template) szakaszban, vagy mint egy [tárolási fiók sablont a GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Ha nem rendelkezik Azure CLI telepítve van, használhatja a [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Erőforrások üzembe helyezésekor az Azure-ba, hogy:

1. Jelentkezzen be az Azure-fiókjába.
2. Hozzon létre egy erőforráscsoportot, amely a telepített erőforrások tárolójaként szolgál. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójelet tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet ponttal.
3. Az erőforráscsoport üzembe a sablont, amely meghatározza az erőforrások létrehozása

A sablon paramétereit, amelyek lehetővé teszik az üzembe helyezés testreszabása tartalmazhat. Például megadhat értékeket, amelyek egy adott környezetben (például fejlesztési, tesztelési és éles környezetben). A mintasablon a tárfiók SKU paraméter határozza meg. 

Az alábbi példa létrehoz egy erőforráscsoportot, és üzembe helyezi a sablont a helyi gépen:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Külső sablon üzembe helyezése

Resource Manager-sablonok tárolása a helyi gépén, helyett érdemesebb lehet külső helyen tárolja őket. Sablonok verziókövetési adattár (például a GitHub) tárolhatja. Vagy tárolhatja őket az Azure storage-fiók, a közös hozzáférésű a szervezetben.

Egy külső sablon üzembe helyezéséhez használja a **sablon-uri** paraméter. A példában az URI használatával helyezhet üzembe a mintául szolgáló sablont a Githubból.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

Az előző példában a sablon, amely a legtöbb esetben működik, mivel a sablon nem tartalmazhatja a bizalmas adatokat egy nyilvánosan elérhető-e URI-t igényel. Adja meg a bizalmas adatok (például egy rendszergazdai jelszót) van szüksége, ha biztonságos paraméterként adja át ezt az értéket. Azonban ha nem szeretné, hogy a sablon nyilvánosan hozzáférhető, segítségével megvédheti azokat a személyes tárolót tárolja őket. Egy közös hozzáférésű jogosultságkód (SAS) igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

A Cloud shellben használja az alábbi parancsokat:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Több erőforráscsoport vagy előfizetés üzembe helyezése

Általában végzi az üzembe helyezést összes erőforrást a sablonhoz, amelyekkel egyetlen erőforráscsoportra. Vannak azonban forgatókönyvek, ahol szeretné erőforráscsoport telepítsen együtt, de különböző erőforráscsoport vagy előfizetés helyezze el őket. Telepíthet egy központi telepítésben csak öt erőforráscsoportokhoz. További információkért lásd: [üzembe helyezése Azure-erőforrásokat az egynél több előfizetésen vagy erőforráscsoporton](resource-manager-cross-resource-group-deployment.md).

## <a name="parameter-files"></a>Alkalmazásparaméter-fájlok

Ahelyett, hogy a paraméterek átadása a parancsfájlban beágyazott értékekként, akkor előfordulhat, hogy egyszerűbb paraméter értékét tartalmazó JSON-fájl használata. A paraméterfájl a következő formátumban kell lennie:

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

Figyelje meg, hogy a paraméterek szakasz tartalmazza-e a paraméter neve, amely megegyezik a paraméter definiálva a sablonban (Tárfióktípus). A paraméterfájl a paraméter értékét tartalmazza. Ezt az értéket automatikusan kerülnek a sablon üzembe helyezése során. Hozzon létre több, különböző telepítési forgatókönyvek esetén alkalmazásparaméter-fájlokat, és akkor továbbítja a megfelelő paraméterfájlban. 

Másolja ki az előző példában, és mentse a fájlt `storage.parameters.json`.

Adja át a helyi alkalmazásparaméter-fájlt, használja a `@` storage.parameters.json nevű helyi fájl megadásához.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Egy sablon üzemelő példány tesztelése

A sablonnal és paraméterfájlokkal értékek teszteléséhez ténylegesen az olyan erőforrások telepítés nélkül használhatja a [az csoport központi telepítésének ellenőrzése](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Ha nincsenek hibák észlelhetők, a parancs visszaadja a teszt üzembe helyezéssel kapcsolatos adatokat. Különösen, vegye figyelembe, hogy a **hiba** null értékű.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Ha hibát észlel, a parancs hibaüzenetet ad vissza. Például próbál adja át a tárfiók SKU, helytelen értéket ad vissza a következő hiba:

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

Ha a sablon szintaktikai hibát tartalmaz, a parancs nem tudta elemezni a sablon jelző hibaüzenetet adja vissza. Az üzenet azt jelzi, hogy a sor száma és az elemzési hiba pozícióját.

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

Teljes módot használja, használja a `mode` paramétert:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Példasablon

Ebben a cikkben szereplő példák a következő sablon szolgál. Másolja, és mentse egy storage.json nevű fájlt. Ez a sablon létrehozása ismertetése: [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).  

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
* Ebben a cikkben szereplő példák erőforrások üzembe helyezése az erőforráscsoporthoz, az alapértelmezett előfizetést. Használjon másik előfizetést, lásd: [több Azure-előfizetések kezelése](/cli/azure/manage-azure-subscriptions-azure-cli).
* Teljes minta parancsfájl, amely üzembe helyezi a sablont, tekintse meg a [Resource Manager-sablon üzembe helyezési parancsfájl](resource-manager-samples-cli-deploy.md).
* A sablonban szereplő paraméterekkel definiálása ismertetése: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Gyakori üzembehelyezési hibák elhárítása a tippek: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* SAS-token igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-cli-sas-token.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
