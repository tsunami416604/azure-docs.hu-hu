---
title: SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban |} A Microsoft Docs
description: Üzemeltetési útmutató a SAP HANA-rendszerek üzembe helyezett Azure-beli virtuális gépeken.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/27/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba24fc1d75e2a2d2e50bfaba89de9404fc5e6c75
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325370"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban
Ez a dokumentum útmutatást nyújt az Azure-infrastruktúra konfigurálása és SAP HANA rendszereit az Azure-beli natív virtuális gépek (VM) üzembe helyezett működő. A dokumentum az SAP HANA kibővített M128s VM-termékváltozat konfigurációs információkat is tartalmaz. Ez a dokumentum nem célja, hogy cserélje le a standard szintű SAP dokumentációját, amely magában foglalja az alábbi tartalommal:

- [Az SAP-felügyeleti útmutató](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP telepítési útmutatóinak](https://service.sap.com/instguides)
- [SAP-megjegyzések](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató használatához az alábbi Azure-összetevők alapszintű ismeretét szüksége:

- [Azure-alapú virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Az Azure-hálózatok és virtuális hálózatok](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

SAP NetWeaver és más Azure-beli SAP összetevők kapcsolatos további információkért tekintse meg a [Azure-beli SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) szakaszában a [Azure dokumentációja](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Az alapszintű kapcsolatos szempontok
A következő szakaszok ismertetik az alapszintű üzembe helyezéséhez Azure virtuális gépeken SAP HANA rendszereit szempontjai.

### <a name="connect-into-azure-virtual-machines"></a>Az Azure-beli virtuális gépek csatlakoztatása
Leírtak szerint a [tervezési útmutatója az Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), két módszerrel alapszintű Azure virtuális gépeken való csatlakozáshoz:

- Kapcsolódás az internethez és a nyilvános végpontokat a Jump virtuális gép vagy a virtuális gépen futó SAP HANA keresztül.
- Csatlakozhat egy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) vagy az Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Hely – hely kapcsolatok VPN- vagy ExpressRoute szükség a termelési forgatókönyvekhez. Ez a kapcsolattípus is szükség van, amely termelési forgatókönyvekhez, ahol SAP software használják, nem éles környezethez. Az alábbi képen egy példa a webhelyek közötti kapcsolat látható:

![Webhelyek közötti kapcsolat](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Válassza ki az Azure-beli Virtuálisgép-típusok
A termelési forgatókönyvekhez használható Azure-beli Virtuálisgép-típusok szerepelnek a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Nem éles környezethez natív Azure-beli Virtuálisgép-típusok választéka áll rendelkezésére.

>[!NOTE]
> Nem éles környezethez, használja a virtuális gépek típusai, amelyek szerepelnek a [SAP-jegyzetnek #1928533](https://launchpad.support.sap.com/#/notes/1928533). Az Azure virtuális gépek használata a termelési forgatókönyvekhez, ellenőrizze az SAP HANA-tanúsítvánnyal rendelkező virtuális gépeket a közzétett SAP [Certified IaaS platformok listájához](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Üzembe helyezése az Azure-beli virtuális gépek használatával:

- Az Azure Portalon.
- Az Azure PowerShell-parancsmagokat.
- Az Azure parancssori felület.

Az Azure virtuális gép Services révén teljes telepített SAP HANA-platform is telepítheti a [SAP-felhőplatform](https://cal.sap.com/). A telepítés menetét a [üzembe helyezése az SAP S/4HANA vagy BW/4hana-t az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) vagy az automation kiadott [Itt](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Azure Storage típusának kiválasztása
Az Azure storage két típusú az SAP HANA futtató Azure virtuális gépek kiválóan alkalmasak kínál:

- [Az Azure standard szintű Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Az Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Az Azure két üzembe helyezési módszert kínál az Azure Standard és Premium Storage virtuális merevlemezek. Ha engedélyezi a teljes forgatókönyv, előnyeit [Azure felügyelt lemez](https://azure.microsoft.com/services/managed-disks/) központi telepítések.

A tárolási típusok és azok SLA-k, az IOPS és a tárolási teljesítmény listáját, tekintse át a [felügyelt lemezeket az Azure dokumentációjában](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Az Azure-beli virtuális gépek a tárolás konfigurálása

Mindaddig, amíg az SAP HANA-berendezések helyszíni vásárolt, soha nem érdeklik az i/o-alrendszerre és annak képességeit történt. Mivel az adott berendezés gyártójához győződjön meg arról, hogy a minimális tárhellyel kapcsolatos követelmények teljesülnek-e az SAP Hana-hoz szükséges. Létrehozása az Azure-infrastruktúra saját maga is is érdemes figyelembe vennie néhány ezeknek az igényeknek. És a konfigurációs követelmények, a következő szakaszokban javasolt is megismerheti. Olyan esetekben, ahol konfigurál a virtuális gépek a kívánt vagy SAP HANA futtatása Azure. A előrejelzéséhez, hogy a rendszer kéri eredményez van szükség:

- Engedélyezze az olvasási/írási kötet a **/hana/log** egy legalább 1 MB i/o-méretű 250 MB/s
- Enable olvasása tevékenység legalább 400MB/s a **/hana/adatok** 16 MB és 64 MB-os i/o-méretek
- Engedélyezze a legalább 250MB/s az írási tevékenység **/hana/adatok** , 16 MB és 64 MB-os i/o-mérettel

A megadott alacsony tárolási késései kritikus következményekkel járnak DBMS-rendszerek, még akkor is, mivel az adott DBMS, például SAP HANA, megtartása a memóriában. A tranzakciós napló írási a DBMS-rendszerek körül általában a storage-ban a kritikus útvonalat. De is műveletek, például visszaállítási pontok írása vagy a Betöltés a memóriában, kritikus fontosságú lehet olyan összeomlás utáni helyreállítást követően. Ezért esetében kötelező, kihasználhatja az Azure Premium szintű lemezekkel **/hana/adatok** és **/hana/log** köteteket. A minimális átviteli sebessége növelhető **/hana/log** és **/hana/adatok** igény szerint az SAP, RAID 0 létrehozásához szükséges MDADM vagy LVM több Azure Premium Storage-lemez használatával. És használja, mint a RAID-kötetek **/hana/adatok** és **/hana/log** köteteket. Ahogy a stripe-méretet nyújt a a RAID 0 a javaslat van használandó:

- 64 KB-os vagy 128KB-   **/hana/adatok**
- 32KB-   **/hana/log**

> [!NOTE]
> Nem kell bármilyen RAID-kötetek használatával, mivel a prémium és Standard tárterület tartsa egy virtuális merevlemez három rendszerkép tárhelyredundancia-szint konfigurálása. A RAID-kötetek használatának tisztán, hogy elegendő i/o-teljesítményt biztosító kötetek konfigurálni.

Halmozódó Azure virtuális merevlemezek RAID alatt számos, a halmozódnak az IOPS és a storage átviteli oldaláról. Tehát ha RAID 0 x 3 P30 Azure Premium Storage-lemez fölé helyezi, azt kell biztosíthatja háromszor az IOPS és háromszor a tárterületek átviteli sebességének egyetlen Azure Premium Storage P30 lemez.

Az alábbi gyorsítótárazási javaslatokat feltételezzük az i/o-jellemzőkkel az SAP Hana-hoz, hogy a lista:

- Nincs alig ellen a HANA-adatfájlokat olvasható számítási feladatait. Kivétel után indítsa újra a HANA-példány, vagy ha a HANA betöltött adatok nagy méretű i/o. Egy másik eset, nagyobb olvasási i adatok fájlokra vonatkozóan lehet HANA-adatbázis biztonsági mentése. Ennek eredményeképpen olvasási gyorsítótárazás többnyire nem értelmezhető óta az esetek többségében, minden adatkötetnél fájlt kell teljesen olvasható.
- A HANA-visszaállítási pontok és a HANA összeomlás utáni helyreállítást alapú adatlöketekkel bekövetkezik szemben az adatfájlok írása. Írás visszaállítási pontok aszinkron, és be minden felhasználói tranzakció nem tárolnak. Írása során összeomlás utáni helyreállítást adata teljesítmény kritikus fontosságú a rendszer ismét a gyors válaszadás érdekében. Azonban összeomlás utáni helyreállítást kell lennie, hanem kivételes esetekben
- A HANA ismétlés fájlok alig minden olvasási származnak. Kivétel a nagy i/o végrehajtásakor a tranzakciónapló biztonsági mentéseivel, összeomlás utáni helyreállítást, vagy a HANA-példányok újraindítása fázisban.  
- Az SAP HANA ismétlés naplófájl fő terhelés írások. Számítási feladat jellegétől függ rendelkezhet i 4 KB-os vagy más esetben i/o-méret 1 MB vagy annál kisebb. Az írási késést, szemben a SAP HANA-visszaállítási napló a teljesítmény kritikus fontosságú.
- A rendszer megőrzi a lemezen egy megbízható módon kell az összes írási művelet

Eredményeként ezek a megfigyelt IO-minták az SAP Hana a gyorsítótárazás az Azure Premium Storage tárolást használ a különböző kötetek kell beállítani hasonló:

- **/ hana/adatok** – nincs gyorsítótárazás
- **/ hana/log** – nincs gyorsítótárazás – M-sorozat (lásd a dokumentum későbbi) kivételt
- **/ hana/megosztott** – olvasási gyorsítótárazás


A teljes virtuális gép i/o-átviteli azt is vegye figyelembe, amikor méretezést, vagy egy virtuális gép mellett dönt. Teljes virtuális gép tárterületek átviteli sebességének a cikkben leírt [memóriahasználatra optimalizált virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>A Cost tudatos Azure Storage-konfiguráció
Az alábbi táblázat olyan konfigurációja, az ügyfelek által leggyakrabban használt Virtuálisgép-típusok az Azure virtuális gépeken futó SAP HANA-gazdagépre. Előfordulhat, hogy bizonyos Virtuálisgép-típusok nem megfelelő összes minimális követelmények az SAP Hana-hoz. Azonban eddig ezeken a virtuális gépeken tűnt részletes végrehajtani a nem éles környezethez. 

> [!NOTE]
> A termelési forgatókönyvekhez e egy adott virtuális gép típusa az SAP az SAP Hana támogatott ellenőrizze a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adat- és naplófájlok/hana /<br /> az LVM vagy MDADM csíkozott | / hana/megosztott | / root kötet | / usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


A lemezek esetében a 3 x P20 oversize típusokat a kisebb méretű virtuális gép a kötetek kapcsolatos ajánlások a következők szerint terület ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Azonban a választási lehetőség jelenik meg a tábla történt az SAP Hana elegendő adatátviteli sebességet biztosítanak. Ha módosításai van szüksége a **/hana/időpontokat**p kötet, amely lett méretezve, hogy a biztonsági mentések kétszer a memória kötet képviselő, nyugodtan módosíthatja.   
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a munkaterhelés igényel nagyobb kötetek **/hana/adatok** és **/hana/log**, növelje az Azure Premium Storage virtuális merevlemezeket kell. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül. 

> [!NOTE]
> A fenti konfigurációk nem kiaknázhatják a [Azure virtuális gép egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) óta az Azure Premium Storage és az Azure standard szintű tárolást használnak. Azonban a kijelölt lett választva, a költségek optimalizálása érdekében.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Az Azure Storage konfiguráció számára, hogy egyetlen virtuális gép SLA értekezlet
Ha azt szeretné, hogy [Azure virtuális gép egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), kell használnia kizárólag az Azure Premium Storage virtuális merevlemezeket.

> [!NOTE]
> A termelési forgatókönyvekhez e egy adott virtuális gép típusa az SAP az SAP Hana támogatott ellenőrizze a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adat- és naplófájlok/hana /<br /> az LVM vagy MDADM csíkozott | / hana/megosztott | / root kötet | / usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


A lemezek esetében a 3 x P20 oversize típusokat a kisebb méretű virtuális gép a kötetek kapcsolatos ajánlások a következők szerint terület ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Azonban a választási lehetőség jelenik meg a tábla történt az SAP Hana elegendő adatátviteli sebességet biztosítanak. Ha módosításai van szüksége a **/hana/biztonsági mentés** kötet, amely lett méretezve, hogy a biztonsági mentések kétszer a memória kötet képviselő, nyugodtan módosíthatja.  
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a munkaterhelés igényel nagyobb kötetek **/hana/adatok** és **/hana/log**, növelje az Azure Premium Storage virtuális merevlemezeket kell. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül. 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Az Azure M sorozatú virtuális gépek Azure Write Accelerator tárolási megoldás
Az Azure Írásgyorsító egy funkció, amely a kezdeti bevezetési az M-sorozat virtuális gépei kizárólag. Az államok neve, ahogy az a funkciók célja, hogy javítása az Azure Premium Storage írására i/o-késését. Az SAP Hana, Write Accelerator használható kellene a **/hana/log** csak kötetre. Ezért a konfiguráció látható, amennyiben módosítani kell. A legfontosabb változás, a darabolása között a **/hana/data** és **/hana/log** Azure Write Accelerator elleni használatához a **/hana/log** csak a kötetre. 

> [!IMPORTANT]
> SAP HANA-minősítési Azure M sorozatú virtuális gépek kizárólag a Azure Write Accelerator van a **/hana/log** kötet. Ennek eredményeképpen éles forgatókönyvet az SAP HANA üzembe helyezéseket az Azure M sorozatú virtuális gépek várható Azure Write Accelerator konfigurálni a **/hana/log** kötet.  

> [!NOTE]
> A termelési forgatókönyvekhez e egy adott virtuális gép típusa az SAP az SAP Hana támogatott ellenőrizze a [IAAS SAP dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Ajánlott konfigurációkat a következőhöz hasonló:

| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adatok | / hana/log | / hana/megosztott | / root kötet | / usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 giB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a munkaterhelés igényel nagyobb kötetek **/hana/adatok** és **/hana/log**, növelje az Azure Premium Storage virtuális merevlemezeket kell. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül.

Az Azure Írásgyorsító csak együtt működik [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Tehát legalább az Azure Premium Storage-lemez versenytársaihoz képest a **/hana/log** kötetet kell üzembe helyezni, felügyelt lemezeket.

Az Azure Premium Storage virtuális merevlemezek Azure Write Accelerator támogató virtuális gépenként korlátozva van. A jelenlegi korlátok a következők:

- 16 virtuális merevlemezek egy M128xx VM
- 8 virtuális merevlemezek egy M64xx VM
- 4 virtuális merevlemezek egy M32xx VM

A cikkben található részletes útmutatást nyújt az Azure Írásgyorsító engedélyezése [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Részletek és az Azure Írásgyorsító korlátozásai a azonos dokumentációjában található.



### <a name="set-up-azure-virtual-networks"></a>Az Azure virtuális hálózatok beállítása
Ha helyek közötti kapcsolat az Azure-ba, VPN vagy ExpressRoute-n keresztül, rendelkeznie kell legalább egy Azure virtuális hálózat, amely a VPN- vagy ExpressRoute-kapcsolatcsoporthoz virtuális átjárón keresztül csatlakozik. Egyszerű üzembe a virtuális átjáró is telepíthető, az Azure virtuális hálózat (VNet), valamint az SAP HANA-példányok üzemeltető alhálózatán. Az SAP HANA telepítése, hozzon létre az Azure virtuális hálózatban két további alhálózatokat. Egy alhálózatot a virtuális gépek futtatásához az SAP HANA-példányok üzemelteti. A többi alhálózat Jumpbox vagy a felügyeleti virtuális gépek üzemeltetéséhez, SAP, HANA Studio, a más felügyeleti szoftverek vagy az alkalmazás szoftver fut.

SAP HANA futtatásához a virtuális gépek telepítésekor a virtuális gépek van szükség:

- Telepítve van, a két virtuális hálózati adapter: szeretne csatlakozni a felügyeleti alhálózat egy hálózati adapter és a példányhoz való kapcsolódáshoz a helyszíni hálózat vagy a más hálózatokból, az SAP HANA az Azure-beli virtuális gépen több hálózati Adapterrel.
- Statikus magánhálózati IP-címek mindkét virtuális hálózati adapterek vannak telepítve.

Azonban, amelyek támpontul telepítések esetén szeretne létrehozni egy virtuális adatközpont hálózati architektúra az Azure-ban. Ez az architektúra az Azure virtuális hálózat átjáró, amely csatlakozik egy külön Azure vnetbe helyszíni szétválasztása javasolja. A virtuális hálózattal külön kell futtatni a forgalmat, hogy helyszíni vagy vagy az interneten. Ez a megközelítés lehetővé teszi a vizsgálati és naplózási forgalmat, amely a virtuális adatközpont belép az Azure-ban a külön agyi virtuális hálózat szoftvereket telepíthet. Ezért kell egy virtuális hálózat, amelyen a szoftverek és konfigurációk, hogy az Azure-alapú in - és kimenő adatforgalom vonatkozik.

A cikkek [Azure Virtual Datacenter: A hálózati nézőpont](https://docs.microsoft.com/azure/networking/networking-virtual-datacenter) és [Azure Virtual Datacenter és a vállalati Vezérlősík](https://docs.microsoft.com/azure/architecture/vdc/) a virtual datacenter megközelítésével a további információkat és a kapcsolódó Az Azure VNet-Tervező.


>[!NOTE]
>Agyi virtuális hálózat és küllő virtuális hálózat használatával közötti forgalom [Azure virtuális hálózatok közötti társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) további tárgya [költségek](https://azure.microsoft.com/pricing/details/virtual-network/). Azok a költségek alapján, szüksége lehet tervezhet a feltörések közötti szigorú küllős hálózati kialakítások fut, és több [Azure ExpressRoute-átjáró](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) , hogy csatlakozni "küllők" annak érdekében, hogy a virtuális hálózatok közötti társviszony megkerülése. Azonban az Azure ExpressRoute-átjáró bevezetése további [költségek](https://azure.microsoft.com/pricing/details/vpn-gateway/) is. Emellett használhatja a hálózati forgalom logging, naplózás és figyelés harmadik féltől származó szoftverek számára további költségek merülhetnek fel. A költségek az adatcseréhez oldalán és további Azure ExpressRoute-átjáró és a egy másik szoftver licencek által létrehozott virtuális hálózatok közötti társviszony útján függ, dönthet úgy mikroszegmentációt egy Vneten belül az elkülönítési egység alhálózatok segítségével helyett virtuális hálózatok.


Az IP-címet rendelni különböző módszerek áttekintését lásd: [IP-címtípusokat és foglalási módszereket az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

A virtuális gépeken futó SAP HANA együttműködve kell hozzárendelt statikus IP-címeket. Oka, hogy néhány konfigurációs attribútum Hana hivatkozhatnak az IP-címeket.

[Az Azure hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) forgalmat, annak biztosítására, hogy az SAP HANA-példányt, vagy a jumpbox irányíthatók. Az NSG-t, és végül [az alkalmazásbiztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) társítva az SAP HANA-alhálózat és a felügyeleti alhálózaton.

Az alábbi képen egy durva telepítési séma áttekintése az SAP Hana egy küllős topológiájú architektúránk VNet a következő:

![Nyers telepítési séma az SAP Hana-hoz](media/hana-vm-operations/hana-simple-networking.PNG)

Az Azure-beli SAP HANA telepítése egy helyek közötti kapcsolat nélkül, továbbra is szeretné védelme biztosítható az SAP HANA-példány a nyilvános interneten keresztül és a egy továbbítói proxy mögött elrejtéséhez. Ez a alapvető forgatókönyvben az üzembe helyezés Azure beépített DNS-szolgáltatások gazdanév feloldása támaszkodik. Egy összetettebb telepítésben lévő nyilvános IP-címeket használja, az Azure beépített DNS-szolgáltatások különösen fontosak. Azure NSG-k és [Azure az nva-k](https://azure.microsoft.com/solutions/network-appliances/) szabályozhatja, az Útválasztás az internetről az Azure VNet-architektúra az Azure-ban történő figyeléséhez. Az alábbi képen látható, az SAP HANA telepítése egy küllős architektúra virtuális hálózat a helyek közötti kapcsolat nélkül hozzávetőleges sémát:
  
![Nyers telepítési séma az SAP Hana-hoz egy helyek közötti kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Egy másik Azure az nva-k eléréséhez használt szabályozhatja és figyelheti az internetes anélkül, hogy a hub és küllő virtuális hálózatok közötti architektúra talál a cikk [magas rendelkezésre állású hálózati virtuális berendezések üzembe helyezése](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>SAP HANA horizontális felskálázás az Azure-infrastruktúra konfigurálása
A Microsoft egy M-sorozat Virtuálisgép-Termékváltozat, amely egy SAP HANA kibővített konfigurációs minősítéssel rendelkezik. A virtuális gép M128s típusa van megkapta az egy kibővített legfeljebb 16 csomóponttal. Az Azure virtuális gépeken SAP HANA-kibővített minősítések módosításokat, ellenőrizze [Certified IaaS platformok listájához](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

A minimális operációsrendszer-kiadások kibővített konfigurációkhoz az Azure-beli virtuális gépek üzembe helyezéséhez a következők:

- SUSE Linux 12 SP3
- Red hat Linux 7.4

A 16 csomópont kibővített minősítés

- Egy csomópont a fő csomópont
- Legfeljebb 15 csomópontok a munkavégző csomópontok

>[!NOTE]
>Az Azure-beli Virtuálisgép-bővítő telepítés nincs lehetőség a készenléti csomópont használata
>

Nem tudja készenléti csomópontok konfigurálásához az az oka rendszer kétszeres:

- Az Azure ezen a ponton rendelkezik egyetlen natív NFS-szolgáltatás. Ennek eredményeképpen NFS-megosztások konfigurálni kell a külső funkciók segítségével.
- A külső NFS konfigurációk sem képes eleget tenni a tárolási késés feltételek az üzembe helyezett Azure-beli megoldásuk az SAP Hana-hoz.

Ennek eredményeképpen **/hana/adatok** és **/hana/log** kötetek nem oszthatók meg. Nem oszt meg ezeket a köteteket egyetlen csomópont, megakadályozza, hogy az SAP HANA készenléti csomópont kibővített konfigurációban használatát.

Ennek eredményeképpen az alapvető tervezési kibővített konfigurációban egy csomópont esetén a következő módon történik:

![Horizontális felskálázás alapjait egyetlen csomópont](media/hana-vm-operations/scale-out-basics.PNG)

Az alapkonfiguráció egy virtuális gép csomópont SAP HANA kibővített hasonlóan néz ki:

- A **/hana/megosztott**, akkor építse ki az NFS magas rendelkezésre állású fürt SUSE Linux 12 SP3 alapján. Ez a fürt üzemelteti a **/hana/megosztott** NFS megosztás(ok) a kibővített konfigurációs és az SAP NetWeaver vagy BW/4HANA Central Services. Hozhat létre ilyen konfiguráció dokumentáció áll rendelkezésre az a cikkben [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Az összes többi lemez kötetek **nem** között a különböző csomópontokon és a megosztott **nem** NFS alapján. Telepítési beállítások és lépések a horizontális felskálázás HANA telepítések a nem megosztott **/hana/adatok** és **/hana/log** biztosított lejjebb ebben a dokumentumban.

>[!NOTE]
>Az NFS magas rendelkezésre állású fürt, amennyiben jelenik meg a grafikus elemek az SUSE Linux csak támogatott. Red hat-alapú NFS magas rendelkezésre állású megoldások javasolni fogja, később.

A csomópontok a kötetek méretezése a ugyanaz, mint a vertikális felskálázás kivételével **/hana/megosztott**. A virtuális gép M128s termékváltozat a javasolt méretek és -típusok kinéznie:

| A VM-TERMÉKVÁLTOZATOK | RAM | Legfeljebb VIRTUÁLIS GÉP I/O<br /> Teljesítmény | / hana/adatok | / hana/log | / root kötet | / usr/sap | Hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a munkaterhelés igényel nagyobb kötetek **/hana/adatok** és **/hana/log**, növelje az Azure Premium Storage virtuális merevlemezeket kell. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül. Is vonatkoznak az Azure Írásgyorsító lemezek alkotó a **/hana/log** kötet.
 
A dokumentum [SAP HANA TDI tárhellyel kapcsolatos követelmények](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), a képlet neve, amely meghatározza a mérete a **/hana/megosztott** köteten a horizontális felskálázás egy 4 munkavégző csomópontok egyetlen feldolgozó csomópontonkénti memória méretének.

Ha az SAP HANA kibővített minősített M128s Azure virtuális gép nagyjából 2 TB memóriával is, az SAP javaslatokat például lehet összegezni:

- Egy fő csomópontot és négy munkavégző csomópont, akár a **/hana/megosztott** köteten kell lennie 2 TB-os méret. 
- Egy fő csomópontot és öt és nyolc munkavégző csomópontok méretét **/hana/megosztott** 4 TB-ig kell lennie. 
- Egy fő csomópontot és 9 és 12 feldolgozó csomópontokat, a 6 TB méret **/hana/megosztott** lenne szükséges. 
- Egy fő csomópontot és 12 és 15 munkavégző csomópontok közötti használ, akkor szükség egy **/hana/megosztott** kötet, amely 8 TB méretig.

A többi fontos kialakítási, a képeket a horizontális felskálázás SAP HANA virtuális gép egyetlen csomópont-konfiguráció megjelenített a virtuális hálózat vagy jobban az alhálózati konfigurációt. SAP erősen ajánlja az ügyfélalkalmazás/irányuló forgalom elkülönítése, a HANA-csomópontok közötti kommunikáció. Mint a grafikák, a cél a virtuális Géphez csatlakoztatott két különböző Vnic küszöbölni érhető el. Mindkét Vnic külön alhálózatokon vannak, a két különböző IP-címet. Ezután szabályozhatja a forgalom útválasztási szabályokat az NSG-k vagy felhasználó által megadott útvonalak segítségével.

Az Azure-ban, különösen nincsenek azt jelenti, hogy és módszerek kényszerítéséhez a szolgáltatás és az adott Vnic kvótái minőségét. Ennek eredményeképpen ügyfélalkalmazás/irányuló és a hitelesítést a csomóponton belüli kommunikáció szétválasztása nem nyitható meg minden olyan lehetőségeket a forgalmat egy stream rangsorolhatja a másikhoz képest. Ehelyett a elválasztása a hitelesítést a csomóponton belüli kommunikációhoz a horizontális felskálázás konfigurációk védelmi biztonsági intézkedés marad.  

>[!IMPORTANT]
>SAP erősen ajánlja, megadhat, az ügyfélalkalmazás/részére a hálózati forgalom és a hitelesítést a csomóponton belüli forgalom ebben a dokumentumban leírtak szerint. Ezért üzembe az architektúra helyen, ahogyan az az utolsó grafikus erősen ajánlott.
>

Egy hálózati szempontból a minimálisan szükséges hálózati architektúra módon jelenik meg:

![Horizontális felskálázás alapjait egyetlen csomópont](media/hana-vm-operations/scale-out-networking-overview.PNG)

Eddig támogatott határértékeket 15 feldolgozó további a egy fő csomóponttal.

A tároló szempontjából a tároló-architektúra módon jelenik meg:


![Horizontális felskálázás alapjait egyetlen csomópont](media/hana-vm-operations/scale-out-storage-overview.PNG)

A **/hana/megosztott** köteten található az NFS-megosztás magas rendelkezésre állású konfigurációban. Mivel az összes többi meghajtók "helyi" csatlakoztatva vannak az egyes virtuális gépekhez. 

### <a name="highly-available-nfs-share"></a>Magas rendelkezésre állású az NFS-megosztások
Az NFS magas rendelkezésre állású fürt, amennyiben dolgoznak SUSE Linux csak. A dokumentum [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) ismerteti, hogyan állítsa be. Ha más HANA konfigurációs kívül az azure virtuális hálózat, amely futtatja az SAP HANA-példányok nem osztjuk meg az NFS-fürt, telepítse ugyanazon a vneten. Telepítse a saját alhálózatán található, és győződjön meg arról, hogy nem minden tetszőleges forgalom hozzáférhet az alhálózat. Ehelyett szeretné korlátozni a forgalom arra az alhálózatra, az IP-címek a virtuális gép által végrehajtása forgalom **/hana/megosztott** kötet.

HANA horizontális felskálázás a virtuális gépek, amelyek kell irányítani a virtuális hálózati adapter kapcsolódik a **/hana/megosztott** forgalom, a javaslatok láthatók:

- Forgalom óta **/hana/megosztott** az üzletmenetre gyakorolt közepes, irányítja át a virtuális hálózati adapter, amely az ügyfél-hálózatot is a minimális konfigurációs van rendelve
- Végül a felé irányuló forgalom **/hana/megosztott**, üzembe helyezése a harmadik alhálózat a SAP HANA horizontális felskálázás konfigurálása és hozzárendelése egy harmadik virtuális hálózati adapter az alhálózatban lévő üzemeltetett telepítése a virtuális hálózaton. A harmadik virtuális hálózati adapter és a társított IP-címet használja az NFS-megosztások a forgalmat. Ezután külön hozzáférés és alkalmazhat útválasztási szabályokat.

>[!IMPORTANT]
>Az SAP HANA üzembe helyezett kibővíthető módon rendelkező virtuális gépeken és a magas rendelkezésre állású NFS közötti hálózati forgalom előfordulhat, hogy semmilyen körülmények között útválasztása egy [NVA](https://azure.microsoft.com/solutions/network-appliances/) vagy hasonló virtuális készülékek. Mivel az NSG-k Azure nincs ilyen típusú eszközök. Ellenőrizze az útválasztási szabályokat annak érdekében, hogy az nva-k vagy hasonló virtuális berendezéseken detoured mikor elérje a virtuális gépeken futó SAP HANA a magas rendelkezésre állású NFS-megosztás.
> 

Ha szeretné megosztani a magas rendelkezésre állású NFS fürt között az SAP HANA-konfigurációk, helyezze át minden HANA konfigurációkat ugyanazon a Vneten. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Horizontális felskálázás n Azure SAP HANA telepítése
Horizontális felskálázás SAP konfiguráció telepítése, kell elvégeznie a nyers lépéseit:

- Új vagy az új Azure-beli virtuális infrastruktúra gyakorlatainak
- Az Azure virtuális gépek felügyelt telepítése az új prémium szintű Storage-kötetek
- Üzembe helyezése egy új vagy meglévő magas rendelkezésre állású NFS fürt alakítása a
- Alkalmazkodjon a hálózati útválasztást, győződjön meg arról, hogy, például hitelesítést a csomóponton belüli virtuális gépek közötti kommunikáció nem infrastruktúráján halad át, egy [NVA](https://azure.microsoft.com/solutions/network-appliances/). Ugyanez vonatkozik a virtuális gépek és az NFS magas rendelkezésre állású fürt közötti forgalom.
- Telepítse a SAP HANA fő csomóponttal.
- Alkalmazkodjon a SAP HANA-főcsomópont konfigurációs paraméterek
- Folytassa a telepítést, a SAP HANA munkavégző csomópontok

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Horizontális felskálázás a konfigurációban az SAP HANA telepítése
Helyezünk üzembe az Azure-beli Virtuálisgép-infrastruktúra, és minden egyéb előkészített történik, ahogy az alábbi lépéseket az SAP HANA kibővített konfigurációkhoz telepítenie kell:

- Telepítse az SAP HANA főcsomópont SAP dokumentáció alapján
- **A telepítés után kell a global.ini fájl módosítása, és adja hozzá a paraméter "basepath_shared = nem", a global.ini**. Ez a paraméter lehetővé teszi az SAP HANA futtatása nélkül "shared" kibővített **/hana/adatok** és **/hana/log** köteteket a csomópontok között. Részletek vannak dokumentálva [SAP Megjegyzés #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Miután megváltoztatta a global.ini paraméter, indítsa újra az SAP HANA-példány
- Adja hozzá a további feldolgozó csomópontokat. Lásd még: <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Adja meg az SAP HANA-csomópontok közötti kommunikáció a telepítés során a belső hálózaton vagy ezt követően használja, például a helyi hdblcm. További részletes dokumentációt: is [SAP Megjegyzés #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

A telepítő rutin a következő futtatásához használja a nem megosztott lemezeket a kibővített konfigurációs telepített fog **/hana/adatok** és **/hana/log**. Mivel a **/hana/megosztott** kötet helyez el a magas rendelkezésre állású az NFS-megosztások.
  


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operations for SAP HANA az Azure virtuális gépek üzembe helyezése
A következő szakaszok ismertetik az Azure virtuális gépeken futó SAP HANA rendszereit telepítésével kapcsolatos műveleteket.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Biztonsági mentése és helyreállítása Azure virtuális gépeken való műveletek
A következő dokumentumok bemutatják, hogyan biztonsági mentése és visszaállítása az SAP HANA üzembe:

- [SAP HANA biztonsági mentés áttekintése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA fájlszintű biztonsági mentés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA tárolási pillanatkép teljesítményteszt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Indítsa el, és indítsa újra a virtuális gépek, amelyek tartalmazzák az SAP HANA
Az Azure nyilvános felhő kiemelkedő funkciója, hogy díjkötelesek csak a számítási perc. Például az SAP HANA rendszerű virtuális gép leállításakor díjköteles csak a tárolási költségeket ebben az időszakban. Egy másik szolgáltatás a statikus IP-címek a virtuális gépek számára az első központi telepítésben megadása esetén érhető el. Ha újraindítja a virtuális gép, amely rendelkezik az SAP HANA, a virtuális gép újraindul, a korábbi IP-címmel. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP távoli támogatáshoz SAProuter használja
Ha helyszíni helyhez és az Azure között helyek közötti kapcsolatot, és futtatja az SAP-összetevőket, majd már valószínűleg futtat SAProuter. Ebben az esetben hajtsa végre a következő elemek távoli támogatás:

- A magán- és statikus IP-cím a virtuális gép a SAProuter konfigurációban futtató SAP HANA kezelése.
- Az NSG-t, amelyen a HANA virtuális Gépet a TCP/IP-port 3299 engedélyezett a forgalom az alhálózat megadása

Ha Azure-t az internethez csatlakozik, és a egy SAP-útválasztó nem rendelkezik a virtuális gép az SAP HANA, majd szeretné telepíteni az összetevőt. Telepítse a SAProuter egy külön virtuális gépre, a felügyeleti alhálózaton található. Az alábbi képen látható, az SAP HANA telepítése nélkül a helyek közötti kapcsolat és SAProuter hozzávetőleges sémát:

![Üzembe helyezés séma nyers az SAP Hana-hoz egy helyek közötti kapcsolat és SAProuter nélkül](media/hana-vm-operations/hana-simple-networking3.PNG)

Mindenképp SAProuter telepítsen egy külön virtuális gépre, és nem a Jumpbox virtuális Géphez. A különálló virtuális gép statikus IP-címmel kell rendelkeznie. A SAProuter csatlakozni az SAP által futtatott SAProuter, lépjen kapcsolatba az SAP egy IP-címet. (Az SAP által futtatott SAProuter a többszörözi a virtuális Gépen telepít SAProuter példány.) Az SAP-IP-cím segítségével konfigurálhatja a SAProuter példány. A konfigurációs beállításokat a csak a szükséges port TCP-port 3299.

Állítsa be, és távoli támogatási kapcsolatok keresztül SAProuter felügyeletével kapcsolatban lásd: a [SAP dokumentációjában](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Magas rendelkezésre állás az SAP HANA az Azure natív virtuális gépein
Ha a SUSE Linux 12 SP1-et futtatja, vagy később is létrehozhat egy támasztja fürt STONITH eszközökkel. Az eszközök segítségével egy SAP HANA-konfigurációt, amely a szinkron replikációt használ a HANA-Rendszerreplikálást és automatikus feladatátvételi beállítása. A telepítési eljárás kapcsolatos további információkért lásd: [SAP HANA magas rendelkezésre állású útmutató az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
