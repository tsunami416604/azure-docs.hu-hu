---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552969"
---
Ha még nem rendelkezik virtuális hálózattal, gyorsan létrehozhat egyet a PowerShell használatával. Vagy az Azure Portalon is létrehozhat egy virtuális hálózatot.

* Mindenképp ellenőrizze, hogy a létrehozott virtuális hálózat címtere nincs-e átfedésben a többi virtuális hálózat címterével, amelyekhez csatlakozni szeretne, vagy a helyszíni hálózat címterével. 
* Ha már rendelkezik virtuális hálózattal, győződjön meg arról, hogy megfelel a követelményeknek, és nem rendelkezik virtuális hálózati átjáróval.

A cikkben lévő „Kipróbálás” lehetőségre kattintva megnyíló PowerShell-konzollal könnyen létrehozhat egy virtuális hálózatot. Állítsa be az értékeket, majd másolja és illessze be a parancsokat a konzolablakba.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Módosítsa a PowerShell-parancsokat, majd hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Módosítsa a PowerShell-parancsokat, hogy egy olyan virtuális hálózatot hozzon létre, amely kompatibilis a környezettel.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```