---
title: Útmutató a Windows virtuális gép erőforrásához címke az Azure-ban |} Microsoft Docs
description: További tudnivalók az Azure-ban a Resource Manager üzembe helyezési modellel létrehozott Windows virtuális gépek címkézés
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917499"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Útmutató a Windows rendszerű virtuális gép címke az Azure-ban
Ez a cikk ismerteti a Windows rendszerű virtuális gép címke az Azure-ban a Resource Manager üzembe helyezési modellel keresztül különböző módjai. Címke található a felhasználó által definiált kulcs/érték párok, amely lehet tenni közvetlenül egy erőforrás vagy egy erőforráscsoportot. Azure jelenleg legfeljebb 15 címkék erőforrás pedig erőforráscsoportban. Címkék erőforrás létrehozása idején helyezni vagy hozzáadni egy meglévő erőforrást. Vegye figyelembe, hogy csak a Resource Manager üzembe helyezési modellel létrehozott erőforrások címkék használhatók. Ha szeretne egy Linux virtuális gép címkét, lásd: [hogyan Linux virtuális gépek címke az Azure-ban](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>A PowerShell-címkézés
Szeretne létrehozni, adja hozzá, és a PowerShell segítségével, először meg kell beállítása címkék törlése a [PowerShell-környezet az Azure Resource Manager][PowerShell environment with Azure Resource Manager]. A telepítés befejezése után a számítási, hálózati és tárolási erőforrásokat a létrehozásakor vagy az erőforrás PowerShell létrehozása után elhelyezheti címkék. Ez a cikk megtekintéséhez vagy szerkesztéséhez címkéket a virtuális gépek összpontosít.

Először keresse meg a virtuális gép keresztül a `Get-AzureRmVM` parancsmag.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Ha a virtuális gép már tartalmazza a címkéket, az erőforrás majd jelenik meg a címkéket:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Ha szeretné a címkéket a PowerShell segítségével, használhatja a `Set-AzureRmResource` parancsot. Vegye figyelembe a PowerShell segítségével, címkék frissítésekor címkék egész frissülnek. Ezért egy címke, amely már a címkék erőforrás kíván hozzáadni, ha szüksége lesz az erőforrás elhelyezni kívánt címkéket tartalmazza. Alább van további címkék hozzáadására egy PowerShell-parancsmagokkal erőforrás példát.

Az első parancsmag beállítja a címkék helyezve *MyTestVM* számára a *$tags* változó, használja a `Get-AzureRmResource` és `Tags` tulajdonság.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

A második parancs megjeleníti az adott változó címkék.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

A harmadik parancs hozzáadja egy további címkét a *$tags* változó. Vegye figyelembe a használatát a **+=** fűzhető hozzá az új kulcs/érték pár, hogy a *$tags* listája.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

A negyedik parancs beállítja a meghatározott címkék a *$tags* változó az adott erőforráshoz. Ebben az esetben is MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Az ötödik parancs megjeleníti a címkék az erőforráson. Ahogy látja, *hely* most egy címkét a típusúként van definiálva *MyLocation* értékeként.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

A PowerShell segítségével címkézés kapcsolatos további tudnivalókért tekintse meg a [Azure Resource parancsmagok][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>További lépések
* Az Azure-erőforrások címkézés kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése] [ Azure Resource Manager Overview] és [címkék használata az Azure-erőforrások rendszerezéséhez][Using Tags to organize your Azure Resources].
* Hogyan címkék segíthetnek az Azure-erőforrások kezeléséhez, olvassa el [ismertetése a Azure számlázási] [ Understanding your Azure Bill] és [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
