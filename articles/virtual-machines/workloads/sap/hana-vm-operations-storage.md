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
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4cc4db9ffcb700d4b65a7f5c21d258e9af52d164
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598527"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure-beli virtuális gépek tárkonfigurációi

Az Azure különböző típusú tárhelyeket biztosít, amelyek a SAP HANA rendszert futtató Azure-beli virtuális gépekhez használhatók. A **SAP HANA minősített Azure Storage-típusok** , amelyek a SAP HANA központi telepítések listájához vehetők figyelembe, például: 

- Azure-prémium SSD  
- [Ultravékony lemez](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Ezekről a lemezekről a lemez [típusának kiválasztása](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) című cikkben olvashat bővebben.

Az Azure két üzembe helyezési módszert kínál a VHD-k számára az Azure standard és a Premium Storage. Ha az általános forgatókönyv lehetővé teszi, használja ki az [Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) üzemelő példányait. 

A IOPS és a tárolási átviteli sebességű tárolási típusok listáját és SLA-kat a [felügyelt lemezek Azure-dokumentációjában](https://azure.microsoft.com/pricing/details/managed-disks/)tekintheti meg.

A különböző tárolási típusok minimális SAP HANA tanúsított feltételei a következők: 

- Azure prémium SSD – a/Hana/log-t az Azure [írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)-mel kell gyorsítótárazni. A/Hana/Data-kötetet prémium SSD Azure írásgyorsító vagy Ultra Disk nélkül helyezhető el
- Legalább a/Hana/log-kötethez tartozó Azure Ultra Disk. A/Hana/Data-kötet az Azure írásgyorsító nélküli prémium SSD helyezhető el, vagy a gyorsabb újraindítási idő (Ultra Disk) használatával
- A/Hana/log és a/Hana/Data. Azure NetApp Files-re épülő **NFS v 4.1** **-** kötetek A/Hana/Shared mennyisége NFS v3 vagy NFS v 4.1 protokollt használhat. Az NFS v 4.1 protokoll a/Hana/Data és/Hana/log kötetek esetében kötelező.

Egyes tárolási típusok kombinálhatók. Előfordulhat például, hogy a/Hana/Data-et Premium Storagera helyezi, és a/Hana/log a szükséges kis késleltetés érdekében a lemezes tárolásra is helyezhető. Ha azonban a/Hana/Data ANF alapuló NFS v 4.1-es kötetet használ, akkor egy másik NFS v 4.1-es kötetet kell használnia a/Hana/log.-ANF alapján. A ANF-en keresztüli NFS használata az egyik kötetre (például a/Hana/Data) és az Azure Premium Storage vagy a másik kötethez (például a/Hana/log) **nem támogatott**.

A helyszíni világban ritkán kell foglalkoznia az I/O-alrendszerekkel és képességeivel. Ennek az az oka, hogy a készülék gyártójának meg kell győződnie arról, hogy a minimális tárolási követelmények teljesülnek SAP HANA esetén. Ha saját maga hozza létre az Azure-infrastruktúrát, tisztában kell lennie az egyes SAP-kiállított követelményekkel. Az SAP által igényelt minimális átviteli sebességek némelyike az alábbiakat eredményezi:

- Írási/olvasási műveletek engedélyezése 250 MB/s **/Hana/log** 1 MB I/O-mérettel
- Legalább 400 MB/s olvasási tevékenység engedélyezése a **/Hana/Data** 16 MB és 64 MB I/O méret esetén
- Az írási tevékenység engedélyezése legalább 250 MB/s **/Hana/Data** 16 mb és 64 MB I/O méret esetén

Mivel az adatbázis-kezelő rendszerekben az alacsony tárolási késés kritikus fontosságú, még az adatbázis-kezelői rendszerek, például a SAP HANA esetében is, az adatmemóriában marad. A tárolóban a kritikus elérési út általában az adatbázis-kezelő rendszerek tranzakciónapló-írásai körébe esik. Az összeomlás utáni helyreállítást követően azonban az olyan műveletek, mint például a visszaállítási pontok írása vagy a memóriában tárolt adatok betöltése is kritikus lehet. Ezért a **/Hana/Data** -és **/Hana/log** -kötetek esetében **kötelező** az Azure Premium-lemezek kihasználása. Az SAP által igénybe venni kívánt **/Hana/log** és **/Hana/Data** minimális átviteli sebességének elérése érdekében a MDADM vagy az LVM használatával több Azure Premium Storage-lemezre kell létrehoznia a RAID 0-ot. A RAID-kötetek pedig **/Hana/Data** -és **/Hana/log** -kötetekként használhatók. 

**Javaslat: a RAID 0 csíkozási méretei a következők:**

- 256 KB a **/Hana/Data**
- 32 KB a **/Hana/log**

> [!IMPORTANT]
> A/Hana/Data szalagos mérete a korábbi javaslatoktól a 64 KB-ra vagy 128 KB-ra irányuló, a legújabb Linux 256-verziókon alapuló ügyfelek tapasztalatai alapján változott. Az 256 KB mérete valamivel jobb teljesítményt biztosít

> [!NOTE]
> Az Azure Premium és a standard Storage esetében nem kell a redundancia-szintet a RAID-kötetek használatával konfigurálnia, mivel a virtuális merevlemezek három rendszerképet tárolnak. A RAID-kötetek használata kizárólag olyan kötetek konfigurálására szolgál, amelyek elegendő I/O-teljesítményt biztosítanak.

A RAID alatt álló számos Azure virtuális merevlemez felhalmozódása a IOPS és a tárterület átviteli sebességével. Ha tehát a RAID 0 több mint 3 x P30 Azure Premium Storage lemezt helyez el, akkor a IOPS háromszor kell megadnia az Azure Premium Storage P30 lemez tárolási sebességét.

A virtuális gép méretezése vagy meghatározása során vegye figyelembe a teljes VM I/O-sebességet is. A virtuálisgép-tárolók teljes átviteli sebességét a cikk a [memória-optimalizált virtuális gépek méreteit](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)ismertető cikkben dokumentálja.

## <a name="linux-io-scheduler-mode"></a>Linux I/O-ütemező mód
A Linux számos különböző I/O-ütemezési módot tartalmaz. A Linux-szállítók és az SAP gyakori javaslata, hogy újrakonfigurálja az I/O-ütemező módot a **cfq** módból a **NOOP** (nem többhelyes) vagy a **none** (többvárólistaos) üzemmódra. A részletekre az [SAP-megjegyzés #1984787](https://launchpad.support.sap.com/#/notes/1984787)hivatkozik. 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Megoldások Premium Storage-és Azure-írásgyorsító Azure M sorozatú virtuális gépekhez
Az Azure írásgyorsító egy olyan funkció, amely kizárólag az Azure M sorozatú virtuális gépekhez érhető el. Ahogy a neve is jelzi, a funkció célja az, hogy javítsa az írások I/O-késését az Azure Premium Storage. SAP HANA esetén a rendszer csak a **/Hana/log** köteten használja írásgyorsító. Ezért a **/Hana/Data** és a **/Hana/log** különálló kötetek, amelyek az Azure-írásgyorsító támogatják a **/Hana/log** -kötetet. 

> [!IMPORTANT]
> Az Azure Premium Storage használatakor az Azure [írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) vagy a/Hana/log-kötet használata kötelező. A írásgyorsító a Premium Storage és az M-Series és a Mv2 sorozatú virtuális gépek esetében is elérhető.

Az alábbi gyorsítótárazási javaslatok feltételezik, hogy a lista SAP HANA I/O-jellemzői:

- A HANA-adatfájlok esetében alig van bármilyen olvasási munkaterhelés. A kivételek nagy méretű I/o-k a HANA-példány újraindítása után, vagy amikor az adatfeltöltés a HANA-ba történik. Az adatfájlok elleni nagyobb olvasási I/o-t egy másik esetben a HANA-adatbázis biztonsági mentései is lehetnek. Ennek eredményeképpen az olvasási gyorsítótárazás többnyire nem logikus, mert a legtöbb esetben az összes adatfájl kötetét teljes egészében el kell olvasni.
- Az adatfájlok írása a HANA visszaállítási pontok és a HANA összeomlás-helyreállítási szolgáltatáson alapul. A visszaállítási pontok írása aszinkron módon történik, és nem tart fenn felhasználói tranzakciókat. Az összeomlás utáni helyreállítás során az adat megírása kritikus fontosságú ahhoz, hogy a rendszer gyorsan reagáljon. Az összeomlások helyreállítása azonban kivételes helyzetekben nem lehetséges
- A HANA által visszaadott fájlokból alig történik olvasási művelet. A kivételek nagyméretű I/o-műveletek a tranzakciós naplók biztonsági másolatainak, összeomlás-helyreállításának vagy a HANA-példány újraindítási fázisának végrehajtásakor.  
- A rendszer a SAP HANA visszaállítási naplófájljának fő terhelését írja le. A számítási feladatok jellegétől függően az I/O-t 4 KB-os vagy más esetekben 1 MB vagy annál nagyobb méretű i/O-méretekkel lehet elkészíteni. A SAP HANA az Ismétlési naplóba való írási késleltetés kritikus fontosságú.
- Az összes írást megbízható módon kell megőrizni a lemezen

**Javaslat: a megfigyelt I/O-mintázatok miatt SAP HANA szerint a különböző kötetek gyorsítótárazását az Azure Premium Storage használatával kell beállítani, például:**

- **/Hana/Data** – nincs gyorsítótárazás
- **/Hana/log** – nincs gyorsítótárazás – kivétel az M-és Mv2 sorozat esetében, ahol a írásgyorsító az olvasási gyorsítótárazás nélkül kell engedélyezni. 
- **/Hana/Shared** – olvasási gyorsítótárazás

### <a name="production-recommended-storage-solution"></a>Éles környezetben ajánlott tárolási megoldás

> [!IMPORTANT]
> Az Azure M sorozatú virtuális gépek SAP HANA minősítése kizárólag az Azure írásgyorsító a **/Hana/log** -kötethez. Ennek eredményeképpen az Azure M sorozatú virtuális gépeken SAP HANA üzemelő példányok üzembe helyezését az Azure írásgyorsító kell konfigurálni a **/Hana/log** -kötethez.  

> [!NOTE]
> Éles forgatókönyvek esetén győződjön meg arról, hogy a IAAS SAP- [dokumentációja](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)támogatja-e egy adott virtuálisgép-típus támogatását az sap által SAP HANA.

A javaslatok gyakran meghaladják az SAP minimális követelményeit a cikkben korábban leírtak szerint. A felsorolt javaslatok az SAP méretével kapcsolatos javaslatok és a különböző virtuálisgép-típusok által biztosított maximális tárolási teljesítmény közötti kompromisszumok.

**Javaslat: az éles környezetekben javasolt konfigurációk a következőképpen néznek ki:**

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /hana/data | /hana/log | /hana/shared | /root-kötet | /usr/sap | Hana/Backup |
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

Győződjön meg arról, hogy a különböző javasolt kötetek tárolási átviteli sebessége megfelel-e a futtatni kívánt munkaterhelésnek. Ha a munkaterhelés nagyobb köteteket igényel a **/Hana/Data** és a **/Hana/log**számára, növelje az Azure Premium Storage VHD-k számát. Az Azure-beli virtuálisgép-típus korlátain belül a kötetek méretezése a IOPS és az I/O-átviteli sebesség növelésével növekszik.

Az Azure írásgyorsító csak az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)szolgáltatással együtt működik. Így legalább az **/Hana/log** -kötetet alkotó Azure Premium Storage lemezeket felügyelt lemezként kell telepíteni.

Az Azure-írásgyorsító által támogatott Azure Premium Storage virtuális merevlemezek száma korlátozott. A jelenlegi korlátok a következők:

- 16 virtuális merevlemez M128xx és M416xx virtuális géphez
- 8 virtuális merevlemez M64xx és M208xx virtuális géphez
- 4 virtuális merevlemez egy M32xx virtuális géphez

Az Azure írásgyorsító engedélyezésével kapcsolatos részletesebb útmutatást a [írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)cikkben találhat.

Az Azure írásgyorsító részletei és korlátozásai ugyanabban a dokumentációban találhatók.

**Javaslat: a/Hana/log-kötetet alkotó lemezekhez írásgyorsító kell használnia**


### <a name="cost-conscious-azure-storage-configuration"></a>Költségtakarékos Azure Storage-konfiguráció
Az alábbi táblázat azon virtuálisgép-típusok konfigurációját mutatja be, amelyeket az ügyfelek a SAP HANA Azure-beli virtuális gépeken való üzemeltetésére is használnak. Előfordulhat, hogy egyes virtuálisgép-típusok nem teljesítik a SAP HANA minimális tárolási feltételeit, vagy az SAP nem támogatja hivatalosan a SAP HANA. De eddig ezek a virtuális gépek nem éles környezetekben is megfelelőnek látszanak. A **/Hana/Data** és a **/Hana/log** egyetlen kötethez vannak egyesítve. Ennek eredményeképpen az Azure írásgyorsító használata a IOPS feltételeinek korlátozására is válhat.

> [!NOTE]
> Az SAP által támogatott forgatókönyvek esetén győződjön meg arról, hogy az SAP által támogatott bizonyos virtuálisgép-típusok a [IAAS SAP-dokumentációjában](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)SAP HANA támogatják-e.

> [!NOTE]
> A költségtakarékos megoldás korábbi javaslatainak változása az [azure standard HDD lemezekről](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) való áttérés az Azure-beli [standard SSD-lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) jobb teljesítményére és megbízhatóbbá tételére

A javaslatok gyakran meghaladják az SAP minimális követelményeit a cikkben korábban leírtak szerint. A felsorolt javaslatok az SAP méretével kapcsolatos javaslatok és a különböző virtuálisgép-típusok által biztosított maximális tárolási teljesítmény közötti kompromisszumok.

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /Hana/Data és/Hana/log<br /> az LVM vagy a MDADM szalagos | /hana/shared | /root-kötet | /usr/sap | Hana/Backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1 200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 GiB | 1 000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 GiB | 2 000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


A kisebb, 3 x P20 rendelkező virtuálisgép-típusokhoz ajánlott lemezek túlméretezettek az [SAP TDI tárolási](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)tanulmánya alapján a tárhelyre vonatkozó ajánlásokat érintő köteteken. Azonban a táblázatban megjelenő választási lehetőség a SAP HANA megfelelő lemez-átviteli sebességének biztosítására szolgál. Ha módosítania kell a **/Hana/Backup** kötetét, amely a memória mennyiségét kétszer ábrázoló biztonsági másolatok tárolásához szükséges, nyugodtan módosítsa.   
Győződjön meg arról, hogy a különböző javasolt kötetek tárolási átviteli sebessége megfelel-e a futtatni kívánt munkaterhelésnek. Ha a munkaterhelés nagyobb köteteket igényel a **/Hana/Data** és a **/Hana/log**számára, növelje az Azure Premium Storage VHD-k számát. Az Azure-beli virtuálisgép-típus korlátain belül a kötetek méretezése a IOPS és az I/O-átviteli sebesség növelésével növekszik. 

> [!NOTE]
> A fenti konfigurációk nem élvezhetik az [Azure-beli virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) -t, mivel az Azure Premium Storage és az Azure standard Storage keverékét használja. A kiválasztás azonban a költségek optimalizálása érdekében lett kiválasztva. Az Azure standard Storage-ban (Sxx) felsorolt összes lemeznél Premium Storage kell választania, hogy a virtuális gép konfigurációja megfeleljen az Azure Single VM SLA-nak.


> [!NOTE]
> A lemez-konfigurációra vonatkozó javaslatok azt célozzák meg, hogy az SAP kifejezi az infrastruktúra-szolgáltatók felé irányuló minimális követelményeket. A valós ügyfelek központi telepítései és munkaterhelési forgatókönyvekben olyan helyzetek fordultak elő, amikor ezek a javaslatok még nem biztosítanak elégséges képességeket. Ezek lehetnek olyan helyzetek, amikor az ügyfél egy HANA-újraindítás után gyorsabb újratöltést igényelt, vagy ha a biztonsági mentési konfigurációk nagyobb sávszélességet igényelnek a tárolóhoz. Egyéb esetekben a **/Hana/log** , ahol az 5000 IOPS nem volt elegendő az adott számítási feladathoz. Ezért ezeket az ajánlásokat kiindulási pontként kell elvégeznie, és a számítási feladatok követelményei alapján kell alkalmazkodnia.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Az Azure ultrakönnyű lemezes tárolásának konfigurációja SAP HANA
A Microsoft egy [Azure Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)nevű új Azure Storage-típust mutat be. Az Azure Storage által kínált eddigi jelentős különbség az, hogy a lemez képességei többé nincsenek a lemez méretéhez kötve. Ügyfélként meghatározhatja ezeket a képességeket az ultra Disk számára:

- Lemez mérete 4 GiB-ról 65 536 GiB-ig
- A IOPS tartománya 100 IOPS – 160K IOPS (a maximum a virtuálisgép-típusoktól függ)
- Tárolási sebesség 300 MB/s-tól 2 000 MB/s-ig

Az ultravékony lemez lehetővé teszi, hogy egyetlen lemezt adjon meg, amely megfelel a méretének, a IOPS és a lemez átviteli sebességi tartományának. Ahelyett, hogy olyan logikai köteteket használjon, mint az LVM vagy a MDADM az Azure Premium Storageon, hogy olyan köteteket szerkesszen, amelyek megfelelnek a IOPS és a tárterület átviteli sebességének. Az ultra Disk és a Premium Storage között konfigurációs kombinációt is futtathat. Ennek eredményeképpen korlátozhatja az ultra Disk használatát a teljesítmény szempontjából kritikus fontosságú/Hana/Data és/Hana/log, valamint a többi kötetet az Azure-Premium Storage

Az ultra Disk egyéb előnyei a Premium Storagehoz képest a jobb olvasási késések lehetnek. A gyorsabb olvasási késés előnyeit kihasználhatja a HANA indítási idejének és az adatmennyiségnek a memóriába való későbbi betöltésének csökkentése érdekében. Az ultrakönnyű lemezes tárolás előnyei is érezhetőek lehetnek, ha HANA visszaállítási pontok ír. Mivel a/Hana/Data Premium Storage lemezei általában nem írásgyorsító gyorsítótárazva, az ultra-lemezhez képest az Premium Storage/Hana/Data írási késése magasabb. Az is előfordulhat, hogy az ultra Disk használatával végzett mentésipont-írás jobban teljesít az ultra diskon.

> [!IMPORTANT]
> Az ultra Disk még nem szerepel az összes Azure-régióban, és még nem támogatja az alább felsorolt virtuálisgép-típusokat. Részletes információkat talál arról, hogy mely virtuális gépek támogatottak, és milyen virtuálisgép-családokat támogatnak? tekintse meg az [Azure-ban elérhető lemez-típusok](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)című cikket.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Éles környezetben ajánlott tárolási megoldás tiszta, ultra Disk konfigurációval
Ebben a konfigurációban a/Hana/Data és a/Hana/log kötetek külön maradnak. A javasolt értékek abból a KPI-ből származnak, amelyet az SAP-nak hitelesítenie kell a SAP HANA és a tárolási konfigurációk virtuálisgép-típusainak az [SAP TDI tárolási](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)tanulmányában javasolt módon.

A javaslatok gyakran meghaladják az SAP minimális követelményeit a cikkben korábban leírtak szerint. A felsorolt javaslatok az SAP méretével kapcsolatos javaslatok és a különböző virtuálisgép-típusok által biztosított maximális tárolási teljesítmény közötti kompromisszumok.

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /Hana/Data-kötet | /Hana/Data I/O-átviteli sebesség | /Hana/Data IOPS | /Hana/log-kötet | /Hana/log I/O-átviteli sebesség | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 MBps | 7500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GiB | 1 000 MB/s | 600 GB | 600 MBps | 7500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MBps | 7500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MBps | 7500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 1 200 MBps |9 000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 1200 MBps |9 000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 1 000 MBps | 9 000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 1 000 MBps | 9 000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 500 MBps | 9 000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MBps | 9 000 | 512 GB | 800 MBps  | 3,000 |   

A felsorolt értékek kiindulási pontként szolgálnak, és a valós igények alapján kell kiértékelni őket. Az Azure Ultra Disk előnye, hogy a IOPS és az átviteli sebesség értékei a virtuális gép leállításának vagy a rendszeren alkalmazott számítási feladatok leállításának szükségessége nélkül módosíthatók.   

> [!NOTE]
> Eddig a tárolási Pillanatképek nem érhetők el az ultra Disk Storage szolgáltatásban. Ez blokkolja a virtuálisgép-Pillanatképek használatát Azure Backup szolgáltatásokkal

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Költségtakarékos tárolási megoldás tiszta, ultra lemezes konfigurációval
Ebben a konfigurációban a/Hana/Data és a/Hana/log kötetek ugyanazon a lemezen vannak. A javasolt értékek abból a KPI-ből származnak, amelyet az SAP-nak hitelesítenie kell a SAP HANA és a tárolási konfigurációk virtuálisgép-típusainak az [SAP TDI tárolási](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)tanulmányában javasolt módon. 

A javaslatok gyakran meghaladják az SAP minimális követelményeit a cikkben korábban leírtak szerint. A felsorolt javaslatok az SAP méretével kapcsolatos javaslatok és a különböző virtuálisgép-típusok által biztosított maximális tárolási teljesítmény közötti kompromisszumok.

| Virtuális gép termékváltozata | RAM | Legfeljebb VM I/O<br /> Átviteli sebesség | /Hana/Data és/log kötete | /Hana/Data és a napló I/O-átviteli sebessége | /Hana/Data és log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 1 200 GB | 1 200 MBps | 9 500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9 500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 1 100 GB | 900 MBps | 10,000 | 
| M64s | 1 000 GiB | 1 000 MB/s |  1 700 GB | 900 MBps | 10,000 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 2 600 GB | 900 MBps | 10,000 | 
| M128s | 2 000 GiB | 2 000 MB/s |2 900 GB | 1 800 MBps |12 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 5 300 GB | 1 800 MBps |12 000 |  
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 700 GB | 1\.800MBps | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 15 000 GB | 1 800 MBps | 12 000 |    

A felsorolt értékek kiindulási pontként szolgálnak, és a valós igények alapján kell kiértékelni őket. Az Azure Ultra Disk előnye, hogy a IOPS és az átviteli sebesség értékei a virtuális gép leállításának vagy a rendszeren alkalmazott számítási feladatok leállításának szükségessége nélkül módosíthatók.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS-v 4.1 kötetek Azure NetApp Files
A Azure NetApp Files olyan natív NFS-megosztásokat biztosít, amelyek a/Hana/Shared, a/Hana/Data és a/Hana/log kötetek esetében használhatók. A ANF-alapú NFS-megosztások használata a/Hana/Data és a/Hana/log kötetek esetében a v 4.1 NFS protokoll használatát igényli. A/Hana/Data és a/Hana/log kötetek használatakor a rendszer nem támogatja az NFS-protokoll v3-as verziójának használatát a ANF-megosztások létrehozásakor. 

> [!IMPORTANT]
> A Azure NetApp Fileson implementált NFS v3 protokoll **nem** támogatott a/Hana/Data és a/Hana/log. Az NFS 4,1 használata kötelező a/Hana/Data és a/Hana/log kötetek számára a funkcionális szempontból. Míg a/Hana/Shared-kötet esetében az NFS v3 vagy az NFS v 4.1 protokoll használható a funkcionális nézetből.

### <a name="important-considerations"></a>Fontos szempontok
Az SAP NetWeaver és SAP HANA Azure NetApp Filesének megfontolása során vegye figyelembe a következő fontos szempontokat:

- A minimális kapacitási készlet 4 TiB.  
- A minimális kötet mérete 100 GiB
- Azure NetApp Files és az összes olyan virtuális gép, amelyben Azure NetApp Files köteteket csatlakoztatni kell, ugyanabban az Azure-Virtual Network vagy egymással azonos régióban lévő [virtuális hálózatokban](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kell lennie.  
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy, a Azure NetApp Files delegált alhálózattal.
- Az Azure NetApp-kötetek átviteli sebessége a mennyiségi kvóta és a szolgáltatási szint függvénye, a [Azure NetApp Files szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)dokumentálva. A HANA Azure NetApp-kötetek méretezése esetén győződjön meg arról, hogy az eredményül kapott átviteli sebesség megfelel a HANA rendszerkövetelményeinek.  
- Azure NetApp Files az [exportálási szabályzatot](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasási & írás, csak olvasható stb.). 
- Azure NetApp Files a szolgáltatás még nem ismeri a zónát. Jelenleg Azure NetApp Files funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei.  
- Fontos, hogy az Azure NetApp-tároló közelében üzembe helyezett virtuális gépek alacsony késéssel rendelkezzenek. 
- A <b>SID</b>adm felhasználói azonosítójának és a virtuális gépek `sapsys`jának azonosítójának meg kell egyeznie Azure NetApp Files-konfigurációval. 

> [!IMPORTANT]
> SAP HANA munkaterhelések esetében a kis késleltetés kritikus fontosságú. A Microsoft képviselőjével együttműködve biztosíthatja, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése közel legyen.  

> [!IMPORTANT]
> Ha eltérés van a <b>SID</b>adm felhasználói azonosítójának és a virtuális gép és az Azure NetApp-konfiguráció közötti `sapsys` csoportazonosító között, akkor az Azure NetApp-köteteken található, a virtuális gépekhez csatlakoztatott fájlok engedélyei `nobody`ként jelennek meg. Ügyeljen arra, hogy a <b>SID</b>adm helyes felhasználói azonosítóját és a `sapsys`csoport azonosítóját határozza meg, amikor [egy új rendszer](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) bekerül a Azure NetApp Filesba.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>HANA-adatbázis méretezése Azure NetApp Files

Az Azure NetApp-kötetek átviteli sebessége a kötet méretének és a szolgáltatási szintnek a függvénye, amely a [Azure NetApp Files szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)van dokumentálva. 

Az Azure-beli SAP-infrastruktúra megtervezése során tisztában kell lennie azzal, hogy az SAP milyen minimális tárterület-átviteli sebességre vonatkozó követelményeket támaszt, ami a következők minimális átviteli teljesítményére fordítható le:

- Írási/olvasási műveletek engedélyezése 250 MB/s/Hana/log 1 MB I/O-mérettel  
- Legalább 400 MB/s olvasási tevékenység engedélyezése a/Hana/Data 16 MB és 64 MB I/O méret esetén  
- Az írási tevékenység engedélyezése legalább 250 MB/s/Hana/Data 16 MB és 64 MB I/O méret esetén  

A [Azure NetApp Files átviteli sebességre vonatkozó határértékek](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 1 TiB-ra vetítve:
- Premium Storage szintű 64 MiB/s  
- Ultra Storage-réteg – 128 MiB/s  

Az adatokhoz és a naplóhoz tartozó minimális teljesítménybeli követelmények teljesítéséhez, valamint a `/hana/shared`iránymutatásainak megfelelően az ajánlott méretek a következőképpen néznek ki:

| Kötet | Méret<br /> Premium Storagei szintű | Méret<br /> Ultra Storage-rétegek | Támogatott NFS-protokoll |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM)/4 feldolgozó csomópont | Max (512 GB, 1xRAM)/4 feldolgozó csomópont | v3 vagy v 4.1 |


> [!NOTE]
> Az itt ismertetett Azure NetApp Files méretezési javaslatok célja, hogy megfeleljenek az SAP expresss infrastruktúra-szolgáltatók felé irányuló minimális követelményeknek. A valós ügyfelek központi telepítései és munkaterhelési forgatókönyvek esetében nem lehet elég. Ezeket a javaslatokat kiindulási pontként és alkalmazkodva használhatja az adott számítási feladatra vonatkozó követelmények alapján.  

Ezért érdemes lehet a ANF-kötetek hasonló átviteli sebességét üzembe helyezni az ultra Disk Storage-ban felsoroltak szerint. Azt is vegye figyelembe, hogy a kötetek mérete a különböző virtuálisgép-SKU-lemezeken a már megadottnál is elérhető.

> [!TIP]
> Azure NetApp Files kötetek átméretezése dinamikusan elvégezhető, anélkül, hogy le kellene `unmount` a köteteket, le kell állítania a virtuális gépeket, vagy le kell állítania SAP HANA. Ez lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás elvárásainak és a várhatóan nem várt átviteli igényeknek.

A ANF szolgáltatásban üzemeltetett, a készenléti csomóponttal rendelkező SAP HANA kibővített konfiguráció üzembe helyezéséről szóló dokumentáció az [Azure-beli virtuális gépek készenléti csomópontján SAP HANA kibővítve, az Azure NetApp Files on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)használatával érhető el.


## <a name="next-steps"></a>Következő lépések
További információkért lásd:

- [SAP HANA magas rendelkezésre állású útmutató Azure-beli virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
