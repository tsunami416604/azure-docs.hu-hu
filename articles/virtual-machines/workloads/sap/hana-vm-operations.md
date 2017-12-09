---
title: "SAP HANA műveletek az Azure-on |} Microsoft Docs"
description: "SAP HANA műveleteinek Azure natív virtuális gépeken"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab609fe9e7b01d7087dd00c22c19e69a471f6599
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Az Azure üzemeltetési útmutatójának SAP HANA
Ez az útmutató rendszerűeket SAP HANA Azure virtuális gépeken telepített útmutatást. Ez a dokumentum nem célja, hogy cserélje le a szabványos SAP dokumentációk bármelyikét. SAP útmutatók és megjegyzések az alábbi helyeken találhatók:

- [SAP felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatók](https://service.sap.com/instguides)
- [SAP Megjegyzés](https://sservice.sap.com/notes)

Előfeltétel, hogy a különböző Azure összetevői vonatkozó általános ismeretekre:

- [Az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Az Azure hálózati és a Vnetek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

SAP NetWeaver további dokumentációiért és más Azure-SAP összetevőket megtalálhatók a [Azure SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) szakasza a [Azure dokumentációja](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Alapszintű beállítása szempontokat
### <a name="connecting-into-azure"></a>Csatlakozás az Azure
Ahogy [Azure virtuális gépek tervezési és megvalósítási az SAP NetWeaver] [-útmutatóban], a csatlakozás az Azure virtuális gépek két alapvető módszer áll rendelkezésre. 

- Internet- és nyilvános végpontok irányítják a virtuális gép vagy a virtuális gép fut az SAP HANA keresztül csatlakozó
- Keresztül csatlakozik egy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) vagy Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

Üzemi forgatókönyvek vagy forgatókönyvek, amely a termelési forgatókönyvek SAP szoftver együtt nem éles esetekben kell keresztül VPN- vagy ExpressRoute a pont-pont kapcsolattal rendelkezik, az ábrán látható módon:

![Webhelyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Azure virtuális gép típusok kiválasztása
Éles környezetben használható Azure Virtuálisgép-típusokon kereshetők [Itt](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Nem éles környezetben dönthet úgy, többféle natív Azure virtuális gépeken. Azonban korlátozására, saját magának a Virtuálisgép-típusokon, amelyek [SAP Megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533). Az Azure virtuális gépek telepítési érhető el:

- Azure Portal
- Az Azure Powershell-parancsmagok
- Azure CLI

Teljes telepített SAP HANA platform, az Azure virtuális gép szolgáltatások keresztül is telepíthet [SAP felhőplatform](https://cal.sap.com/) dokumentált [Itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Az Azure storage kiválasztása
Azure biztosít két fő típusú tárolókat Azure virtuális gépekhez megfelelő SAP HANA fut

- [Az Azure standard szintű tárolót](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Prémium szintű Azure Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Az Azure két telepítési módszerek kínál a VHD-k Azure Standard és prémium szintű Storage. Ha engedélyezi a teljes forgatókönyv, javasoljuk, hogy kihasználja [Azure kezelt lemez](https://azure.microsoft.com/services/managed-disks/) központi telepítéseket.

A pontos tárolási típusok és adott típusú tárolókat körül SLA tekintse [jelen dokumentáció](https://azure.microsoft.com/pricing/details/managed-disks/)

Javasoljuk, hogy Azure Premium lemezek /hana/data és /hana/log köteteihez használja. Hozza létre egy LVM RAID keresztül prémium szintű Storage több lemez, és ezek /hana/data, RAID-kötetek és /hana/log kötetek esetén támogatott.

A lehetséges konfigurációs közös virtuális gép különböző használó ügyfelek, amennyiben az Azure virtuális gépeken SAP HANA üzemeltetési nézhet:

| VIRTUÁLIS GÉP TERMÉKVÁLTOZAT | RAM | / hana/adatainak és naplókönyvtárainak/hana /<br /> csíkozott LVM vagy MDADM | / hana/megosztott | / root kötet | / usr/sap | Hana, illetve biztonsági mentési |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 TB-OS | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | A 2TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Az Azure hálózatkezelés
Feltételezve, hogy az Azure VPN- vagy ExpressRoute-helyek csatlakozási, minimum kellene lennie egy [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , amely a VPN- vagy ExpressRoute körön virtuális átjáró csatlakozik. A virtuális átjáró él, az Azure virtuális alhálózat. HANA telepítéséhez a Vneten belül egy másik két alhálózat hozna létre. Egy alhálózatot a virtuális gép van, az SAP HANA-példányokat, és a végleges Jumpbox vagy futtató felügyeleti virtuális gép van, amely egy másik alhálózat rendszerű állomások tárolhatja, SAP HANA Studio vagy más felügyeleti szoftverek.
Amikor telepíti a HANA kell futtató virtuális gépeken, a virtuális gépek kell rendelkezniük:

- Két virtuális hálózati adaptert telepített, amelyek egy csatlakozik a felügyeleti alhálózatot és egy hálózati adapter az SAP HANA-példányra, az Azure virtuális gépen vagy a helyszíni vagy más hálózatokkal való csatlakozáshoz használt.
- A mindkét vNICs telepítésére statikus magánhálózati IP-címek

IP-cím hozzárendelése különböző lehetőségeinek áttekintését található [Itt](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

A forgalom útválasztásához a közvetlenül az SAP HANA-példány vagy a jumpbox utasítására [Azure hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) , amelyek a HANA-alhálózatot és a felügyeleti alhálózatot társítva.

A teljes a nyers telepítési séma alábbihoz hasonlóan fog kinézni:

![SAP HANA nyers telepítési sémája](media/hana-vm-operations/hana-simple-networking.PNG)


Ha csupán telepítse az Azure-ban SAP HANA anélkül, hogy a pont-pont (VPN- vagy az Azure ExpressRoute), bár egy nyilvános IP-címet, amely hozzá van rendelve a Jumpbox virtuális Gépet futtató Azure virtuális gép elérésére az SAP HANA-példány. Az egyszerű esetben is használ, az Azure beépített DNS-szolgáltatások állomásnevek feloldásához. Különösen ha nyilvános felé néző IP-címeket használ, érdemes Azure hálózati biztonsági csoportok használatával korlátozhatók a megnyitott portok vagy IP-címtartományt, amely engedélyezi a csatlakozást rendszert futtató eszközök nyilvános felé néző IP-címekkel rendelkező Azure alhálózatokra. A séma, a központi telepítés nézhet:
  
![Nyers telepítési séma SAP Hana-helyek kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Műveletek
### <a name="backup-and-restore-operations-on-azure-vms"></a>Biztonsági mentési és visszaállítási műveletek Azure virtuális gépeken
SAP HANA biztonsági mentési és helyreállítási lehetőségek ismertetett ezeket a dokumentumokat:

- [SAP HANA biztonsági másolat áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA fájlszintű biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA tárolási pillanatkép teljesítményteszt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Indítsa el, és indítsa újra a virtuális gépek SAP HANA tartalmazó
Az Azure nyilvános felhőjében szintjeiről egyike az, hogy csak a rendszer ugyanis számítási percig van szó. Ez azt jelenti, hogy ha kikapcsolja a virtuális gép és az azt futtató SAP HANA, csak a tárolási költségek ebben az időszakban vannak számlázva. Mivel a virtuális gép indítása, a SAP HANA újra, a virtuális gép indítsa el újra lesz, és lesz az azonos IP-címek (ha telepítve vannak a statikus IP-címek). 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter engedélyezése SAP távoli támogatása
Ha a helyi (ek) ről és Azure, és már futtatása az SAP-összetevők közötti pont-pont kapcsolattal rendelkezik, nagyon valószínű, hogy már futtassa SAProuter már. Ebben az esetben nincs mit kell tennie az Azure-ban telepít SAP HANA-osztályt. Kivéve karbantartása a magán- és statikus IP-címet a virtuális gép, amely futtatja HANA SAPRouter konfigurációjában, és az alhálózati tároló NSG rendelkezik a HANA VM leírtakon (forgalom port 3299 engedélyezett TCP/IP-porton keresztül).

Ha SAP HANA telepít, és Azure az interneten keresztül csatlakozik, és nincs telepítve a vnetben, amely a virtuális gép fut, SAP HANA egy SAP-útválasztó, telepítenie kell a SAPRouter egy külön virtuális gépre, ahogy az itt látható a felügyeleti alhálózat :


![Nyers telepítési séma SAP Hana webhelyek kapcsolat és SAPRouter nélkül](media/hana-vm-operations/hana-simple-networking3.PNG)

SAPRouter egy külön virtuális gépre, és nem a Jumpbox virtuális Gépre kell telepíteni. A különálló virtuális gép kell egy statikus IP-címet. SAPRouter való csatlakozáshoz a SAPRouter SAP futtató (megfelelőjére SAPRouter-példány telepítése virtuális gép), kapcsolatba kell lépnie az SAP IP-cím lekérése SAP, konfigurálnia kell a SAPRouter példányát. A csak a szükséges portot 3299 TCP-port.
Beállítása és karbantartása a távoli támogatásának kapcsolatok SAPRouter keresztül módjáról további információkért ellenőrizze a [SAP forrás](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Magas rendelkezésre állású az SAP HANA Azure natív virtuális gépeken
Az SUSE Linux 12 SP1 és újabb létrehozhat egy támasztja fürt STONITH eszközökkel egy SAP HANA-konfiguráció, amely szinkron replikációt használ a HANA replikációs és automatikus feladatátvételt. A telepítő az eljárást a cikkben ismertetett [magas rendelkezésre állás az SAP HANA Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










