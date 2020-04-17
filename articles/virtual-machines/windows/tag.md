---
title: Windows virtuálisgép-erőforrások címkézése az Azure-ban
description: Tudnivalók az Azure-ban létrehozott Windows virtuális gépek címkézéséről az Erőforrás-kezelő telepítési modelljével
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 8f95c11f93ca2075eb2472ad5bb7360df7d69234
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456447"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Windows-virtuális gépek címkézése az Azure-ban
Ez a cikk az Azure-beli Windows-virtuális gépek címkézésének különböző módjait ismerteti az Erőforrás-kezelő telepítési modelljén keresztül. A címkék a felhasználó által definiált kulcs-/értékpárok, amelyek közvetlenül egy erőforrásra vagy erőforráscsoportra helyezhetők. Az Azure jelenleg erőforrás- és erőforráscsoportonként legfeljebb 50 címkét támogat. A címkék elhelyezhetők egy erőforráson a létrehozás időpontjában, vagy hozzáadhatók egy meglévő erőforráshoz. Vegye figyelembe, hogy a címkék csak az Erőforrás-kezelő telepítési modellen keresztül létrehozott erőforrások támogatottak. Ha egy Linux-virtuális gépet szeretne címkézni, olvassa el [A Linux virtuális gépek címkézése az Azure-ban című témakört.](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Címkézés a PowerShell használatával
Címkék létrehozásához, hozzáadásához és törléséhez a PowerShellen keresztül először be kell állítania a [PowerShell-környezetet][PowerShell environment with Azure Resource Manager]az Azure Resource Manager rel. Miután befejezte a telepítést, címkéket helyezhet el a számítási, hálózati és tárolási erőforrások létrehozásakor vagy az erőforrás létrehozása után a PowerShell en keresztül. Ez a cikk a virtuális gépeken elhelyezett címkék megtekintésére/szerkesztésére összpontosít.

 

Először keresse meg a `Get-AzVM` virtuális gépet a parancsmagon keresztül.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Ha a virtuális gép már tartalmaz címkéket, akkor az erőforrás összes címkéje megjelenik:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Ha címkéket szeretne hozzáadni a PowerShellen `Set-AzResource` keresztül, használhatja a parancsot. Vegye figyelembe, ha a címkék et a PowerShellen keresztül frissíti, a címkék egészében frissülnek. Ha tehát egy címkét ad hozzá egy olyan erőforráshoz, amely már rendelkezik címkékkel, akkor az erőforrásra helyezni kívánt összes címkét tartalmaznia kell. Az alábbi példa bemutatja, hogyan adhat hozzá további címkéket egy erőforráshoz a PowerShell-parancsmagokon keresztül.

Ez az első parancsmag a *MyTestVM-en* elhelyezett összes címkét `Tags` a *$tags* változóra állítja be a és a `Get-AzResource` tulajdonság használatával.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

A második parancs megjeleníti a megadott változó címkéit.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

A harmadik parancs további címkét ad hozzá a *$tags* változóhoz. Figyelje meg, **+=** hogy az új kulcs/érték párt a *$tags* listához fűzi.

        PS C:\> $tags += @{Location="MyLocation"}

A negyedik parancs az *$tags* változóban definiált összes címkét az adott erőforrásra állítja be. Ebben az esetben ez MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Az ötödik parancs megjeleníti az erőforrás összes címkéit. Amint *láthatja, a Hely* mostantól a *MyLocation* értékkel rendelkező címkeként van definiálva.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Ha többet szeretne megtudni a PowerShellen keresztüli címkézésről, tekintse meg az [Azure Resource Cmdlets című cikket.][Azure Resource Cmdlets]

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>További lépések
* Ha többet szeretne megtudni az Azure-erőforrások címkézéséről, olvassa el az [Azure Resource Manager áttekintése][Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez című témakört.][Using Tags to organize your Azure Resources]
* Ha meg szeretné tekinteni, hogy a címkék hogyan segíthetnek az Azure-erőforrások használatának kezelésében, [olvassa el Az Azure-számla ismertetése][Understanding your Azure Bill] és [a Microsoft Azure-erőforrás-felhasználás elemzéseinek megismerése című témakört.][Gain insights into your Microsoft Azure resource consumption]

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
