---
title: Erőforrások áthelyezése új előfizetésbe vagy erőforráscsoporthoz
description: Azure Resource Manager segítségével az erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250169"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe

Ebből a cikkből megtudhatja, hogyan helyezheti át az Azure-erőforrásokat egy másik Azure-előfizetésbe vagy egy másik erőforráscsoporthoz ugyanahhoz az előfizetéshez. Az erőforrások áthelyezéséhez az Azure Portal, az Azure PowerShell, az Azure CLI vagy a REST API használható.

A forrásoldali csoport és a célcsoport is zárolva van az áthelyezési művelet során. Írási és törlési műveletek az áthelyezés befejezéséig az erőforráscsoportok elakad. Ez a zárolás azt jelenti, hogy nem lehet erőforrásokat felvenni, frissíteni vagy törölni az erőforráscsoportok között. Nem jelenti azt, hogy az erőforrások zárolva vannak. Ha például egy SQL Server és az adatbázis áthelyezése egy új erőforráscsoportot, ha nem az adatbázist használó alkalmazások teljesen állásidő nélkül. Továbbra is olvasni és írni az adatbázisba. A zárolás legfeljebb négy órán át tarthat, de a legtöbb lépés sokkal kevesebb időt vehet igénybe.

Az erőforrás az áthelyezése során csak egy új erőforráscsoportba vagy előfizetésbe kerül. Az erőforrás helyét az áthelyezési művelet nem módosítja.

## <a name="checklist-before-moving-resources"></a>Erőforrások áthelyezése előtti ellenőrzőlistát

Néhány fontos lépést végre kell hajtani az erőforrások áthelyezése előtt. Ezen feltételek ellenőrzésével a hibák elkerülhetőek.

1. Az áthelyezni kívánt erőforrásoknak támogatniuk kell az áthelyezési műveletet. Az áthelyezni kívánt erőforrások listáját itt tekintheti meg: a [műveletek támogatásának áthelyezése az erőforrásokhoz](move-support-resources.md).

1. Egyes szolgáltatások az erőforrások áthelyezésekor bizonyos korlátozásokkal vagy követelményekkel rendelkeznek. Ha a következő szolgáltatások bármelyikét áthelyezi, a mozgatás előtt ellenőrizze az útmutatást.

   * [Útmutató App Services](./move-limitations/app-service-move-limitations.md)
   * [Útmutató az Azure DevOps Serviceshez](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasszikus üzembe helyezési modell – útmutató](./move-limitations/classic-model-move-limitations.md) – klasszikus számítás, klasszikus tárolás, klasszikus virtuális hálózatok és Cloud Services
   * [Útmutató a hálózatkezelés áthelyezéséhez](./move-limitations/networking-move-limitations.md)
   * [Útmutató Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Útmutató Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

1. A forrás-és a cél-előfizetésnek aktívnak kell lennie. Ha problémája van egy letiltott fiók engedélyezésével, [hozzon létre egy Azure-támogatási kérelmet](../../azure-portal/supportability/how-to-create-azure-support-request.md). Válassza az **előfizetés kezelése** lehetőséget a probléma típusához.

1. A forrás és a cél előfizetésnek ugyanabban a [Azure Active Directory bérlőn](../../active-directory/develop/quickstart-create-new-tenant.md)belül kell lennie. Ellenőrizze, hogy mindkét előfizetéshez tartozik-e az azonos bérlő azonosítója, használja az Azure PowerShell vagy az Azure CLI.

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

   * [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../billing/billing-subscription-transfer.md)
   * [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A cél előfizetést regisztrálni kell az áthelyezett erőforrás erőforrás-szolgáltatóján. Ha nem, hibaüzenetet kap arról, hogy az **előfizetés nincs regisztrálva az erőforrás típusától**függően. Ez a hiba akkor fordulhat elő, amikor új előfizetésre helyezi át az erőforrást, de az előfizetést soha nem használták az adott erőforrás-típussal.

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

   * **Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action** a forrás erőforráscsoport.
   * **Microsoft. Resources/Subscriptions/resourceGroups/Write** a cél erőforráscsoporthoz.

1. Erőforrások áthelyezése előtt tekintse át az erőforrásokat az előfizetés az előfizetési kvóták. Erőforrások áthelyezése azt jelenti, hogy az előfizetés túl fogja lépni a teljesítménye korlátait, ha meg kell tekintenie a e kérheti a kvótájának növelését. Az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és megkötéseit lásd: az Azure-előfizetés és a szolgáltatási korlátok](../../azure-resource-manager/management/azure-subscription-service-limits.md)listája.

1. **Az előfizetések közötti áthelyezéshez az erőforrásnak és a hozzá tartozó erőforrásoknak ugyanabban az erőforráscsoporthoz kell lenniük, és együtt kell őket áthelyezni.** A felügyelt lemezekkel rendelkező virtuális gépek például megkövetelik, hogy a virtuális gép és a felügyelt lemezek együtt legyenek áthelyezve a többi függő erőforrással együtt.

   Ha egy erőforrást új előfizetésre helyez át, ellenőrizze, hogy az erőforrás rendelkezik-e függő erőforrásokkal, és hogy azok ugyanabban az erőforráscsoporthoz találhatók-e. Ha az erőforrások nem ugyanabban az erőforráscsoporthoz találhatók, ellenőrizze, hogy az erőforrások összevonható-e ugyanabba az erőforráscsoporthoz. Ha igen, az összes erőforrást ugyanabba az erőforráscsoporthoz helyezheti át egy áthelyezési művelettel az erőforráscsoportok között.

   További információ: [forgatókönyv az előfizetések közötti áthelyezéshez](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Az előfizetések közötti áthelyezés forgatókönyve

Az erőforrások egyik előfizetésből egy másikba való áthelyezése három lépésből álló folyamat:

![előfizetések közötti áthelyezés forgatókönyve](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Illusztrációs célokból csak egy függő erőforrás áll rendelkezésre.

* 1\. lépés: Ha a függő erőforrások különböző erőforráscsoportok között oszlanak el, először helyezze át őket egyetlen erőforráscsoport-csoportba.
* 2\. lépés: helyezze át az erőforrást és a függő erőforrásokat a forrás-előfizetésből a cél előfizetésbe.
* 3\. lépés: igény szerint terjesztheti újra a függő erőforrásokat a cél előfizetésben lévő különböző erőforráscsoportok között. 

## <a name="validate-move"></a>Áthelyezésének ellenőrzése

Az [áthelyezési művelet ellenőrzése](/rest/api/resources/resources/validatemoveresources) lehetővé teszi az áthelyezési forgatókönyv tesztelését anélkül, hogy ténylegesen áthelyezi az erőforrásokat. Ezzel a művelettel ellenőrizhető, hogy az áthelyezés sikeres lesz-e. Az érvényesítés automatikusan megtörténik az áthelyezési kérelem elküldésekor. Ezt a műveletet csak akkor használja, ha előre meg kell határoznia az eredményeket. Ez a művelet végrehajtásához szükséges a:

* a forrás erőforráscsoport nevét
* erőforrás-azonosító a célként megadott erőforráscsoport
* erőforrás-azonosító az egyes erőforrások áthelyezése
* a fiók [hozzáférési jogkivonata](/rest/api/azure/#acquire-an-access-token)

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

A 202-es állapotkód jelzi az ellenőrzési kérést elfogadták, de még nem még meg ha az áthelyezés sikeres lesz. A `location` érték tartalmaz egy URL-címet, amelyet a hosszan futó művelet állapotának vizsgálatához használ.  

Ellenőrizheti az állapotot, a következő kérelem küldése:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

A művelet továbbra is fut, miközben továbbra is megjelenik a 202-es állapotkódot. A próbálkozás előtt várjon a `retry-after` értékben jelzett másodpercek számát. Ha az áthelyezés érvényesítése sikeres, a 204 állapotkód jelenik meg. Ha az áthelyezés érvényesítése sikertelen, kap egy hibaüzenet, például:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>A portál használata

Az erőforrások áthelyezéséhez válassza ki az erőforráscsoportot az adott erőforrásokkal, majd kattintson az **Áthelyezés** gombra.

![erőforrások áthelyezése](./media/move-resource-group-and-subscription/select-move.png)

Válassza ki, hogy az erőforrások telepít át egy új erőforráscsoportot, vagy egy új előfizetést.

Válassza ki az áthelyezni kívánt erőforrások és a cél erőforráscsoport. Nyugtázza, hogy frissítenie kell a parancsfájlokat ezekhez az erőforrásokhoz, és kattintson **az OK gombra**. Ha az előfizetés szerkesztési ikonra az előző lépésben, a cél előfizetést is választania kell.

![cél kiválasztása](./media/move-resource-group-and-subscription/select-destination.png)

Az **értesítések**területen láthatja, hogy az áthelyezési művelet fut.

![Áthelyezés állapot megjelenítése](./media/move-resource-group-and-subscription/show-status.png)

Ha befejeződött, értesítést kap arról, az eredmény.

![Áthelyezés eredmény megjelenítése](./media/move-resource-group-and-subscription/show-result.png)

Ha hibaüzenetet kap, tekintse meg [Az Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos](troubleshoot-move.md)témakört.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A meglévő erőforrások másik erőforráscsoporthoz vagy előfizetésbe való áthelyezéséhez használja a [Move-AzResource](/powershell/module/az.resources/move-azresource) parancsot. Az alábbi példa bemutatja, hogyan helyezhet át több erőforrást egy új erőforráscsoporthoz.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Új előfizetésre való áttéréshez adja meg a `DestinationSubscriptionId` paraméter értékét.

Ha hibaüzenetet kap, tekintse meg [Az Azure-erőforrások új erőforráscsoporthoz vagy előfizetésbe való áthelyezésével kapcsolatos](troubleshoot-move.md)témakört.

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

A meglévő erőforrások másik erőforráscsoporthoz vagy előfizetésbe való áthelyezéséhez használja az az [Resource Move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) parancsot. Adja meg az erőforrás-azonosítókat az erőforrások áthelyezése. Az alábbi példa bemutatja, hogyan helyezhet át több erőforrást egy új erőforráscsoporthoz. A `--ids` paraméterben adja meg az áthelyezni kívánt erőforrás-azonosítók szóközzel tagolt listáját.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Új előfizetésre való áttéréshez adja meg a `--destination-subscription-id` paramétert.

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

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés: az erőforrás-áthelyezési művelet, amely általában néhány percet vesz igénybe, majdnem egy óráig futott. Van valami baj?**

Az erőforrások áthelyezése egy összetett művelet, amely különböző fázisokat tartalmaz. Többek között az áthelyezni kívánt erőforrás erőforrás-szolgáltatóját is magában foglalja. Az erőforrás-szolgáltatók közötti függőségek miatt Azure Resource Manager 4 órát engedélyez a művelet befejezéséhez. Ez az időszak lehetővé teszi az erőforrás-szolgáltatók számára, hogy helyreálljon az átmeneti problémák miatt. Ha az áthelyezési kérelem a 4 órás időszakon belül van, a művelet továbbra is próbálkozik a befejezéssel, és lehetséges, hogy sikeres lesz. A forrás és a cél erőforráscsoport ebben az időszakban zárolva van a konzisztencia-problémák elkerülése érdekében.

**Kérdés: Miért van zárolva az erőforráscsoport 4 órán át az erőforrás áthelyezése során?**

A 4 órás időszak az erőforrás-áthelyezés engedélyezett maximális időtartama. Az áthelyezett erőforrások módosításának megakadályozásához a forrás és a cél erőforráscsoport is zárolva lesz az erőforrás-áthelyezés időtartamára.

Az áthelyezési kérelemnek két fázisa van. Az első fázisban az erőforrás át lesz helyezve. A második fázisban az értesítéseket a rendszer az áthelyezett erőforrástól függő más erőforrás-szolgáltatóknak küldi el. Egy erőforráscsoport zárolható a teljes 4 órás időszakra, amikor egy erőforrás-szolgáltató vagy fázis meghibásodik. Az engedélyezett idő alatt a Resource Manager újrapróbálkozik a sikertelen lépéssel.

Ha egy erőforrás nem helyezhető át a 4 órás időszakon belül, a Resource Manager mindkét erőforráscsoportot feloldja. A sikeresen áthelyezett erőforrások a cél erőforráscsoporthoz tartoznak. A nem áthelyezni kívánt erőforrások a forrás erőforráscsoport felett maradnak.

**Kérdés: milyen következményekkel jár a forrás-és a cél-erőforráscsoport zárolása az erőforrás-áthelyezés során?**

A zárolás megakadályozza az erőforráscsoport törlését, egy új erőforrás létrehozását bármelyik erőforráscsoport számára, vagy az áthelyezésben érintett erőforrások törlését.

Az alábbi képen a Azure Portal hibaüzenet jelenik meg, amikor egy felhasználó egy folyamatos áthelyezés részét képező erőforráscsoportot próbál törölni.

![A törölni próbált üzenet áthelyezése](./media/move-resource-group-and-subscription/move-error-delete.png)

**Kérdés: mit jelent a "MissingMoveDependentResources" hibakód?**

Egy erőforrás áthelyezésekor a hozzá tartozó erőforrásoknak léteznie kell a célként megadott erőforráscsoport vagy előfizetés részeként, vagy szerepelniük kell az áthelyezési kérelemben. A MissingMoveDependentResources hibakód jelenik meg, ha egy függő erőforrás nem felel meg ennek a követelménynek. A hibaüzenet tartalmazza az áthelyezési kérelemben szerepeltetni kívánt függő erőforrás részleteit.

Előfordulhat például, hogy egy virtuális gép áthelyezéséhez hét erőforrás-típust kell áthelyeznie három különböző erőforrás-szolgáltatóval. Ezek az erőforrás-szolgáltatók és típusok a következők:

* Microsoft.Compute
   * virtualMachines
   * lemezek
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Egy másik gyakori példa egy virtuális hálózat áthelyezését jelenti. Előfordulhat, hogy az adott virtuális hálózathoz kapcsolódó további erőforrásokat kell áthelyeznie. Az áthelyezési kérelemhez nyilvános IP-címeket, útválasztási táblákat, virtuális hálózati átjárókat, hálózati biztonsági csoportokat és másokat is át kell helyezni.

**Kérdés: Miért nem tudok áthelyezni néhány erőforrást az Azure-ban?**

Jelenleg az Azure-támogatás nem minden erőforrást helyez át. Az áthelyezést támogató erőforrások listáját itt tekintheti meg: a [műveletek támogatásának áthelyezése az erőforrásokhoz](move-support-resources.md).

## <a name="next-steps"></a>Következő lépések

Az áthelyezni kívánt erőforrások listáját itt tekintheti meg: a [műveletek támogatásának áthelyezése az erőforrásokhoz](move-support-resources.md).
