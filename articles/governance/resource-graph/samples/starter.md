---
title: Alapszintű Azure Resource Graph-lekérdezések
description: Az Azure Resource Graph-fal bizonyos alapszintű lekérdezéseket futtathat.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bcc6c6e9d38948cb4be09c9f52049790ccd5ac7e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227027"
---
# <a name="starter-resource-graph-queries"></a>Alapszintű Resource Graph-lekérdezések

Az Azure Resource Graph-fal végzett lekérdezések megértéséhez először a [lekérdezés nyelvét](../concepts/query-language.md) kell alapszinten megismernie. Ha még nem ismeri az [Azure Data Explorert](../../../data-explorer/data-explorer-overview.md), javasoljuk, hogy a keresett erőforrásokra vonatkozó kérések összeállításának megértéséhez tekintse át az alapokat.

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

## <a name="language-support"></a>Nyelvi támogatás

Az Azure Resource Graph-ot az Azure CLI (bővítményen keresztül) és az Azure PowerShell (modulon keresztül) támogatja. Mielőtt a következő lekérdezések bármelyikét végrehajtaná, ellenőrizze, hogy a környezet készen áll-e. A kiválasztott parancshéj környezet telepítéséhez és ellenőrzéséhez lásd: [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) és [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="count-resources"></a>Az Azure-erőforrások száma

Ez a lekérdezés az Ön által elérhető előfizetésekben lévő Azure-erőforrások számát adja vissza. Ez a lekérdezés emellett annak ellenőrzésére is jól használható, hogy a kiválasztott parancshéj rendelkezik-e a megfelelő, működő sorrendben telepített Azure Resource Graph-összetevőkkel.

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```powershell
Search-AzureRmGraph -Query "summarize count()"
```

## <a name="list-resources"></a>Az erőforrások listája név szerint rendezve

Ez a lekérdezés bármely erőforrástípusra vagy adott egyező tulajdonságra való korlátozás nélkül csak az Azure-erőforrások **nevét**, **típusát** és **helyét** adja vissza, de használhatja az `order by` paramétert a **név** tulajdonság szerinti növekvő (`asc`) sorrendű rendezésükhöz.

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>Az összes virtuális gép megjelenítése név szerint rendezve, csökkenő sorrendben

Ha az összes Azure-erőforrás listája helyett csak a virtuális gépek listáját szeretnénk megkapni (amelyek típusa `Microsoft.Compute/virtualMachines`), a **típus** tulajdonság egyezését is megadhatjuk az eredményekhez.
Az előző lekérdezéshez hasonlóan a `desc` paraméter az `order by` paramétert csökkenő sorrendre módosítja. A típusegyezésben megadott `=~` esetén a Resource Graph megkülönbözteti a kis- és a nagybetűt.

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```powershell
Search-AzureRmGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
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

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
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

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
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

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
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

```powershell
Search-AzureRmGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>Az összes nyilvános IP-cím listázása

Az előző lekérdezéshez hasonlóan minden olyan elemet megtalál, amelynek a típusa tartalmazza a **publicIPAddresses** szót. Ez a lekérdezés úgy bővül ki, hogy kizárja azokat az eredményeket, amelyeknél a **properties.ipAddress** értéke null, hogy csak a **properties.ipAddress** értékkel rendelkezőket adja vissza, és az eredményeket az első 100-ra korlátozza (`limit`). A kiválasztott parancshéjtól függően szükség lehet az idézőjelek escape-elésére.

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```powershell
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
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

```powershell
Search-AzureRmGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
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

```powershell
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name"
```

Ha annak megadására is szükség volt, hogy az erőforrás milyen címkékkel rendelkezik, és melyek ezek értékei, ez a példa kibővíthető úgy, hogy hozzáadjuk a **tags** tulajdonságot a `project` kulcsszóhoz.

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```powershell
Search-AzureRmGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>Az adott címkeértékkel rendelkező összes tárfiók listázása

Ha az előző példában szereplő szűrési lehetőséget kombináljuk az Azure-erőforrás típusa szerinti szűréssel a **type** tulajdonság használatával, a keresést adott címkenévvel és értékkel rendelkező, adott típusú Azure-erőforrásokra korlátozhatjuk.

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Ez példa az egyező találatok kereséséhez az `==` paramétert használja az `=~` feltételes helyett. Az `==` kis- és nagybetűket megkülönböztető találatot ad.

## <a name="next-steps"></a>További lépések

- További információk a [lekérdezés nyelvéről](../concepts/query-language.md)
- Információk az [erőforrások felfedezéséről](../concepts/explore-resources.md)
- Tekintse meg a [Speciális lekérdezések](advanced.md) példáit