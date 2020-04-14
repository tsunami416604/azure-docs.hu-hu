---
title: SAP HANA Azure virtuálisgép-tároló konfigurációk | Microsoft dokumentumok
description: Tárolási javaslatok a virtuális gép, amely az SAP HANA üzembe helyezett bennük.
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
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c4e8b544ea3daeb23b22f3864beb21ba9d3f342f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255617"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure-beli virtuális gépek tárkonfigurációi

Az Azure különböző típusú tárolókat biztosít, amelyek alkalmasak az SAP HANA-t futtató Azure-beli virtuális gépekhez. Az **SAP HANA-tanúsítvánnyal rendelkező Azure-tárolótípusok,** amelyek figyelembe vehetők az SAP HANA-telepítések listájához, például: 

- Azure Prémium SSD  
- [Ultralemez](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Ezekről a lemeztípusokról a [Lemeztípus kiválasztása](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) című témakörben olvashat.

Az Azure két üzembe helyezési módszert kínál a virtuális gépekhez az Azure Standard és a Prémium szintű storage szolgáltatásban. Ha az általános forgatókönyv megengedi, használja ki az [Azure felügyelt lemeztelepítéseit.](https://azure.microsoft.com/services/managed-disks/) 

A tárolási típusok és azok SLE-k az IOPS és a tárolási átviteli kapacitás, tekintse át az [Azure dokumentációját a felügyelt lemezek.](https://azure.microsoft.com/pricing/details/managed-disks/)

> [!IMPORTANT]
> Függetlenül az Azure storage-típus választott, a tárolón használt fájlrendszert az SAP-nak támogatnia kell az adott operációs rendszer hez és a DBMS-hez. [AZ SAP támogatási megjegyzése #405827](https://launchpad.support.sap.com/#/notes/405827) felsorolja a különböző operációs rendszerek és adatbázisok támogatott fájlrendszereit, beleértve az SAP HANA-t is. Ez vonatkozik az összes köteten SAP HANA hozzáférhet az olvasás hoz és írásbármilyen feladathoz. Az NFS-t az SAP HANA-hoz való Azure-ban kifejezetten használva az NFS-verziók további korlátozásai a jelen cikk későbbi részében foglaltak szerint érvényesek. 


A minimális SAP HANA tanúsítvánnyal rendelkező feltételek a különböző tárolási típusok: 

- Az Azure Premium SSD - /hana/log gyorsítótárazása az Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)segítségével történik. A /hana/data kötet azure-írásgyorsító nélkül is elhelyezhető prémium szintű SSD-n vagy Ultra lemezen
- Az Azure Ultra lemez legalább a /hana/log kötet. A /hana/data kötet elhelyezhető prémium szintű SSD-n Az Azure Write Accelerator nélkül, vagy az Ultra lemez gyorsabb újraindítási ideje érdekében.
- **NFS v4.1-es** kötetek az Azure NetApp-fájlok /hana/log **és** /hana/data tetején. A /hana/shared kötete nfs v3 vagy NFS v4.1 protokollt használhat. Az NFS v4.1 protokoll kötelező a /hana/data és/hana/log kötetek esetében.

A tárolótípusok egy része kombinálható. Például lehetséges, hogy /hana/data-ra Prémium storage és /hana/log lehet helyezni ultra lemeztároló annak érdekében, hogy a szükséges alacsony késés. Ha azonban anf-alapú NFS-v4.1-es kötetet használ a /hana/data kapcsolóhoz, akkor a /hana/log eszközhöz egy másik NFS v4.1-es kötetet kell használnia a /hana/log kapcsolóhoz. Az NFS használata az ANF tetején az egyik kötethez (például /hana/data) és az Azure Premium Storage vagy Ultra lemez a másik kötethez (például a /hana/log) **nem támogatott.**

A helyszíni világban ritkán kellett törődnie az I/O alrendszerekkel és annak képességeivel. Ennek oka az volt, hogy a készülék szállítója szükséges győződjön meg arról, hogy a minimális tárolási követelmények teljesülnek az SAP HANA. Az Azure-infrastruktúra saját kezű létrehozásakor tisztában kell lennie néhány ilyen SAP kiadott követelmények. Az SAP-tól megkövetelt minimális átviteli jellemzők közül néhány a következőket eredményezi:

- Írás/írás engedélyezése **/hana/log** 250 MB/mp/s/s/s-on 1 MB I/O-mérettel
- Legalább 400 MB/s olvasási tevékenység engedélyezése **a /hana/data** esetében 16 MB és 64 MB I/O-méretben
- Legalább 250 MB/mp írási tevékenység engedélyezése **a /hana/data** 16 MB és 64 MB I/O-mérettel

Tekintettel arra, hogy az alacsony tárolási késés kritikus fontosságú a DBMS-rendszerek számára, még akkor is, ha a DBMS, például az SAP HANA, az adatok at a memóriában tartják. A kritikus elérési út a tárolás általában körül a tranzakciós napló írja a DBMS-rendszerek. De az olyan műveletek is kritikus fontosságúak lehetnek, mint a mentési pontok írása vagy a memóriában lévő adatok betöltése az összeomlás utáni helyreállítás után. Ezért **kötelező** az Azure Premium lemezek használata **a /hana/data** és **a /hana/log** kötetek számára. A **/hana/log** és a **/hana/data** minimális átviteli igény ének elérése érdekében az SAP-nak létre kell állítania egy RAID 0-t MDADM vagy LVM használatával több Azure Premium Storage-lemezen. És használja a RAID köteteket **/hana/data** és **/hana/log** kötetekként. 

**Javaslat: A RAID 0 csíkméreteként a következő ajánlott:**

- 256 KB a **/hana/data esetében**
- 32 KB a **/hana/log kapcsolóhoz**

> [!IMPORTANT]
> A /hana/data csíkmérete megváltozott a korábbi ajánlásokhoz képest, amelyek 64 KB-ot vagy 128 KB-ot hívtak meg 256 KB-ra az újabb Linux-verziók felhasználói élményei alapján. A 256 KB-os méret valamivel jobb teljesítményt nyújt

> [!NOTE]
> Nem kell konfigurálnia semmilyen redundanciaszintet RAID-kötetek használatával, mivel az Azure Premium és standard szintű tárhely három lemezképet tárol a virtuális merevlemezről. A RAID-kötetek használata pusztán a megfelelő I/O-átviteli forgalmat biztosító kötetek konfigurálására szolgál.

A RAID alatt több Azure Virtuális gép együttesen történő összegyűjtését egy IOPS- és tárolási átviteli oldalról halmozza fel. Így ha egy RAID 0-t helyez el 3 x P30-as Azure Premium Storage-lemez felett, akkor az IOPS háromszorosát és egyetlen Azure Premium Storage P30-lemez tárolási átviteli kapacitásának háromszorosát kell biztosítania.

Is tartsa szem előtt a teljes virtuálisgép I/O átviteli közben, amikor méretezése vagy a virtuális gép meghatározása. A virtuális gép teljes tárolási átviteli igényét a [Memória optimalizált virtuálisgép-méretek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)című cikk dokumentálja.

## <a name="linux-io-scheduler-mode"></a>Linux I/O ütemező mód
A Linux számos különböző I/O ütemezési móddal rendelkezik. A Linux-szállítókon és az SAP-on keresztüli közös ajánlás az I/O-ütemező mód újrakonfigurálása az **mq-határidővagy** **kyber** módlemezes kötetek számára a **noop** (nem többvárólistás) vagy **a** (többvárólistás) mód ba. A részletekre az [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787)hivatkozik. 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Megoldások prémium szintű storage-tal és Azure Write Accelerator-al az Azure M sorozatú virtuális gépekhez
Az Azure Write Accelerator egy olyan funkció, amely kizárólag az Azure M sorozatú virtuális gépekhez érhető el. Ahogy a név is kimondja, a funkció célja, hogy javítsa az Írások I/O-késését az Azure Premium Storage-on. Az SAP HANA esetében az írásgyorsítót csak a **/hana/log** kötet ellen kell használni. Ezért a **/hana/data** és **a /hana/log** külön kötetek, az Azure Write Accelerator csak a **/hana/log** kötetet támogatja. 

> [!IMPORTANT]
> Az Azure Premium Storage használatakor az Azure [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) vagy a /hana/log kötet használata kötelező. Az írásgyorsító csak prémium szintű storage és M sorozatú és Mv2 sorozatú virtuális gépekhez érhető el.

Az alábbi gyorsítótárazási javaslatok feltételezik az SAP HANA I/O-jellemzőit, amelyek a következőkhöz hasonlóak:

- Alig van olvasási számítási feladat a HANA adatfájlok ellen. Kivételek nagy méretű I/O a HANA-példány újraindítása után, vagy amikor az adatok betöltődnek hana. Egy másik esetben a nagyobb olvasási I / O adatfájlok ellen lehet HANA adatbázis biztonsági mentések. Ennek eredményeként olvasni cache többnyire nincs értelme, mivel a legtöbb esetben, az összes adatfájl kötetek kell olvasni teljesen.
- Az adatfájlok írása a HANA mentési pontok és a HANA összeomlás-helyreállítás által tapasztalt adatsorozatok tapasztalt. A savepoints írása aszinkron, és nem tart fel semmilyen felhasználói tranzakciót. Az összeomlás utáni helyreállítás során az adatok írása kritikus teljesítményszempontjából fontos ahhoz, hogy a rendszer ismét gyorsan reagáljon. A balesetek helyreállításának azonban meglehetősen kivételes
- Alig van olyan olvas a HANA redo fájlokat. Kivételek a nagy I/O tranzakciónapló biztonsági mentések végrehajtásakor, összeomlás helyreállítása, vagy egy HANA-példány újraindítási fázisában.  
- Az SAP HANA újranaplófájl fő terhelése írási műveletek. A munkaterhelés jellegétől függően az I/O-k akár 4 KB-os is lehetnek, vagy más esetekben 1 MB vagy annál nagyobb I/O-méret. Írási késés az SAP HANA újra-napló javunkra kritikus.
- Minden írást megbízható módon kell megtartani a lemezen

**Javaslat: Az SAP HANA által megfigyelt I/O-minták eredményeképpen az Azure Premium Storage használatával a különböző kötetek gyorsítótárazását a következőképpen kell beállítani:**

- **/hana/data** - nincs gyorsítótárazás
- **/hana/log** - nincs gyorsítótárazás - kivétel az M- és Mv2-sorozathoz, ahol az írásgyorsítót engedélyezni kell olvasási gyorsítótárazás nélkül. 
- **/hana/shared** - gyorsítótárazás olvasása

### <a name="production-recommended-storage-solution"></a>Termelés ajánlott tárolási megoldás

> [!IMPORTANT]
> SAP HANA minősítés az Azure M sorozatú virtuális gépek kizárólag az Azure Write Accelerator a **/hana/log** kötet. Ennek eredményeképpen éles forgatókönyv SAP HANA telepítések az Azure M sorozatú virtuális gépeken várhatóan konfigurálva kell lennie az Azure Write Accelerator a **/hana/log** kötet.  

> [!NOTE]
> Éles környezetben ellenőrizze, hogy egy bizonyos virtuális gép típus támogatja-e az SAP HANA az SAP az [IAAS SAP dokumentációjában.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)

A javaslatok gyakran meghaladja az SAP minimális követelmények, korábban ebben a cikkben. A felsorolt javaslatok az SAP és a különböző virtuálisgép-típusok által biztosított maximális tárolási átviteli teljesítmény közötti kompromisszumot tartalmaznak.

**Javaslat: Az éles környezetben ajánlott konfigurációk a következőkre néznek ki:**

| Virtuális gép termékváltozata | RAM | Legfeljebb Virtuális gép i/o-ja<br /> Teljesítmény | /hana/adatok | /hana/log | /hana/megosztott | /gyökérkötet | /usr/nedv | hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts között | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64-esek | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128-asok | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Ellenőrizze, hogy a különböző javasolt kötetek tárolási átviteli igénye megfelel-e a futtatni kívánt számítási feladatnak. Ha a számítási feladatok nagyobb köteteket igényelnek a **/hana/data** és **a /hana/log**számára, növelnie kell az Azure Premium Storage virtuális gépek számát. A felsoroltnál több virtuális számítógéppel rendelkező kötet méretezése növeli az IOPS- és I/O-átviteli forgalmat az Azure virtuálisgép-típus korlátain belül.

Az Azure Write Accelerator csak az [Azure által felügyelt lemezekkel](https://azure.microsoft.com/services/managed-disks/)együtt működik. Így legalább a **/hana/log** kötetet alkotó Azure Premium Storage-lemezeket felügyelt lemezként kell telepíteni.

Az Azure Premium Storage virtuális gépekenkénti virtuális gépek korlátai, amelyeket az Azure Write Accelerator támogat. A jelenlegi határértékek a következők:

- 16 VHD Egy M128xx és M416xx VM
- 8 VHD Egy M64xx és M208xx VM
- 4 VHD Egy M32xx VM

Az Azure Write Accelerator engedélyezésére vonatkozó részletesebb utasítások a [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)című cikkben találhatók.

Az Azure Write Accelerator részletei és korlátozásai ugyanabban a dokumentációban találhatók.

**Javaslat: A /hana/log kötetet alkotó lemezekhez írásgyorsítót kell használnia.**


### <a name="cost-conscious-azure-storage-configuration"></a>Költségtudatos Azure Storage-konfiguráció
Az alábbi táblázat a virtuálisgép-típusok konfigurációját mutatja be, amelyet az ügyfelek az SAP HANA Azure-beli virtuális gépeken való üzemeltetéséhez is használnak. Előfordulhat, hogy néhány virtuális géptípusok, amelyek nem felelnek meg az SAP HANA összes minimális tárolási feltételeket, vagy nem hivatalosan támogatja az SAP HANA az SAP által. De eddig ezek a virtuális gépek úgy tűnt, hogy végre finom nem éles forgatókönyvek. A **/hana/data** és **a /hana/log** egy kötethez lesz kombinálva. Ennek eredményeképpen az Azure Write Accelerator használata az IOPS korlátozásává válhat.

> [!NOTE]
> Az SAP által támogatott forgatókönyvek esetén ellenőrizze, hogy az SAP támogatja-e az SAP HANA egy bizonyos virtuálisgép-típust az [IAAS SAP-dokumentációjában.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)

> [!NOTE]
> A költségtudatos megoldásra vonatkozó korábbi javaslatokhoz képest az [Azure Standard HDD-lemezekről](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) a jobban teljesítő és megbízhatóbb [Azure Standard SSD-lemezekre](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) való áttérés

A javaslatok gyakran meghaladja az SAP minimális követelmények, korábban ebben a cikkben. A felsorolt javaslatok az SAP és a különböző virtuálisgép-típusok által biztosított maximális tárolási átviteli teljesítmény közötti kompromisszumot tartalmaznak.

| Virtuális gép termékváltozata | RAM | Legfeljebb Virtuális gép i/o-ja<br /> Teljesítmény | /hana/data és /hana/log<br /> csíkozott LVM vagy MDADM | /hana/megosztott | /gyökérkötet | /usr/nedv | hana/biztonsági mentés |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts között | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64-esek | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128-asok | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


A kisebb, 3 x P20-as géptípussal rendelkező virtuálisgép-típusokhoz ajánlott lemezek az [SAP TDI Storage tanulmányának](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)megfelelően túlméretesítik a térjavaslatokra vonatkozó köteteket. Azonban a táblában látható választás úgy történt, hogy elegendő lemezátviteli az SAP HANA számára. Ha módosítania kell a **/hana/backup** kötetet, amely a memóriakötet kétszeresét képviselő biztonsági mentések megőrzéséhez volt méretezve, nyugodtan módosíthassa.   
Ellenőrizze, hogy a különböző javasolt kötetek tárolási átviteli igénye megfelel-e a futtatni kívánt számítási feladatnak. Ha a számítási feladatok nagyobb köteteket igényelnek a **/hana/data** és **a /hana/log**számára, növelnie kell az Azure Premium Storage virtuális gépek számát. A felsoroltnál több virtuális számítógéppel rendelkező kötet méretezése növeli az IOPS- és I/O-átviteli forgalmat az Azure virtuálisgép-típus korlátain belül. 

> [!NOTE]
> A fenti konfigurációk nem részesülnek az [Azure virtuális gép egyetlen virtuális gép SLA,](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) mivel az Azure Premium Storage és az Azure Standard Storage keverékét használja. A kiválasztást azonban a költségek optimalizálása érdekében választották ki. Az Azure Standard Storage (Sxx) néven felsorolt összes fenti lemezek prémium szintű storage-át kell választania ahhoz, hogy a virtuális gép konfigurációja megfeleljen az Azure egyvirtuális gép SLA-jának.


> [!NOTE]
> A lemezkonfigurációs javaslatok a minimális SAP-kifejezett követelmények az infrastruktúra-szolgáltatók felé célzó. A valós ügyfél-telepítések és a számítási feladatok forgatókönyvek, olyan helyzetek merültek fel, ahol ezek a javaslatok még mindig nem nyújtanak megfelelő képességeket. Ezek lehetnek olyan helyzetek, amikor az ügyfél a HANA újraindítása után gyorsabban újrabetöltheti az adatokat, vagy ahol a biztonsági mentési konfigurációk nagyobb sávszélességet igényelnek a tárolóhoz. Más esetekben a **/hana/log,** ahol 5000 IOPS nem volt elegendő az adott számítási feladathoz. Ezért vegye be ezeket az ajánlásokat kiindulási pontként, és alkalmazkodjon a munkaterhelés követelményei alapján.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Az Azure Ultra lemeztárolási konfigurációja az SAP HANA-hoz
A Microsoft jelenleg egy új Azure-tárolótípust vezet be, az [Azure Ultra lemezt.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) Az eddig kínált Azure storage és az Ultra lemez közötti jelentős különbség az, hogy a lemez képességek már nem kötődnek a lemez méretéhez. Ügyfélként ezeket a képességeket határozhatja meg az Ultra lemezhez:

- 4 GiB és 65 536 GiB közötti lemez mérete
- Az IOPS 100 IOPS-tól 160 K IOPS-ig terjed (a maximális érték a virtuálisgép-típusoktól is függ)
- Tárolási átviteli sebesség 300 MB/mp és 2000 MB/s között

Az Ultra lemez lehetővé teszi egyetlen olyan lemez definiálását, amely megfelel a méretnek, az IOPS-nak és a lemezátviteli tartománynak. Ahelyett, hogy logikai kötetkezelők, például LVM vagy MDADM az Azure Premium Storage-on az IOPS és a tárolási átviteli követelményeknek megfelelő kötetek létrehozásához. Az Ultra lemez és a prémium szintű storage konfigurációs keveréket futtathat. Ennek eredményeképpen korlátozhatja az Ultra lemez használatát a kritikus teljesítményre /hana/data és /hana/log kötetekre, és lefedheti a többi kötetet az Azure Premium Storage szolgáltatással.

Az Ultra lemez egyéb előnyei a prémium szintű storage-hoz képest a jobb olvasási késleltetés lehetnek. A gyorsabb olvasási késés nek előnyei lehetnek, ha csökkenteni szeretné a HANA indítási idejét és az adatok memóriabe való további terhelését. Az Ultra lemezes tárolás előnyei akkor is érezhetők, amikor a HANA mentési pontokat ír. Mivel a /hana/data prémium szintű storage-lemezei általában nem gyorsítótárazott írási gyorssegédek, a /hana/data írási késése a Prémium szintű storage-ban az Ultra lemezhez képest magasabb. Várható, hogy a savepoint írás ultralemezzel jobban teljesít az Ultra lemezen.

> [!IMPORTANT]
> Az Ultra-lemez még nem található meg az összes Azure-régióban, és még nem támogatja az alább felsorolt összes virtuálisgép-típust. Ha részletesinformációt talál arról, hogy hol érhető el az Ultra lemez, és mely virtuálisgép-családok támogatottak, olvassa el a [cikkEt, hogy milyen lemeztípusok érhetők el az Azure-ban?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Termelés ajánlott tárolási megoldás tiszta Ultra lemez konfiguráció
Ebben a konfigurációban a /hana/data és a /hana/log köteteket külön kell tartani. A javasolt értékek származnak a KPI-k, hogy az SAP-nak kell tanúsítania vm-típusok SAP HANA és tárolási konfigurációk ajánlott az [SAP TDI Storage tanulmány.](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

A javaslatok gyakran meghaladja az SAP minimális követelmények, korábban ebben a cikkben. A felsorolt javaslatok az SAP és a különböző virtuálisgép-típusok által biztosított maximális tárolási átviteli teljesítmény közötti kompromisszumot tartalmaznak.

| Virtuális gép termékváltozata | RAM | Legfeljebb Virtuális gép i/o-ja<br /> Teljesítmény | /hana/adatkötet | /hana/data I/O átviteli | /hana/data IOPS | /hana/log kötet | /hana/log I/O átviteli | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1200 MB/s | 600 GB | 700 Mb/s | 7500 | 512 GB | 500 Mb/s  | 2000 |
| M32ts között | 192 GiB | 500 MB/s | 250 GB | 400 Mb/s | 7500 | 256 GB | 250 Mb/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 Mb/s | 7500 | 256 GB | 250 Mb/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 600 Mb/s | 7500 | 512 GB | 400 Mb/s  | 2500 |
| M64-esek | 1000 GiB | 1000 MB/s |  1200 GB | 600 Mb/s | 7500 | 512 GB | 400 Mb/s  | 2500 |
| M64ms | 1750 GiB | 1000 MB/s | 2100 GB | 600 Mb/s | 7500 | 512 GB | 400 Mb/s  | 2500 |
| M128-asok | 2000 GiB | 2000 MB/s |2400 GB | 1200 Mb/s |9000 | 512 GB | 800 Mb/s  | 3,000 | 
| M128ms | 3800 GiB | 2000 MB/s | 4800 GB | 1200 Mb/s |9000 | 512 GB | 800 Mb/s  | 3,000 | 
| M208s_v2 | 2850 GiB | 1000 MB/s | 3500 GB | 1000 Mb/s | 9000 | 512 GB | 400 Mb/s  | 2500 | 
| M208ms_v2 | 5700 GiB | 1000 MB/s | 7200 GB | 1000 Mb/s | 9000 | 512 GB | 400 Mb/s  | 2500 | 
| M416s_v2 | 5700 GiB | 2000 MB/s | 7200 GB | 1500 Mb/s | 9000 | 512 GB | 800 Mb/s  | 3,000 | 
| M416ms_v2 | 11 400 GiB | 2000 MB/s | 14 400 GB | 1500 Mb/s | 9000 | 512 GB | 800 Mb/s  | 3,000 |   

A felsorolt értékek célja, hogy a kiindulási pont, és ki kell értékelni a valós igényeket. Az Azure Ultra-lemez előnye, hogy az IOPS és az átviteli értékértékek a virtuális gép leállítása vagy a rendszerre alkalmazott számítási feladatok leállítása nélkül is módosíthatók.   

> [!NOTE]
> Eddig az Ultra lemezes tárolással rendelkező tárolási pillanatképek nem érhetők el. Ez blokkolja a virtuális gép pillanatképeinek használatát az Azure Backup Services szolgáltatással

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Költségtudatos tárolási megoldás tiszta Ultra lemezkonfigurációval
Ebben a konfigurációban a /hana/data és a /hana/log kötetek ugyanazon a lemezen. A javasolt értékek származnak a KPI-k, hogy az SAP-nak kell tanúsítania vm-típusok SAP HANA és tárolási konfigurációk ajánlott az [SAP TDI Storage tanulmány.](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 

A javaslatok gyakran meghaladja az SAP minimális követelmények, korábban ebben a cikkben. A felsorolt javaslatok az SAP és a különböző virtuálisgép-típusok által biztosított maximális tárolási átviteli teljesítmény közötti kompromisszumot tartalmaznak.

| Virtuális gép termékváltozata | RAM | Legfeljebb Virtuális gép i/o-ja<br /> Teljesítmény | Kötet a /hana/data és a /log számára | /hana/data és log I/O átviteli | /hana/data és log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1200 MB/s | 1200 GB | 1200 Mb/s | 9,500 | 
| M32ts között | 192 GiB | 500 MB/s | 512 GB | 400 Mb/s | 9,500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 Mb/s | 9,500 | 
| M64ls | 512 GiB | 1000 MB/s | 1100 GB | 900 Mb/s | 10,000 | 
| M64-esek | 1000 GiB | 1000 MB/s |  1700 GB | 900 Mb/s | 10,000 | 
| M64ms | 1750 GiB | 1000 MB/s | 2600 GB | 900 Mb/s | 10,000 | 
| M128-asok | 2000 GiB | 2000 MB/s |2900 GB | 1800 Mb/s |12 000 | 
| M128ms | 3800 GiB | 2000 MB/s | 5300 GB | 1800 Mb/s |12 000 |  
| M208s_v2 | 2850 GiB | 1000 MB/s | 4000 GB | 900 Mb/s | 10,000 |  
| M208ms_v2 | 5700 GiB | 1000 MB/s | 7700 GB | 900 Mb/s | 10,000 | 
| M416s_v2 | 5700 GiB | 2000 MB/s | 7700 GB | 1 800 Mb/s | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2000 MB/s | 15 000 GB | 1800 Mb/s | 12 000 |    

A felsorolt értékek célja, hogy a kiindulási pont, és ki kell értékelni a valós igényeket. Az Azure Ultra-lemez előnye, hogy az IOPS és az átviteli értékértékek a virtuális gép leállítása vagy a rendszerre alkalmazott számítási feladatok leállítása nélkül is módosíthatók.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS 4.1-es kötetek az Azure NetApp-fájlokon
Az Azure NetApp Files natív NFS-megosztásokat biztosít, amelyek a /hana/shared, a /hana/data és a /hana/log kötetek. Az ANF-alapú NFS-megosztások használata a /hana/data és a /hana/log kötetek esetében a v4.1 NFS protokoll használatát igényli. Az NFS v3 protokoll nem támogatott a /hana/data és a /hana/log kötetek használatához, amikor a megosztásokat anf-re alapozza. 

> [!IMPORTANT]
> Az Azure NetApp-fájlokon megvalósított NFS v3 protokoll **nem** használható a /hana/data és a /hana/log protokollhoz. Az NFS 4.1 használata funkcionális szempontból kötelező a /hana/data és a /hana/log kötetek esetében. Mivel a /hana/shared kötetesetében az NFS v3 vagy az NFS v4.1 protokoll funkcionális szempontból használható.

### <a name="important-considerations"></a>Fontos szempontok
Ha az SAP Netweaver és az SAP HANA Azure NetApp-fájljait veszi figyelembe, vegye figyelembe az alábbi fontos szempontokat:

- A minimális kapacitású medence 4 TiB.  
- A minimális térfogatméret 100 GiB
- Az Azure NetApp-fájloknak és minden olyan virtuális gépnek, ahol az Azure NetApp-fájlok kötetei csatlakoztatva lesznek, ugyanabban az Azure virtuális hálózatban vagy [társviszonyban lévő virtuális hálózatokban](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kell lennie ugyanabban a régióban.  
- A kijelölt virtuális hálózatnak rendelkeznie kell egy alhálózattal, amelyet az Azure NetApp-fájlok delegálnak.
- Az Azure NetApp-kötet átviteli forgalma a mennyiségi kvóta és a szolgáltatásszint függvénye, az [Azure NetApp-fájlok szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)dokumentált módon. A HANA Azure NetApp-kötetek méretezésekénél győződjön meg arról, hogy az eredményül kapott átviteli igény megfelel a HANA rendszerkövetelményeinek.  
- Az Azure NetApp Files [exportálási szabályzatot](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)kínál: szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasás&írás, írás, írás stb.). 
- Az Azure NetApp Files szolgáltatás még nem ismeri a zónát. Jelenleg az Azure NetApp Files szolgáltatás nem telepíthető az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe a lehetséges késési következményekkel jár egyes Azure-régiókban.  
- Fontos, hogy a virtuális gépek telepítése az Azure NetApp-tároló közvetlen közelében legyen alacsony késés esetén. 
- A felhasználói azonosító <b>sid</b>adm és a `sapsys` csoportazonosító a virtuális gépeken meg kell egyeznie a konfiguráció az Azure NetApp Files. 

> [!IMPORTANT]
> AZ SAP HANA számítási feladatok esetén az alacsony késés kritikus fontosságú. Együttműködve a Microsoft képviselőjével annak érdekében, hogy a virtuális gépek és az Azure NetApp Files kötetek közvetlen közelben legyenek telepítve.  

> [!IMPORTANT]
> Ha eltérés van a <b>sid</b>adm felhasználói azonosítója és a `sapsys` csoportazonosító között a virtuális gép és az Azure NetApp-konfiguráció között, a virtuális gépekre csatlakoztatott Azure NetApp-kötetek fájljaira vonatkozó engedélyek a következőképpen `nobody`jelennek meg: . Győződjön meg arról, hogy adja meg a megfelelő felhasználói `sapsys` <b>azonosítót sid</b>adm és a csoport azonosítója, amikor a [beszállás egy új rendszer](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) az Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>HanA-adatbázis méretezése az Azure NetApp-fájlokon

Az Azure NetApp-kötet átviteli forgalma a kötet méretének és a szolgáltatás szintjének függvénye, az [Azure NetApp-fájlok szolgáltatási szintjén](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)dokumentált függvény. 

Az SAP azure-beli infrastruktúrájának tervezésekor tisztában kell lennie az SAP néhány minimális tárolási átviteli követelményeivel, amelyek a következők minimális átviteli jellemzőivé alakulnak ki:

- Írás/írás engedélyezése 250 MB/mp/s/s/s-on 1 MB I/O-mérettel  
- Legalább 400 MB/s olvasási tevékenység engedélyezése a /hana/data esetében 16 MB és 64 MB I/O-méretben  
- Legalább 250 MB/mp írási tevékenység engedélyezése a /hana/data 16 MB és 64 MB I/O-mérettel  

Az [Azure NetApp Files átviteli korlát1](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) TiB kötetkvóta a következők:
- Prémium szintű tárolási szint - 64 MiB/s  
- Ultra tárolási szint - 128 MiB/s  

> [!IMPORTANT]
> Függetlenül attól, hogy a kapacitás egyetlen NFS-köteten üzembe helyezett, az átviteli, várhatóan fennsíkon a tartományban 1,2-1,4 GB/sec sávszélesség által a felhasználó egy virtuális gépben. Ennek köze van az ANF ajánlat alapjául szolgáló architektúrához és a kapcsolódó Linux munkamenet korlátokhoz az NFS körül. A teljesítmény- és átviteli-számok a cikkben dokumentált [Teljesítmény-teljesítmény teszt eredmények az Azure NetApp-fájlok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) hajtottak végre egy megosztott NFS-kötet több ügyfél virtuális gépek, és ennek eredményeként több munkamenetek. Ez a forgatókönyv eltér az SAP-ban mérjük forgatókönyv. Ahol egyetlen virtuális gép átviteli erejét egy NFS-kötethez mérjük. az ANF-en.

Az SAP minimális átviteli követelményeinek teljesítéséhez az adatokés `/hana/shared`a napló, és az irányelvek nek megfelelően az ajánlott méretek a következőkre néznek ki:

| Kötet | Méret<br /> Prémium szintű tárolási szint | Méret<br /> Ultra tárolási szint | Támogatott NFS protokoll |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | 4.1.-es |
| /hana/adatok | 6.3 TiB | 3.2 TiB | 4.1.-es |
| /hana/megosztott | Max(512 GB, 1xRAM) 4 munkavégző csomópontonként | Max(512 GB, 1xRAM) 4 munkavégző csomópontonként | 3-as vagy 4.1-es |


> [!NOTE]
> Az Azure NetApp-fájlok méretezési javaslatok itt megadott célzás, hogy megfeleljen a minimális követelményekNEK SAP kifejezi az infrastruktúra-szolgáltatók. A valós ügyfél-telepítések és a számítási feladatok forgatókönyvek, ez nem lehet elég. Ezeket a javaslatokat kiindulási pontként használja, és alkalmazkodjon az adott munkaterhelés követelményei alapján.  

Ezért érdemes lehet az ANF-kötetek hasonló átviteli kapacitásának üzembe helyezését, ahogy az Ultra lemezes tárolásmár szerepel. Is vegye figyelembe a méreteket a felsorolt méretek a kötetek a különböző virtuálisgép-skus, mint az Ultra lemez táblák már megtörtént.

> [!TIP]
> Az Azure NetApp-fájlok kötetek dinamikusan méretezhetők, `unmount` a kötetek nélkül, a virtuális gépek leállítása vagy az SAP HANA leállítása. Ez lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás nak mind a várható, mind az előre nem látható átviteli igényeknek.

Az ANF-ben üzemeltetett NFS v4.1-es köteteket használó SAP HANA kibővített konfigurációval rendelkező SAP HANA kibővített konfigurációk telepítésének dokumentációját az SAP HANA kibővített verzióban teszi közzé az [Azure-beli Virtuális gépeken lévő, SUSE Linux Enterprise Server rendszeren lévő Készenléti csomópontokkal.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)


## <a name="next-steps"></a>További lépések
További információkért lásd:

- [SAP HANA magas rendelkezésre állási útmutató az Azure virtuális gépek.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
