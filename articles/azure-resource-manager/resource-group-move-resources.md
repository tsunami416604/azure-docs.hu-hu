---
title: Azure-erőforrások áthelyezése új előfizetésre vagy erőforráscsoport-csoportba | Microsoft Docs
description: Azure Resource Manager segítségével az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 53482fdd760517967c9a4a976b43b64ba745c637
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542965"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe

Ebből a cikkből megtudhatja, hogyan helyezheti át az Azure-erőforrásokat egy másik Azure-előfizetésbe vagy egy másik erőforráscsoporthoz ugyanahhoz az előfizetéshez. Az erőforrások áthelyezéséhez használhatja a Azure Portal, a Azure PowerShell, az Azure CLI vagy a REST API.

A forrásoldali csoport és a célcsoport is zárolva van az áthelyezési művelet során. Írási és törlési műveletek az áthelyezés befejezéséig az erőforráscsoportok elakad. A zárolás azt jelenti, hogy a nem hozzáadása, frissítése vagy törlése az erőforráscsoportok erőforrásaihoz, de ez nem jelenti azt, az erőforrások szüneteltetve legyenek. Ha például egy SQL Server és az adatbázis áthelyezése egy új erőforráscsoportot, ha nem az adatbázist használó alkalmazások teljesen állásidő nélkül. Továbbra is olvasni és írni az adatbázisba.

Az erőforrások áthelyezése csak új erőforráscsoporthoz vagy előfizetésbe helyezi át. Nem módosítja az erőforrás helyét.

## <a name="checklist-before-moving-resources"></a>Erőforrások áthelyezése előtti ellenőrzőlistát

Az erőforrások áthelyezése előtt néhány fontos lépést kell végrehajtania. Ezen feltételek ellenőrzésével a hibák elkerülhetőek.

1. Az áthelyezni kívánt erőforrásoknak támogatniuk kell az áthelyezési műveletet. Az áthelyezni kívánt erőforrások listáját itt tekintheti meg: a [műveletek támogatásának áthelyezése az erőforrásokhoz](move-support-resources.md).

1. Egyes szolgáltatások az erőforrások áthelyezésekor bizonyos korlátozásokkal vagy követelményekkel rendelkeznek. Ha a következő szolgáltatások bármelyikét áthelyezte, ellenőrizze az útmutatást az áthelyezés előtt.

   * [Útmutató App Services](./move-limitations/app-service-move-limitations.md)
   * [Útmutató az Azure DevOps Serviceshez](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasszikus üzembe helyezési modell – útmutató](./move-limitations/classic-model-move-limitations.md) – klasszikus számítás, klasszikus tárolás, klasszikus virtuális hálózatok és Cloud Services
   * [Útmutató Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Útmutató Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)
   * [A virtuális hálózatok útmutatást nyújtanak](./move-limitations/virtual-network-move-limitations.md)

1. A forrás-és a cél-előfizetésnek aktívnak kell lennie. Ha problémája van egy letiltott fiók engedélyezésével, [hozzon létre egy Azure-támogatási kérelmet](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **előfizetés-kezelési** issue type számára.

1. A forrás- és az előfizetések léteznie kell az előfizetésen belül [Azure Active Directory-bérlő](../active-directory/develop/quickstart-create-new-tenant.md). Ellenőrizze, hogy mindkét előfizetéshez tartozik-e az azonos bérlő azonosítója, használja az Azure PowerShell vagy az Azure CLI.

   Azure PowerShell esetén használja:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI esetén használja az alábbi parancsot:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Ha a bérlőazonosítók a forrás- és előfizetés esetében nem ugyanaz, a következő módszerek használatával a bérlőazonosítók egyeztetése:

   * [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)
   * [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A cél előfizetést regisztrálni kell az áthelyezett erőforrás erőforrás-szolgáltatóján. Ha nem, akkor megjelenik egy hibaüzenet, arról, hogy a **az előfizetés nincs regisztrálva az erőforrástípushoz**. Ez a hiba akkor fordulhat elő, amikor új előfizetésre helyezi át az erőforrást, de az előfizetést soha nem használták az adott erőforrás-típussal.

   A PowerShell a következő parancsok használatával a regisztrációs állapot lekérdezése:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Erőforrás-szolgáltató regisztrálásához használja:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI-hez a következő parancsok használatával a regisztrációs állapot lekérdezése:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Erőforrás-szolgáltató regisztrálásához használja:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Erőforrások áthelyezése a fióknak legalább a következő engedélyekkel kell rendelkeznie:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** a a forrásként szolgáló erőforráscsoportot.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** a cél erőforráscsoport található.

1. Erőforrások áthelyezése előtt tekintse át az erőforrásokat az előfizetés az előfizetési kvóták. Erőforrások áthelyezése azt jelenti, hogy az előfizetés túl fogja lépni a teljesítménye korlátait, ha meg kell tekintenie a e kérheti a kvótájának növelését. Korlátok, és hogyan lehet növelni listáját lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).

1. **Az előfizetések közötti áthelyezéshez az erőforrásnak és a hozzá tartozó erőforrásoknak ugyanabban az erőforráscsoporthoz kell lenniük, és együtt kell őket áthelyezni.** A felügyelt lemezekkel rendelkező virtuális gépek például megkövetelik, hogy a virtuális gép és a felügyelt lemezek együtt legyenek áthelyezve a többi függő erőforrással együtt.

   Ha egy erőforrást új előfizetésre helyez át, ellenőrizze, hogy az erőforrás rendelkezik-e függő erőforrásokkal, és hogy azok ugyanabban az erőforráscsoporthoz találhatók-e. Ha az erőforrások nem ugyanabban az erőforráscsoporthoz találhatók, ellenőrizze, hogy az erőforrások összevonható-e ugyanabba az erőforráscsoporthoz. Ha igen, az összes erőforrást ugyanabba az erőforráscsoporthoz helyezheti át egy áthelyezési művelettel az erőforráscsoportok között.
    
További információ: [forgatókönyv az előfizetések közötti áthelyezéshez](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Az előfizetések közötti áthelyezés forgatókönyve
Az erőforrások egyik előfizetésből egy másikba való áthelyezése három lépésből álló folyamat:

![előfizetések közötti áthelyezés forgatókönyve](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

Illusztrációs célokból csak egy függő erőforrás áll rendelkezésre.

* 1\. lépés: Ha a függő erőforrások különböző erőforráscsoportok között oszlanak el, először helyezze át őket egyetlen erőforráscsoport-csoportba.
* 2\. lépés: Helyezze át az erőforrást és a függő erőforrásokat a forrás-előfizetésből a cél előfizetésbe.
* 3\. lépés: Igény szerint terjesztheti újra a függő erőforrásokat a cél előfizetésben lévő különböző erőforráscsoportok között. 

## <a name="validate-move"></a>Áthelyezésének ellenőrzése

A [áthelyezési művelet érvényesítése](/rest/api/resources/resources/validatemoveresources) az áthelyezési forgatókönyv teszteléséhez ténylegesen az erőforrások áthelyezése nélkül teszi lehetővé. Ezzel a művelettel ellenőrizhető, hogy az áthelyezés sikeres lesz-e. Az érvényesítés automatikusan megtörténik az áthelyezési kérelem elküldésekor. Ezt a műveletet csak akkor használja, ha előre meg kell határoznia az eredményeket. Ez a művelet végrehajtásához szükséges a:

* a forrás erőforráscsoport nevét
* erőforrás-azonosító a célként megadott erőforráscsoport
* erőforrás-azonosító az egyes erőforrások áthelyezése
* a [hozzáférési jogkivonat](/rest/api/azure/#acquire-an-access-token) fiókja

A következő kérelem küldése:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

A kéréstörzs:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Ha a kérés formátuma megfelelő, a művelet adja vissza:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

A 202-es állapotkód jelzi az ellenőrzési kérést elfogadták, de még nem még meg ha az áthelyezés sikeres lesz. A `location` érték tartalmaz egy URL-címet, amellyel a hosszú ideig futó művelet állapotának ellenőrzéséhez.  

Ellenőrizheti az állapotot, a következő kérelem küldése:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

A művelet továbbra is fut, miközben továbbra is megjelenik a 202-es állapotkódot. Várjon jelzi a másodpercek számát a `retry-after` értéket, majd próbálkozzon újra. Ha az áthelyezés érvényesítése sikeres, a 204 állapotkód jelenik meg. Ha az áthelyezés érvényesítése sikertelen, kap egy hibaüzenet, például:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>A portál használata

Az erőforrások áthelyezéséhez válassza ki az erőforráscsoportot az adott erőforrásokkal, majd kattintson az **Áthelyezés** gombra.

![erőforrások áthelyezése](./media/resource-group-move-resources/select-move.png)

Válassza ki, hogy az erőforrások telepít át egy új erőforráscsoportot, vagy egy új előfizetést.

Válassza ki az áthelyezni kívánt erőforrások és a cél erőforráscsoport. Tudomásul veszi, hogy szeretne-e frissíteni a következő ezeket az erőforrásokat a szkripteket, és válassza ki **OK**. Ha az előfizetés szerkesztési ikonra az előző lépésben, a cél előfizetést is választania kell.

![cél kiválasztása](./media/resource-group-move-resources/select-destination.png)

A **értesítések**, láthatja, hogy fut-e az áthelyezési művelet.

![Áthelyezés állapot megjelenítése](./media/resource-group-move-resources/show-status.png)

Ha befejeződött, értesítést kap arról, az eredmény.

![Áthelyezés eredmény megjelenítése](./media/resource-group-move-resources/show-result.png)

Ha hibaüzenetet kap, tekintse meg [Az Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos](troubleshoot-move.md)témakört.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A meglévő erőforrások másik erőforráscsoporthoz vagy előfizetésbe való áthelyezéséhez használja a [Move-AzResource](/powershell/module/az.resources/move-azresource) parancsot. Az alábbi példa bemutatja, hogyan helyezhet át több erőforrást egy új erőforráscsoporthoz.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Át egy új előfizetést, adjon meg egy értéket a `DestinationSubscriptionId` paraméter.

Ha hibaüzenetet kap, tekintse meg [Az Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos](troubleshoot-move.md)témakört.

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja a [az erőforrás-áthelyezési](/cli/azure/resource?view=azure-cli-latest#az-resource-move) parancsot. Adja meg az erőforrás-azonosítókat az erőforrások áthelyezése. Az alábbi példa bemutatja, hogyan helyezhet át több erőforrást egy új erőforráscsoporthoz. Az a `--ids` paramétert, adja meg az erőforrás-azonosítók áthelyezése szóközzel elválasztott listáját.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Szeretne áthelyezni egy új előfizetést, adja meg a `--destination-subscription-id` paraméter.

Ha hibaüzenetet kap, tekintse meg [Az Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos](troubleshoot-move.md)témakört.

## <a name="use-rest-api"></a>A REST API használata

A meglévő erőforrások másik erőforráscsoporthoz vagy előfizetésbe való áthelyezéséhez használja az [erőforrások áthelyezése](/rest/api/resources/Resources/MoveResources) műveletet.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

A kérelem törzsében szereplő adja meg a céloldali erőforráscsoport és erőforrások áthelyezéséhez.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Ha hibaüzenetet kap, tekintse meg [Az Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos](troubleshoot-move.md)témakört.

## <a name="next-steps"></a>További lépések

Az áthelyezni kívánt erőforrások listáját itt tekintheti meg: a [műveletek támogatásának áthelyezése az erőforrásokhoz](move-support-resources.md).
