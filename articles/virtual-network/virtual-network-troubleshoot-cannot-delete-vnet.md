---
title: Nem lehet törölni a virtuális hálózat az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémát, amelyben az Azure-beli virtuális hálózat nem törölhető.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4bd143b37e4403d039108b4349b27604b6503e0e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415219"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Hibaelhárítás: Nem sikerült törölni a virtuális hálózat az Azure-ban

Hibák jelenhet meg a Microsoft Azure virtuális hálózat törlése. Ez a cikk segítséget nyújt a probléma megoldásához hibaelhárítási lépéseket. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Ellenőrizze, hogy fut-e a virtuális hálózati átjáró a virtuális hálózatban](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Ellenőrizze, hogy a virtuális hálózatban fut-e az application gateway](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Ellenőrizze, hogy az Azure Active Directory tartományi szolgáltatások engedélyezve van-e a virtuális hálózatban](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Ellenőrizze a többi erőforrás csatlakozik a virtuális hálózat](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Ellenőrizze a virtuális gép továbbra is fut-e a virtuális hálózatban](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Ellenőrizze-e a virtuális hálózati áttelepítés Beragadt](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Ellenőrizze, hogy fut-e a virtuális hálózati átjáró a virtuális hálózatban

Távolítsa el a virtuális hálózatot, először el kell távolítania a virtuális hálózati átjárót.

A klasszikus virtuális hálózatok, keresse fel a **áttekintése** az Azure Portalon a klasszikus virtuális hálózat lapján. Az a **VPN-kapcsolatok** szakaszban, ha az átjáró fut, a virtuális hálózatban, látni fogja az átjáró IP-címét. 

![Ellenőrizze, hogy az átjáró fut.](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Virtuális hálózatok esetében, nyissa meg a **áttekintése** a virtuális hálózat lapján. Ellenőrizze **csatlakoztatott eszközök** a virtuális hálózati átjáróhoz.

![A csatlakoztatott eszköz](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Az átjáró eltávolítása előtt először távolítsa el bármelyik **kapcsolat** objektumok az átjáróban. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Ellenőrizze, hogy az application gateway fut-e a virtuális hálózatban

Nyissa meg a **áttekintése** a virtuális hálózat lapján. Ellenőrizze a **csatlakoztatott eszközök** az application gateway számára.

![A csatlakoztatott eszköz](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Ha egy application gateway, el kell távolítania azt a virtuális hálózat törlése előtt.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Ellenőrizze, hogy az Azure Active Directory tartományi szolgáltatások engedélyezve van-e a virtuális hálózatban

Ha az Active Directory tartományi szolgáltatások engedélyezve, és a virtuális hálózathoz csatlakoztatva van, ez a virtuális hálózat nem törölhető. 

![A csatlakoztatott eszköz](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Letilthatja a szolgáltatást, lásd: [tiltsa le az Active Directory Domain Servicest az Azure portal használatával](../active-directory-domain-services/active-directory-ds-disable-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Ellenőrizze a többi erőforrás csatlakozik a virtuális hálózat

Ellenőrizze a Kapcsolatcsoportok kapcsolatai, kapcsolatok és virtuális hálózati társviszony-létesítéseket. Ezek egyikét sem okozhat, egy virtuális hálózat törlése meghiúsul. 

A javasolt törlési sorrend a következőképpen történik:

1. Gateway-kapcsolatok
2. Átjárók
3. IP-címek
4. Virtuális társhálózatok
5. Az App Service Environment (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Ellenőrizze a virtuális gép továbbra is fut-e a virtuális hálózatban

Győződjön meg arról, hogy egyetlen virtuális gép se a virtuális hálózatban.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>-E a virtuális hálózat elakadt az áttelepítés ellenőrzése

Ha a virtuális hálózat elakadt áttelepítési állapotban lévő, nem lehet törölni. A migrálás megszakítása a következő parancsot, és ezután törölje a virtuális hálózat.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>További lépések

- [Azure Virtual Network](virtual-networks-overview.md)
- [Azure Virtual Network – Gyakori kérdések (GYIK)](virtual-networks-faq.md)
