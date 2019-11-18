---
title: Üzembehelyezési módok
description: Leírja, hogy miként lehet megállapítani, hogy a teljes vagy növekményes központi telepítési módot használja-e Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 44111787736d4b80cbdd68263b137d67de2218ba
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149875"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager üzembe helyezési módok

Az erőforrások telepítésekor meg kell adnia, hogy a központi telepítés Növekményes frissítés vagy teljes frissítés.  A két mód közötti különbség az, hogy az erőforrás-kezelő hogyan kezelje a sablonban nem szereplő erőforrás-csoport meglévő erőforrásait. Az alapértelmezett mód növekményes.

A Resource Manager mindkét módban megpróbálja létrehozni a sablonban megadott összes erőforrást. Ha az erőforrás már létezik az erőforráscsoporthoz, és a beállításai nem változnak, akkor a rendszer nem végez műveletet az adott erőforráshoz. Ha módosítja egy erőforrás tulajdonságérték értékét, az erőforrás frissül az új értékekkel. Ha egy meglévő erőforrás helyét vagy típusát kísérli meg frissíteni, a központi telepítés hibát jelez. Ehelyett helyezzen üzembe egy új erőforrást a szükséges hely vagy típus szerint.

## <a name="complete-mode"></a>Teljes mód

Teljes módban a Resource Manager **törli** az erőforráscsoport meglévő erőforrásait, de nincs megadva a sablonban.

Ha a sablon olyan erőforrást tartalmaz, amely nincs központilag telepítve, mert a [feltétel](conditional-resource-deployment.md) hamis értéket ad vissza, akkor az eredmény attól függ, hogy melyik REST API-verziót használja a sablon telepítéséhez. Ha 2019-05-10-nál korábbi verziót használ, az erőforrás **nem törlődik**. A 2019-05-10-es vagy újabb verziókban az erőforrás **törölve lesz**. Az Azure PowerShell és az Azure CLI legújabb verziói törlik az erőforrást.

Ügyeljen arra, hogy a teljes mód legyen a [másolási hurkokkal](resource-group-create-multiple.md). A rendszer a másolási hurok feloldása után nem a sablonban megadott erőforrásokat törli.

Ha [egy sablonban több erőforráscsoporthoz](resource-manager-cross-resource-group-deployment.md)is telepít, a telepítési műveletben megadott erőforráscsoport erőforrásai törölhetők. A másodlagos erőforráscsoportok erőforrásai nem törlődnek.

Az erőforrástípusok eltérő módon kezelik a teljes módú törlést. A fölérendelt erőforrások automatikusan törlődnek, ha nem egy teljes módban telepített sablonban vannak. Néhány alárendelt erőforrás nem törlődik automatikusan, ha nem a sablonban. Ezeket a gyermek erőforrásokat azonban törli a rendszer, ha törli a fölérendelt erőforrást. 

Ha például az erőforráscsoport egy DNS-zónát (Microsoft. Network/dnsZones erőforrástípus) és egy CNAME rekordot (Microsoft. Network/dnsZones/CNAME erőforrástípus) tartalmaz, a DNS-zóna a CNAME rekord szülő erőforrása. Ha a üzembe helyezése teljes módban történik, és nem tartalmazza a DNS-zónát a sablonban, a DNS-zónát és a CNAME-rekordot is törli a rendszer. Ha belefoglalja a DNS-zónát a sablonba, de nem tartalmazza a CNAME rekordot, a CNAME nem törlődik. 

Az erőforrástípusok törlésének módjával kapcsolatban lásd: [Az Azure-erőforrások törlése a teljes módú telepítésekhez](complete-mode-deletion.md).

Ha az erőforráscsoport [zárolva](resource-group-lock-resources.md)van, a teljes mód nem törli az erőforrásokat.

> [!NOTE]
> Csak a legfelső szintű sablonok támogatják a teljes telepítési módot. [Csatolt vagy beágyazott sablonok](resource-group-linked-templates.md)esetén a növekményes módot kell használnia. 
>
> Az [előfizetési szintű központi telepítések](deploy-to-subscription.md) nem támogatják a teljes üzemmódot.
>
> A portál jelenleg nem támogatja a teljes üzemmódot.
>

## <a name="incremental-mode"></a>Növekményes mód

A növekményes módban a Resource Manager változatlan erőforrásokat **hagy** az erőforráscsoporthoz, de nincs megadva a sablonban.

Ha azonban egy meglévő erőforrást növekményes módban helyez üzembe, az eredmény egy másik. Az erőforrás összes tulajdonságát megadhatja, nem csak a frissíteni kívánt tulajdonságokat. Gyakori félreértés, hogy a nem megadott tulajdonságok változatlanok maradnak. Ha nem ad meg bizonyos tulajdonságokat, a Resource Manager az értékek felülírásával értelmezi a frissítést.

## <a name="example-result"></a>Példa eredménye

A növekményes és a teljes üzemmód közötti különbség szemléltetéséhez vegye figyelembe a következő helyzetet.

**Erőforráscsoport** :

* A erőforrás
* B erőforrás
* C erőforrás

A **sablon** tartalma:

* A erőforrás
* B erőforrás
* D erőforrás

**Növekményes** módban való üzembe helyezéskor az erőforráscsoport a következőket végzi el:

* A erőforrás
* B erőforrás
* C erőforrás
* D erőforrás

A **teljes** módban való üzembe helyezéskor a C erőforrás törlődik. Az erőforráscsoport a következőket tartalmazta:

* A erőforrás
* B erőforrás
* D erőforrás

## <a name="set-deployment-mode"></a>Telepítési mód beállítása

A PowerShell használatával történő üzembe helyezési mód beállításához használja a `Mode` paramétert.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Az Azure CLI-vel történő üzembe helyezési mód beállításához használja a `mode` paramétert.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

A következő példa egy olyan csatolt sablont mutat be, amely növekményes üzembe helyezési módra van beállítva:

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

* A Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok](resource-group-authoring-templates.md)készítése.
* További információ az erőforrások üzembe helyezéséről: [alkalmazások központi telepítése Azure Resource Manager sablonnal](resource-group-template-deploy.md).
* Az erőforrás-szolgáltató műveleteinek megtekintéséhez lásd: [Azure REST API](/rest/api/).
