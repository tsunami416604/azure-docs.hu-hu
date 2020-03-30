---
title: Az Azure virtuális gépek közötti kapcsolódási problémák elhárítása | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el az Azure virtuális gépek közötti kapcsolódási problémákat.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ab3ae45081ecc481cb90af8961174e23c86e84b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056814"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása

Előfordulhat, hogy kapcsolódási problémák at Azure virtuális gépek (VM-ek) között. Ez a cikk hibaelhárítási lépéseket tartalmaz a probléma megoldásához. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Hibajelenség

Egy Azure-beli virtuális gép nem tud csatlakozni egy másik Azure-beli virtuális géphez.

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Annak ellenőrzése, hogy a hálózati adapter helytelenül van-e konfigurálva](#step-1-check-whether-nic-is-misconfigured)
2. [Annak ellenőrzése, hogy az NSG vagy az UDR blokkolja-e a hálózati forgalmat](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Annak ellenőrzése, hogy a virtuális gép tűzfala blokkolja-e a hálózati forgalmat](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Annak ellenőrzése, hogy a VM-alkalmazás vagy -szolgáltatás figyel-e a porton](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Annak ellenőrzése, hogy a problémát az SNAT okozta-e](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Annak ellenőrzése, hogy a klasszikus virtuális gép acl-jai blokkolják-e a forgalmat](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Annak ellenőrzése, hogy a végpont a klasszikus virtuális géphez jön-e létre](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Próbáljon meg csatlakozni egy virtuális gép hálózati megosztásához](#step-8-try-to-connect-to-a-vm-network-share)
9. [Az Inter-Vnet-kapcsolat ellenőrzése](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A probléma elhárításához kövesse az alábbi lépéseket. Az egyes lépéseket követően ellenőrizze, hogy megoldódott-e a probléma. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>1. lépés: Annak ellenőrzése, hogy a hálózati adapter helytelenül van-e konfigurálva

Kövesse az [Azure Windows VM hálózati felületének alaphelyzetbe állítása című lépéseit.](../virtual-machines/windows/reset-network-interface.md) 

Ha a probléma a hálózati adapter módosítása után jelentkezik, kövesse az alábbi lépéseket:

**Több hálózati virtuális gépek**

1. Adjon hozzá egy hálózati adaptert.
2. Javítsa ki a rossz hálózati adapter problémáit, vagy távolítsa el a rossz hálózati adaptert.  Ezután adja hozzá újra a hálózati adaptert.

További információt a [Hálózati adapterek hozzáadása a virtuális gépekhez vagy azok eltávolításácímű témakörben talál.](virtual-network-network-interface-vm.md)

**Egyhálózati virtuális gép** 

- [Windows virtuális gép újratelepítése](../virtual-machines/windows/redeploy-to-new-node.md)
- [Linux os virtuális gép újratelepítése](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>2. lépés: Ellenőrizze, hogy az NSG vagy az UDR blokkolja-e a hálózati forgalmat

A [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) és az [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) segítségével megállapíthatja, hogy van-e olyan hálózati biztonsági csoport (NSG) vagy felhasználó által definiált útvonal (UDR), amely zavarja a forgalmat.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>3. lépés: Ellenőrizze, hogy a virtuális gép tűzfala blokkolja-e a hálózati forgalmat

Tiltsa le a tűzfalat, majd tesztelje az eredményt. Ha a probléma megoldódott, ellenőrizze a tűzfal beállításait, majd engedélyezze újra a tűzfalat.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>4. lépés: Ellenőrizze, hogy a VM-alkalmazás vagy -szolgáltatás figyel-e a porton

Az alábbi módszerek egyikével ellenőrizheti, hogy a virtuális gép alkalmazás vagy szolgáltatás figyel-e a porton.

- Futtassa a következő parancsokat annak ellenőrzéséhez, hogy a kiszolgáló figyel-e az adott porton.

**Windows rendszerű virtuális gép**

    netstat –ano

**Linux rendszerű virtuális gép**

    netstat -l

- Futtassa a **telnet** parancsot magán a virtuális gépen a port teszteléséhez. Ha a teszt sikertelen, az alkalmazás vagy szolgáltatás nincs beállítva az adott porton való figyelésre.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>5. lépés: Ellenőrizze, hogy a problémát az SNAT okozza-e

Bizonyos esetekben a virtuális gép egy terheléselosztási megoldás mögé kerül, amely az Azure-on kívüli erőforrásoktól függ. Ezekben az esetekben, ha időszakos csatlakozási problémákat tapasztal, a problémát az [SNAT-port kimerülése](../load-balancer/load-balancer-outbound-connections.md)okozhatja. A probléma megoldásához hozzon létre egy VIP (vagy ILPIP klasszikus) minden virtuális gép, amely a terheléselosztó mögött, és biztonságos NSG vagy ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>6. lépés: Ellenőrizze, hogy a forgalmat blokkolják-e az ACL-ek a klasszikus virtuális géphez

A hozzáférés-vezérlési lista (ACL) lehetővé teszi a virtuális gép végpontjának forgalom szelektív engedélyezését vagy megtagadását. További információt [az ACL kezelése végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)című témakörben talál.

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>7. lépés: Ellenőrizze, hogy a végpont létre jön-e a klasszikus virtuális géphez

A klasszikus üzembe helyezési modell használatával az Azure-ban létrehozott összes virtuális gép automatikusan kommunikálhat egy magánhálózati csatornán keresztül az azonos felhőszolgáltatásban vagy virtuális hálózatban lévő más virtuális gépekkel. Más virtuális hálózatok számítógépei azonban végpontokra van szükségük ahhoz, hogy a bejövő hálózati forgalmat egy virtuális gépre irányítsák. További információ: [Végpontok beállítása.](../virtual-machines/windows/classic/setup-endpoints.md)

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>8. lépés: Próbáljon meg csatlakozni egy virtuális gép hálózati megosztásához

Ha nem tud csatlakozni egy virtuális gép hálózati megosztásához, a problémát a virtuális gép nem elérhető hálózati hálózati adapterei okozhatják. A nem elérhető hálózati adapterek törléséről a [Nem elérhető hálózati adapterek törlése című témakörben](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>9. lépés: A Vnet-ek közötti kapcsolat ellenőrzése

A [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) és az [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) segítségével megállapíthatja, hogy van-e olyan NSG vagy UDR, amely zavarja a forgalmat. Az Inter-Vnet konfigurációját [itt](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)is ellenőrizheti.

### <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.
Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.