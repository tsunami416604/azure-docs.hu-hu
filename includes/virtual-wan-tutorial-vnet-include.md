---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004168"
---
Ha még nem rendelkezik virtuális hálózattal, gyorsan létrehozhat egyet a PowerShell használatával. Vagy az Azure Portalon is létrehozhat egy virtuális hálózatot.

* Mindenképp ellenőrizze, hogy a létrehozott virtuális hálózat címtere nincs-e átfedésben a többi virtuális hálózat címterével, amelyekhez csatlakozni szeretne, vagy a helyszíni hálózat címterével. 
* Ha már rendelkezik virtuális hálózattal, győződjön meg arról, hogy megfelel a követelményeknek, és nem rendelkezik virtuális hálózati átjáróval.

A cikkben lévő „Kipróbálás” lehetőségre kattintva megnyíló PowerShell-konzollal könnyen létrehozhat egy virtuális hálózatot. Állítsa be az értékeket, majd másolja és illessze be a parancsokat a konzolablakba.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Módosítsa a PowerShell-parancsokat, majd hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Módosítsa a PowerShell-parancsokat, hogy egy olyan virtuális hálózatot hozzon létre, amely kompatibilis a környezettel.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```