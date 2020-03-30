---
title: Hyper-V VM replikációtesztelése egy másodlagos helyre a VMM-mel az Azure Site Recovery használatával
description: Ez a cikk a Hyper-V virtuális gépek VMM-felhőkben történő replikációjának teljesítményteszteléséről nyújt tájékoztatást egy másodlagos helyre az Azure Site Recovery használatával.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 3edd182e335bc679d95d7be64f45b617a9f54c1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73663167"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>A Hyper-V másodlagos helyre való replikációjának vizsgálati eredményei


Ez a cikk a teljesítménytesztelés eredményeit tartalmazza a System Center virtuálisgép-felhőkben lévő Hyper-V vm-ek másodlagos adatközpontba történő replikálásakor.

## <a name="test-goals"></a>Vizsgálati célok

A tesztelés célja az volt, hogy megvizsgálja, hogyan működik a Site Recovery az állandósult állapotú replikáció során.

- Az állandó sult állapotú replikáció akkor következik be, amikor a virtuális gépek befejezték a kezdeti replikációt, és szinkronizálják a különbözeti módosításokat.
- Fontos, hogy a teljesítmény mérése állandósult állapotban, mert ez az állapot, amelyben a legtöbb virtuális gépek maradnak, kivéve, ha váratlan kimaradások fordulnak elő.
- A teszttelepítés két helyszíni helyből állt, mindegyik helyen egy VMM-kiszolgálóval. Ez a teszttelepítés jellemző a központi iroda/fiókiroda üzembe helyezésére, a központi iroda az elsődleges hely, a fiókiroda pedig másodlagos vagy helyreállítási hely.

## <a name="what-we-did"></a>Amit tettünk

Itt van, amit csináltunk a teszt menetben:

1. Virtuális gépek létrehozása VMM-sablonok használatával.
2. Elindított virtuális gépek, és 12 órán keresztül rögzítette az alapkonfiguráció teljesítménymutatóit.
3. Felhőket hozott létre az elsődleges és helyreállítási VMM-kiszolgálókon.
4. Konfigurált replikáció a Site Recovery szolgáltatásban, beleértve a forrás- és helyreállítási felhők közötti hozzárendelést.
5. Engedélyezett a virtuális gépek védelme, és lehetővé tette számukra a kezdeti replikáció befejezését.
6. Vártam pár órát a rendszer stabilizálására.
7. Rögzített teljesítmény metrikák 12 órán keresztül, ahol az összes virtuális gép maradt a várt replikációs állapotban az adott 12 óra.
8. Az alapszintű teljesítménymutatók és a replikációs teljesítménymutatók közötti különbözetet mérte.


## <a name="primary-server-performance"></a>Elsődleges kiszolgáló teljesítménye

* A (Site Recovery által használt) Hyper-V replika aszinkron módon nyomon követi a naplófájl változásait, és minimális tárterülettel rendelkezik az elsődleges kiszolgálón.
* A Hyper-V Replica saját karbantartott memória-gyorsítótárat használ az IOPS-terhelés ek minimalizálásához a követéshez. A memóriában tárolja a VHDX-re írt adatokat, és kiüríti azokat a naplófájlba, mielőtt a naplót elküldi a helyreállítási helyre. A lemez kiürítése akkor is megtörténik, ha az írási műveletek egy előre meghatározott korlátot ér nek el.
* Az alábbi grafikon mutatja az állandósult állapot IOPS terhelés replikációra. Láthatjuk, hogy az IOPS terhelésmiatt replikáció körülbelül 5%, ami elég alacsony.

  ![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744913.png)

A Hyper-V Replica memóriát használ az elsődleges kiszolgálón a lemez teljesítményének optimalizálásához. Amint az a következő grafikonon látható, az elsődleges fürt összes kiszolgálóján a memória terhelése elhanyagolható. A megjelenített memóriaterhelés a replikáció által használt memória százalékos aránya a Hyper-V kiszolgálón telepített teljes memóriához képest.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744914.png)

A Hyper-V Replica minimális processzorterheléssel rendelkezik. Amint az a grafikonon látható, a replikációs terhelés 2-3% tartományban van.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Másodlagos kiszolgáló teljesítménye

A Hyper-V Replica kis mennyiségű memóriát használ a helyreállítási kiszolgálón a tárolási műveletek számának optimalizálásához. A diagram összefoglalja a helyreállítási kiszolgáló memóriahasználatát. A megjelenített memóriaterhelés a replikáció által használt memória százalékos aránya a Hyper-V kiszolgálón telepített teljes memóriához képest.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744916.png)

A helyreállítási helyen az I/O-műveletek mennyisége az elsődleges helyen végzett írási műveletek számától függ. Nézzük meg a helyreállítási helyen a teljes I/O-műveleteket a teljes I/O-műveletekhez és az elsődleges helyen végzett írási műveletekhez képest. A grafikonok azt mutatják, hogy a helyreállítási helyen a teljes IOPS

* Körülbelül 1,5-szer az írási IOPS az elsődleges.
* Az elsődleges helyen a teljes IOPS-szint körülbelül 37%-a.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744917.png)

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>A hálózat kihasználtságára gyakorolt hatás

Az elsődleges és a helyreállítási csomópontok között (a tömörítés engedélyezve) átlagosan 275 Mb/s hálózati sávszélességet használtak a meglévő 5 Gb/s sávszélességgel szemben.

![Eredmények hálózati kihasználtsága](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>A virtuális gép teljesítményére gyakorolt hatás

Fontos szempont a replikáció hatása a virtuális gépeken futó éles számítási feladatokra. Ha az elsődleges hely megfelelően van kiépítve a replikációhoz, nem lehet semmilyen hatással a számítási feladatokra. A Hyper-V Replica könnyű nyomon követési mechanizmusa biztosítja, hogy a virtuális gépeken futó számítási feladatokne befolyásolják az állandósult állapotú replikáció során. Ezt az alábbi grafikonok szemléltetik.

Ez a grafikon a különböző számítási feladatokat futtató virtuális gépek által végrehajtott IOPS-t mutatja a replikáció engedélyezése előtt és után. Megfigyelheti, hogy nincs különbség a kettő között.

![Replikahatás eredményei](./media/hyper-v-vmm-performance-results/IC744920.png)

Az alábbi grafikon a különböző számítási feladatokat futtató virtuális gépek átviteli igényét mutatja a replikáció engedélyezése előtt és után. Megfigyelheti, hogy a replikációnak nincs jelentős hatása.

![Eredmények replikahatások](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Összegzés

Az eredmények egyértelműen azt mutatják, hogy a Site Recovery a Hyper-V replika és a Hyper-V Replika segítségével jól skálázódik, és egy nagy fürt minimális többletterhelése megmérettetésre kerül. A Site Recovery egyszerű telepítést, replikációt, felügyeletet és figyelést biztosít. A Hyper-V Replica biztosítja a sikeres replikációs méretezéshez szükséges infrastruktúrát. 

## <a name="test-environment-details"></a>A tesztkörnyezet részletei

### <a name="primary-site"></a>Elsődleges hely

* Az elsődleges hely egy fürtöt tartalmaz, amely öt Hyper-V kiszolgálót tartalmaz, amelyek 470 virtuális gépet futtatnak.
* A virtuális gépek különböző számítási feladatokat futtatnak, és mindegyikrendelkezik site recovery védelem engedélyezve van.
* A fürtcsomópont tárolását egy iSCSI SAN biztosítja. Modell – Hitachi HUS130.
* Minden fürtkiszolgáló négy, egyenként egy Gbps-es hálózati kártyával rendelkezik.
* A hálózati kártyák közül kettő iSCSI-magánhálózathoz, kettő pedig külső vállalati hálózathoz csatlakozik. Az egyik külső hálózat csak a fürtkommunikáció számára van fenntartva.

![Elsődleges hardverkövetelmények](./media/hyper-v-vmm-performance-results/IC744922.png)

| Kiszolgáló | RAM | Modell | Processzor | Feldolgozók száma | Hálózati adapter | Szoftverek |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V kiszolgálók fürtben: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 már 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Másodlagos hely

* A másodlagos hely hatcsomópontos feladatátvevő fürttel rendelkezik.
* A fürtcsomópont tárolását egy iSCSI SAN biztosítja. Modell – Hitachi HUS130.

![Elsődleges hardver specifikációja](./media/hyper-v-vmm-performance-results/IC744923.png)

| Kiszolgáló | RAM | Modell | Processzor | Feldolgozók száma | Hálózati adapter | Szoftverek |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V kiszolgálók fürtben: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2,20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Kiszolgálói munkaterhelések

* Tesztelési célokra a vállalati ügyfélesetekben gyakran használt számítási feladatokat választottuk ki.
* Az [IOMeter-t](http://www.iometer.org) a táblázatban a szimulációhoz összefoglalt munkaterhelési jellemzővel használjuk.
* Az összes IOMeter-profil úgy van beállítva, hogy véletlenszerű bájtokat írjon a számítási feladatok legrosszabb írási mintáinak szimulálására.

| Számítási feladat | I/O-méret (KB) | %-os hozzáférés | %Olvasás | Kiemelkedő I/O | I/O minta |
| --- | --- | --- | --- | --- | --- |
| Fájlkiszolgáló |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Minden 100%, véletlenszerű |
| SQL Server (1. kötet)<br />SQL Server (2. kötet) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100%-ban véletlenszerű<br />100%-osan szekvenciális |
| Exchange |32 |100% |67% |8 |100%-ban véletlenszerű |
| Munkaállomás/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Mindkettő 100% véletlenszerű |
| Webfájl-kiszolgáló |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Mind a 75% véletlenszerű |

### <a name="vm-configuration"></a>Virtuális gép konfigurációja

* 470 virtuális gép az elsődleges fürtön.
* Minden VHDX lemezzel rendelkező virtuális gép.
* A számítási feladatokat futtató virtuális gépek a táblázatban összegezve. Mindegyik VMM sablonokkal készült.

| Számítási feladat | # Virtuális gépek | Minimális RAM (GB) | Maximális RAM (GB) | Logikai lemezméret (GB) virtuális gépenként | Maximális IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Fájlkiszolgáló |50 |1 |2 |552 |22 |
| Vdi |149 |.5 |1 |80 |6 |
| Webkiszolgáló |149 |.5 |1 |80 |6 |
| ÖSSZESEN |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Webhely-helyreállítási beállítások

* A Site Recovery a helyszíni védelemhez lett konfigurálva
* A VMM-kiszolgáló négy felhők konfigurálva, amely tartalmazza a Hyper-V fürt kiszolgálók és a virtuális gépek.

| Elsődleges VMM-felhő | Védett virtuális gépek | Replikáció gyakorisága | További helyreállítási pontok |
| --- | --- | --- | --- |
| ElsődlegesCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 másodperc |None |
| ElsődlegesCloudRpo30sArp1 |47 |30 másodperc |1 |
| ElsődlegesCloudRpo5m |235 |5 perc |None |

### <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A táblázat összefoglalja a teljesítmény mutatókat és számlálókat, amelyek et a központi telepítésben mért.

| Metrika | Számláló |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Igénybe vehető memória |\Memória\Elérhető MBájt |
| IOPS |\PhysicalDisk(_Total)\Lemezátvitel/mp |
| Virtuálisgép olvasási (IOPS) műveletei másodpercenként |\Hyper-V virtuális tárolóeszköz(\<VHD>)\Olvasási műveletek/mp |
| Virtuálisgép írási (IOPS) műveletei/mp |\Hyper-V virtuális tárolóeszköz(\<VHD>)\Írási műveletek/S |
| Virtuális gép olvasási átviteli-átmenő |\Hyper-V virtuális tárolóeszköz(\<VHD>)\Olvasási bájt/mp |
| Virtuális gép írási átviteli áteresztő-áteresztődése |\Hyper-V virtuális tárolóeszköz(\<VHD>)\Írási bájt/mp |

## <a name="next-steps"></a>További lépések

[Replikáció beállítása](hyper-v-vmm-disaster-recovery.md)
