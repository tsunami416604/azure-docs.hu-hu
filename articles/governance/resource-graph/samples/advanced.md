---
title: Speciális lekérdezési minták
description: Néhány speciális lekérdezéseket futtatnia, beleértve a VMSS-kapacitás használt, minden címke listázása az Azure Erőforrás-grafikon és a megfelelő virtuális gépek használata a reguláris kifejezéseket.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 9a243dd236a8c499602a9070a7dd61e69541d58d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256821"
---
# <a name="advanced-resource-graph-queries"></a>Speciális Resource Graph-lekérdezések

Az Azure Resource Graph-fal végzett lekérdezések megértéséhez először a [lekérdezés nyelvét](../concepts/query-language.md) kell alapszinten megismernie. Ha még nem ismeri az [Azure Data Explorert](../../../data-explorer/data-explorer-overview.md), javasoljuk, hogy tekintse át az alapokat a keresett erőforrásokra vonatkozó kérések összeállításának megértéséhez.

A következő speciális lekérdezéseken vezetjük végig:

> [!div class="checklist"]
> - [VMSS-kapacitás és méret](#vmss-capacity)
> - [Az összes tartalmaznak a címkenevek listázása](#list-all-tags)
> - [Reguláris kifejezéssel egyező virtuális gépek](#vm-regex)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Nyelvi támogatás

Az Azure Resource Graph-ot az Azure CLI (bővítményen keresztül) és az Azure PowerShell (modulon keresztül) támogatja. Mielőtt a következő lekérdezések bármelyikét végrehajtaná, ellenőrizze, hogy a környezet készen áll-e. A kiválasztott parancshéj környezet telepítéséhez és ellenőrzéséhez lásd: [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) és [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="vmss-capacity"></a>Virtuálisgép-méretezési csoport kapacitásának és méretének lekérése

Ez a lekérdezés a virtuálisgép-méretezési csoportok erőforrásait keresi meg, és különböző adatokat kér le, többet között a méretezési csoport virtuálisgép-méretét és kapacitását. Ez a lekérdezés a `toint()` függvénnyel képezi le a kapacitást egy számmá, amely így rendezhető lesz. Végül a rendszer egyéni elnevezett tulajdonságokká nevezi át az oszlopokat.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Összes címkenév listázása

Ez a lekérdezés a címkével kezdi, majd felépít egy JSON-objektumot, amely listázza az összes egyedi címkét és annak típusát.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Reguláris kifejezésekkel egyező virtuális gépek

Ez a lekérdezés olyan virtuális gépeket keres, amelyek egyeznek egy [reguláris kifejezéssel](/dotnet/standard/base-types/regular-expression-language-quick-reference) (más néven _regex-szel_).
A **blobnévelőtagjaként \@**  lehetővé teszi számunkra, hogy adja meg a következő reguláris kifejezésre megfelelően, amely `^Contoso(.*)[0-9]+$`. A reguláris kifejezés definíciójának magyarázata:

- `^` -Match a karakterlánc elején kell elindítani.
- `Contoso` – A kis-és nagybetűket karakterlánc.
- `(.*)` -A alkifejezés egyezés:
  - `.` -A (kivéve egy új sor) minden egyes karakterére illeszkedik.
  - `*` – Egyezések előző elemhez nulla vagy több alkalommal.
- `[0-9]` -0 – 9 számokat csoport egyezéssel karakter.
- `+` – Egyezések előző elem egy vagy több alkalommal.
- `$` -Az előző elemhez match a karakterlánc végén kell észlelnie.

A név alapján történő egyezéseket követően a lekérdezés levetíti és ábécé sorrendbe rendezi a neveket.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>További lépések

- Az [Alapszintű lekérdezések](starter.md) példáinak megtekintése
- További információk a [lekérdezés nyelvéről](../concepts/query-language.md)
- Információk az [erőforrások felfedezéséről](../concepts/explore-resources.md)