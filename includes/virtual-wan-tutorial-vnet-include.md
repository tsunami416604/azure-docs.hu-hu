---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150820"
---
Gyorsan hozhat létre egy Vnetet, nyisson meg egy PowerShell-konzolt, ebben a cikkben kattinthat "Kipróbálom". Állítsa be az értékeket, majd másolja és illessze be a parancsokat a konzolablakba. Az új modul és AzureRM kompatibilitási kapcsolatos további tudnivalókért lásd: [az Azure PowerShell-lel Az új modul bevezetése](/powershell/azure/new-azureps-module-az). Az modul telepítési utasításokért lásd: [Azure PowerShell telepítése](/powershell/azure/install-az-ps).

Mindenképp ellenőrizze, hogy a létrehozott virtuális hálózat címtere nincs-e átfedésben a többi virtuális hálózat címterével, amelyekhez csatlakozni szeretne, vagy a helyszíni hálózat címterével.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ha még nem rendelkezik egy erőforráscsoportot, amelyet használni szeretne, hozzon létre egy újat. Állítsa be a PowerShell-parancsok tükrözi az erőforráscsoport nevét is használni szeretné, majd futtassa a következő parancsmagot:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Állítsa be, amely kompatibilis a környezet VNet létrehozása a PowerShell-parancsokat.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```