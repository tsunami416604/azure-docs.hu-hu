---
title: Kezdő lekérdezési példák
description: Az Azure Resource Graph használatával néhány kezdő lekérdezést futtathat, beleértve az erőforrások számlálását, az erőforrások megrendelését vagy egy adott címkét.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: c2a8c60502aeb75173371d40475b5d2875417791
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808642"
---
# <a name="starter-resource-graph-queries"></a>Alapszintű Resource Graph-lekérdezések

Az Azure Resource Graph-fal végzett lekérdezések megértéséhez először a [lekérdezés nyelvét](../concepts/query-language.md) kell alapszinten megismernie. Ha még nem ismeri a [Kusto lekérdezési nyelvét (KQL)](/azure/kusto/query/index), javasoljuk, hogy tekintse át a [KQL vonatkozó oktatóanyagot](/azure/kusto/query/tutorial) , és ismerkedjen meg a keresett erőforrásokra vonatkozó kérelmek összeállításával.

A következő alapszintű lekérdezéseken vezetjük végig:

> [!div class="checklist"]
> - [Az Azure-erőforrások száma](#count-resources)
> - [Key Vault-erőforrások száma](#count-keyvaults)
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
> - [Nem társított hálózati biztonsági csoportok megjelenítése](#unassociated-nsgs)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="language-support"></a>Nyelvi támogatás

Az Azure Resource Graph-ot az Azure CLI (bővítményen keresztül) és az Azure PowerShell (modulon keresztül) támogatja. Mielőtt a következő lekérdezések bármelyikét végrehajtaná, ellenőrizze, hogy a környezet készen áll-e. A kiválasztott parancshéj környezet telepítéséhez és ellenőrzéséhez lásd: [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) és [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="a-namecount-resources-count-azure-resources"></a>Azure-erőforrások <a name="count-resources" />száma

Ez a lekérdezés az Ön által elérhető előfizetésekben lévő Azure-erőforrások számát adja vissza. Ez a lekérdezés emellett annak ellenőrzésére is jól használható, hogy a kiválasztott parancshéj rendelkezik-e a megfelelő, telepített és működőképes Azure Resource Graph-összetevőkkel.

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a>a Key Vault-erőforrások <a name="count-keyvaults" />száma

A lekérdezés `count` értéket használ a visszaadott rekordok számának megszámlálásához `summarize` helyett. Csak a Key vaultok szerepelnek a darabszámban.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />az erőforrások listázása név szerint rendezve

Ez a lekérdezés bármilyen típusú erőforrást vissza tud adni, de csak a **név**, **típus** és **hely** tulajdonságokkal. Az `order by` paranccsal rendezi a tulajdonságokat a **név** tulajdonság alapján növekvő (`asc`) sorrendben.

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />a név szerint rendezett összes virtuális gép megjelenítése csökkenő sorrendben

Ha csak a (`Microsoft.Compute/virtualMachines` típusú) virtuális gépeket kívánja listázni, megfeleltetheti a **típus** tulajdonságot az eredmények között. Az előző lekérdezéshez hasonlóan a `desc` paraméter az `order by` paramétert csökkenő sorrendre módosítja. A típusegyezésben megadott `=~` esetén a Resource Graph megkülönbözteti a kis- és a nagybetűt.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />az első öt virtuális gépet a név és az operációs rendszer típusa szerint jeleníti meg

Ez a lekérdezés a `top` paramétert használja ahhoz, hogy a feltételeknek megfelelőkből csak öt rekordot kérjen le, amelyek név szerint vannak rendezve. Az Azure-erőforrás típusa `Microsoft.Compute/virtualMachines`. A `project` adja meg az Azure Resource Graph-nak, hogy a lekérdezés mely tulajdonságokat tartalmazza.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />virtuális gépek száma operációs rendszer típusa szerint

Az előző lekérdezésre épülve a listánk továbbra is a `Microsoft.Compute/virtualMachines` típusú Azure-erőforrásokra korlátozódik, viszont a visszaadott rekordok száma szerint már nincs szűrve.
Ehelyett a `summarize` és a `count()` paramétert használtuk annak meghatározásához, hogyan csoportosítsa és összesítse az értékeket tulajdonság alapján, amely ebben a példában a `properties.storageProfile.osDisk.osType`. Az alábbi helyen talál példát arra, hogy hogyan néz ki ez a sztring a teljes objektumban: [erőforrások felfedezése – virtuális gépek felderítése](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

Egy másik módszer ugyanazon lekérdezés írásához, ha bővít (`extend`) egy tulajdonságot, és ad neki egy ideiglenes nevet a lekérdezésben való használathoz, ebben az esetben ez az **os**. Ekkor a `summarize` és a `count()` az **os**-t használja az előző példában látható módon.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

> [!NOTE]
> Vegye figyelembe, hogy míg az `=~` paraméter lehetővé teszi a kis- és nagybetűk megkülönböztetése nélküli lekérdezést, ha tulajdonságokat (például **properties.storageProfile.osDisk.osType**) használ a lekérdezésben, a kis- és nagybetűt helyesen kell megadni. Ha a tulajdonság nevében helytelen a kis- és nagybetűk használata, attól még adhat vissza értéket, viszont a csoportosítás vagy az összegzés helytelen lehet.

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />tárolót tartalmazó erőforrások megjelenítése

Ahelyett, hogy explicit módon meghatározná a típust az egyező találatok lekéréséhez, ez a példaként megadott lekérdezés bármely Azure-erőforrást megtalálja, amely tartalmazza (`contains`) a **storage** szót.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />az összes nyilvános IP-cím listázása

Az előző lekérdezéshez hasonlóan minden olyan elemet megtalál, amelynek a típusa tartalmazza a **publicIPAddresses** sztringet.
Ez a lekérdezés csak olyan eredményeket tartalmaz, amelyekben a **Properties. ip_cím**
`isnotempty`, hogy csak a **Properties. IP_cím parancsot**adja vissza, és az eredményeket a legfelül `limit`.
100. A kiválasztott parancshéjtól függően szükség lehet az idézőjelek escape-elésére.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a>az előfizetés által konfigurált IP-címmel rendelkező erőforrások <a name="count-resources-by-ip" />száma

Ha maradunk az előző lekérdezéspéldánál, és hozzáadjuk a `summarize` és a `count()` paramétert, megkaphatjuk a konfigurált IP-címekkel rendelkező erőforrások előfizetés szerinti listáját.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a>egy adott címke értékkel rendelkező erőforrások listázása <a name="list-tag" />

Az eredményeket az Azure-erőforrás típusán kívül más tulajdonságok, pl. a címke alapján is korlátozhatjuk. Ebben a példában azokra az **Environment** címkenévvel rendelkező Azure-erőforrásokra szűrünk, amelyek értéke **Internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

Az erőforrás címkéinek és a hozzájuk tartozó értékek megadásához adja hozzá a **tags** tulajdonságot a `project` kulcsszóhoz.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />az összes olyan Storage-fiók listázása, amely adott címke értékkel rendelkezik

Kombinálhatja az előző példa szűrőfunkcióját, és **type** tulajdonság alapján szűrheti az Azure-erőforrásokat. Ez a lekérdezés az Azure-erőforrások adott típusainak keresését is adott címkenévre és -értékre korlátozza.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

> [!NOTE]
> Ez példa az egyező találatok kereséséhez az `==` paramétert használja az `=~` feltételes helyett. Az `==` kis- és nagybetűket megkülönböztető találatot ad.

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />a virtuális gép erőforrásaihoz tartozó aliasok megjelenítése

A Azure Policy az [Azure Policy aliasokat](../../policy/concepts/definition-structure.md#aliases) használja az erőforrások megfelelőségének kezeléséhez. Az Azure Resource Graph egy erőforrástípus _aliasneveit_ is visszaállíthatja. Ezek az értékek hasznosak az aliasok aktuális értékének összehasonlításához az egyéni házirend-definíció létrehozásakor. Az _aliasok_ tömbje alapértelmezés szerint nincs megadva a lekérdezés eredményeiben. A `project aliases` használatával explicit módon adhatja hozzá az eredményekhez.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a>egy adott alias különböző értékeinek megjelenítése <a name="distinct-alias-values" />

Az aliasok értékének egyetlen erőforráson való megjelenítése hasznos lehet, de nem jeleníti meg a valódi értéket az Azure Resource Graph használatával az előfizetések közötti lekérdezéshez. Ez a példa egy adott alias összes értékét áttekinti, és a különböző értékeket adja vissza.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />nem társított hálózati biztonsági csoportok megjelenítése

Ez a lekérdezés olyan hálózati biztonsági csoportokat (NSG) ad vissza, amelyek nincsenek hálózati adapterhez vagy alhálózathoz társítva.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Resource Graph Explorer ikon](../media/resource-graph-small.png) Próbálja ki ezt a lekérdezést az Azure Resource Graph Explorerben:

- Azure Portal: a <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![a hivatkozás megnyitása az új ablakban ikon](../../media/new-window.png)

---

## <a name="next-steps"></a>Következő lépések

- További információk a [lekérdezés nyelvéről](../concepts/query-language.md)
- Információ az [erőforrások felfedezéséről](../concepts/explore-resources.md)
- Tekintse meg a [Speciális lekérdezések](advanced.md) példáit
