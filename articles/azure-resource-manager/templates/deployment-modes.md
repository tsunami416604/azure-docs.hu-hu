---
title: Üzembehelyezési módok
description: Ez a témakör azt ismerteti, hogy miként adható meg, hogy teljes vagy növekményes központi telepítési módot használjon-e az Azure Resource Manager rel.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460245"
---
# <a name="azure-resource-manager-deployment-modes"></a>Az Azure Resource Manager telepítési módjai

Az erőforrások üzembe helyezésekor megadhatja, hogy a központi telepítés növekményes vagy teljes frissítés. A két mód közötti különbség az, hogy az Erőforrás-kezelő hogyan kezeli az erőforráscsoportban lévő, a sablonban nem lévő erőforrásokat.

Mindkét mód esetében az Erőforrás-kezelő megpróbálja létrehozni a sablonban megadott összes erőforrást. Ha az erőforrás már létezik az erőforráscsoportban, és a beállításai változatlanok, az adott erőforráshoz nem történik művelet. Ha módosítja egy erőforrás tulajdonságértékeit, az erőforrás frissül ezekkel az új értékekkel. Ha egy meglévő erőforrás helyét vagy típusát próbálja frissíteni, a központi telepítés hiba miatt sikertelen lesz. Ehelyett telepítsen egy új erőforrást a szükséges hellyel vagy típussal.

Az alapértelmezett mód növekményes.

## <a name="complete-mode"></a>Teljes mód

Teljes módban az Erőforrás-kezelő **törli** az erőforráscsoportban lévő, de a sablonban meg nem adott erőforrásokat.

Ha a sablon olyan erőforrást tartalmaz, amely nincs telepítve, mert [a feltétel kiértékelése](conditional-resource-deployment.md) hamis, az eredmény attól függ, hogy melyik REST API-verziót használja a sablon üzembe helyezéséhez. Ha a 2019-05-10-nél korábbi verziót használja, az erőforrás **nem törlődik.** A 2019-05-10 vagy újabb, az erőforrás **törlődik**. Az Azure PowerShell és az Azure CLI legújabb verziói törlik az erőforrást.

Legyen óvatos a teljes mód [másolási hurkokkal](copy-resources.md). A másolási ciklus feloldása után a sablonban nem megadott erőforrások törlődnek.

Ha egy [sablonban egynél több erőforráscsoportra](cross-resource-group-deployment.md)telepíti a központi telepítésben megadott erőforrásokat, törölhetők. A másodlagos erőforráscsoportokban lévő erőforrások nem törlődnek.

Az erőforrástípusok teljes módú törlések kezelésében vannak különbségek. A szülőerőforrások automatikusan törlődnek, ha nem teljes módban üzembe helyezett sablonban vannak telepítve. Egyes gyermekerőforrások nem törlődnek automatikusan, ha nem a sablonban vannak. Ezek a gyermekerőforrások azonban törlődnek, ha a fölérendelt erőforrást törlik.

Ha például az erőforráscsoport dns-zónát (Microsoft.Network/dnsZones erőforrástípus) és CNAME rekordot (Microsoft.Network/dnsZones/CNAME erőforrástípus) tartalmaz, a DNS-zóna a CNAME rekord szülőerőforrása. Ha teljes móddal telepíti a központi telepítést, és nem tartalmazza a DNS-zónát a sablonban, a DNS-zóna és a CNAME rekord is törlődik. Ha a DNS-zónát a sablonba is beilleszti, de nem tartalmazza a CNAME rekordot, a CNAME nem törlődik.

Az erőforrástípusok törlési kezelésének listáját az [Azure-erőforrások törlése a teljes módú telepítések esetén](complete-mode-deletion.md)olvassa el.

Ha az erőforráscsoport [zárolva](../management/lock-resources.md)van, a teljes mód nem törli az erőforrásokat.

> [!NOTE]
> Csak a gyökérszintű sablonok támogatják a teljes telepítési módot. [Csatolt vagy beágyazott sablonok](linked-templates.md)esetén növekményes módot kell használnia.
>
> [Az előfizetési szintű telepítések](deploy-to-subscription.md) nem támogatják a teljes módot.
>
> Jelenleg a portál nem támogatja a teljes módot.
>

## <a name="incremental-mode"></a>Növekményes mód

Növekményes módban az Erőforrás-kezelő **változatlan ulta** az erőforráscsoportban található, de a sablonban meg nem adott erőforrásokat. A sablonban lévő erőforrások **hozzáadódnak** az erőforráscsoporthoz.

> [!NOTE]
> Meglévő erőforrás növekményes módban történő újratelepítésekor az összes tulajdonság újra alkalmazásra kerül. A **tulajdonságok nem lesznek fokozatosan hozzáadva.** Gyakori félreértés, hogy a sablonban nem megadott tulajdonságok változatlanok maradnak. Ha nem ad meg bizonyos tulajdonságokat, az Erőforrás-kezelő úgy értelmezi a központi telepítést, mint az értékek felülírása. A sablonban nem szereplő tulajdonságok visszaállnak az alapértelmezett értékekre. Adja meg az erőforrás összes nem alapértelmezett értékét, ne csak a frissítendőértékeket. A sablonban lévő erőforrás-definíció mindig az erőforrás végső állapotát tartalmazza. Nem képviselhet részleges frissítést egy meglévő erőforráshoz.

## <a name="example-result"></a>Példa eredménye

A növekményes és a teljes üzemmódok közötti különbség szemléltetéséhez vegye figyelembe a következő forgatókönyvet.

**Az erőforráscsoport a következőket** tartalmazza:

* A erőforrás
* B erőforrás
* C erőforrás

A sablon a **következőket** tartalmazza:

* A erőforrás
* B erőforrás
* Erőforrás D

**Növekményes** módban telepítve az erőforráscsoport a következőket:

* A erőforrás
* B erőforrás
* C erőforrás
* Erőforrás D

**Ha teljes** módban van telepítve, a C erőforrás törlődik. Az erőforráscsoport:

* A erőforrás
* B erőforrás
* Erőforrás D

## <a name="set-deployment-mode"></a>Telepítési mód beállítása

A központi telepítési mód beállítása a PowerShell `Mode` használatával történő üzembe helyezéskor használja a paramétert.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

A telepítési mód beállítása az Azure CLI `mode` üzembe helyezésekor használja a paramétert.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A következő példa egy növekményes telepítési módba beállított csatolt sablont mutat be:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>További lépések

* Az Erőforrás-kezelő sablonok létrehozásáról az [Azure Resource Manager-sablonok létrehozása című témakörben olvashat.](template-syntax.md)
* Az erőforrások üzembe helyezéséről az [Alkalmazás üzembe helyezése az Azure Resource Manager sablonnal témakörben](deploy-powershell.md)olvashat.
* Az erőforrás-szolgáltató műveleteinek megtekintéséhez tekintse meg az [Azure REST API-t.](/rest/api/)
