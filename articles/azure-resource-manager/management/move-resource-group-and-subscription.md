---
title: Erőforrások áthelyezése új előfizetésbe vagy erőforráscsoportba
description: Az Azure Resource Manager használatával erőforrásokat helyezhet át egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248852"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe

Ez a cikk bemutatja, hogyan helyezheti át az Azure-erőforrásokat egy másik Azure-előfizetésbe vagy egy másik erőforráscsoportba ugyanazon előfizetés alatt. Az erőforrások áthelyezéséhez az Azure Portal, az Azure PowerShell, az Azure CLI vagy a REST API használható.

Mind a forráscsoport, mind a célcsoport zárolva van az áthelyezési művelet során. Az írási és törlési műveletek az áthelyezés befejezéséig le vannak tiltva az erőforráscsoportokban. Ez a zárolás azt jelenti, hogy nem adhat hozzá, nem frissíthet és nem törölhet erőforrásokat az erőforráscsoportokban. Ez nem jelenti azt, hogy az erőforrások be vannak fagyasztva. Ha például egy SQL Server kiszolgálót és annak adatbázisát áthelyezi egy új erőforráscsoportba, az adatbázist használó alkalmazás nem tapasztal leállást. Továbbra is tud olvasni és írni az adatbázisba. A zár legfeljebb négy óráig tarthat, de a legtöbb lépés sokkal rövidebb idő alatt fejeződik be.

Az erőforrás az áthelyezése során csak egy új erőforráscsoportba vagy előfizetésbe kerül. Az erőforrás helyét az áthelyezési művelet nem módosítja.

## <a name="checklist-before-moving-resources"></a>Ellenőrzőlista az erőforrások áthelyezése előtt

Néhány fontos lépést végre kell hajtani az erőforrások áthelyezése előtt. Ezen feltételek ellenőrzésével a hibák elkerülhetőek.

1. Az áthelyezni kívánt erőforrásoknak támogatniuk kell az áthelyezési műveletet. Az erőforrások at, amelyek támogatják az áthelyezést, olvassa el [az Erőforrások művelettámogatásának áthelyezése (Áthelyezése) témakört.](move-support-resources.md)

1. Egyes szolgáltatások speciális korlátozásokkal vagy követelményekkel rendelkeznek az erőforrások áthelyezésekor. Ha az alábbi szolgáltatások bármelyikét helyezi át, az áthelyezés előtt ellenőrizze ezt az útmutatót.

   * [Az App Services áthelyezési útmutatója](./move-limitations/app-service-move-limitations.md)
   * [Az Azure DevOps Services áthelyezési útmutatója](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klasszikus üzembe helyezési modell áthelyezési útmutató](./move-limitations/classic-model-move-limitations.md) – Klasszikus számítási, Klasszikus tárolás, Klasszikus virtuális hálózatok és felhőszolgáltatások
   * [Hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md)
   * [Helyreállítási szolgáltatások áthelyezési útmutató](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtuális gépek áthelyezése útmutató](./move-limitations/virtual-machines-move-limitations.md)

1. A forrás- és célelőfizetéseknek aktívnak kell lenniük. Ha nem tudja engedélyezni a letiltott fiókot, [hozzon létre egy Azure-támogatási kérelmet.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Válassza **az Előfizetéskezelés lehetőséget** a probléma típusához.

1. A forrás- és célelőfizetéseknek ugyanabban az [Azure Active Directory-bérlőn](../../active-directory/develop/quickstart-create-new-tenant.md)belül kell létezniük. Annak ellenőrzéséhez, hogy mindkét előfizetés azonos bérlői azonosítóval rendelkezik-e, használja az Azure PowerShellt vagy az Azure CLI-t.

   Az Azure PowerShell használatához használja:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI esetén használja az alábbi parancsot:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Ha a forrás- és cél-előfizetések bérlői azonosítói nem azonosak, a következő módszerekkel egyeztetheti a bérlői azonosítókat:

   * [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../billing/billing-subscription-transfer.md)
   * [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A cél előfizetést regisztrálni kell az áthelyezett erőforrás erőforrás-szolgáltatóján. Ha nem, hibaüzenetet kap arról, hogy az **előfizetés nincs regisztrálva erőforrástípushoz.** Ez a hiba akkor jelenhet meg, amikor egy erőforrást új előfizetésbe helyez át, de az előfizetést még soha nem használták ezzel az erőforrástípussal.

   A PowerShell esetében a következő parancsokkal kapja meg a regisztrációs állapotot:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Erőforrás-szolgáltató regisztrálásához használja a következőket:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Az Azure CLI esetében a következő parancsokkal szerezheti be a regisztrációs állapotot:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Erőforrás-szolgáltató regisztrálásához használja a következőket:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Az erőforrásokat áthelyező fióknak legalább a következő engedélyekkel kell rendelkeznie:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** a forráserőforrás-csoportban.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** a cél erőforráscsoportra.

1. Az erőforrások áthelyezése előtt ellenőrizze az előfizetési kvótákat az előfizetéshez, amelybe az erőforrásokat áthelyezi. Ha az erőforrások áthelyezése azt jelenti, hogy az előfizetés túllépi a korlátait, át kell tekintenie, hogy kérheti-e a kvóta növelését. A korlátok listáját és a növekedés kérésének módját az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md)témakörben található.

1. **Az előfizetések közötti áthelyezéshez az erőforrásnak és a függő erőforrásoknak ugyanabban az erőforráscsoportban kell lenniük, és együtt kell őket áthelyezni.** Például egy felügyelt lemezekkel rendelkező virtuális gép a virtuális gép és a felügyelt lemezek együtt kell áthelyezni, más függő erőforrásokkal együtt.

   Ha egy erőforrást új előfizetésbe helyez át, ellenőrizze, hogy az erőforrás rendelkezik-e függő erőforrásokkal, és hogy ugyanabban az erőforráscsoportban találhatók-e. Ha az erőforrások nem ugyanabban az erőforráscsoportban vannak, ellenőrizze, hogy az erőforrások konszolidálhatók-e ugyanabba az erőforráscsoportba. Ha igen, hozza az összes erőforrást ugyanabba az erőforráscsoportba egy erőforráscsoportok közötti áthelyezési művelet használatával.

   További információ: [Forgatókönyv az előfizetések közötti áthelyezéshez.](#scenario-for-move-across-subscriptions)

## <a name="scenario-for-move-across-subscriptions"></a>Forgatókönyv az előfizetések közötti áthelyezéshez

Az erőforrások egyik előfizetésből a másikba történő áthelyezése három lépésből áll:

![előfizetések közötti áthelyezési forgatókönyv](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Szemléltetéscéljából csak egy függő erőforrásunk van.

* 1. lépés: Ha a függő erőforrások különböző erőforráscsoportok között vannak elosztva, először helyezze át őket egy erőforráscsoportba.
* 2. lépés: Helyezze át az erőforrást és a függő erőforrásokat együtt a forrás-előfizetésből a cél-előfizetésbe.
* 3. lépés: Tetszés szerint a függő erőforrások at a cél-előfizetés különböző erőforráscsoportok között. 

## <a name="validate-move"></a>Áthelyezés ellenőrzése

Az [áthelyezés idotartama](/rest/api/resources/resources/validatemoveresources) lehetővé teszi az áthelyezési forgatókönyv tesztelését az erőforrások tényleges áthelyezése nélkül. Ezzel a művelettel ellenőrizheti, hogy az áthelyezés sikeres lesz-e. Az ellenőrzés automatikusan megtörténik, amikor áthelyezési kérelmet küld. Ezt a műveletet csak akkor használja, ha előre meg kell határoznia az eredményeket. A művelet futtatásához a következőkre van szükség:

* a forráserőforrás-csoport neve
* a célerőforrás-csoport erőforrásazonosítója
* az áthelyezandó erőforrások erőforrásazonosítója
* a fiók [hozzáférési tokenje](/rest/api/azure/#acquire-an-access-token)

Küldje el a következő kérést:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

A kérelem törzs:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Ha a kérelem formázása megfelelő, a művelet a következőket adja vissza:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

A 202-es állapotkód azt jelzi, hogy az érvényesítési kérelem elfogadásra került, de még nem határozta meg, hogy az áthelyezési művelet sikeres lesz-e. Az `location` érték egy URL-címet tartalmaz, amelyet a hosszú ideig futó művelet állapotának ellenőrzésére használ.  

Az állapot ellenőrzéséhez küldje el a következő kérést:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Amíg a művelet még fut, továbbra is megkapja a 202-es állapotkódot. Várjon az értékben `retry-after` jelzett másodpercek számával, mielőtt újra próbálkozna. Ha az áthelyezési művelet sikeresen érvényesítve van, megkapja a 204-es állapotkódot. Ha az áthelyezés érvényesítése sikertelen, hibaüzenet jelenik meg, például:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>A portál használata

Erőforrások áthelyezéséhez jelölje ki az ezeket az erőforrásokat, majd kattintson az **Áthelyezés** gombra.

![erőforrások áthelyezése](./media/move-resource-group-and-subscription/select-move.png)

Válassza ki, hogy az erőforrásokat új erőforráscsoportba vagy új előfizetésbe helyezi-e át.

Válassza ki az áthelyezni kívánt erőforrásokat és a célerőforrás-csoportot. Nyugtázza, hogy frissítenie kell az erőforrások parancsfájljait, és válassza az **OK gombot.** Ha az előző lépésben kiválasztotta az előfizetés szerkesztési ikonját, akkor a célelőfizetést is ki kell választania.

![cél kiválasztása](./media/move-resource-group-and-subscription/select-destination.png)

Az **Értesítések alkalmazásban**láthatja, hogy az áthelyezési művelet fut.

![áthelyezési állapot megjelenítése](./media/move-resource-group-and-subscription/show-status.png)

Ha befejeződött, értesítést kap az eredményről.

![mozgás eredménymegjelenítése](./media/move-resource-group-and-subscription/show-result.png)

Ha hibaüzenetet kap, olvassa [el Az Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe – problémamegoldás.](troubleshoot-move.md)

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Ha meglévő erőforrásokat szeretne áthelyezni egy másik erőforráscsoportba vagy előfizetésbe, használja az [Move-AzResource](/powershell/module/az.resources/move-azresource) parancsot. A következő példa bemutatja, hogyan helyezhet át több erőforrást egy új erőforráscsoportba.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Új előfizetésre való áttéréshez adjon `DestinationSubscriptionId` meg egy értéket a paraméterhez.

Ha hibaüzenetet kap, olvassa [el Az Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe – problémamegoldás.](troubleshoot-move.md)

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Meglévő erőforrások áthelyezése egy másik erőforráscsoportba vagy előfizetésbe, használja az [erőforrás áthelyezése](/cli/azure/resource?view=azure-cli-latest#az-resource-move) parancsot. Adja meg az áthelyezandó erőforrások erőforrásazonosítóit. A következő példa bemutatja, hogyan helyezhet át több erőforrást egy új erőforráscsoportba. A `--ids` paraméterben adja meg az áthelyezendő erőforrásazonosítók térelválasztó listáját.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Új előfizetésre való áttéréshez `--destination-subscription-id` adja meg a paramétert.

Ha hibaüzenetet kap, olvassa [el Az Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe – problémamegoldás.](troubleshoot-move.md)

## <a name="use-rest-api"></a>A REST API használata

Ha a meglévő erőforrásokat egy másik erőforráscsoportba vagy előfizetésbe szeretné áthelyezni, használja az [Erőforrások áthelyezése](/rest/api/resources/Resources/MoveResources) műveletet.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

A kérelem törzsében megadhatja a célerőforráscsoportot és az áthelyezandó erőforrásokat.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Ha hibaüzenetet kap, olvassa [el Az Azure-erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe – problémamegoldás.](troubleshoot-move.md)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés: Az erőforrás áthelyezési művelete, amely általában néhány percet vesz igénybe, majdnem egy órája fut. Valami baj van?**

Az erőforrás áthelyezése olyan összetett művelet, amelynek különböző fázisai vannak. Ez nem csak az áthelyezni kívánt erőforrás erőforrás-szolgáltatóját foglalhatja magában. Az erőforrás-szolgáltatók közötti függőségek miatt az Azure Resource Manager 4 órát engedélyez a művelet befejezéséhez. Ez az időszak lehetőséget ad az erőforrás-szolgáltatóknak az átmeneti problémák helyreállítására. Ha az áthelyezési kérelem a 4 órás időszakon belül van, a művelet folytatja a befejezést, és továbbra is sikeres lehet. A forrás- és célerőforrás-csoportok ez idő alatt zárolva vannak a konzisztenciaproblémák elkerülése érdekében.

**Kérdés: Miért van zárolva az erőforráscsoport 4 órán keresztül az erőforrás áthelyezése során?**

A 4 órás ablak az erőforrás-áthelyezéshez engedélyezett maximális idő. Az áthelyezett erőforrások módosításának megakadályozása érdekében mind a forrás,mind a célerőforrás-csoportok zárolva vannak az erőforrás áthelyezése idejére.

Az áthelyezési kérelemnek két fázisa van. Az első fázisban az erőforrás átkerül. A második fázisban a rendszer értesítéseket küld más erőforrás-szolgáltatóknak, amelyek az áthelyezett erőforrástól függenek. Egy erőforráscsoport zárolható a teljes 4 órás időszakban, ha egy erőforrás-szolgáltató bármelyik fázisban meghibásodik. A megengedett idő alatt az Erőforrás-kezelő újrapróbálkozik a sikertelen lépéssel.

Ha egy erőforrás nem helyezhető át a 4 órás időszakon belül, az Erőforrás-kezelő mindkét erőforráscsoportot feloldja. A sikeresen áthelyezett erőforrások a cél erőforráscsoportban találhatók. Az átnem helyezhető erőforrások közül a forráserőforrás-csoport marad.

**Kérdés: Milyen következményekkel jár a forrás- és célerőforrás-csoportok zárolva az erőforrás áthelyezése során?**

A zárolás megakadályozza, hogy bármelyik erőforráscsoportot, új erőforrást hozzon létre az erőforráscsoportban, vagy az áthelyezésben részt vevő erőforrásokat.

Az alábbi képen egy hibaüzenet jelenik meg az Azure Portalon, amikor egy felhasználó megpróbál törölni egy erőforráscsoportot, amely egy folyamatban lévő áthelyezés része.

![Törlésre kerülő hibaüzenet áthelyezése](./media/move-resource-group-and-subscription/move-error-delete.png)

**Kérdés: Mit jelent a "MissingMoveDependentResources" hibakód?**

Erőforrás áthelyezésekor a függő erőforrásoknak vagy a célerőforrás-csoportban vagy az előfizetésben kell lenniük, vagy szerepelniük kell az áthelyezési kérelemben. A MissingMoveDependentResources hibakódot kap, ha egy függő erőforrás nem felel meg ennek a követelménynek. A hibaüzenet az áthelyezési kérelemben szerepeltetni kívánt függő erőforrás részleteit tartalmazza.

Egy virtuális gép áthelyezéséhez például hét erőforrástípus áthelyezése szükséges három különböző erőforrás-szolgáltatóval. Ezek az erőforrás-szolgáltatók és típusok a következők:

* Microsoft.Compute
   * virtualMachines
   * Lemezek
* Microsoft.Network
  * hálózati interfészek
  * nyilvánosIP-címek
  * networkSecurityGroups (hálózatibiztonsági csoportok)
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Egy másik gyakori példa a virtuális hálózat áthelyezése. Előfordulhat, hogy a virtuális hálózathoz társított számos más erőforrást is át kell helyeznie. Az áthelyezési kérelem nyilvános IP-címek, útvonaltáblák, virtuális hálózati átjárók, hálózati biztonsági csoportok és mások áthelyezését igényelheti.

**Kérdés: Miért nem helyezhetek át bizonyos erőforrásokat az Azure-ban?**

Jelenleg nem minden azure-támogatás áthelyezése. Az áthelyezést támogató erőforrások listáját az [Erőforrások művelettámogatásának áthelyezése (Áthelyezése) témakörben tetszésszerint.](move-support-resources.md)

## <a name="next-steps"></a>További lépések

Az erőforrások at, amelyek támogatják az áthelyezést, olvassa el [az Erőforrások művelettámogatásának áthelyezése (Áthelyezése) témakört.](move-support-resources.md)
