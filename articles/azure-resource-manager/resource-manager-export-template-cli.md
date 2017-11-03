---
title: "Azure parancssori felülettel Resource Manager sablon exportálása |} Microsoft Docs"
description: "Azure Resource Manager és az Azure parancssori felület használatával sablonokat exportálhat egy erőforráscsoportot."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2017
ms.author: tomfitz
ms.openlocfilehash: 617664129a5353e25da1e90c742c4b009db172ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Az Azure CLI Azure Resource Manager-sablonok exportálása

A Resource Manager lehetővé teszi az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjainak exportálását. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

Fontos megjegyezni, hogy sablonokat két különböző módon lehet exportálni:

* Exportálhatja az üzembe helyezéshez is használt tényleges sablont. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a módszer akkor hasznos, ha egy sablon visszakeresését.
* A másik megoldás, hogy úgy exportálja a sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. A rendszer ehelyett új sablont hoz létre az erőforráscsoport aktuális állapota alapján. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a módszer akkor hasznos, ha módosította az erőforráscsoportot. és most szeretne létrehozni egy sablont az így létrejött egyedi erőforráscsoport alapján.

Ebben a témakörben mind a két megoldást bemutatjuk.

## <a name="deploy-a-solution"></a>A megoldás üzembe helyezéséhez

A sablon exportálása mindkét megközelítés mutatja be, először egy megoldás telepítésére az előfizetéséhez. Ha már rendelkezik egy erőforráscsoportot az előfizetés az exportálni kívánt, nem rendelkeznek a megoldás üzembe helyezéséhez. Ez a cikk fennmaradó azonban ez a megoldás sablonját hivatkozik. A példaként megadott parancsfájlt a storage-fiók telepíti.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Menti a sablont a központi telepítés előzményei

Beolvasható egy sablont az üzembe helyezési előzményeket használva a [az telepítési exportálása](/cli/azure/group/deployment#export) parancsot. Az alábbi példa menti a sablont, amely korábban központilag:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

A sablon adja vissza. Másolja át a JSON, és menteni a fájlt. Figyelje meg, hogy a rendszer a központi telepítéshez használt pontos sablont. A paraméterek és változók felel meg a sablont a Githubból. Ez a sablon központilag telepítheti.


## <a name="export-resource-group-as-template"></a>Erőforráscsoportok exportálása sablonként

Helyett egy sablon fogadása az üzembe helyezési előzményeket, a sablont, amely az erőforráscsoport aktuális állapotát jeleníti meg használatával kérheti le a [az exportálása](/cli/azure/group#export) parancsot. Ez a parancs használni, amikor sok módosításokat végzett az erőforráscsoporton, és nincs meglévő sablon jelenti. a módosításokat.

```azurecli
az group export --name ExampleGroup
```

A sablon adja vissza. Másolja át a JSON, és menteni a fájlt. Figyelje meg, hogy nem egyezik a GitHub-sablon. Különböző paraméterek és változók nem rendelkezik. A tárolási SKU és hely érték nem módosítható értékeket is. A következő példa bemutatja az exportált sablon, de a sablon némileg eltérő névvel rendelkezik:

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

Ez a sablon központilag telepítheti, de egy egyedi nevet a tárfiók találgatás igényel. A paraméter neve kis mértékben eltér.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Exportált sablon személyre szabása

Ez a sablon használatával egyszerűbbé és rugalmasabbá módosíthatja. Engedélyezi a további helyeket, módosítsa a helyet jelölő tulajdonsághoz használandó ugyanazon a helyen az erőforráscsoport:

```json
"location": "[resourceGroup().location]",
```

Ne kelljen kitalálni a tárfiók uniques nevét, távolítsa el a paramétert a tárfiók nevét. Tárolási névutótag, és egy tárolási SKU paraméter hozzáadása:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Adja hozzá egy változó, amely a tárfiók nevét a uniqueString függvénnyel hoz létre:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

A változó értéke a tárfiók nevét:

```json
"name": "[variables('storageAccountName')]",
```

A paraméter értéke a Termékváltozat:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

A sablon most a következőképpen néz ki:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

A módosított sablon újbóli.

## <a name="next-steps"></a>Következő lépések
* Sablon exportálása a portál használatával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).
* Sablon paraméterek megadásához tekintse meg a [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).