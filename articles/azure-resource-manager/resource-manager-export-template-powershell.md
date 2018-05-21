---
title: Az Azure PowerShell Resource Manager sablon exportálása |} Microsoft Docs
description: Azure Resource Manager és az Azure PowerShell használatával sablonokat exportálhat egy erőforráscsoportot.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: c69bab9d2956568473dd6def86ecbd9bbb6577cf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>A PowerShell-lel Azure Resource Manager-sablonok exportálása

A Resource Manager lehetővé teszi az előfizetéshez tartozó meglévő erőforrások Resource Manager-sablonjainak exportálását. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését.

Fontos megjegyezni, hogy két különböző módon sablon exportálása:

* Exportálhatja a **tényleges, a központi telepítéshez használt sablon**. Ebben az esetben az exportált sablon pontosan úgy tartalmazza a különböző paramétereket és változókat, ahogy azok az eredeti sablonban szerepeltek. Ez a módszer akkor hasznos, ha egy sablon visszakeresését.
* A másik megoldás, hogy úgy exportálja a **létrejött sablont, hogy az az erőforráscsoport aktuális állapotát tükrözze**. Ebben az esetben az exportált sablon nem az üzembe helyezéshez használt sablonon alapul. Ehelyett az alkalmazás létrehozza a sablont, amely a "snapshot" vagy "Mentés" az erőforráscsoport. Az exportált sablon számos nem módosítható értéket tartalmaz, és valószínűleg kevesebb paraméter található benne, mint amennyit általában használni szokott. Ez a beállítás segítségével telepítse újra az erőforrásokat ugyanabban az erőforráscsoportban. Egy másik erőforráscsoport a sablon használatához, előfordulhat, hogy jelentősen módosítható.

Ez a cikk mindkét megközelítés jeleníti meg.

## <a name="deploy-a-solution"></a>A megoldás üzembe helyezéséhez

A sablon exportálása mindkét megközelítés mutatja be, először egy megoldás telepítésére az előfizetéséhez. Ha már rendelkezik egy erőforráscsoportot az exportálni kívánt előfizetés, a megoldás üzembe helyezéséhez nincs. Ez a cikk többi azonban ez a megoldás sablonját hivatkozik. A példaként megadott parancsfájlt a storage-fiók telepíti.

```powershell
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Menti a sablont a központi telepítés előzményei

Beolvasható egy sablont az üzembe helyezési előzményeket használva a [mentés-AzureRmResourceGroupDeploymentTemplate](/powershell/module/azurerm.resources/save-azurermresourcegroupdeploymenttemplate) parancsot. Az alábbi példa menti a sablont, amely korábban központilag:

```powershell
Save-AzureRmResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

A sablon elérési útját adja vissza.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Nyissa meg a fájlt, és figyelje meg, hogy a rendszer a központi telepítéshez használt pontos sablont. A paraméterek és változók felel meg a sablont a Githubból. Ez a sablon központilag telepítheti.

## <a name="export-resource-group-as-template"></a>Erőforráscsoportok exportálása sablonként

Helyett egy sablon fogadása az üzembe helyezési előzményeket, a sablont, amely az erőforráscsoport aktuális állapotát jeleníti meg használatával kérheti le a [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) parancsot. Ez a parancs használni, amikor sok módosításokat végzett az erőforráscsoporton, és nincs meglévő sablon jelenti. a módosításokat. Az erőforráscsoport, amelyet felhasználhat az ugyanabban az erőforráscsoportban újratelepíteni pillanatképként szolgál. Az exportált sablon használata az egyéb megoldások, jelentősen módosítania kell azt.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleGroup
```

A sablon elérési útját adja vissza.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Nyissa meg a fájlt, és figyelje meg, hogy nem egyezik a GitHub-sablon. Különböző paraméterek és változók nem rendelkezik. A tárolási SKU és hely érték nem módosítható értékeket is. A következő példa bemutatja az exportált sablon, de a sablon némileg eltérő névvel rendelkezik:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Ez a sablon központilag telepítheti, de egy egyedi nevet a tárfiók találgatás igényel. A paraméter neve kis mértékben eltér.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
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

## <a name="next-steps"></a>További lépések
* Sablon exportálása a portál használatával kapcsolatos információkért lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).
* Sablon paraméterek megadásához tekintse meg a [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
