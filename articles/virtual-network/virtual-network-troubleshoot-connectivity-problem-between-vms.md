---
title: Azure virtuális gépek közötti kapcsolódási problémák elhárítása |} A Microsoft Docs
description: Útmutató az Azure virtuális gépek közötti kapcsolódási problémák elhárításához.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732515"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure virtuális gépek közötti kapcsolódási problémák hibaelhárítása

Azure-beli virtuális gépek (VM) közötti kapcsolati problémák merülhetnek fel. Ez a cikk segítséget nyújt a probléma megoldásához hibaelhárítási lépéseket. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Jelenség

Egy Azure virtuális Gépen egy másik Azure virtuális gép nem tud kapcsolódni.

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Ellenőrizze, hogy a hálózati adapter helytelenül van konfigurálva](#step-1-check-whether-nic-is-misconfigured)
2. [Ellenőrizze, hogy hálózati forgalmat blokkolja az NSG- vagy udr-t](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Ellenőrizze, hogy hálózati forgalmat blokkolja tűzfal a virtuális gép](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Ellenőrizze, hogy VM alkalmazást vagy szolgáltatást figyel a következőn: a port](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Ellenőrizze, hogy a probléma okozta SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Ellenőrizze, hogy forgalom le van tiltva a hozzáférés-vezérlési listák a klasszikus virtuális gép](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Ellenőrizze, hogy a végpont jön létre a klasszikus virtuális gép](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Próbálja ki a virtuális gép hálózati megosztáshoz való csatlakozáshoz](#step-8-try-to-connect-to-a-vm-network-share)
9. [Virtuális hálózatok közötti kapcsolat ellenőrzése](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Kövesse az alábbi lépéseket a probléma elhárításához. Minden lépés végrehajtása után ellenőrizze, hogy megoldódott-e a probléma. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>1. lépés: Ellenőrizze, hogy a hálózati adapter helytelenül van konfigurálva

Kövesse a [Azure Windows virtuális gép alaphelyzetbe állítása a hálózati adapter](../virtual-machines/windows/reset-network-interface.md). 

Ha a probléma akkor fordul elő, a hálózati adapter (NIC) módosítása után, kövesse az alábbi lépéseket:

**Több hálózati adapterrel rendelkező virtuális gépek**

1. Adjon hozzá egy hálózati adapterhez.
2. Hárítsa el a problémákat a hibás hálózati Adaptert, vagy távolítsa el a hibás hálózati adaptert.  Majd adja hozzá újra a hálózati Adapterhez.

További információkért lásd: [hálózati adapterek hozzáadása vagy eltávolítása a virtuális gépek](virtual-network-network-interface-vm.md).

**Egyetlen hálózati adapterrel rendelkező virtuális gép** 

- [Windows virtuális gép újratelepítése](../virtual-machines/windows/redeploy-to-new-node.md)
- [Linux rendszerű virtuális gép újratelepítése](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>2. lépés: Ellenőrizze, hogy hálózati forgalmat blokkolja az NSG- vagy udr-t

Használat [Network Watcher IP-folyamat ellenőrzésével](../network-watcher/network-watcher-ip-flow-verify-overview.md) és [folyamatnaplózást az NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) meghatározni, hogy van-e a hálózati biztonsági csoport (NSG) vagy a felhasználó által megadott útvonal (UDR), amely ütközik az adatforgalmat.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>3. lépés: Ellenőrizze, hogy hálózati forgalmat blokkolja tűzfal a virtuális gép

Tiltsa le a tűzfalat, és tesztelje az eredményt. Ha a probléma megoldódott, ellenőrizze a tűzfal beállításait, és majd engedélyezze újra a tűzfalat.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>4. lépés: Ellenőrizze, hogy VM alkalmazást vagy szolgáltatást figyel a következőn: a port

Ellenőrizze, hogy a virtuális gép alkalmazás vagy szolgáltatás a a porton figyel a következő módszerek egyikét használhatja.

- Futtassa a következő parancsok futtatásával ellenőrizze, hogy a kiszolgáló adott portot figyel.

**Windows rendszerű virtuális gép**

    netstat –ano

**Linux rendszerű virtuális gép**

    netstat -l

- Futtassa a **telnet** parancsot a virtuális gépen, magát a port teszteléséhez. Ha a teszt meghiúsul, az alkalmazás vagy szolgáltatás nincs konfigurálva a, hogy a port figyelésére.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>5. lépés: Ellenőrizze, hogy a probléma okozta SNAT

Bizonyos esetekben a virtuális gép mögé terhelés egyenleg megoldás, amely az Azure-on kívüli erőforrásokon. Ezekben a forgatókönyvekben, ha időszakos csatlakozási problémák, a problémát okozhatja [SNAT portfogyás](../load-balancer/load-balancer-outbound-connections.md). A probléma megoldása érdekében hozzon létre egy virtuális IP-cím (vagy klasszikus ILPIP) minden virtuális géphez, amely a terheléselosztó mögött, és biztonságos az NSG-n vagy az ACL-t. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>6. lépés: Ellenőrizze, hogy forgalom le van tiltva a hozzáférés-vezérlési listák a klasszikus virtuális gép

Hozzáférés-vezérlési lista (ACL) lehetővé teszi a szelektív engedélyezéséhez, vagy megtagadják a forgalmat a virtuálisgép-végpont. További információkért lásd: [kezelése az ACL-végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>7. lépés: Ellenőrizze, hogy a végpont jön létre a klasszikus virtuális gép

Az Azure-ban a klasszikus üzemi modell használatával létrehozott összes virtuális gép automatikusan képes kommunikálni az ugyanazon a felhőszolgáltatáson vagy virtuális hálózaton lévő más virtuális gépekkel egy magánhálózati csatornán. Azonban más virtuális hálózatokon működő számítógépek szükséges végpontokat a virtuális gép bejövő hálózati forgalmat. További információkért lásd: [végpontok beállítása](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>8. lépés: Próbálja ki a virtuális gép hálózati megosztáshoz való csatlakozáshoz

Ha nem tud csatlakozni a virtuális gép hálózati megosztást, a problémát okozhatja a virtuális hálózati adapter nem érhető el. Törli a hálózati adapter nem érhető el, lásd: [nem érhető el a hálózati adapter törlése](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>9. lépés: Virtuális hálózatok közötti kapcsolat ellenőrzése

Használat [Network Watcher IP-folyamat ellenőrzésével](../network-watcher/network-watcher-ip-flow-verify-overview.md) és [folyamatnaplózást az NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) meghatározni, hogy van-e egy NSG- vagy udr-t, amely ütközik az adatforgalmat. Azt is ellenőrizheti a virtuális hálózatok közötti konfigurációs [Itt](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.