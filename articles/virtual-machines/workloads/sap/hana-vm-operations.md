---
title: Azure-beli SAP HANA-műveletek |} A Microsoft Docs
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
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2480ad464f2fc716cf68672387a189aeb92f5737
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918832"
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA az Azure üzemeltetési útmutató
Ez a dokumentum útmutatással rendszerűeket SAP HANA Azure-beli natív virtuális gépek (VM) telepített. Ez a dokumentum nem célja, hogy cserélje le a standard szintű SAP dokumentációját, amely magában foglalja az alábbi tartalommal:

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
> Nem éles környezethez, használja a virtuális gépek típusai, amelyek szerepelnek a [SAP-jegyzetnek #1928533](https://launchpad.support.sap.com/#/notes/1928533). Az Azure virtuális gépek használata a termelési forgatókönyvekhez, ellenőrizze az SAP HANA-tanúsítvánnyal rendelkező virtuális gépeket a közzétett SAP [Certified IaaS platformok listájához](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

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

Mindaddig, amíg az SAP HANA-berendezések helyszíni vásárolt, sem volt, mert az adott berendezés gyártójához kell győződjön meg arról, hogy a minimális tárhellyel kapcsolatos követelmények teljesülnek-e az SAP Hana-hoz, az i/o-alrendszerre és annak képességeit törődni. Akkor hozhat létre az Azure-infrastruktúra, mivel is érdemes figyelembe egyes ezek a követelmények is megismeréséhez javasoljuk, hogy az alábbi szakaszokban található konfigurációs követelményekről. Olyan esetekben, ahol konfigurál a virtuális gépek a kívánt vagy SAP HANA futtatása Azure. A előrejelzéséhez, hogy a rendszer kéri eredményez van szükség:

- /Hana/log egy 250 MB/s, 1 MB i/o-mérettel legalább olvasási/írási köteten engedélyezése
- Legalább 400MB/s /hana/data 16 MB és 64 MB-os i/o-méretek esetében a olvasási tevékenység engedélyezése
- A legalább 250MB/s, 16 MB és 64 MB-os i/o-mérettel /hana/data írási tevékenység engedélyezése

A megadott alacsony tárolási késései kritikus következményekkel járnak DBMS-rendszerek, még akkor is, mivel azokat, mint az SAP HANA, megtartása a memóriában. A tranzakciós napló írási a DBMS-rendszerek körül általában a storage-ban a kritikus útvonalat. De is műveletek, például visszaállítási pontok írása vagy a Betöltés a memóriában, kritikus fontosságú lehet olyan összeomlás utáni helyreállítást követően. Ezért azt kötelező kihasználhatja az Azure Premium szintű lemezekkel /hana/data és /hana/log kötetek esetében. A minimális átviteli sebessége/hana/naplóhoz és az/hana vagy SAP által igény szerint növelhető a RAID 0 létrehozásához szükséges MDADM vagy LVM használatával több Azure Premium Storage-lemez keresztül, és használja a RAID-kötetek/hana/data/hana/naplózási köteteket. Ahogy a stripe-méretet nyújt a a RAID 0 a javaslat van használandó:

- 64K vagy a 128K/hana/adatok
- 32 ezer/hana/naplója

> [!NOTE]
> Nem kell bármilyen RAID-kötetek használatával, mivel a prémium és Standard tárterület tartsa egy virtuális merevlemez három rendszerkép tárhelyredundancia-szint konfigurálása. A RAID-kötetek használatának tisztán, hogy elegendő i/o-teljesítményt biztosító kötetek konfigurálni.

Halmozódó Azure virtuális merevlemezek RAID alatt számos, a halmozódnak az IOPS és a storage átviteli oldaláról. Tehát ha RAID 0 x 3 P30 Azure Premium Storage-lemez fölé helyezi, azt kell biztosíthatja háromszor az IOPS és háromszor a tárterületek átviteli sebességének egyetlen Azure Premium Storage P30 lemez.

Az alábbi gyorsítótárazási javaslatokat feltételezzük az i/o-jellemzőkkel az SAP Hana-hoz, hogy a lista:

- Nincs alig ellen a HANA-adatfájlokat olvasható számítási feladatait. Kivétel a nagy méretű i/o a HANA-példányt, vagy az Azure virtuális gép újraindítása az újraindítás után, amikor a HANA betöltött adatok. Egy másik eset, nagyobb olvasási i adatok fájlokra vonatkozóan lehet HANA-adatbázis biztonsági mentése. Ennek eredményeképpen olvasási gyorsítótárazás többnyire nem értelmezhető óta az esetek többségében, minden adatkötetnél fájlt kell teljesen olvasható.
- A HANA-visszaállítási pontok és a HANA összeomlás utáni helyreállítást alapú adatlöketekkel bekövetkezik szemben az adatfájlok írása. Írás visszaállítási pontok aszinkron, és be minden felhasználói tranzakció nem tárolnak. Írása során összeomlás utáni helyreállítást adata teljesítmény kritikus fontosságú a rendszer ismét a gyors válaszadás érdekében. Azonban összeomlás utáni helyreállítást kell lennie, hanem kivételes esetekben
- A HANA ismétlés fájlok alig minden olvasási származnak. Kivétel a nagy i/o végrehajtásakor a tranzakciónapló biztonsági mentéseivel, összeomlás utáni helyreállítást, vagy a HANA-példányok újraindítása fázisban.  
- Az SAP HANA ismétlés naplófájl fő terhelés írások. Számítási feladat jellegétől függ rendelkezhet i 4 KB-os vagy más esetben i/o-méret 1 MB vagy annál kisebb. Az írási késést, szemben a SAP HANA-visszaállítási napló a teljesítmény kritikus fontosságú.
- A rendszer megőrzi a lemezen egy megbízható módon kell az összes írási művelet

Eredményeként ezek a megfigyelt IO-minták az SAP Hana a gyorsítótárazás az Azure Premium Storage tárolást használ a különböző kötetek kell beállítani hasonló:

- / hana/adatok – nincs gyorsítótárazás
- / hana/log - nincs gyorsítótárazás – kivétel az M-sorozat (lásd a dokumentum későbbi)
- /Hana/Shared - olvasási gyorsítótárazás


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


A lemezek esetében a 3 x P20 oversize típusokat a kisebb méretű virtuális gép a kötetek kapcsolatos ajánlások a következők szerint terület ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Azonban a választási lehetőség jelenik meg a tábla történt az SAP Hana elegendő adatátviteli sebességet biztosítanak. Ha a /hana/backup kötetre, amely lett méretezve, hogy a biztonsági mentések kétszer a memória kötet képviselő, módosításokat kell nyugodtan módosíthatja.   
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a számítási feladatok nagyobb kötetek /hana/data és /hana/log igényel, az Azure Premium Storage VHD-k számának növelése szeretné. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül. 

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


A lemezek esetében a 3 x P20 oversize típusokat a kisebb méretű virtuális gép a kötetek kapcsolatos ajánlások a következők szerint terület ajánlott a [SAP TDI tárolási tanulmány](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Azonban a választási lehetőség jelenik meg a tábla történt az SAP Hana elegendő adatátviteli sebességet biztosítanak. Ha a /hana/backup kötetre, amely lett méretezve, hogy a biztonsági mentések kétszer a memória kötet képviselő, módosításokat kell nyugodtan módosíthatja.  
Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a számítási feladatok nagyobb kötetek /hana/data és /hana/log igényel, az Azure Premium Storage VHD-k számának növelése szeretné. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Az Azure M sorozatú virtuális gépek Azure Write Accelerator tárolási megoldás
Az Azure Írásgyorsító egy funkció, amely a kezdeti bevezetési az M-sorozat virtuális gépei kizárólag. Az államok neve, ahogy az a funkciók célja, hogy javítása az Azure Premium Storage írására i/o-késését. Az SAP Hana Write Accelerator használható a /hana/log kötetet csak lehet. Ezért a konfiguráció látható, amennyiben módosítani kell. A fő módosítás össze az /hana/log csak Azure Write Accelerator használatához a darabolása a /hana/data és /hana/log között. 

> [!IMPORTANT]
> SAP HANA-minősítési Azure M sorozatú virtuális gépek kizárólag a /hana/log kötet Azure Write Accelerator van. Ennek eredményeképpen éles forgatókönyvet az SAP HANA üzembe helyezéseket az Azure M sorozatú virtuális gépek várt Azure Write Accelerator /hana/log kötet konfigurálni.  

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

Ellenőrizze, hogy a különböző javasolt kötetek a tárterületek átviteli sebességének felel meg a futtatni kívánt számítási feladatokra. Ha a számítási feladatok nagyobb kötetek /hana/data és /hana/log igényel, az Azure Premium Storage VHD-k számának növelése szeretné. Olyan kötetet, további virtuális merevlemezeket, mint a felsorolt méretezési fog növeléséhez IOPS és az i/o az Azure virtuális gép típusát keretein belül.

Az Azure Írásgyorsító csak együtt működik [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Tehát legalább az Azure Premium Storage-lemez a /hana/log kötetet alkotó kell üzembe helyezni, felügyelt lemezeket.

Az Azure Premium Storage virtuális merevlemezek Azure Write Accelerator támogató virtuális gépenként korlátozva van. A jelenlegi korlátok a következők:

- 16 virtuális merevlemezek egy M128xx VM
- 8 virtuális merevlemezek egy M64xx VM
- 4 virtuális merevlemezek egy M32xx VM

A cikkben található részletes útmutatást nyújt az Azure Írásgyorsító engedélyezése [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Részletek és az Azure Írásgyorsító korlátozásai a azonos dokumentációjában található.


### <a name="set-up-azure-virtual-networks"></a>Az Azure virtuális hálózatok beállítása
Ha a helyek közötti kapcsolat az Azure-ba, VPN vagy ExpressRoute-n keresztül, rendelkeznie kell legalább egy [az Azure virtual network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) VPN vagy ExpressRoute-kapcsolatcsoporthoz virtuális átjárón keresztül csatlakozó. A virtuális átjáró alhálózatán, az Azure virtuális hálózatban találhatók. Az SAP HANA telepítése, hozzon létre két további alhálózatokat a virtuális hálózaton belül. Egy alhálózatot a virtuális gépek futtatásához az SAP HANA-példányok üzemelteti. A többi alhálózathoz futtatja az SAP HANA Studio vagy más felügyeleti szoftverek a Jumpbox vagy a felügyeleti virtuális gépek.

SAP HANA futtatásához a virtuális gépek telepítésekor a virtuális gépek van szükség:

- Telepítve van, a két virtuális hálózati adapter: szeretne csatlakozni a felügyeleti alhálózat egy hálózati adapter és a példányhoz való kapcsolódáshoz a helyszíni hálózat vagy a más hálózatokból, az SAP HANA az Azure-beli virtuális gépen több hálózati Adapterrel.
- Statikus magánhálózati IP-címek mindkét virtuális hálózati adapterek vannak telepítve.

Az IP-címet rendelni különböző módszerek áttekintését lásd: [IP-címtípusokat és foglalási módszereket az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

A virtuális gépeken futó SAP HANA együttműködve kell hozzárendelt statikus IP-címeket. Oka, hogy néhány konfigurációs attribútum Hana hivatkozhatnak az IP-címeket.

[Az Azure hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) forgalmat, annak biztosítására, hogy az SAP HANA-példányt, vagy a Jumpbox irányíthatók. Az NSG-k az SAP HANA-alhálózat és a felügyeleti alhálózaton társítva.

Az alábbi képen egy durva telepítési séma áttekintése látható, az SAP Hana-hoz:

![Nyers telepítési séma az SAP Hana-hoz](media/hana-vm-operations/hana-simple-networking.PNG)


Az Azure-beli SAP HANA üzembe egy helyek közötti kapcsolat nélkül, nyissa meg az SAP HANA-példány, ha egy nyilvános IP-címet. Az IP-címet hozzá kell rendelni az Azure virtuális Gépen, amelyen fut a Jumpbox virtuális Géphez. Ez a alapvető forgatókönyvben az üzembe helyezés Azure beépített DNS-szolgáltatások gazdanév feloldása támaszkodik. Egy összetettebb telepítésben lévő nyilvános IP-címeket használja, az Azure beépített DNS-szolgáltatások különösen fontosak. Azure NSG-k használatával korlátozza a nyitott portok és IP-címtartományokat, amelyek csatlakozhatnak az eszközök, amelyek nyilvános IP-címek Azure alhálózatokra. Az alábbi képen látható, az SAP HANA üzembe helyezése a helyek közötti kapcsolat nélkül hozzávetőleges sémát:
  
![Nyers telepítési séma az SAP Hana-hoz egy helyek közötti kapcsolat nélkül](media/hana-vm-operations/hana-simple-networking2.PNG)
 


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
