---
title: Kezdő lekérdezési példák
description: Az Azure Resource Graph használatával néhány kezdő lekérdezést futtathat, beleértve az erőforrások számlálását, az erőforrások megrendelését vagy egy adott címkét.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 14bac2299505214b8b087946222c5560a9d90efd
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976861"
---
# <a name="starter-resource-graph-queries"></a>Alapszintű Resource Graph-lekérdezések

Az Azure Resource Graph-fal végzett lekérdezések megértéséhez először a [lekérdezés nyelvét](../concepts/query-language.md) kell alapszinten megismernie. Ha még nem ismeri az [Azure Data Explorert](../../../data-explorer/data-explorer-overview.md), javasoljuk, hogy tekintse át az alapokat a keresett erőforrásokra vonatkozó kérések összeállításának megértéséhez.

A következő alapszintű lekérdezéseken vezetjük végig:

> [!div class="checklist"]
> - [Az Azure-erőforrások száma](#count-resources)
> - [Az erőforrások listája név szerint rendezve](#list-resources)
> - [Az összes virtuális gép megjelenítése név szerint rendezve, csökkenő sorrendben](#show-vms)
> - [Az első öt virtuális gép megjelenítése a nevük és az operációs rendszerük típusa szerint](#show-sorted)
> - [A virtuális gépek száma az operációs rendszer típusa szerint](#count-os)
> - [A tárolót tartalmazó erőforrások megjelenítése](#show-storage)
> - [Az összes nyilvános IP-cím listázása](#list-publicip)
> - [A konfigurált IP-címekkel rendelkező erőforrások száma előfizetés szerint](#count-resources-by-ip)
> - [Az adott címkeértékkel rendelkező erőforrások listázása](#list-tag)
> - [Az összes, adott címkeértékkel rendelkező tárfiók listázása](#list-specific-tag)
> - [Virtuális gép erőforrásaihoz tartozó aliasok megjelenítése](#show-aliases)
> - [Egy adott alias különböző értékeinek megjelenítése](#distinct-alias-values)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="language-support"></a>Nyelvi támogatás

Az Azure Resource Graph-ot az Azure CLI (bővítményen keresztül) és az Azure PowerShell (modulon keresztül) támogatja. Mielőtt a következő lekérdezések bármelyikét végrehajtaná, ellenőrizze, hogy a környezet készen áll-e. A kiválasztott parancshéj környezet telepítéséhez és ellenőrzéséhez lásd: [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) és [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="a-namecount-resourcescount-azure-resources"></a>@no__t – Azure-erőforrások 0Count

Ez a lekérdezés az Ön által elérhető előfizetésekben lévő Azure-erőforrások számát adja vissza. Ez a lekérdezés emellett annak ellenőrzésére is jól használható, hogy a kiválasztott parancshéj rendelkezik-e a megfelelő, telepített és működőképes Azure Resource Graph-összetevőkkel.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a>@no__t 0List-erőforrások név szerint rendezve

Ez a lekérdezés bármilyen típusú erőforrást vissza tud adni, de csak a **név**, **típus** és **hely** tulajdonságokkal. Az `order by` paranccsal rendezi a tulajdonságokat a **név** tulajdonság alapján növekvő (`asc`) sorrendben.

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a>@no__t – az összes virtuális gép 0Show sorrendben rendezve

Ha csak a (`Microsoft.Compute/virtualMachines` típusú) virtuális gépeket kívánja listázni, megfeleltetheti a **típus** tulajdonságot az eredmények között. Az előző lekérdezéshez hasonlóan a `desc` paraméter az `order by` paramétert csökkenő sorrendre módosítja. A típusegyezésben megadott `=~` esetén a Resource Graph megkülönbözteti a kis- és a nagybetűt.

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a>@no__t – 0Show első öt virtuális gép neve és az operációs rendszer típusa szerint

Ez a lekérdezés a `top` paramétert használja ahhoz, hogy a feltételeknek megfelelőkből csak öt rekordot kérjen le, amelyek név szerint vannak rendezve. Az Azure-erőforrás típusa `Microsoft.Compute/virtualMachines`. A `project` adja meg az Azure Resource Graph-nak, hogy a lekérdezés mely tulajdonságokat tartalmazza.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Count virtuális gépek operációsrendszer-típus szerint

Az előző lekérdezésre épülve a listánk továbbra is a `Microsoft.Compute/virtualMachines` típusú Azure-erőforrásokra korlátozódik, viszont a visszaadott rekordok száma szerint már nincs szűrve.
Ehelyett a `summarize` és a `count()` paramétert használtuk annak meghatározásához, hogyan csoportosítsa és összesítse az értékeket tulajdonság alapján, amely ebben a példában a `properties.storageProfile.osDisk.osType`. Az alábbi helyen talál példát arra, hogy hogyan néz ki ez a sztring a teljes objektumban: [erőforrások felfedezése – virtuális gépek felderítése](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Egy másik módszer ugyanazon lekérdezés írásához, ha bővít (`extend`) egy tulajdonságot, és ad neki egy ideiglenes nevet a lekérdezésben való használathoz, ebben az esetben ez az **os**. Ekkor a `summarize` és a `count()` az **os**-t használja az előző példában látható módon.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Vegye figyelembe, hogy míg az `=~` paraméter lehetővé teszi a kis- és nagybetűk megkülönböztetése nélküli lekérdezést, ha tulajdonságokat (például **properties.storageProfile.osDisk.osType**) használ a lekérdezésben, a kis- és nagybetűt helyesen kell megadni. Ha a tulajdonság nevében helytelen a kis- és nagybetűk használata, attól még adhat vissza értéket, viszont a csoportosítás vagy az összegzés helytelen lehet.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a>@no__t – tárolót tartalmazó 0Show-erőforrások

Ahelyett, hogy explicit módon meghatározná a típust az egyező találatok lekéréséhez, ez a példaként megadott lekérdezés bármely Azure-erőforrást megtalálja, amely tartalmazza (`contains`) a **storage** szót.

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a>@no__t – az összes nyilvános IP-cím 0List

Az előző lekérdezéshez hasonlóan minden olyan elemet megtalál, amelynek a típusa tartalmazza a **publicIPAddresses** sztringet.
Ez a lekérdezés csak olyan eredményeket tartalmaz, amelyekben a **Properties. ip_cím**
 @ no__t-2, csak a **Properties. IP_cím**értéket adja vissza, és az eredményeket felülről `limit` értékre.
100. A kiválasztott parancshéjtól függően szükség lehet az idézőjelek escape-elésére.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a>@no__t – az előfizetés által konfigurált IP-címmel rendelkező 0Count-erőforrások

Ha maradunk az előző lekérdezéspéldánál, és hozzáadjuk a `summarize` és a `count()` paramétert, megkaphatjuk a konfigurált IP-címekkel rendelkező erőforrások előfizetés szerinti listáját.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a>@no__t 0List-erőforrások egy adott címke értékkel

Az eredményeket az Azure-erőforrás típusán kívül más tulajdonságok, pl. a címke alapján is korlátozhatjuk. Ebben a példában azokra az **Environment** címkenévvel rendelkező Azure-erőforrásokra szűrünk, amelyek értéke **Internal**.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Az erőforrás címkéinek és a hozzájuk tartozó értékek megadásához adja hozzá a **tags** tulajdonságot a `project` kulcsszóhoz.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a>@no__t – az összes 0List megadott címke értékű

Kombinálhatja az előző példa szűrőfunkcióját, és **type** tulajdonság alapján szűrheti az Azure-erőforrásokat. Ez a lekérdezés az Azure-erőforrások adott típusainak keresését is adott címkenévre és -értékre korlátozza.

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Ez példa az egyező találatok kereséséhez az `==` paramétert használja az `=~` feltételes helyett. Az `==` kis- és nagybetűket megkülönböztető találatot ad.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a>@no__t – 0Show-aliasok a virtuális gép erőforrásaihoz

A Azure Policy az [Azure Policy aliasokat](../../policy/concepts/definition-structure.md#aliases) használja az erőforrások megfelelőségének kezeléséhez. Az Azure Resource Graph egy erőforrástípus _aliasneveit_ is visszaállíthatja. Ezek az értékek hasznosak az aliasok aktuális értékének összehasonlításához az egyéni házirend-definíció létrehozásakor. Az _aliasok_ tömbje alapértelmezés szerint nincs megadva a lekérdezés eredményeiben. A `project aliases` használatával explicit módon adhatja hozzá az eredményekhez.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a>@no__t – egy adott alias 0Show egyedi értékei

Az aliasok értékének egyetlen erőforráson való megjelenítése hasznos lehet, de nem jeleníti meg a valódi értéket az Azure Resource Graph használatával az előfizetések közötti lekérdezéshez. Ez a példa egy adott alias összes értékét áttekinti, és a különböző értékeket adja vissza.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>További lépések

- További információk a [lekérdezés nyelvéről](../concepts/query-language.md)
- Információk az [erőforrások felfedezéséről](../concepts/explore-resources.md)
- Tekintse meg a [Speciális lekérdezések](advanced.md) példáit
