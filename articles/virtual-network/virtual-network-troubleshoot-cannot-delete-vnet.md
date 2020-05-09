---
title: Nem lehet törölni egy virtuális hálózatot az Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a problémát, amelyben nem törölhet virtuális hálózatot az Azure-ban.
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
ms.openlocfilehash: c2152b872c82c224c786e56db0318c9df994ac25
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801586"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Hibaelhárítás: nem sikerült törölni a virtuális hálózatot az Azure-ban
<p class="alert is-flex is-primary"><span class="has-padding-left-medium has-padding-top-extra-small"><a class="button is-primary" href="https://azurevirtualsupportagent.services.microsoft.com?content=a13a0908-28e0-f9f7-c6c7-301fcd187560" target='_blank'>Start</a></span><span class="has-padding-small">A virtuális ügynök használatával gyorsan megoldhatja a problémát, ha <b>automatizált diagnosztikát futtat.</b> </span> <span class="has-padding-small"> <a href="https://privacy.microsoft.com/privacystatement" target='_blank'> <sub>Privacy Statement</sub> Adatvédelmi <div align="right">nyilatkozat</div></a></span></p>
Előfordulhat, hogy hibaüzeneteket kap, amikor Microsoft Azure-ben próbál meg törölni egy virtuális hálózatot. Ez a cikk a probléma megoldásához szükséges hibaelhárítási lépéseket ismerteti. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Győződjön meg arról, hogy a virtuális hálózati átjáró fut-e a virtuális hálózaton](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Győződjön meg arról, hogy fut-e az Application Gateway a virtuális hálózaton](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Győződjön meg arról, hogy a Azure Active Directory tartományi szolgáltatás engedélyezve van-e a virtuális hálózaton](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Győződjön meg arról, hogy a virtuális hálózat más erőforráshoz van csatlakoztatva](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Győződjön meg arról, hogy a virtuális gép továbbra is fut-e a virtuális hálózaton](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Győződjön meg arról, hogy a virtuális hálózat beragadt-e az áttelepítés során](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Győződjön meg arról, hogy a virtuális hálózati átjáró fut-e a virtuális hálózaton

A virtuális hálózat eltávolításához először el kell távolítania a virtuális hálózati átjárót.

A klasszikus virtuális hálózatok esetében lépjen a Azure Portal klasszikus virtuális hálózatának **Áttekintés** lapjára. Ha az átjáró a virtuális hálózaton fut, a **VPN-kapcsolatok** szakaszban látni fogja az átjáró IP-címét. 

![Annak megállapítása, hogy az átjáró fut-e](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

A virtuális hálózatok esetében lépjen a virtuális hálózat **Áttekintés** lapjára. Keresse meg a virtuális hálózati átjáróhoz **csatlakoztatott eszközöket** .

![A csatlakoztatott eszköz keresése](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Az átjáró eltávolítása előtt először távolítsa el az átjárón lévő összes **összekötő** objektumot. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Annak megállapítása, hogy fut-e az Application Gateway a virtuális hálózaton

Nyissa meg a virtuális hálózat **Áttekintés** lapját. Keresse meg az Application Gateway **csatlakoztatott eszközeit** .

![A csatlakoztatott eszköz keresése](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Ha van egy Application Gateway, el kell távolítania a virtuális hálózat törlése előtt.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Annak megállapítása, hogy a Azure Active Directory tartományi szolgáltatás engedélyezve van-e a virtuális hálózaton

Ha a Active Directory-tartomány szolgáltatás engedélyezve van, és csatlakozik a virtuális hálózathoz, a virtuális hálózat nem törölhető. 

![A csatlakoztatott eszköz keresése](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

A szolgáltatás letiltásához lásd: [Azure Active Directory Domain Services letiltása a Azure Portal használatával](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Győződjön meg arról, hogy a virtuális hálózat más erőforráshoz van csatlakoztatva

Az áramköri kapcsolatok, a kapcsolatok és a virtuális hálózati társítások keresése. Ezek bármelyike okozhatja, hogy a virtuális hálózat törlése meghiúsul. 

Az ajánlott törlési sorrend a következő:

1. Átjárókapcsolatok
2. Átjárók
3. IP-címek
4. Virtuális hálózati társítások
5. App Service Environment (bemutató)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Annak megkeresése, hogy a virtuális gép továbbra is fut-e a virtuális hálózaton

Győződjön meg arról, hogy egyetlen virtuális gép sincs a virtuális hálózaton.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Győződjön meg arról, hogy a virtuális hálózat beragadt-e az áttelepítés során

Ha a virtuális hálózat áttelepítési állapotban van, nem törölhető. Futtassa az alábbi parancsot az áttelepítés megszakításához, majd törölje a virtuális hálózatot.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>További lépések

- [Azure-Virtual Network](virtual-networks-overview.md)
- [Azure Virtual Network – Gyakori kérdések (GYIK)](virtual-networks-faq.md)
