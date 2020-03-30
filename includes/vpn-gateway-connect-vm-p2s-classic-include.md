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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178985"
---
Hozzon létre egy távoli asztali kapcsolat, hogy csatlakozzon egy virtuális gép, amely telepítve van a virtuális hálózatra. A legjobb módja annak ellenőrzésére, hogy tud csatlakozni a virtuális géphez, hogy csatlakozzon a saját IP-címét, nem pedig a számítógép nevét. Így teszteli, hogy tud-e csatlakozni, nem azt, hogy a névfeloldás megfelelően van-e konfigurálva. 

1. Keresse meg a virtuális gép magánhálózati IP-címét. A virtuális gép privát IP-címének megkereséséhez tekintse meg a virtuális gép tulajdonságait az Azure Portalon, vagy használja a PowerShellt.
2. Ellenőrizze, hogy a pont-hely VPN-kapcsolattal csatlakozik-e a virtuális hálózathoz. 
3. A Távoli asztali kapcsolat megnyitásához írja be az *RDP* vagy a *Távoli asztali kapcsolat kifejezést* a tálca keresőmezőjébe, majd válassza a Távoli asztali **kapcsolat**lehetőséget. Azt is megnyithatja a PowerShell **mstsc** parancsával. 
3. A **Távoli asztali kapcsolat mezőbe**írja be a virtuális gép privát IP-címét. Ha szükséges, válassza **a Beállítások megjelenítése** lehetőséget a további beállítások módosításához, majd csatlakozzon.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Egy virtuális géppel létrehozni kívánt RDP-kapcsolat hibaelhárítása

Ha nem sikerül csatlakoznia egy virtuális géphez a VPN-kapcsolaton keresztül, néhány dolgot ellenőrizhet. 

- Ellenőrizze, hogy a VPN-kapcsolat sikeresen létrejött-e.
- Ellenőrizze, hogy csatlakozik-e a virtuális gép privát IP-címéhez.
- Írja be az **ipconfig** értéket, és ellenőrizze az Ethernet-adapterhez rendelt IPv4-címet azon a számítógépen, amelyről csatlakozik. Egymást átfedő címterület akkor fordul elő, ha az IP-cím a VPNClientAddressPool címtartományán belül vagy a VPNClientAddressPool címtartományán belül van. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.
- Ha a privát IP-cím használatával tud csatlakozni a virtuális géphez, de a számítógépnév nem, ellenőrizze, hogy megfelelően konfigurálta-e a DNS-t. A virtuális gépek névfeloldásának működésével kapcsolatos további információkért lásd [a virtuális gépek névfeloldásával](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) foglakozó cikket.
- Ellenőrizze, hogy a VPN-ügyfél konfigurációs csomagja létrejön-e, miután megadta a virtuális hálózat DNS-kiszolgálójának IP-címeit. Ha frissíti a DNS-kiszolgáló IP-címeit, hozzon létre és telepítsen egy új VPN-ügyfélkonfigurációs csomagot.

A hibaelhárítással kapcsolatos további információkért lásd [a virtuális gép távoli asztali kapcsolatainak hibaelhárításával](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) foglalkozó cikket.