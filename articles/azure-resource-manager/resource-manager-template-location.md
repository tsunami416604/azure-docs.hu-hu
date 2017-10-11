---
title: "A sablon az Azure erőforrás helye |} Microsoft Docs"
description: "Bemutatja, hogyan erőforrás helyének beállítása az Azure Resource Manager-sablonok"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Az Azure Resource Manager sablonokban erőforrás hely beállítása
Ha a sablonok telepítésével, meg kell adnia az egyes erőforrások helyét. Ez a témakör azt ismerteti, hogyan határozza meg a helyek, az előfizetés minden erőforrástípus számára elérhető.

## <a name="determine-supported-locations"></a>Határozza meg a támogatott helyek

Az egyes erőforrás támogatott helyek teljes listáját lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/). Azonban az előfizetés nem tudja elérni, hogy a listában a helyekhez. Az előfizetés számára elérhető helyek testreszabott listájának megjelenítéséhez használja az Azure PowerShell vagy Azure CLI. 

Az alábbi példában PowerShell a helyek az beszerzése a `Microsoft.Web\sites` erőforrás típusa:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Az alábbi példában Azure CLI 2.0 a helyek az beszerzése a `Microsoft.Web\sites` erőforrás típusa:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>Állítsa helyre a sablon

Után annak meghatározásához, hogy az erőforrások támogatott helyeket, be kell erre a helyre a sablonban. Állítsa ezt az értéket a legegyszerűbb módja, amely támogatja a erőforrástípusok helyen hozzon létre egy erőforráscsoportot, és mindegyik helyen `[resourceGroup().location]`. A sablon különböző helyeken való újbóli, és nem módosítható a sablonból vagy a paraméterek az értékeket. 

A következő példa bemutatja, amelyek az erőforráscsoportot és ugyanazon a helyen a rendszer:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Ha szeretné kódba foglalni a hely a sablonban, adja meg a támogatott régiók egyikéhez sem nevét. A következő példa bemutatja egy tárfiókot, északi középső Régiójában mindig központilag telepített:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>Következő lépések
* Sablonok létrehozásával kapcsolatos ajánlások, lásd: [ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](resource-manager-template-best-practices.md).

