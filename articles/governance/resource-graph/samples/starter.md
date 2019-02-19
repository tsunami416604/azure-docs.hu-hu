---
title: Alapszintű lekérdezési minták
description: Használja az Azure Erőforrás-grafikon futtatása néhány alapszintű lekérdezi, számlálási erőforrások rendezése erőforrásokat, beleértve, vagy egy adott címkével.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fd945b5fd9f26cc65c5b049406831228a3d5f327
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338716"
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

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Nyelvi támogatás

Az Azure Resource Graph-ot az Azure CLI (bővítményen keresztül) és az Azure PowerShell (modulon keresztül) támogatja. Mielőtt a következő lekérdezések bármelyikét végrehajtaná, ellenőrizze, hogy a környezet készen áll-e. A kiválasztott parancshéj környezet telepítéséhez és ellenőrzéséhez lásd: [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) és [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="count-resources"></a>Az Azure-erőforrások száma

Ez a lekérdezés az Ön által elérhető előfizetésekben lévő Azure-erőforrások számát adja vissza. Ez a lekérdezés emellett annak ellenőrzésére is jól használható, hogy a kiválasztott parancshéj rendelkezik-e a megfelelő, telepített és működőképes Azure Resource Graph-összetevőkkel.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Az erőforrások listája név szerint rendezve

Ez a lekérdezés bármilyen típusú erőforrást vissza tud adni, de csak a **név**, **típus** és **hely** tulajdonságokkal. Az `order by` paranccsal rendezi a tulajdonságokat a **név** tulajdonság alapján növekvő (`asc`) sorrendben.

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Az összes virtuális gép megjelenítése név szerint rendezve, csökkenő sorrendben

Ha csak a (`Microsoft.Compute/virtualMachines` típusú) virtuális gépeket kívánja listázni, megfeleltetheti a **típus** tulajdonságot az eredmények között. Az előző lekérdezéshez hasonlóan a `desc` paraméter az `order by` paramétert csökkenő sorrendre módosítja. A típusegyezésben megadott `=~` esetén a Resource Graph megkülönbözteti a kis- és a nagybetűt.

```Query
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

## <a name="show-sorted"></a>Az első öt virtuális gép megjelenítése a nevük és az operációs rendszerük típusa szerint

Ez a lekérdezés a `limit` paramétert használja ahhoz, hogy a feltételeknek megfelelőkből csak öt rekordot kérjen le, amelyek név szerint vannak rendezve. Az Azure-erőforrás típusa `Microsoft.Compute/virtualMachines`. A `project` adja meg az Azure Resource Graph-nak, hogy a lekérdezés mely tulajdonságokat tartalmazza.

```Query
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

## <a name="count-os"></a>A virtuális gépek száma az operációs rendszer típusa szerint

Az előző lekérdezésre épülve a listánk továbbra is a `Microsoft.Compute/virtualMachines` típusú Azure-erőforrásokra korlátozódik, viszont a visszaadott rekordok száma szerint már nincs szűrve.
Ehelyett a `summarize` és a `count()` paramétert használtuk annak meghatározásához, hogyan csoportosítsa és összesítse az értékeket tulajdonság alapján, amely ebben a példában a `properties.storageProfile.osDisk.osType`. Az alábbi helyen talál példát arra, hogy hogyan néz ki ez a karakterlánc a teljes objektumban: [erőforrások felfedezése – virtuális gépek felderítése](../concepts/explore-resources.md#virtual-machine-discovery).

```Query
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

```Query
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

## <a name="show-storage"></a>A storage szót tartalmazó erőforrások megjelenítése

Ahelyett, hogy explicit módon meghatározná a típust az egyező találatok lekéréséhez, ez a példaként megadott lekérdezés bármely Azure-erőforrást megtalálja, amely tartalmazza (`contains`) a **storage** szót.

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Az összes nyilvános IP-cím listázása

Az előző lekérdezéshez hasonlóan minden olyan elemet megtalál, amelynek a típusa tartalmazza a **publicIPAddresses** sztringet.
Ez a lekérdezés úgy bővül ki, hogy kizárja azokat az eredményeket, amelyeknél a **properties.ipAddress** értéke null, hogy csak a **properties.ipAddress** értékkel rendelkezőket adja vissza, és az eredményeket az első 100-ra korlátozza (`limit`). A kiválasztott parancshéjtól függően szükség lehet az idézőjelek escape-elésére.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>A konfigurált IP-címekkel rendelkező erőforrások száma előfizetés szerint csoportosítva

Ha maradunk az előző lekérdezéspéldánál, és hozzáadjuk a `summarize` és a `count()` paramétert, megkaphatjuk a konfigurált IP-címekkel rendelkező erőforrások előfizetés szerinti listáját.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>Az adott címkeértékkel rendelkező erőforrások listázása

Az eredményeket az Azure-erőforrás típusán kívül más tulajdonságok, pl. a címke alapján is korlátozhatjuk. Ebben a példában azokra az **Environment** címkenévvel rendelkező Azure-erőforrásokra szűrünk, amelyek értéke **Internal**.

```Query
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

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Az adott címkeértékkel rendelkező összes tárfiók listázása

Kombinálhatja az előző példa szűrőfunkcióját, és **type** tulajdonság alapján szűrheti az Azure-erőforrásokat. Ez a lekérdezés az Azure-erőforrások adott típusainak keresését is adott címkenévre és -értékre korlátozza.

```Query
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

## <a name="next-steps"></a>További lépések

- További információk a [lekérdezés nyelvéről](../concepts/query-language.md)
- Információk az [erőforrások felfedezéséről](../concepts/explore-resources.md)
- Tekintse meg a [Speciális lekérdezések](advanced.md) példáit