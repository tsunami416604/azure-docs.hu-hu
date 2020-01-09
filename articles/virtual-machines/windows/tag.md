---
title: Windows rendszerű virtuális gépek erőforrásának címkézése az Azure-ban
description: Tudnivalók az Azure-ban létrehozott Windows rendszerű virtuális gépek a Resource Manager-alapú üzemi modellel történő címkézéséről
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b17a126ac870e96c79c4c391f5f8381ce70f8d41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462293"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Windows rendszerű virtuális gép címkézése az Azure-ban
Ez a cikk az Azure-beli Windows rendszerű virtuális gépek Resource Manager-alapú üzemi modellel történő címkézésének különböző módszereit ismerteti. A címkék felhasználó által definiált kulcs/érték párok, amelyek közvetlenül egy erőforráson vagy erőforráscsoporton helyezhetők el. Az Azure jelenleg legfeljebb 15 címkét támogat erőforrás és erőforráscsoport szerint. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor lehet elhelyezni egy erőforráson. Vegye figyelembe, hogy a csak a Resource Manager-alapú üzemi modellen keresztül létrehozott erőforrások esetében támogatott a címkék használata. Ha Linux rendszerű virtuális gépet szeretne címkézni, tekintse meg [a Linux rendszerű virtuális gép címkézése az Azure-ban](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakört.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Címkézés a PowerShell-lel
Címkék a PowerShell használatával történő létrehozásához, hozzáadásához és törléséhez először be kell állítania a [PowerShell-környezetet Azure Resource Manager][PowerShell environment with Azure Resource Manager]használatával. A telepítés befejezése után címkéket helyezhet el a számítási, hálózati és tárolási erőforrásokon a létrehozáskor vagy az erőforrás PowerShell használatával történő létrehozása után. Ez a cikk a Virtual Machinesre helyezett címkék megtekintésére és szerkesztésére koncentrál.

 

Először navigáljon a virtuális géphez a `Get-AzVM` parancsmaggal.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Ha a virtuális gép már tartalmaz címkéket, akkor az összes címkét látni fogja az erőforráson:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Ha a PowerShell használatával szeretne címkéket felvenni, használja a `Set-AzResource` parancsot. Megjegyzés a címkék PowerShell használatával történő frissítésekor a címkék teljes egészében frissülnek. Tehát ha olyan erőforráshoz ad hozzá egy címkét, amely már rendelkezik címkékkel, akkor meg kell adnia az erőforráson elhelyezni kívánt címkéket is. Az alábbi példa bemutatja, hogyan adhat hozzá további címkéket egy erőforráshoz a PowerShell-parancsmagok használatával.

Ez az első parancsmag a *MyTestVM* -ra helyezett összes címkét a *$Tags* változóra állítja be a `Get-AzResource` és a `Tags` tulajdonság használatával.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

A második parancs az adott változó címkéit jeleníti meg.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

A harmadik parancs egy további címkét szúr be a *$Tags* változóba. Figyelje meg, hogy a **+=** használja az új kulcs/érték párok hozzáfűzésére a *$Tags* listához.

        PS C:\> $tags += @{Location="MyLocation"}

A negyedik parancs a *$Tags* változóban meghatározott összes címkét az adott erőforrásra állítja be. Ebben az esetben ez a MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Az ötödik parancs megjeleníti az erőforrás összes címkéjét. Amint láthatja, a *Location (hely* ) mostantól a *MyLocation* értékű címkeként van definiálva.

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

Ha többet szeretne megtudni a PowerShell használatával történő címkézésről, tekintse meg az [Azure Resource parancsmagokat][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Következő lépések
* Az Azure-erőforrások címkézésével kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése][Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez][Using Tags to organize your Azure Resources].
* Ha szeretné megtekinteni, hogyan segíthetnek az Azure-erőforrások használatának kezelésében, tekintse meg [Az Azure-számla megismerése][Understanding your Azure Bill] és [a Microsoft Azure erőforrás-felhasználás betekintését][Gain insights into your Microsoft Azure resource consumption]ismertető témakört.

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
