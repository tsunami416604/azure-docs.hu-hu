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
ms.openlocfilehash: 40ae634897361219c39e60d2161d3576cc44a400
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077524"
---
Gyorsan hozhat létre egy Vnetet, ebben a cikkben egy PowerShell-konzol megnyitásához az Azure Cloud Shellben kattinthat "Kipróbálom". Állítsa be az értékeket, majd másolja és illessze be a parancsokat a konzolablakba. 

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
