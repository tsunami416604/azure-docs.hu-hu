---
title: Az Azure Resource Manager üzembe helyezési mód |} A Microsoft Docs
description: Ismerteti, hogyan adhatja meg, hogy egy teljes vagy növekményes üzembe helyezési mód használatára az Azure Resource Managerrel.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: tomfitz
ms.openlocfilehash: 8a53ed1eea66c976c46a21378a9c48a1ad5ce902
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508202"
---
# <a name="azure-resource-manager-deployment-modes"></a>Az Azure Resource Manager üzembe helyezési mód

Az erőforrások üzembe helyezésekor, megadhatja, hogy az üzemelő példány-e a növekményes frissítés vagy a frissítés befejezéséhez.  A legfontosabb különbség a két módról, hogyan kezeli a Resource Manager a meglévő erőforrások az erőforráscsoportban, amelyek nem szerepelnek a sablonban. Az alapértelmezett mód növekményes.

Mindkét mód esetében az erőforrás-kezelő megpróbálja létrehozni a sablonban megadott összes erőforrást. Ha az erőforrás már létezik az erőforráscsoportban, és a beállítások nem változnak, nincs művelet az adott erőforrás használatban van. Ha módosítja egy erőforráshoz tartozó tulajdonságértékek, ezeket az új értékeket az erőforrás frissül. Próbálja meg frissíteni a hely vagy egy meglévő erőforrás típusa, ha a központi telepítés egy hibaüzenettel meghiúsul. Ehelyett üzembe helyezése egy új erőforrás helyét, vagy adjon meg, hogy nincs szüksége.

## <a name="complete-mode"></a>Teljes mód

Resource Manager a teljes módban **törli** erőforráscsoportban létezik, de nem a sablonban megadott erőforrások. Erőforrások, amelyek a sablonban megadott, de nincs telepítve, mert egy [feltétel](resource-group-authoring-templates.md#condition) kifejezés hamis, a rendszer nem törli.

Legyen óvatos a teljes mód használatával [hurkok másolása](resource-group-create-multiple.md). Olyan erőforrások, a másolási ciklust megoldása után a sablonban megadott nem törlődnek.

Nincsenek eltérések hogyan kezelik az erőforrástípusok a teljes mód törlések. Szülő erőforrások telepítésekor nem a sablon, amely teljes körű módban automatikusan törlődnek. Néhány gyermekerőforrásait automatikusan nem, amely a sablon nem törlődnek. Ezek gyermekerőforrásait azonban akkor is törlődik, ha a szülő erőforrás törléséig. 

Például az erőforráscsoport tartalmazza a DNS-zóna (Microsoft.Network/dnsZones erőforrás típusa) és a egy CNAME-rekordot (Microsoft.Network/dnsZones/CNAME erőforrástípus), a DNS-zóna-e a CNAME rekord a szülő erőforrás. Ha üzembe helyezés a teljes módban, és a sablon ne tartalmazza a DNS-zóna, a DNS-zóna és a CNAME-rekordot is törlődik. Ha a DNS-zónát tartalmazza a sablonban szereplő, de nem tartalmazza a CNAME rekord, a CNAME nem törlődik. 

Hogyan erőforrástípusok kezeli a törlés listáját lásd: [törlése az Azure-erőforrások teljes üzemmód központi telepítésekhez](complete-mode-deletion.md).

Ha az erőforráscsoport [zárolva](resource-group-lock-resources.md), teljes körű mód nem törli az erőforrásokat.

> [!NOTE]
> Csak a legfelső szintű sablonok támogatja a teljes üzembe helyezési módot. A [sablonok beágyazott vagy kapcsolódó](resource-group-linked-templates.md), növekményes módot kell használnia. 
>
> [Előfizetés-szintű telepítések](deploy-to-subscription.md) teljes mód nem támogatott.
>
> A portálon jelenleg nem támogatja teljes mód.
>

## <a name="incremental-mode"></a>Növekményes mód

Erőforrás-kezelő a növekményes módban **hagyja változatlanul** erőforráscsoportban létezik, de nem a sablonban megadott erőforrások.

Viszont ha újbóli üzembe helyezés növekményes módban meglévő erőforrást, az eredménye, egy másik. Adja meg az összes tulajdonság az erőforrás, nem csak a kapcsolatok, amikor frissít. Egy gyakori félreértés, hogy úgy gondolja, hogy, amelyek nincsenek megadva tulajdonságok változatlanul. Bizonyos tulajdonságokat nem adja meg, ha a Resource Manager értelmezi a frissítést, ezek az értékek felülírják.

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

Az alábbi példa bemutatja egy hivatkozott sablonnak növekményes üzembe helyezési mód beállítása:

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
