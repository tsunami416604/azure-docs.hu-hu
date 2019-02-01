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
ms.date: 01/24/2019
ms.author: tomfitz
ms.openlocfilehash: c0875861a90080c649596880804fe6538c04bc51
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497612"
---
# <a name="azure-resource-manager-deployment-modes"></a>Az Azure Resource Manager üzembe helyezési mód

Az erőforrások üzembe helyezésekor, megadhatja, hogy az üzemelő példány-e a növekményes frissítés vagy a frissítés befejezéséhez.  A legfontosabb különbség a két módról, hogyan kezeli a Resource Manager a meglévő erőforrások az erőforráscsoportban, amelyek nem szerepelnek a sablonban. Az alapértelmezett mód növekményes.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="incremental-and-complete-deployments"></a>A növekményes és teljes körű központi telepítések

Mindkét mód esetében az erőforrás-kezelő megpróbálja létrehozni a sablonban megadott összes erőforrást. Ha az erőforrás már létezik az erőforráscsoportban, és a beállítások nem változnak, nincs művelet az adott erőforrás használatban van. Ha módosítja egy erőforráshoz tartozó tulajdonságértékek, ezeket az új értékeket az erőforrás frissül. Próbálja meg frissíteni a hely vagy egy meglévő erőforrás típusa, ha a központi telepítés egy hibaüzenettel meghiúsul. Ehelyett üzembe helyezése egy új erőforrás helyét, vagy adjon meg, hogy nincs szüksége.

Resource Manager a teljes módban **törli** erőforráscsoportban létezik, de nem a sablonban megadott erőforrások. Erőforrások, amelyek a sablonban megadott, de nincs telepítve, mert egy [feltétel](resource-manager-templates-resources.md#condition) kifejezés hamis, a rendszer nem törli.

Erőforrás-kezelő a növekményes módban **hagyja változatlanul** erőforráscsoportban létezik, de nem a sablonban megadott erőforrások. Ha újbóli üzembe helyezés növekményes módban egy erőforrást, adja meg az erőforrást, nem csak a kapcsolatok, amikor frissít minden tulajdonság értékét. Bizonyos tulajdonságokat nem adja meg, ha a Resource Manager értelmezi a frissítést, ezek az értékek felülírják.

## <a name="example-result"></a>Példa eredménye

A növekményes és teljes körű módok közötti különbséget mutatja be, fontolja meg az alábbi forgatókönyvet.

**Erőforráscsoport** tartalmazza:

* Erőforrás
* B erőforrás
* Erőforrás C

**Sablon** tartalmazza:

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

```azurepowershell-interactive
New-AzResourceGroupDeployment `
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

Használata esetén egy [társított vagy beágyazott sablon](resource-group-linked-templates.md), be kell állítani a `mode` tulajdonságot `Incremental`. Csak a legfelső szintű sablonok támogatja a teljes üzembe helyezési módot.

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
