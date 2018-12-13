---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323695"
---
Hozzon létre egy távoli asztali kapcsolat egy virtuális hálózat üzembe helyezett virtuális Géphez való csatlakozáshoz. Ellenőrizze, hogy a virtuális gép csatlakozhat a legjobb módja a számítógép neve helyett a magánhálózati IP-címére kapcsolódni fog. Ezzel a módszerrel tesztelt megtekintheti, ha akkor is csatlakozhat, nem e névfeloldás megfelelően van konfigurálva. 

1. Keresse meg a virtuális gép magánhálózati IP-címét. Keresse meg a virtuális gép magánhálózati IP-címét, az Azure Portalon a virtuális gép tulajdonságainak megtekintése vagy PowerShell-lel.
2. Győződjön meg arról, hogy a virtuális hálózat, a pont – hely VPN-kapcsolattal csatlakozik. 
3. A távoli asztali kapcsolat megnyitásához írja be a *RDP* vagy *távoli asztali kapcsolat* a keresőmezőbe, a tálcán, majd válassza ki **távoli asztali kapcsolat**. Használatával is megnyithatja a **mstsc** parancsot a PowerShellben. 
3. A **távoli asztali kapcsolat**, adja meg a virtuális gép magánhálózati IP-címét. Ha szükséges, válassza ki a **beállítások megjelenítése** további beállításokat, majd csatlakozzon.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Egy virtuális géppel létrehozni kívánt RDP-kapcsolat hibaelhárítása

Ha problémába ütközik a VPN-kapcsolaton keresztül egy virtuális géphez való csatlakozás, van néhány dolgot ellenőrizheti. 

- Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.
- Győződjön meg arról, hogy csatlakozik a magánhálózati IP-címet a virtuális gép számára.
- Adja meg **ipconfig** , ellenőrizze a számítógép, amelyről csatlakozik az Ethernet-adapteréhez rendelt IPv4-címet. Egy átfedő címtér akkor fordul elő, ha az IP-címet a virtuális hálózattal, amelyhez csatlakozik, a címtartományán belül, vagy a VPNClientAddressPool címtartományán belül. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.
- A virtuális gép magánhálózati IP-címet, de nem a számítógépnév használatával csatlakozhat, győződjön meg arról, hogy DNS megfelelően van konfigurálva. A virtuális gépek névfeloldásának működésével kapcsolatos további információkért lásd [a virtuális gépek névfeloldásával](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) foglakozó cikket.
- Győződjön meg arról, hogy a VPN-ügyfélkonfigurációs csomag jön létre a virtuális hálózat számára a DNS-kiszolgáló IP-címek megadása után. Ha frissíti a DNS-kiszolgáló IP-címek, hozzon létre, és a egy új VPN-ügyfél konfigurációs csomagjának telepítése.

A hibaelhárítással kapcsolatos további információkért lásd [a virtuális gép távoli asztali kapcsolatainak hibaelhárításával](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) foglalkozó cikket.