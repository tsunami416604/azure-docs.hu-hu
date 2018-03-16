---
title: "SAP HANA-műveletek az Azure-on |} Microsoft Docs"
description: "Üzemeltetési útmutató a SAP HANA-rendszerek üzembe helyezett Azure virtuális gépeken."
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
ms.date: 03/13/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cb715960a516c6b2ca16376c12cb6f796e0b395
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Az Azure üzemeltetési útmutatójának SAP HANA
Ez a dokumentum nyújt útmutatást rendszerűeket SAP HANA telepített Azure natív virtuális gépek (VM). Ez a dokumentum nem célja, hogy cserélje le a szabványos SAP dokumentációját, amely a következőket tartalmazza:

- [SAP felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatók](https://service.sap.com/instguides)
- [SAP megjegyzések](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató használata esetén a következő Azure összetevők alapszintű ismeretét kell:

- [Azure-alapú virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Az Azure hálózati és a virtuális hálózatok](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

SAP NetWeaver és más Azure-SAP összetevőket kapcsolatos további tudnivalókért tekintse meg a [Azure SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) szakasza a [Azure dokumentációja](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Alapszintű beállítása szempontokat
A következő szakaszok ismertetik az Azure virtuális gépeken futó SAP HANA-rendszerek telepítése alapbeállítások szempontjai.

### <a name="connect-into-azure-virtual-machines"></a>Csatlakozás az Azure virtuális gépeken
Ahogy a [tervezési útmutató az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), két alapvető módszer Azure virtuális gépeken való csatlakozáshoz:

- Az internethez, és a irányítják a virtuális gépek publikus végpontjaira keresztül csatlakozni, vagy a virtuális Gépet futtató SAP HANA.
- Csatlakozhat egy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) vagy Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Hely-hely kapcsolatot keresztül VPN- vagy ExpressRoute szükség, éles környezetben. A kapcsolat típusa nem éles környezetben, amely a termelési forgatókönyvek a SAP szoftver használatára is szükség van. Az alábbi ábrán például a webhelyek közötti kapcsolat:

![Webhelyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Válassza ki az Azure virtuális gép típusa
A termelési környezetben használható Azure virtuális gép típusok szerepelnek a [IAAS SAP dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Nem éles környezetben natív Azure Virtuálisgép-típusokon többféle érhető el.

>[!NOTE]
> Nem éles környezetben, használja a virtuális gép szerepelnek a [SAP Megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533). A éles forgatókönyvek az Azure virtuális gépek használatát, tekintse meg az SAP HANA hitelesített a közzétett SAP a virtuális gépek [hitelesített IaaS platformok listája](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Központi telepítése a virtuális gépeket az Azure-ban használatával:

- Az Azure-portálon.
- Az Azure PowerShell-parancsmagokkal.
- Az Azure parancssori felület.

Az Azure virtuális gép Services használatával teljes telepített SAP HANA-platform is telepítheti a [SAP felhőplatform](https://cal.sap.com/). A telepítési folyamat ismertetett [telepítése SAP S/4HANA vagy BW/4HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) vagy kiadott automatizálással [Itt](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Azure Storage típusának kiválasztása
Azure storage két olyan típusú, amelyek SAP HANA futtató Azure virtuális gépekhez megfelelő biztosítja:

- [Az Azure standard szintű tárolót](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Prémium szintű Azure Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Az Azure két telepítési módszerek kínál a VHD-k Azure Standard és prémium szintű Storage. Ha engedélyezi a teljes forgatókönyv, előnyeit [Azure kezelt lemez](https://azure.microsoft.com/services/managed-disks/) központi telepítések.

A tárolási típusok és azok SLA-k az IOPS- és tárolási teljesítmény listáját a [kezelt lemezeken Azure dokumentációjában](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Az Azure virtuális gépek a tárolás konfigurálása

Mindaddig, amíg a helyszíni SAP HANA-készülékek vásárolta, sem az i/o-alrendszerek és platformképességei ügyeljen arra, mert a készülék szállító győződjön meg arról, hogy a minimális tárhellyel kapcsolatos követelmények teljesülnek-e SAP Hana volt. Létrehozása az Azure-infrastruktúra saját magának, akkor is érdemes figyelembe ezt a követelményt a konfigurációs követelményeinek, javasoljuk, hogy a következő szakaszok is tudni némelyike. Olyan esetekben, ahol a virtuális gépek konfigurálja a kívánt vagy SAP HANA futtatnak. Egyes feltett jellemzőivel kellene eredményező:

- Egy legalább 1 MB i/o-méretek és 250 MB/s /hana/log olvasási/írási köteten engedélyezése
- Olvasási tevékenység legalább 400MB/s /hana/data az 16 MB és 64 MB i/o-méretek a engedélyezése
- Engedélyezze a legalább 250MB/s /hana/data 16 MB és 64 MB i/o-méretek és az írási tevékenység

Megadott alacsony tárolási késés fontos a adatbázis-kezelő rendszerek, még akkor is, mivel azokat, például SAP HANA memóriabeli adatok megőrzése. A kritikus Storage elérési út általában a tranzakciós napló írása az adatbázis-kezelő rendszerek körül. De is műveletek, például visszaállítási pontok írása, vagy a memóriabeli adatok betöltése után lehet, hogy kritikus összeomlás utáni helyreállítást. Ezért is kihasználhatják Azure Premium lemezek /hana/data és /hana/log kötetek esetében kötelező. A minimális átviteli sebességgel/hana/naplóhoz és az/hana/SAP által megkívánt mértéket kell felépítenie RAID 0 MDADM vagy LVM over több prémium szintű Azure Storage-lemezek használatával, és használja a RAID-kötetek/hana/adatok és a/hana/naplózási kötetek. A RAID 0 a méretének paritásos az ajánlás szerint, hogy használja:

- 64 KB-os vagy 128K/hana/adatok
- 32 KB-os/hana/napló

> [!NOTE]
> Nem kell minden RAID-kötetek használatával, mert prémium és standard szintű tárolást tartsa egy virtuális merevlemez három képek redundancia szint konfigurálása. A használati RAID kötet tisztán konfigurálásához, amely elegendő i/o-átviteli kötetek.

Azure virtuális merevlemezek RAID alatt számos gyűlik, egy IOPS- és tárolási teljesítmény oldaláról adódnak össze. Igen ha egy RAID 0 x 3 P30 prémium szintű Azure Storage-lemezek keresztül, akkor biztosítani fogja háromszor az IOPS, és állítsa be háromszor a tárterületek átviteli sebességének egyetlen Azure Premium Storage P30 lemez.

Prémium szintű Storage /hana/data és /hana/log használt lemezeken gyorsítótárazás ne állítson be. Minden, a összeállítása a köteteket lemezeken nem lehetnek, "None" értékre lemezek gyorsítótárazását.

A teljes méretű i/o-teljesítményt is vegye figyelembe, méretezése vagy egy virtuális gép számára legmegfelelőbb. A teljes VM tárterületek átviteli sebességének a cikkben ismertetett [memóriaoptimalizált virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Költség tudatos Azure tárolás beállítása
A következő táblázat olyan konfigurációja, az ügyfelek a gyakran használt Virtuálisgép-típusokon gazdagépre SAP HANA Azure virtuális gépeken. Előfordulhat, hogy néhány VM-típust, amely nem felel meg a minimális feltételeket SAP Hana. De eddig virtuális gépek úgy tűnik, nem éles környezetben részletes végrehajtani. 

> [!NOTE]
> Éles környezetben, ellenőrizze, hogy egy bizonyos VM típust támogatja SAP Hana az SAP a [IAAS SAP dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



| VIRTUÁLIS GÉP TERMÉKVÁLTOZAT | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adatainak és naplókönyvtárainak/hana /<br /> csíkozott LVM vagy MDADM | / hana/megosztott | / root kötet | / usr/sap | Hana, illetve biztonsági mentési |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


A lemezek számára a kisebb méretű meg kell adnia a 3 x P20 oversize kapcsolatos ajánlások a következők szerint terület kötetek ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). A táblázatban megjelenő lehetősége azonban elegendő lemez átviteli sebesség biztosításához SAP Hana történt. Ha megőrzi a biztonsági mentések, amelyek megfelelnek a memória kétszeresével lett méretezett, /hana/backup kötet módosításai kell nyugodtan beállítása.   
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt feladat. Az alkalmazások és szolgáltatások magasabb kötetek /hana/data és /hana/log igényel, ha szüksége az Azure Premium Storage VHD-k számának növeléséhez. Olyan kötetet, amelyen több virtuális merevlemezeket, mint a felsorolt méretezése fog növelhető iops-érték és az i/o az Azure virtuális gép típus keretein belül. 

> [!NOTE]
> A fenti beállítások nem előnyös [Azure virtuális gép egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) mivel azt használja a prémium szintű Azure Storage és az Azure standard szintű tárolást. Azonban a kijelölés választott ahhoz, hogy optimalizálni a költségeket.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Egyetlen VM SLA kiválassza kihasználják az Azure tárolási konfigurációt
Ha azt szeretné, hogy igénybe vehesse az [Azure virtuális gép egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), kell használnia kizárólag Azure Premium Storage virtuális merevlemezeket.

> [!NOTE]
> Éles környezetben, ellenőrizze, hogy egy bizonyos VM típust támogatja SAP Hana az SAP a [IAAS SAP dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| VIRTUÁLIS GÉP TERMÉKVÁLTOZAT | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adatainak és naplókönyvtárainak/hana /<br /> csíkozott LVM vagy MDADM | / hana/megosztott | / root kötet | / usr/sap | Hana, illetve biztonsági mentési |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


A lemezek számára a kisebb méretű meg kell adnia a 3 x P20 oversize kapcsolatos ajánlások a következők szerint terület kötetek ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). A táblázatban megjelenő lehetősége azonban elegendő lemez átviteli sebesség biztosításához SAP Hana történt. Ha megőrzi a biztonsági mentések, amelyek megfelelnek a memória kétszeresével lett méretezett, /hana/backup kötet módosításai kell nyugodtan beállítása.  
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt feladat. Az alkalmazások és szolgáltatások magasabb kötetek /hana/data és /hana/log igényel, ha szüksége az Azure Premium Storage VHD-k számának növeléséhez. Olyan kötetet, amelyen több virtuális merevlemezeket, mint a felsorolt méretezése fog növelhető iops-érték és az i/o az Azure virtuális gép típus keretein belül. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Az Azure írási gyorsító M-sorozat Azure virtuális gépek tárolási megoldás
Azure írási gyorsító az első megkezdődött M sorozatú virtuális gépek kizárólag funkcionalitást. Állapota a neve, mint a funkció célja javítása a prémium szintű Azure Storage elleni írások késését i/o. SAP Hana írási gyorsító kellene használható csak a /hana/log kötet szemben. Ezért látható, amennyiben a konfigurációk módosítani kell. A fő módosítása a /hana/data és /hana/log közötti darabolását ahhoz, hogy Azure írási gyorsító csak a /hana/log köteten. 

> [!IMPORTANT]
> M-sorozat Azure virtuális gépek SAP HANA-hitelesítésszolgáltató kizárólag Azure írási gyorsító a /hana/log kötet jelenti. Ennek eredményeképpen forgatókönyv SAP HANA üzemelő Azure M sorozatú virtuális gépek várt Azure írási gyorsító a /hana/log kötet kell konfigurálni.  

> [!NOTE]
> Éles környezetben, ellenőrizze, hogy egy bizonyos VM típust támogatja SAP Hana az SAP a [IAAS SAP dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Az ajánlott konfiguráció látható:

| VIRTUÁLIS GÉP TERMÉKVÁLTOZAT | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adatok | / hana/napló | / hana/megosztott | / root kötet | / usr/sap | Hana, illetve biztonsági mentési |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt feladat. Az alkalmazások és szolgáltatások magasabb kötetek /hana/data és /hana/log igényel, ha szüksége az Azure Premium Storage VHD-k számának növeléséhez. Olyan kötetet, amelyen több virtuális merevlemezeket, mint a felsorolt méretezése fog növelhető iops-érték és az i/o az Azure virtuális gép típus keretein belül.

Azure írási gyorsító csak működik együtt [Azure által kezelt lemezeken](https://azure.microsoft.com/services/managed-disks/). Ezért legalább a prémium szintű Azure Storage-lemezek /hana/log kötet képező kell felügyelt lemezként üzembe helyezni.

Nincsenek Azure prémium szintű Storage a VHD-k, amely támogatja-e írási gyorsító Azure virtuális gépenként korlátozások. A jelenlegi korlátozások a következők:

- Egy M128xx 16 virtuális merevlemezek méretű VM
- Egy M64xx 8 virtuális merevlemezek méretű VM

Ahhoz, hogy Azure írási gyorsító részletes útmutatást a cikkben található [Azure írási gyorsító SAP telepítések](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/how-to-enable-write-accelerator).

Részletek és Azure írási gyorsító korlátozásai a azonos dokumentációjában található.


### <a name="set-up-azure-virtual-networks"></a>Az Azure virtuális hálózat beállítása
Ha a hely-hely kapcsolatot az Azure VPN- vagy ExpressRoute keresztül, rendelkeznie kell legalább egy [Azure-beli virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , amely a VPN- vagy ExpressRoute körön virtuális átjáró csatlakozik. A virtuális átjáró él, az Azure virtuális hálózat egyik alhálózatát. SAP HANA telepítéséhez hozzon létre két további alhálózatokat a virtuális hálózaton belül. Egy alhálózatot a virtuális gépek futtatásához a SAP HANA-példányokat futtatja. A más alhálózati Jumpbox vagy a felügyeleti virtuális gépek üzemeltetéséhez SAP HANA Studio vagy más felügyeleti szoftverek fut.

A virtuális gépek futtatásához SAP HANA telepítésekor kell a virtuális gépek:

- Két virtuális hálózati adaptert telepített: csatlakozni a felügyeleti alhálózat egy hálózati Adapterre, és csatlakozni a helyi hálózaton vagy más hálózatokhoz, a SAP HANA-példányra, az Azure virtuális gép egyetlen hálózati Adapterrel.
- Statikus magánhálózati IP-címek, amelyek telepítve vannak az mindkét virtuális hálózati adapter.

A különböző módszereket a IP-címek hozzárendelése áttekintését lásd: [IP-cím, típusok és az Azure-ban elosztási módszer](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Az SAP HANA futó virtuális gépeket statikus IP-címtartományból együtt kell működnie. Oka az, hogy néhány konfigurációs attribútum Hana hivatkoznak-e IP-címeket.

[Azure hálózati biztonsági csoportokkal (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) segítségével a forgalmat, annak biztosítására, hogy az SAP HANA-példány vagy a Jumpbox. Az NSG-k legyenek társítva a SAP HANA-alhálózatot és a felügyeleti alhálózatot.

Az alábbi ábrán egy durva telepítési séma áttekintést SAP Hana:

![SAP HANA nyers telepítési sémája](media/hana-vm-operations/hana-simple-networking.PNG)


Az Azure-ban SAP HANA-helyek létesített kapcsolat nélkül telepítéséhez elérni az SAP HANA-példány, ha egy nyilvános IP-címet. Az IP-cím hozzá kell rendelni a Jumpbox virtuális Gépet futtató Azure virtuális Gépen. Alapvető ilyenkor a központi telepítés állomásnevek feloldani az Azure beépített DNS-szolgáltatásokra támaszkodik. Egy összetettebb környezetben, amennyiben a nyilvánosan elérhető IP-címeket használnak az Azure beépített DNS-szolgáltatások különösen fontosak. Azure NSG-k segítségével korlátozhatja a megnyitott portok vagy IP-címtartományok nyilvánosan elérhető IP-címmel rendelkező eszközök Azure alhálózatokra csatlakoztatható. Az alábbi ábrán egy durva séma üzembe helyezéséhez SAP HANA-helyek létesített kapcsolat nélkül:
  
![Nyers telepítési séma SAP Hana-helyek létesített kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure virtuális gépeken SAP HANA telepítéséhez műveletek
A következő szakaszok ismertetik az Azure virtuális gépeken SAP HANA-rendszerek telepítésével kapcsolatos műveleteket.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Biztonsági mentése és visszaállítása az Azure virtuális gépeken futó műveletek
A következő dokumentumok biztonsági mentése és visszaállítása a SAP HANA-telepítés ismertetik:

- [SAP HANA biztonsági mentés áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA fájlszintű biztonsági mentése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA tárolási pillanatkép teljesítményteszt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Indítsa el, és indítsa újra a virtuális gépek, amelyek tartalmazzák az SAP HANA
Az Azure nyilvános felhőjében jól láthatóan elhelyezett szolgáltatása, hogy most felszámított csak a számítógépes percig. Például egy SAP HANA futtató virtuális gép leállítása, hogy díjon számlázzuk a csak a tárolási költségeket az ebben az időszakban. Egy másik szolgáltatás akkor használható, ha az első központi telepítésben a virtuális gépek a statikus IP-címet megadni. Egy SAP HANA rendelkező virtuális gép újraindítása esetén a virtuális gép korábbi IP-címéhez indul újra. 


### <a name="use-saprouter-for-sap-remote-support"></a>A SAP távoli támogatáshoz SAProuter használja
Ha a helyszíni helyek és az Azure közötti pont-pont kapcsolattal rendelkezik, és ezt követően valószínűleg már használ SAProuter SAP-összetevők futtatja. Ebben az esetben hajtsa végre a következő elemek távoli támogatásához:

- A magán- és statikus IP-címet a virtuális gép karbantartása, amely futtatja SAP HANA SAProuter konfigurációjában.
- Az NSG az alhálózat, a forgalom engedélyezése TCP/IP-port 3299 HANA virtuális Gépet üzemeltető konfigurálása.

Ha Azure az interneten keresztül csatlakozik, és az SAP HANA a virtuális gép nem rendelkezik egy SAP-útválasztó, majd szeretné telepíteni az összetevőt. Telepítse a SAProuter egy külön virtuális gépre, a felügyeleti alhálózaton. Az alábbi ábrán egy SAP HANA üzembe helyezéséhez, pont-pont kapcsolat nélkül és SAProuter nyers séma:

![Központi telepítés séma nyers SAP Hana webhelyek kapcsolat és SAProuter nélkül](media/hana-vm-operations/hana-simple-networking3.PNG)

Győződjön meg arról, SAProuter telepítéséhez, egy külön virtuális gépre, és nem a Jumpbox virtuális Gépet. A különálló virtuális gép rendelkeznie kell egy statikus IP-címet. A SAProuter csatlakozhat az SAP által futtatott SAProuter, forduljon a SAP az IP-címet. (Az SAP által futtatott SAProuter megfelelője a telepítése a virtuális Gépre SAProuter-példány.) Az IP-cím elérhető segítségével konfigurálhatja a SAProuter példányát. A konfigurációs beállításaiban az csak a szükséges port az 3299 TCP-portot.

Beállításához és karbantartásához távoli támogatási kapcsolatok keresztül SAProuter módjáról további információkért tekintse meg a [SAP dokumentáció](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Magas rendelkezésre állású az SAP HANA Azure natív virtuális gépeken
Ha a SUSE Linux 12 SP1 fut, vagy később, létrehozhat egy támasztja fürt STONITH eszközökkel. Az eszközök segítségével egy SAP HANA-konfiguráció, amely szinkron replikációt használ a HANA replikációs és automatikus feladatátvételt. A telepítés eljárással kapcsolatos további információkért lásd: [SAP HANA magas rendelkezésre állású útmutató az Azure virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
