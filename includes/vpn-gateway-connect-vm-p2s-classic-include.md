---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e49cfe786272b34675ca377808e2961e61a757e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197584"
---
Ha létrehoz egy távoli asztali kapcsolatot a virtuális géppel, csatlakozhat egy virtuális hálózaton üzembe helyezett virtuális géphez. A legjobb mód arra, hogy először ellenőrizze, hogy tud-e csatlakozni a virtuális géphez, ha egy magánhálózati IP-címet használ a számítógép neve helyett. Ily módon azt teszteli, hogy tud-e csatlakozni, nem azt, hogy a névfeloldás megfelelően van-e konfigurálva. 

1. Keresse meg a virtuális gép magánhálózati IP-címét. Ha egy virtuális gép magánhálózati IP-címét szeretné megkeresni, vagy tekintse meg a virtuális gép tulajdonságait az Azure Portalon, vagy használja a PowerShellt.
2. Győződjön meg arról, hogy pont–hely típusú VPN-kapcsolattal csatlakozik a virtuális hálózathoz. 
3. A távoli asztali kapcsolat megnyitásához írja be a tálca keresőmezőjébe az „RDP” vagy a „Távoli asztali kapcsolat” kifejezést, majd válassza a Távoli asztali kapcsolat lehetőséget. A távoli asztali kapcsolatot megnyithatja a PowerShell „mstsc” parancsával is. 
3. A távoli asztali kapcsolatban írja be a virtuális gép magánhálózati IP-címét. A további beállításokat a „Beállítások megjelenítése” gombra kattintva módosíthatja. Ha végzett, hozza létre a kapcsolatot.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Egy virtuális géppel létrehozni kívánt RDP-kapcsolat hibaelhárítása

Ha probléma adódik egy virtuális gép VPN-kapcsolaton keresztüli csatlakoztatása során, érdemes ellenőrizni néhány dolgot. A hibaelhárítással kapcsolatos további információkért lásd [a virtuális gép távoli asztali kapcsolatainak hibaelhárításával](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) foglalkozó cikket.

- Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.
- Ellenőrizze, hogy a virtuális gép magánhálózati IP-címéhez csatlakozik-e.
- Az „ipconfig” használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.
- Ha tud csatlakozni a virtuális géphez a magánhálózati IP-címmel, de a számítógép nevével nem, ellenőrizze, hogy a DNS-konfiguráció megfelelő-e. A virtuális gépek névfeloldásának működésével kapcsolatos további információkért lásd [a virtuális gépek névfeloldásával](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) foglakozó cikket.
- Ellenőrizze, hogy létrejött-e a VPN-ügyfél konfigurációs csomagja azután, hogy a DNS-kiszolgáló IP-címei meg lettek adva a virtuális hálózathoz. Ha frissítette a DNS-kiszolgáló IP-címeit, hozzon létre és telepítsen egy új VPN-ügyfélkonfigurációs csomagot.