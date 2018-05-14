---
title: Azure virtuális gépek közötti kapcsolati problémák elhárítása |} Microsoft Docs
description: Útmutató az Azure virtuális gépek közötti kapcsolódási problémák megoldásáról.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6decb0e9188db00608be35d9ba4e84df92ceb671
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure virtuális gépek közötti kapcsolati problémák elhárítása

Az Azure virtuális gépek (VM) közötti kapcsolat problémák merülhetnek fel. Ez a cikk nyújt hibaelhárítási segítséget nyújtanak a probléma megoldásához. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Jelenség

Egy Azure virtuális gép egy másik Azure virtuális gép nem tud kapcsolódni.

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Ellenőrizze, hogy a hálózati adapter helytelenül van konfigurálva](#step-1-check-whether-nic-is-misconfigured)
2. [Ellenőrizze, hogy hálózati forgalmát blokkolja NSG-t vagy UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Ellenőrizze, hogy hálózati forgalmát blokkolja a virtuális gép tűzfal](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Ellenőrizze, hogy virtuális gép alkalmazást vagy szolgáltatást a portot figyel](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Ellenőrizze, hogy a probléma okozza-e SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Ellenőrizze, hogy forgalmat blokkolja hozzáférés-vezérlési listákat a klasszikus virtuális géphez](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Ellenőrizze, hogy a végpont jön létre a klasszikus virtuális gép](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [A virtuális gép hálózati megosztáshoz való csatlakozáshoz próbálja](#step-8-try-to-connect-to-a-vm-network-share)
9. [Ellenőrizze a kapcsolatot Inter-virtuális hálózat](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Kövesse az alábbi lépéseket a probléma elhárításához. Minden lépés befejezése után ellenőrizze, hogy a probléma nem oldódik. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>1. lépés: Ellenőrizze, hogy a hálózati adapter helytelenül van konfigurálva

Kövesse a [hálózati kapcsolat visszaállítása az Azure virtuális gépekhez Windows](../virtual-machines/windows/reset-network-interface.md). 

Ha a probléma akkor fordul elő, a hálózati kártya (NIC) módosítása után, kövesse az alábbi lépéseket:

**Több hálózati Adapterrel virtuális gépek**

1. Adja hozzá a hálózati adaptert.
2. Hárítsa el a problémákat a hibás hálózati adapter, vagy távolítsa el a hibás hálózati adaptert.  Majd újból vegye fel a hálózati adapter.

További információkért lásd: [adja hozzá a hálózati kapcsolatokat, vagy távolítsa el a virtuális gépek](virtual-network-network-interface-vm.md).

**Single-NIC VM** 

- [Telepítse újra a Windows virtuális gép](../virtual-machines/windows/redeploy-to-new-node.md)
- [Telepítse újra a Linux virtuális gép](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>2. lépés: Ellenőrizze, hogy hálózati forgalmát blokkolja NSG-t vagy UDR

Használja [hálózati figyelő IP folyamata ellenőrizze](../network-watcher/network-watcher-ip-flow-verify-overview.md) és [NSG folyamata naplózás](../network-watcher/network-watcher-nsg-flow-logging-overview.md) annak meghatározásához, hogy van-e a hálózati biztonsági csoport (NSG) vagy a felhasználói útvonal (UDR), amely ütközik az adatforgalmat.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>3. lépés: Ellenőrizze, hogy hálózati forgalmát blokkolja a virtuális gép tűzfal

Tiltsa le a tűzfalat, és tesztelje az eredmény. Ha a probléma megoldódott, ellenőrizze a tűzfal beállításait, és majd engedélyezze újra a tűzfalon.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>4. lépés: Ellenőrizze, hogy virtuális gép alkalmazást vagy szolgáltatást a portot figyel

Ellenőrizze, hogy a virtuális gép alkalmazás vagy szolgáltatás a porton figyel a következő módszerek egyikét használhatja.

- Futtassa a következő parancsok futtatásával ellenőrizze, hogy a kiszolgáló adott portot figyel.

**Windows rendszerű virtuális gép**

    netstat –ano

**Linux rendszerű virtuális gép**

    netstat -l

- Futtassa a **telnet** parancs magát a port tesztelése a virtuális gépen. A teszt sikertelen, az alkalmazás vagy szolgáltatás beállítása nem lehet figyelni, hogy a porton.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>5. lépés: Ellenőrizze, hogy a probléma okozza-e SNAT

Bizonyos esetekben a virtuális gép el van helyezve a terhelés elosztása megoldás, amely az Azure-on kívüli erőforrások mögött. Ezekben az esetekben időszakos kapcsolódási problémák léptek fel, ha a probléma oka lehet [SNAT port Erőforrásfogyás](../load-balancer/load-balancer-outbound-connections.md). A probléma megoldásához, VIP (vagy létrehozása a klasszikus ILPIP) az egyes virtuális gépek, amelyek a terheléselosztó mögött van, és az NSG-t vagy a hozzáférés-vezérlési lista biztonságáról. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>6. lépés: Ellenőrizze, hogy forgalmat blokkolja hozzáférés-vezérlési listákat a klasszikus virtuális géphez

Hozzáférés-vezérlési listaként (ACL) lehetővé teszi a szelektív módon engedélyezheti vagy megtagadhatja a forgalom egy virtuális gép végpontja. További információkért lásd: [végponti ACL kezelése](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>7. lépés: Ellenőrizze, hogy a végpont jön létre a klasszikus virtuális gép

Minden virtuális gép, amely a klasszikus telepítési modell segítségével hoz létre Azure automatikusan kommunikálhat más virtuális gépekkel a felhőalapú szolgáltatás- vagy virtuális hálózati magánhálózati csatornán keresztül. Azonban más virtuális hálózatokon lévő számítógépek számára szükséges végpontok át tudja irányítani a bejövő hálózati forgalmat a virtuális gép. További információkért lásd: [végpontok beállítása](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>8. lépés: A virtuális gép hálózati megosztáshoz való csatlakozáshoz próbálja

Ha nem tud csatlakozni a virtuális gép hálózati megosztást, a problémát okozhatja a virtuális hálózati adapter nem érhető el. Törölje a nem elérhető hálózati adapterek, lásd: [az elérhető hálózati adapterek törlése](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>9. lépés: Ellenőrizze Inter-virtuális hálózat kapcsolatának

Használja [hálózati figyelő IP folyamata ellenőrizze](../network-watcher/network-watcher-ip-flow-verify-overview.md) és [NSG folyamata naplózás](../network-watcher/network-watcher-nsg-flow-logging-overview.md) annak meghatározásához, hogy van-e egy NSG-t vagy UDR, amely ütközik az adatforgalmat. Azt is ellenőrizheti az Inter-virtuális hálózat konfigurációs [Itt](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.