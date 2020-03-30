---
title: Virtuális hálózat nem törölhető az Azure-ban | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el azt a problémát, amely nek nem lehet törölnie egy virtuális hálózatot az Azure-ban.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056930"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Hibaelhárítás: Nem sikerült törölni egy virtuális hálózatot az Azure-ban

Előfordulhat, hogy hibaüzenetet kap, amikor megpróbál törölni egy virtuális hálózatot a Microsoft Azure-ban. Ez a cikk hibaelhárítási lépéseket tartalmaz a probléma megoldásához. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Ellenőrizze, hogy fut-e virtuális hálózati átjáró a virtuális hálózatban](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Ellenőrizze, hogy fut-e alkalmazásátjáró a virtuális hálózatban](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Ellenőrizze, hogy az Azure Active Directory tartományi szolgáltatás engedélyezve van-e a virtuális hálózatban](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Ellenőrizze, hogy a virtuális hálózat más erőforráshoz csatlakozik-e.](#check-whether-the-virtual-network-is-connected-to-other-resource)
5. [Ellenőrizze, hogy a virtuális gép továbbra is fut-e a virtuális hálózatban](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Ellenőrizze, hogy a virtuális hálózat beragadt-e az áttelepítésbe](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Annak ellenőrzése, hogy fut-e virtuális hálózati átjáró a virtuális hálózatban

A virtuális hálózat eltávolításához először el kell távolítania a virtuális hálózati átjárót.

A klasszikus virtuális hálózatok, keresse fel a klasszikus virtuális hálózat **áttekintése** az Azure Portalon. Ha a **VPN-kapcsolatok** szakaszban az átjáró a virtuális hálózatban fut, látni fogja az átjáró IP-címét. 

![Annak ellenőrzése, hogy fut-e az átjáró](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Virtuális hálózatok esetén nyissa meg a virtuális hálózat **Áttekintés lapját.** Ellenőrizze **a csatlakoztatott eszközök a** virtuális hálózati átjáró.

![A csatlakoztatott eszköz ellenőrzése](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Az átjáró eltávolítása előtt először **Connection** távolítsa el az átjáróban lévő kapcsolatobjektumokat. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Annak ellenőrzése, hogy fut-e alkalmazásátjáró a virtuális hálózatban

Nyissa meg a virtuális hálózat **Áttekintés** lapját. Ellenőrizze a **csatlakoztatott eszközök** az alkalmazás átjáró.

![A csatlakoztatott eszköz ellenőrzése](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Ha van alkalmazásátjáró, el kell távolítania azt, mielőtt törölheti a virtuális hálózatot.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Annak ellenőrzése, hogy engedélyezve van-e az Azure Active Directory tartományi szolgáltatás a virtuális hálózatban

Ha az Active Directory tartományi szolgáltatás engedélyezve van, és csatlakozik a virtuális hálózathoz, nem törölheti ezt a virtuális hálózatot. 

![A csatlakoztatott eszköz ellenőrzése](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

A szolgáltatás letiltásához olvassa el az [Azure Active Directory tartományi szolgáltatások letiltása az Azure Portal használatával című témakört.](../active-directory-domain-services/delete-aadds.md)

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Annak ellenőrzése, hogy a virtuális hálózat más erőforráshoz csatlakozik-e

Ellenőrizze az áramköri kapcsolatokat, a kapcsolatokat és a virtuális hálózati társviszony-létesítéseket. Ezek bármelyike a virtuális hálózat törlésének sikertelensülését okozhatja. 

Az ajánlott törlési sorrend a következő:

1. Átjárókapcsolatok
2. Átjárók
3. Ips
4. Virtuális hálózati társviszony-létesítések
5. App Service-környezet (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Annak ellenőrzése, hogy a virtuális gép továbbra is fut-e a virtuális hálózatban

Győződjön meg arról, hogy nincs virtuális gép a virtuális hálózatban.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Annak ellenőrzése, hogy a virtuális hálózat beragadt-e az áttelepítésbe

Ha a virtuális hálózat áttelepítési állapotban van, nem törölhető. Az áttelepítés megszakításához futtassa a következő parancsot, majd törölje a virtuális hálózatot.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>További lépések

- [Azure virtuális hálózat](virtual-networks-overview.md)
- [Azure Virtual Network – Gyakori kérdések (GYIK)](virtual-networks-faq.md)
