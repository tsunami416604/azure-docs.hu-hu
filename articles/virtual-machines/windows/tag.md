---
title: A Windows VM-erőforrás címkézése az Azure-ban |} A Microsoft Docs
description: További tudnivalók az Azure Resource Manager-alapú üzemi modellel létrehozott Windows virtuális gép címkézése
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
ms.openlocfilehash: 75a6466578808cb5c0dd8d2e32d9445a6e5a5bf8
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140536"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Az Azure-beli Windows virtuális gép címkézése
Ez a cikk ismerteti a különböző módjait a Windows virtuális gép címkézése Azure-ban a Resource Manager-alapú üzemi modellel. A címkék olyan felhasználó által definiált kulcs/érték párok, amelyeket közvetlenül az erőforrás vagy erőforráscsoport helyezhető. Az Azure jelenleg támogatja az erőforrás és erőforráscsoport legfeljebb 15 címkék. Címkék erőforrás elhelyezve a létrehozásakor vagy hozzáadni egy meglévő erőforrást. Vegye figyelembe, hogy a címkék csak a Resource Manager üzemi modell használatával létrehozott erőforrások támogatottak. Ha szeretne Linux virtuális gép címkézése, [Linux rendszerű virtuális gép címkézése Azure-ban](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Címkézés, a PowerShell-lel
Szeretne létrehozni, hozzáadása és törlése a PowerShell, először meg kell állíthat be címkéket az [az Azure Resource Manager PowerShell környezetet][PowerShell environment with Azure Resource Manager]. Miután végzett a telepítő, elhelyezhet címkék számítási, hálózati és tárolási erőforrások létrehozásakor, vagy a Powershellen keresztül az erőforrás létrehozása után. Ez a cikk megtekintéséhez vagy szerkesztéséhez címkék elhelyezni a virtuális gépek összpontosít.

Először keresse meg a virtuális gép a `Get-AzureRmVM` parancsmagot.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Ha a virtuális gép már tartalmazza a címkék, ekkor megjelenik az összes címke az erőforráson:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Ha szeretné Powershellen keresztül címkéket adhat hozzá, akkor használhatja a `Set-AzureRmResource` parancsot. Megjegyzés: amikor frissíti a PowerShell, a címkék címkéket egészét frissülnek. Ezért egy címke hozzáadásakor a címkékkel rendelkező erőforráshoz, szüksége lesz az erőforrás elhelyezni kívánt összes címkét felvenni. Alább van további címkék hozzáadására a PowerShell parancsmagokon keresztül egy erőforrás egy példát.

Az első parancsmag beállítja elhelyezett címkék *MyTestVM* , a *$tags* változó, használja a `Get-AzureRmResource` és `Tags` tulajdonság.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

A második parancs jeleníti meg a címkéket az adott változó.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

A harmadik parancs hozzáad egy további címkét a *$tags* változó. Vegye figyelembe a használatát a **+=** hozzáfűzni a kulcs-érték párokat a *$tags* listája.

        PS C:\> $tags += @{Location="MyLocation"}

A negyedik parancs beállítja a megadott címkék mindegyikével a *$tags* változó az adott erőforráshoz. Ebben az esetben MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Az ötödik parancs megjeleníti, címkék az erőforráson. Amint láthatja, *hely* most számít, ha a címke *MyLocation* értékeként.

```
    PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Címkézés PowerShell-lel kapcsolatos további információkért tekintse meg a [Azure erőforrás-parancsmagok][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>További lépések
* Az Azure-erőforrások címkézése kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése] [ Azure Resource Manager Overview] és [címkék használatával az Azure-erőforrások rendszerezéséhez] [ Using Tags to organize your Azure Resources].
* Hogyan címkék segít Önnek az Azure-erőforrások kezeléséhez, olvassa el [az Azure-elszámolások ismertetése] [ Understanding your Azure Bill] és [betekintést nyerhet a Microsoft Azure erőforrás-használat] [Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
