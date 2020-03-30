---
title: Globális virtuális hálózat-társviszony-létesítés konfigurálása az Azure Virtual WAN-hoz | Microsoft dokumentumok
description: Csatlakoztasson egy virtuális hálózatot egy másik régióban a virtuális WAN-központhoz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588225"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Globális virtuális hálózat-társviszony-létesítés (régióközi virtuális hálózat) konfigurálása virtuális WAN-hoz

Egy másik régióban lévő virtuális hálózatot csatlakoztathat a virtuális WAN-központhoz.

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy teljesítette-e az alábbi feltételeket:

* A régióközi virtuális hálózat (küllős) nem csatlakozik egy másik Virtual WAN hub. A küllő csak egy virtuális hubhoz csatlakoztatható.
* A virtuális hálózat (küllős) nem tartalmaz virtuális hálózati átjárót (például egy Azure VPN-átjárót vagy egy ExpressRoute virtuális hálózati átjárót). Ha a virtuális hálózat virtuális hálózati átjárót tartalmaz, el kell távolítania az átjárót, mielőtt csatlakoztatja a küllővirtuális hálózatot a hubhoz.

## <a name="register-this-feature"></a><a name="register"></a>A funkció regisztrálása

Ehhez a funkcióhoz a PowerShell használatával regisztrálhat. Ha az alábbi példában a "Try It" lehetőséget választja, megnyílik az Azure Cloud-Shell, és nem kell helyileg telepítenie a PowerShell-parancsmagokat a számítógépre. Szükség esetén módosíthatja az előfizetéseket a "Select-AzSubscription <subid>-SubscriptionId" parancsmag használatával.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Regisztráció ellenőrzése

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Virtuális hálózat csatlakoztatása a hubhoz

Ebben a lépésben hozza létre a társviszony-létesítési kapcsolatot a hub és a régiók közötti virtuális hálózat között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A társviszony-kapcsolat létrehozásához kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Virtual WAN-ról, olvassa el a [Virtuális WAN – áttekintés című témakört.](virtual-wan-about.md)