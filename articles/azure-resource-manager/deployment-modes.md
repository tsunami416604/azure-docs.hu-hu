---
title: Az Azure Resource Manager üzembe helyezési mód |} A Microsoft Docs
description: Ismerteti, hogyan adhatja meg, hogy egy teljes vagy növekményes üzembe helyezési mód használatára az Azure Resource Managerrel.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2018
ms.author: tomfitz
ms.openlocfilehash: 00800cb233776878e1fa330ce72cb067b8c698f3
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634619"
---
# <a name="azure-resource-manager-deployment-modes"></a>Az Azure Resource Manager üzembe helyezési mód
Az erőforrások üzembe helyezésekor, megadhatja, hogy az üzemelő példány-e a növekményes frissítés vagy a frissítés befejezéséhez.  A legfontosabb különbség a két módról, hogyan kezeli a Resource Manager a meglévő erőforrások az erőforráscsoportban, amelyek nem szerepelnek a sablonban.
Az alapértelmezett mód növekményes.

## <a name="incremental-and-complete-deployments"></a>A növekményes és teljes körű központi telepítések
Erőforrások üzembe helyezésekor:

* Resource Manager a teljes módban **törli** erőforráscsoportban létezik, de nem a sablonban megadott erőforrások. 
* Erőforrás-kezelő a növekményes módban **hagyja változatlanul** erőforráscsoportban létezik, de nem a sablonban megadott erőforrások.

Két mód esetében az erőforrás-kezelő próbál a sablonban megadott összes erőforrás kiépítéséhez. Ha az erőforrás már létezik az erőforráscsoportban, és a beállítások nem változnak, a művelet eredményeként nem változik. Ha egy erőforrás beállításait módosítja, az erőforrás kiosztása ezekkel a beállításokkal. Ha megpróbálja frissíteni a hely vagy egy meglévő erőforrás típusa, a központi telepítés egy hibaüzenettel meghiúsul. Ehelyett üzembe helyezése egy új erőforrás helyét, vagy adjon meg, hogy nincs szüksége.

## <a name="example-result"></a>Példa eredménye

A növekményes és teljes körű módok közötti különbséget mutatja be, fontolja meg az alábbi forgatókönyvet.

**Meglévő erőforráscsoport** tartalmazza:

* Erőforrás
* B erőforrás
* Erőforrás C

**Sablon** határozza meg:

* Erőforrás
* B erőforrás
* Erőforrás D

Ha telepítve **növekményes** módot, az erőforráscsoport rendelkezik:

* Erőforrás
* B erőforrás
* Erőforrás C
* Erőforrás D

Ha telepítve **teljes** módban erőforrás C törlődik. Az erőforráscsoporthoz:

* Erőforrás
* B erőforrás
* Erőforrás D

## <a name="set-deployment-mode"></a>Üzembe helyezési mód beállítása

A PowerShell-lel üzembe helyezésekor az üzembe helyezési mód beállításához használja a `Mode` paraméter.

```powershell
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json 
```

Az Azure CLI-vel való telepítésekor, állítsa be az üzembe helyezési mód, használja a `mode` paraméter.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezési mód beállítása egy [társított vagy beágyazott sablon](resource-group-linked-templates.md), használja a `mode` tulajdonság.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>További lépések
* Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Erőforrások üzembe helyezése kapcsolatos további információkért lásd: [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).
* A műveletek esetében egy erőforrás-szolgáltató megtekintése: [Azure REST API](/rest/api/).

