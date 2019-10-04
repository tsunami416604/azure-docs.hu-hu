---
title: Azure-beli virtuális gépek tárolási konfigurációinak SAP HANAa | Microsoft Docs
description: Tárolási javaslatok a SAP HANA üzembe helyezett virtuális gépekhez.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 45d8844a34c5b7d9f36099304c1619e09d39305c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099633"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure-beli virtuális gépek tárkonfigurációi

Az Azure különböző típusú tárhelyeket biztosít, amelyek a SAP HANA rendszert futtató Azure-beli virtuális gépekhez használhatók. A SAP HANA központi telepítések listájához hasonló Azure Storage-típusok, például: 

- Standard SSD lemezmeghajtók (SSD)
- Prémium szintű állapotú meghajtók (SSD)
- [Ultravékony lemez](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

Ezekről a lemezekről a lemez [típusának kiválasztása](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) című cikkben olvashat bővebben.

Az Azure két üzembe helyezési módszert kínál a VHD-k számára az Azure standard és a Premium Storage. Ha az általános forgatókönyv lehetővé teszi, használja ki az [Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) üzemelő példányait. 

A IOPS és a tárolási átviteli sebességű tárolási típusok listáját és SLA-kat a [felügyelt lemezek Azure](https://azure.microsoft.com/pricing/details/managed-disks/)-dokumentációjában tekintheti meg.

**Ajánlás Az Azure Premium Storage használata az Azure írásgyorsító és az Azure Managed Disks használata az üzembe helyezéshez**

A helyszíni világban ritkán kell foglalkoznia az I/O-alrendszerekkel és képességeivel. Ennek az az oka, hogy a készülék gyártójának meg kell győződnie arról, hogy a minimális tárolási követelmények teljesülnek SAP HANA esetén. Ha saját maga hozza létre az Azure-infrastruktúrát, vegye figyelembe a követelmények némelyikét. A minimális átviteli sebességre vonatkozó követelmények közül néhányat a következőknek kell megadnia:

- Írási/olvasási műveletek engedélyezése 250 MB/s **/Hana/log** 1 MB I/O-mérettel
- Legalább 400 MB/s olvasási tevékenység engedélyezése a **/Hana/Data** 16 MB és 64 MB I/O méret esetén
- Az írási tevékenység engedélyezése legalább 250 MB/s **/Hana/Data** 16 mb és 64 MB I/O méret esetén

Mivel az adatbázis-kezelő rendszerekben az alacsony tárolási késés kritikus fontosságú, még az adatbázis-kezelői rendszerek, például a SAP HANA esetében is, az adatmemóriában marad. A tárolóban a kritikus elérési út általában az adatbázis-kezelő rendszerek tranzakciónapló-írásai körébe esik. Az összeomlás utáni helyreállítást követően azonban az olyan műveletek, mint például a visszaállítási pontok írása vagy a memóriában tárolt adatok betöltése is kritikus lehet. Ezért a **/Hana/Data** -és **/Hana/log** -kötetek esetében **kötelező** az Azure Premium-lemezek kihasználása. Az SAP által igénybe venni kívánt **/Hana/log** és **/Hana/Data** minimális átviteli sebességének elérése érdekében a MDADM vagy az LVM használatával több Azure Premium Storage-lemezre kell létrehoznia a RAID 0-ot. A RAID-kötetek pedig **/Hana/Data** -és **/Hana/log** -kötetekként használhatók. 

**Ajánlás A RAID 0 számára a következőt kell használni:**

- 64 KB vagy 128 KB a **/Hana/Data**
- 32 KB a **/Hana/log**

> [!NOTE]
> Az Azure Premium és a standard Storage esetében nem kell a redundancia-szintet a RAID-kötetek használatával konfigurálnia, mivel a virtuális merevlemezek három rendszerképet tárolnak. A RAID-kötetek használata kizárólag olyan kötetek konfigurálására szolgál, amelyek elegendő I/O-teljesítményt biztosítanak.

A RAID alatt álló számos Azure virtuális merevlemez felhalmozódása a IOPS és a tárterület átviteli sebességével. Ha tehát a RAID 0 több mint 3 x P30 Azure Premium Storage lemezt helyez el, akkor a IOPS háromszor kell megadnia az Azure Premium Storage P30 lemez tárolási sebességét.

Az alábbi gyorsítótárazási javaslatok feltételezik, hogy a lista SAP HANA I/O-jellemzői:

- A HANA-adatfájlok esetében alig van bármilyen olvasási munkaterhelés. A kivételek nagy méretű I/o-k a HANA-példány újraindítása után, vagy amikor az adatfeltöltés a HANA-ba történik. Az adatfájlok elleni nagyobb olvasási I/o-t egy másik esetben a HANA-adatbázis biztonsági mentései is lehetnek. Ennek eredményeképpen az olvasási gyorsítótárazás többnyire nem logikus, mert a legtöbb esetben az összes adatfájl kötetét teljes egészében el kell olvasni.
- Az adatfájlok írása a HANA visszaállítási pontok és a HANA összeomlás-helyreállítási szolgáltatáson alapul. A visszaállítási pontok írása aszinkron módon történik, és nem tart fenn felhasználói tranzakciókat. Az összeomlás utáni helyreállítás során az adat megírása kritikus fontosságú ahhoz, hogy a rendszer gyorsan reagáljon. Az összeomlások helyreállítása azonban kivételes helyzetekben nem lehetséges
- A HANA által visszaadott fájlokból alig történik olvasási művelet. A kivételek nagyméretű I/o-műveletek a tranzakciós naplók biztonsági másolatainak, összeomlás-helyreállításának vagy a HANA-példány újraindítási fázisának végrehajtásakor.  
- A rendszer a SAP HANA visszaállítási naplófájljának fő terhelését írja le. A számítási feladatok jellegétől függően az I/O-t 4 KB-os vagy más esetekben 1 MB vagy annál nagyobb méretű i/O-méretekkel lehet elkészíteni. A SAP HANA az Ismétlési naplóba való írási késleltetés kritikus fontosságú.
- Az összes írást megbízható módon kell megőrizni a lemezen

**Ajánlás A megfigyelt I/O-mintázatok SAP HANA alapján történő elvégzése következtében a különböző kötetek gyorsítótárazását az Azure Premium Storage használatával kell beállítani, például:**

- **/Hana/Data** – nincs gyorsítótárazás
- **/Hana/log** – nincs gyorsítótárazás – kivétel az M sorozathoz (lásd a dokumentum későbbi részében)
- **/Hana/Shared** – olvasási gyorsítótárazás


A virtuális gép méretezése vagy meghatározása során vegye figyelembe a teljes VM I/O-sebességet is. A virtuálisgép-tárolók teljes átviteli sebességét a cikk a [memória-optimalizált virtuális gépek méreteit](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)ismertető cikkben dokumentálja.

## <a name="linux-io-scheduler-mode"></a>Linux I/O-ütemező mód
A Linux számos különböző I/O-ütemezési módot tartalmaz. A Linux-szállítók és az SAP gyakori javaslata, hogy újrakonfigurálja az I/O-ütemező módot a lemezes kötetek számára a **cfq** módból a **NOOP** módba. A részletekre az [SAP-megjegyzés #1984798](https://launchpad.support.sap.com/#/notes/1984787)hivatkozik. 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Üzemi tárolási megoldás Azure írásgyorsító Azure M sorozatú virtuális gépekhez
Az Azure írásgyorsító egy olyan funkció, amely kizárólag az Azure M sorozatú virtuális gépekhez készül. Ahogy a neve is jelzi, a funkció célja az, hogy javítsa az írások I/O-késését az Azure Premium Storage. SAP HANA esetén a rendszer csak a **/Hana/log** köteten használja írásgyorsító. Ezért a **/Hana/Data** és a **/Hana/log** különálló kötetek, amelyek az Azure-írásgyorsító támogatják a **/Hana/log** -kötetet. 

> [!IMPORTANT]
> Az Azure M sorozatú virtuális gépek SAP HANA minősítése kizárólag az Azure írásgyorsító a **/Hana/log** -kötethez. Ennek eredményeképpen az Azure M sorozatú virtuális gépeken SAP HANA üzemelő példányok üzembe helyezését az Azure írásgyorsító kell konfigurálni a **/Hana/log** -kötethez.  

> [!NOTE]
> Éles forgatókönyvek esetén győződjön meg arról, hogy a [IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)SAP-dokumentációja támogatja-e egy adott virtuálisgép-típus támogatását az sap által SAP HANA.

**Ajánlás Az éles környezetekben javasolt konfigurációk a következőképpen néznek ki:**

| VM SKU | RAM | Legfeljebb VM I/O<br /> Teljesítmény | /hana/data | /hana/log | /hana/shared | /root-kötet | /usr/sap | Hana/Backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

A Microsoft a M416xx_v2 virtuális gépek típusát még nem tette elérhetővé a nyilvánosság számára. Győződjön meg arról, hogy a különböző javasolt kötetek tárolási átviteli sebessége megfelel-e a futtatni kívánt munkaterhelésnek. Ha a munkaterhelés nagyobb köteteket igényel a **/Hana/Data** és a **/Hana/log**számára, növelje az Azure Premium Storage VHD-k számát. Az Azure-beli virtuálisgép-típus korlátain belül a kötetek méretezése a IOPS és az I/O-átviteli sebesség növelésével növekszik.

Az Azure írásgyorsító csak az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)szolgáltatással együtt működik. Így legalább az **/Hana/log** -kötetet alkotó Azure Premium Storage lemezeket felügyelt lemezként kell telepíteni.

Az Azure-írásgyorsító által támogatott Azure Premium Storage virtuális merevlemezek száma korlátozott. A jelenlegi korlátok a következők:

- 16 virtuális merevlemez M128xx és M416xx virtuális géphez
- 8 virtuális merevlemez M64xx és M208xx virtuális géphez
- 4 virtuális merevlemez egy M32xx virtuális géphez

Az Azure írásgyorsító engedélyezésével kapcsolatos részletesebb útmutatást a [írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)cikkben találhat.

Az Azure írásgyorsító részletei és korlátozásai ugyanabban a dokumentációban találhatók.

**Ajánlás A/Hana/log-kötetet alkotó lemezekhez írásgyorsító kell használnia**


## <a name="cost-conscious-azure-storage-configuration"></a>Költségtakarékos Azure Storage-konfiguráció
Az alábbi táblázat azon virtuálisgép-típusok konfigurációját mutatja be, amelyeket az ügyfelek a SAP HANA Azure-beli virtuális gépeken való üzemeltetésére is használnak. Előfordulhat, hogy egyes virtuálisgép-típusok nem teljesítik a SAP HANA minimális tárolási feltételeit, vagy az SAP nem támogatja hivatalosan a SAP HANA. De eddig ezek a virtuális gépek nem éles környezetekben is megfelelőnek látszanak. A **/Hana/Data** és a **/Hana/log** egyetlen kötethez vannak egyesítve. Ennek eredményeképpen az Azure írásgyorsító használata a IOPS feltételeinek korlátozására is válhat.

> [!NOTE]
> Az SAP által támogatott forgatókönyvek esetén győződjön meg arról, hogy az SAP által támogatott bizonyos virtuálisgép-típusok a [IAAS SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)-dokumentációjában SAP HANA támogatják-e.

> [!NOTE]
> A költségtakarékos megoldás korábbi javaslatainak változása az [azure standard HDD lemezekről](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) való áttérés az Azure-beli [standard SSD-lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) jobb teljesítményére és megbízhatóbbá tételére


| VM SKU | RAM | Legfeljebb VM I/O<br /> Teljesítmény | /Hana/Data és/Hana/log<br /> az LVM vagy a MDADM szalagos | /hana/shared | /root-kötet | /usr/sap | Hana/Backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


A Microsoft a M416xx_v2 virtuális gépek típusát még nem tette elérhetővé a nyilvánosság számára. A kisebb, 3 x P20 rendelkező virtuálisgép-típusokhoz ajánlott lemezek túlméretezettek az [SAP TDI tárolási](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)tanulmánya alapján a tárhelyre vonatkozó ajánlásokat érintő köteteken. Azonban a táblázatban megjelenő választási lehetőség a SAP HANA megfelelő lemez-átviteli sebességének biztosítására szolgál. Ha módosítania kell a **/Hana/Backup** kötetét, amely a memória mennyiségét kétszer ábrázoló biztonsági másolatok tárolásához szükséges, nyugodtan módosítsa.   
Győződjön meg arról, hogy a különböző javasolt kötetek tárolási átviteli sebessége megfelel-e a futtatni kívánt munkaterhelésnek. Ha a munkaterhelés nagyobb köteteket igényel a **/Hana/Data** és a **/Hana/log**számára, növelje az Azure Premium Storage VHD-k számát. Az Azure-beli virtuálisgép-típus korlátain belül a kötetek méretezése a IOPS és az I/O-átviteli sebesség növelésével növekszik. 

> [!NOTE]
> A fenti konfigurációk nem élvezhetik az [Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) -beli virtuális gépekre vonatkozó SLA-t, mivel az Azure Premium Storage és az Azure standard Storage keverékét használja. A kiválasztás azonban a költségek optimalizálása érdekében lett kiválasztva. Az Azure standard Storage-ban (Sxx) felsorolt összes lemeznél Premium Storage kell választania, hogy a virtuális gép konfigurációja megfeleljen az Azure Single VM SLA-nak.


> [!NOTE]
> A lemez-konfigurációra vonatkozó javaslatok azt célozzák meg, hogy az SAP kifejezi az infrastruktúra-szolgáltatók felé irányuló minimális követelményeket. A valós ügyfelek központi telepítései és munkaterhelési forgatókönyvekben olyan helyzetek fordultak elő, amikor ezek a javaslatok még nem biztosítanak elégséges képességeket. Ezek lehetnek olyan helyzetek, amikor az ügyfél egy HANA-újraindítás után gyorsabb újratöltést igényelt, vagy ha a biztonsági mentési konfigurációk nagyobb sávszélességet igényelnek a tárolóhoz. Egyéb esetekben a **/Hana/log** , ahol az 5000 IOPS nem volt elegendő az adott számítási feladathoz. Ezért ezeket az ajánlásokat kiindulási pontként kell elvégeznie, és a számítási feladatok követelményei alapján kell alkalmazkodnia.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Az Azure ultrakönnyű lemezes tárolásának konfigurációja SAP HANA
A Microsoft egy [Azure Ultra Disk](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk)nevű új Azure Storage-típust mutat be. Az Azure Storage eddigi nagy különbsége, hogy a lemez képességei már nem kötődnek a lemez méretétől. Ügyfélként meghatározhatja ezeket a képességeket az ultra Disk számára:

- Lemez mérete 4 GiB-ról 65 536 GiB-ig
- A IOPS tartománya 100 IOPS – 160K IOPS (a maximum a virtuálisgép-típusoktól függ)
- Tárolási sebesség 300 MB/s-tól 2 000 MB/s-ig

Az ultravékony lemez lehetővé teszi, hogy egyetlen lemezt adjon meg, amely megfelel a méretének, a IOPS és a lemez átviteli sebességi tartományának. Ahelyett, hogy olyan logikai köteteket használjon, mint az LVM vagy a MDADM az Azure Premium Storageon, hogy olyan köteteket szerkesszen, amelyek megfelelnek a IOPS és a tárterület átviteli sebességének. Az ultra Disk és a Premium Storage között konfigurációs kombinációt is futtathat. Ennek eredményeképpen korlátozhatja az ultra Disk használatát a teljesítmény szempontjából kritikus fontosságú/Hana/Data és/Hana/log, valamint a többi kötetet az Azure-Premium Storage

> [!IMPORTANT]
> Az ultra Disk még nem szerepel az összes Azure-régióban, és még nem támogatja az összes virtuálisgép-típust. Részletes információkat talál arról, hogy mely virtuális gépek támogatottak, és milyen virtuálisgép-családokat támogatnak? tekintse meg az [Azure-ban elérhető lemez-típusok](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk)című cikket.

| VM SKU | RAM | Legfeljebb VM I/O<br /> Teljesítmény | /Hana/Data-kötet | /Hana/Data I/O-átviteli sebesség | /Hana/Data IOPS | /Hana/log-kötet | /Hana/log I/O-átviteli sebesség | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1 000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1 000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2 000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2 000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1 000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1 000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2 000 MB/s | 7200 GB | 1500M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

A Microsoft a M416xx_v2 virtuális gépek típusát még nem tette elérhetővé a nyilvánosság számára. A felsorolt értékek kiindulási pontként szolgálnak, és a valós igények alapján kell kiértékelni őket. Az Azure Ultra Disk előnye, hogy a IOPS és az átviteli sebesség értékei a virtuális gép leállításának vagy a rendszeren alkalmazott számítási feladatok leállításának szükségessége nélkül módosíthatók.   

> [!NOTE]
> Eddig a tárolási Pillanatképek nem érhetők el az ultra Disk Storage szolgáltatásban. Ez blokkolja a virtuálisgép-Pillanatképek használatát Azure Backup szolgáltatásokkal

## <a name="next-steps"></a>További lépések
További információkért lásd:

- [SAP HANA magas rendelkezésre állású útmutató Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)-beli virtuális gépekhez.