---
title: Azure-erőforrások áthelyezése új csoportba vagy erőforráscsoportonként |} A Microsoft Docs
description: Azure Resource Manager segítségével az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721385"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe

Ez a cikk bemutatja, hogyan Azure-erőforrások áthelyezése egy másik Azure-előfizetés és a egy előfizetésen belül egy másik erőforráscsoportot. Az Azure Portalon, az Azure PowerShell, az Azure CLI vagy a REST API használatával helyezhetők át erőforrások.

Mind a forrás és a cél csoport írásvédett az áthelyezési művelet során. Írási és törlési műveletek az áthelyezés befejezéséig az erőforráscsoportok elakad. A zárolás azt jelenti, hogy a nem hozzáadása, frissítése vagy törlése az erőforráscsoportok erőforrásaihoz, de ez nem jelenti azt, az erőforrások szüneteltetve legyenek. Ha például egy SQL Server és az adatbázis áthelyezése egy új erőforráscsoportot, ha nem az adatbázist használó alkalmazások teljesen állásidő nélkül. Továbbra is olvasni és írni az adatbázisba.

Erőforrások áthelyezése csak áthelyezi egy új erőforráscsoportot vagy előfizetést. Azt az erőforrás helye nem változik.

## <a name="checklist-before-moving-resources"></a>Erőforrások áthelyezése előtti ellenőrzőlistát

Nincsenek erőforrások áthelyezése előtt néhány fontos lépést. Ezen feltételek ellenőrzésével a hibák elkerülhetőek.

1. Az áthelyezni kívánt erőforrások támogatnia kell az áthelyezési művelet. Erőforrások, amelyek támogatja az áthelyezés listáját lásd: [művelet támogatási erőforrások áthelyezése](move-support-resources.md).

1. Egyes szolgáltatások rendelkeznek bizonyos korlátozások vagy követelmények, erőforrások áthelyezésekor. Ha áthelyezi a következő szolgáltatások bármelyike, ellenőrizze a áthelyezése előtt az útmutatások.

   * [App Services áthelyezése útmutató](./move-limitations/app-service-move-limitations.md)
   * [Az Azure DevOps-szolgáltatásokkal áthelyezése útmutató](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasszikus üzemi modell áthelyezési útmutatást](./move-limitations/classic-model-move-limitations.md) -Classic Compute, a klasszikus tárolóba, a klasszikus virtuális hálózatok és a Cloud Services
   * [A Recovery Services áthelyezése útmutató](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtuális gépek áthelyezése útmutató](./move-limitations/virtual-machines-move-limitations.md)
   * [Virtuális hálózatok áthelyezése útmutató](./move-limitations/virtual-network-move-limitations.md)

1. A forrás- és az előfizetések aktívnak kell lennie. Ha problémája, amely le van tiltva, így [hozzon létre egy Azure-támogatáskérést](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **előfizetés-kezelési** issue type számára.

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

1. A cél előfizetést regisztrálni kell az áthelyezett erőforrás erőforrás-szolgáltatóján. Ha nem, akkor megjelenik egy hibaüzenet, arról, hogy a **az előfizetés nincs regisztrálva az erőforrástípushoz**. Előfordulhat, hogy ezt a hibaüzenetet erőforrás új előfizetésre történő áthelyezésekor azonban, hogy az előfizetés még nem volt használva az adott erőforrástípushoz.

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

## <a name="validate-move"></a>Áthelyezésének ellenőrzése

A [áthelyezési művelet érvényesítése](/rest/api/resources/resources/validatemoveresources) az áthelyezési forgatókönyv teszteléséhez ténylegesen az erőforrások áthelyezése nélkül teszi lehetővé. Ez a művelet segítségével ellenőrizheti, ha az áthelyezés sikeres lesz. Érvényesítési automatikusan nevezzük, amikor egy áthelyezési kérelmet küld. Használja ezt a műveletet csak akkor, amikor szüksége van az eredmények előre meghatározni. Ez a művelet végrehajtásához szükséges a:

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

Erőforrások áthelyezése, válassza ki az erőforráscsoportot, és ezeket az erőforrásokat, és válassza a **áthelyezése** gombra.

![erőforrások áthelyezése](./media/resource-group-move-resources/select-move.png)

Válassza ki, hogy az erőforrások telepít át egy új erőforráscsoportot, vagy egy új előfizetést.

Válassza ki az áthelyezni kívánt erőforrások és a cél erőforráscsoport. Tudomásul veszi, hogy szeretne-e frissíteni a következő ezeket az erőforrásokat a szkripteket, és válassza ki **OK**. Ha az előfizetés szerkesztési ikonra az előző lépésben, a cél előfizetést is választania kell.

![cél kiválasztása](./media/resource-group-move-resources/select-destination.png)

A **értesítések**, láthatja, hogy fut-e az áthelyezési művelet.

![Áthelyezés állapot megjelenítése](./media/resource-group-move-resources/show-status.png)

Ha befejeződött, értesítést kap arról, az eredmény.

![Áthelyezés eredmény megjelenítése](./media/resource-group-move-resources/show-result.png)

Ha hibaüzenetet kap, tekintse meg [hibaelhárítás Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja a [Move-AzResource](/powershell/module/az.resources/move-azresource) parancsot. Az alábbi példa bemutatja, hogyan több erőforrás áthelyezése egy új erőforráscsoportot.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Át egy új előfizetést, adjon meg egy értéket a `DestinationSubscriptionId` paraméter.

Ha hibaüzenetet kap, tekintse meg [hibaelhárítás Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja a [az erőforrás-áthelyezési](/cli/azure/resource?view=azure-cli-latest#az-resource-move) parancsot. Adja meg az erőforrás-azonosítókat az erőforrások áthelyezése. Az alábbi példa bemutatja, hogyan több erőforrás áthelyezése egy új erőforráscsoportot. Az a `--ids` paramétert, adja meg az erőforrás-azonosítók áthelyezése szóközzel elválasztott listáját.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Szeretne áthelyezni egy új előfizetést, adja meg a `--destination-subscription-id` paraméter.

Ha hibaüzenetet kap, tekintse meg [hibaelhárítás Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](troubleshoot-move.md).

## <a name="use-rest-api"></a>A REST API használata

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja a [erőforrások áthelyezése](/rest/api/resources/Resources/MoveResources) műveletet.

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

Ha hibaüzenetet kap, tekintse meg [hibaelhárítás Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](troubleshoot-move.md).

## <a name="next-steps"></a>További lépések

Erőforrások, amelyek támogatja az áthelyezés listáját lásd: [művelet támogatási erőforrások áthelyezése](move-support-resources.md).
