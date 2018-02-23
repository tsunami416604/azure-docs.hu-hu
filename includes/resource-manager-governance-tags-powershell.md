---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 87b9bd74fc59c86bf177e45c18bfc4e104d9c996
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
Két címkék hozzáadása egy erőforráscsoport, használja:

```powershell
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Tegyük fel, hogy hozzá szeretne adni egy harmadik címkét. Minden alkalommal, amikor címkével lát el egy erőforrást vagy erőforráscsoportot, felülírja a hozzá tartozó korábbi címkéket. Új címke hozzáadása a meglévő címkék elvesztése nélkül, le kell kérnie a meglévő címkéket, egy új címke hozzáadása, és alkalmazza újra a címkék a gyűjteményben:

```powershell
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags += @{Project="Documentation"}
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

Erőforrások címkék nem örökli az erőforráscsoportot. Jelenleg az erőforráscsoport három címkék rendelkezik, de az erőforrások nem rendelkeznek címkéket. Címkéket az összes erőforráscsoport erőforrásaihoz, és tartsa meg az erőforrásokat, amelyek nincsenek ismétlődések meglévő címkék, használja a következő parancsfájlt:

```powershell
$group = Get-AzureRmResourceGroup myResourceGroup
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Alternatív megoldásként címkékkel láthatja az erőforráscsoportból forrásokat tartani a meglévő címkék nélkül:

```powershell
$g = Get-AzureRmResourceGroup -Name myResourceGroup
Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Egyesítése egyetlen címkének értékeket, használja a JSON karakterláncnak.

```powershell
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Távolítsa el az összes címkék, egy üres kivonattábla át.

```powershell
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
