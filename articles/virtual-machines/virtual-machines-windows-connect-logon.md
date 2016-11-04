---
title: Csatlakozás egy Windows Server rendszerű virtuális géphez | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat és jelentkezhet be egy Windows virtuális gépre az Azure Portal és a Resource Manager-alapú üzemi modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2016
ms.author: cynthn

---
# Csatlakozás és bejelentkezés Windows rendszert futtató Azure virtuális gépre
Használja az Azure Portal **Csatlakozás** gombját egy távoli asztali (RDP) munkamenet elindításához. Először csatlakozzon a virtuális géphez, majd jelentkezzen be.

## Csatlakozás a virtuális géphez
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Virtuális gépek** elemre.
3. Válassza ki a virtuális gépet a listából.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
   
    ![A virtuális géphez való csatlakozást ismertető képernyőkép az Azure Portalról.](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > Ha a portálon a **Csatlakozás** gomb szürkén jelenik meg, és nem [ExpressRoute](../expressroute/expressroute-introduction.md)- vagy [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)-kapcsolaton keresztül kapcsolódik az Azure-hoz, létre kell hoznia egy nyilvános IP-címet, és hozzá kell rendelnie a virtuális géphez, mielőtt az RDP-t használhatná. További információ: [nyilvános IP-címek az Azure-ban](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## Bejelentkezés a virtuális gépre
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Következő lépések
A csatlakozási kísérlet során felmerülő hibákkal kapcsolatban tekintse meg [Távoli asztali kapcsolatok hibaelhárítása](virtual-machines-windows-troubleshoot-rdp-connection.md) című témakört. Ez a cikk útmutatást nyújt a gyakori problémák diagnosztizálásához és elhárításához.

<!--HONumber=Sep16_HO4-->


