---
title: Globális VNet-társítás konfigurálása Azure-beli virtuális WAN-hoz | Microsoft Docs
description: Csatlakoztasson egy VNet egy másik régióban a virtuális WAN-hubhoz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 78c06ec1d93dcda5d171099943c287a9e4f43bc1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750565"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Globális VNet-társítás konfigurálása (régiók közötti VNet) virtuális WAN-hoz

Egy VNet egy másik régióban is csatlakoztatható a virtuális WAN-hubhoz.

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy teljesültek-e az alábbi feltételek:

* A régiók közötti VNet (küllő) nincs csatlakoztatva egy másik virtuális WAN-hubhoz. Küllő csak egyetlen virtuális hubhoz csatlakoztatható.
* A VNet (küllő) nem tartalmaz virtuális hálózati átjárót (például Azure VPN Gateway vagy ExpressRoute virtuális hálózati átjárót). Ha a VNet virtuális hálózati átjárót tartalmaz, el kell távolítania az átjárót, mielőtt csatlakoztatná a küllős VNet a hubhoz.

## <a name="register-this-feature"></a><a name="register"></a>A funkció regisztrálása

Ezt a funkciót a PowerShell használatával lehet regisztrálni. Ha az alábbi példában a "kipróbálás" lehetőséget választja, megnyílik az Azure Cloud-shell, és nem kell helyileg telepítenie a PowerShell-parancsmagokat a számítógépre. Ha szükséges, módosíthatja az előfizetéseket a "Select-AzSubscription-SubscriptionId <subid> " parancsmag használatával.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Regisztráció ellenőrzése

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>VNet összekötése a hubhoz

Ebben a lépésben létrehozza a köztes kapcsolatot a központ és a régiók közötti VNet között. Ismételje meg a fenti lépéseket minden csatlakoztatni kívánt virtuális hálózat esetében.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A társviszony-kapcsolat létrehozásához kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

További információ a virtuális WAN-ról: [virtuális WAN – áttekintés](virtual-wan-about.md).