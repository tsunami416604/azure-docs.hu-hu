---
title: Az Azure CLI-vel Resource Manager-sablonok exportálása |} A Microsoft Docs
description: Azure Resource Manager és az Azure CLI használatával sablonokat exportálhat egy erőforráscsoportot.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: d4a1a687700badc550d37bf74f6a7e1680388897
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440315"
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Az Azure CLI Azure Resource Manager-sablonok exportálása

A Resource Manager lehetővé teszi az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjainak exportálását. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

Fontos megjegyezni, hogy nincsenek-e kétféleképpen exportálhat sablont:

* Exportálhatja a **üzembe helyezéséhez használt tényleges sablont**. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a megközelítés akkor hasznos, ha ki kell olvasnia a sablont.
* A másik megoldás, hogy úgy exportálja a **létrejött sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze**. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. Ehelyett egy sablont, amely egy "snapshot" vagy "Mentés" az erőforráscsoport hoz létre. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a beállítás használatával telepítse újra az erőforrásokat ugyanabban az erőforráscsoportban. A sablon használatához egy másik erőforrás-csoport, előfordulhat, jelentős mértékben módosítani azt.

Ez a cikk mindkét módszert bemutatja.

## <a name="deploy-a-solution"></a>Megoldás üzembe helyezése

Mindkét módszerénél a sablon exportálását mutatja be, először megoldás telepítése az előfizetéshez. Ha már rendelkezik erőforráscsoporttal az előfizetésében, amelyet szeretne exportálni, nem rendelkezik a megoldás üzembe helyezéséhez. Ez a cikk további részének azonban ennél a megoldásnál a sablon hivatkozik. A példaszkript üzembe helyez egy tárfiókot.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Sablon mentése üzembe helyezési előzményekből

Lehet lekérdezni egy sablont az üzembe helyezési előzmények használatával a [az üzembe helyezés exportálása](/cli/azure/group/deployment#az-group-deployment-export) parancsot. Az alábbi példa a korábban üzembe helyezett sablon menti:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

A sablon adja vissza. Másolja a JSON-, és mentse a fájlt. Figyelje meg, hogy-e az üzembe helyezéshez használt pontos sablont. A paraméterek és változók felel meg a sablont a Githubból. Ez a sablon újbóli telepítése is.


## <a name="export-resource-group-as-template"></a>Erőforráscsoport exportálása sablonként

Helyett az üzembe helyezés előzményeiből a sablonok lekérése, a sablont, amely az erőforráscsoport aktuális állapotát tükrözze használatával lekérheti a [az exportálása](/cli/azure/group#az-group-export) parancsot. Ez a parancs használnia, ha sok módosítást végzett az erőforráscsoportban, és nincs meglévő sablon jelöli az összes módosítást. A szándék szerint egy pillanatkép, az erőforráscsoport, amely segítségével ugyanabban az erőforráscsoportban üzembe helyezése. Használja az exportált sablon más megoldásokkal, jelentős mértékben módosítani kell azt.

```azurecli
az group export --name ExampleGroup
```

A sablon adja vissza. Másolja a JSON-, és mentse a fájlt. Figyelje meg, hogy nem egyezik a GitHub-sablon. A sablon rendelkezik a különböző paraméterek, és nincsenek változók. A tároló-Termékváltozat és a hely módosíthatóként értékekre. Az alábbi példa bemutatja az exportált sablon, de a sablon némileg eltérő paraméter neve van:

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

Ez a sablon újbóli telepítése is, de egy egyedi nevet a tárfiók találgatás igényel. A paraméter neve kismértékben eltér.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Exportált sablon személyre szabása

A sablon használatához egyszerűbbé és rugalmasabbá módosíthatja. Ahhoz, hogy további helyeket, ugyanazt a helyet használja, az erőforráscsoport helyét tulajdonság módosítása:

```json
"location": "[resourceGroup().location]",
```

A storage-fiók uniques nevet találjon ki kellene elkerüléséhez távolítsa el a paramétert, a storage-fiók neve. A tárolási utótag és a egy tároló-Termékváltozat paraméter hozzáadása:

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

Adjon hozzá egy változó, amely a tárfiók nevét a uniqueString függvénnyel hoz létre:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Állítsa be a tárfiók nevét a változó:

```json
"name": "[variables('storageAccountName')]",
```

A Termékváltozat a paraméter értéke:

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

A módosított sablon újbóli telepítése.

## <a name="next-steps"></a>További lépések
* Sablon exportálása a portál használatával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).
* A paraméterek meghatározása sablonban, lásd: [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Gyakori üzembehelyezési hibák elhárítása a tippek: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).