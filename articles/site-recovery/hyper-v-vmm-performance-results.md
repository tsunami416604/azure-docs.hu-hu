---
title: Teszteredmények Hyper-V virtuális gépek replikálása egy másodlagos helyre, az Azure Site Recovery VMM-felhőkben |} Microsoft Docs
description: Ez a cikk a Hyper-V virtuális gépek VMM-felhőkben egy másodlagos helyre Azure Site Recovery segítségével replikáció teljesítménytesztelési információkat biztosít.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: e15f435a3f32b8908b5b93bccc6c57710ab589bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378896"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>A vizsgálati eredmények, a Hyper-V replikáció egy másodlagos helyre

Ez a cikk ismerteti a System Center Virtual Machine Manager (VMM) egy felhőben, a Hyper-V virtuális gépek replikálása másodlagos adatközpontba esetén teljesítménytesztelési eredményeit.

## <a name="test-goals"></a>Tesztelési célokat

A cél vizsgálati volt, hogy vizsgálja meg, hogyan hajtja végre a Site Recovery stabil állapotot replikáció során.

- Ha virtuális gépek kezdeti replikáció befejeződött, és szinkronizál a változási különbözeteket stabil állapotot replikáció.
- Fontos stabil állapotban teljesítményének mérése, mert az állam, mely a legtöbb virtuális gépeken maradnak, hacsak nem várt leállások esetén.
- A teszttelepítés állt két helyszíni hely, a VMM-kiszolgáló minden helyen. Ez az üzembe helyezési teszt a központi iroda/fiók office központi telepítés, a jellemző nevében jár el az elsődleges hely központi iroda és a fiókiroda a másodlagos vagy helyreállítási helyként.

## <a name="what-we-did"></a>Azt adta

Mi a Microsoft a teszt felelt meg az itt található:

1. A VMM-sablonok használatával létrehozott virtuális gépeket.
2. Virtuális gépek elindult, és rögzített alapterv teljesítménymutatók 12 óránál nagyobb.
3. Az elsődleges létrehozott felhők és a helyreállítási VMM-kiszolgálókon.
4. A Site Recovery szolgáltatásban, beleértve a forrás- és a helyreállítási közötti megfeleltetés felhők konfigurált replikációs.
5. Engedélyezve van a virtuális gépek védelmét, és a őket teljes kezdeti replikálása.
6. Néhány óra múlva a rendszer stabilizációs várt.
7. Rögzített teljesítménymutatók 12 óránál nagyobb, ahol minden virtuális gép maradt várt replikációs állapotban ezeket 12 óra.
8. A különbözeti a baseline metrikák, valamint a replikációs metrikák mérni.


## <a name="primary-server-performance"></a>Elsődleges kiszolgáló teljesítménye

* Hyper-V replika (a Site Recovery által használt) aszinkron módon állapotváltozásait egy naplófájlba, a minimális tárolási terhet az elsődleges kiszolgálón.
* A Hyper-V replika használja az önálló karbantartott memóriagyorsítótár IOPS terhet nyomon követésére minimalizálása érdekében. Tárolja a memóriában VHDX ír, és akkor írja ki azokat a naplófájlba a naplót a helyreállítási helyen küldött időpont előtt. A kiürítési lemez is akkor történik, ha az írási műveletek egy előre meghatározott korlátot elérte.
* Az alábbi diagram megjeleníti a stabil állapotot IOPS terhelést a replikáció. Láthatja, hogy a terhelés miatt replikációs IOPS körülbelül 5 % meglehetősen kicsi.

  ![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744913.png)

A Hyper-V replika az elsődleges kiszolgálón memóriát használja lemez teljesítmény optimalizálása érdekében. Az alábbi diagramon látható, terhet elsődleges a fürtben található összes kiszolgáló memóriájának marginális. A terhelés látható memória a replikáció, a Hyper-V kiszolgálón az összes telepített memória képest által használt memória aránya.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V replika rendelkezik olyan minimális CPU-terhelés. Ahogy az a diagramon, replikációs terhelés 2-3 % a tartományban van.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Másodlagos kiszolgáló teljesítménye

A Hyper-V replika a helyreállítási kiszolgálón a kis méretű memória segítségével optimalizálja a tárolási műveletek száma. A diagram a memóriahasználat a helyreállítási kiszolgálón foglalja össze. A terhelés látható memória a replikáció, a Hyper-V kiszolgálón az összes telepített memória képest által használt memória aránya.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744916.png)

A helyreállítási helyen i/o-műveletek mennyiségének feladata az elsődleges hely az írási műveletek számát. Most nézze meg a teljes i/o-műveletek a helyreállítási helyen összehasonlítva az összes i/o-művelet és az írási műveletek az elsődleges helyen. A diagramokon jelzik, hogy a teljes IOPS a helyreállítási helyen

* Körülbelül 1,5-szerese az írási IOPS az elsődleges.
* Körülbelül 37 %-a a teljes IOPS, az elsődleges helyen.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744917.png)

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Hálózathasználat hatása

Átlagosan 275 Mb / s a hálózati sávszélesség elleni egy meglévő sávszélesség 5 GB / s (a tömörítés engedélyezve), az elsődleges és a helyreállítási csomópontok között használt.

![Hálózathasználat eredmények](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>VM teljesítményre gyakorolt hatás

Egy fontos szempont, hogy a replikáció a termelési számítási feladatokhoz a virtuális gépeken futó hatását. Ha az elsődleges hely képes megfelelően lett beállítva a replikáció, akkor nem adható meg gyakorolt hatás a munkaterhelések. Követés mechanizmus a Hyper-V replika lightweight biztosítja, hogy a virtuális gépeken futó számítási feladatok nem érintettek stabil állapot replikáció során. Ezt mutatja be a következő diagramjait.

Ez a grafikon azt ábrázolja, IOPS végrehajt a különböző terhelésekhez előtt futó virtuális gépek, és a replikáció engedélyezése után. Azt is láthatja, hogy nincs-e a kettő közötti különbség.

![A replika hatás eredmények](./media/hyper-v-vmm-performance-results/IC744920.png)

A következő grafikon azt ábrázolja, az átviteli sebesség a különböző terhelésekhez előtt futó virtuális gépek és a replikáció engedélyezése után. Azt is láthatja, hogy a replikáció nincs jelentős hatással van.

![Eredmények replika hatások](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Összegzés

Az eredmények szétválasztásához, hogy a hely helyreállítását követően alapján kialakulhat Hyper-V replika, méretezze át a helyes, legalább egy nagy méretű fürt esetén. A Site Recovery egyszerű üzembe helyezés, a replikáció, a kezelési és figyelési biztosít. Hyper-V replika a szükséges infrastruktúrát biztosít a sikeres replikáció méretezést. 

## <a name="test-environment-details"></a>Környezet részletei

### <a name="primary-site"></a>Elsődleges hely

* Az elsődleges hely öt Hyper-V rendszert futtató, 470 virtuális gépeket tartalmazó fürt rendelkezik.
* A virtuális gépek különböző alkalmazásokat és szolgáltatásokat futtathatnak, és mindegyik rendelkezik a Site Recovery általi védelem engedélyezve van.
* A fürt összes csomópontjára vonatkozóan tárolási egy iSCSI SAN által biztosított. Modell – Hitachi HUS130.
* Minden egyes fürtkiszolgálón rendelkezik négy hálózati kártyák (NIC) a egy kapcsolóport.
* Két, a hálózati kártyák egy iSCSI magánhálózati csatlakoznak, és két külső vállalati hálózatra csatlakoznak. A külső hálózatokat egyik csak a fürtkommunikáció számára van fenntartva.

![Elsődleges hardverkövetelmények](./media/hyper-v-vmm-performance-results/IC744922.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | NIC | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V fürtben lévő kiszolgálókat: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB-HOST25 rendelkezik 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 |I x 4 kapcsolóport |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM Server |2 | | |2 |1 Gbps |Windows Server 2012 adatbázis R2 (x 64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Másodlagos hely

* A másodlagos hely tartozik egy hat csomópontos feladatátvevő fürtöt.
* A fürt összes csomópontjára vonatkozóan tárolási egy iSCSI SAN által biztosított. Modell – Hitachi HUS130.

![Elsődleges hardver meghatározása](./media/hyper-v-vmm-performance-results/IC744923.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | NIC | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V fürtben lévő kiszolgálókat: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30-as GHz |2 |I x 4 kapcsolóport |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM Server |2 | | |2 |1 Gbps |Windows Server 2012 adatbázis R2 (x 64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Kiszolgáló-munkaterhelések

* Tesztelési célokra azt kivételezett gyakori vállalati forgatókönyvet a munkaterhelések.
* Használjuk [IOMeter](http://www.iometer.org) a munkaterhelés jellemző szimuláció táblázat foglalja össze.
* Minden IOMeter profilok írása a legrosszabb szimulálása véletlenszerű bájt írása minták munkaterhelések vannak állítva.

| Számítási feladat | I/o-méret (KB) | % Hozzáférés | % Olvasása | Függőben lévő i/o műveletek | I/o-minta |
| --- | --- | --- | --- | --- | --- |
| Fájlkiszolgáló |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Véletlenszerű 100 % |
| SQL Server (1. kötet) SQL Server (kötet 2) |864 |100%100% |70%0% |88 |szekvenciális 100 %-os random100 % |
| Exchange |32 |100% |67% |8 |100 %-os véletlenszerű |
| Munkaállomás/VDI |464 |66%34% |70%95% |11 |Mindkét véletlenszerű 100 %. |
| Web File Server |4864 |33%34%33% |95%95%95% |888 |Véletlenszerű 75 % |

### <a name="vm-configuration"></a>Virtuálisgép-konfiguráció

* Az elsődleges fürtön 470 virtuális géphez.
* Minden virtuális gépek VHDX-lemezzel.
* A táblázat foglalja össze a munkaterheléseket futtatnak virtuális gépeket. Az összes VMM-sablonok-ekkel hozta létre.

| Számítási feladat | Száma virtuális gépek | Minimális memória (GB) | Maximális memória (GB) | Logikai lemez mérete (GB) virtuális gépenként | Maximális IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Fájlkiszolgáló |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webkiszolgáló |149 |.5 |1 |80 |6 |
| ÖSSZES |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Webhely-helyreállítási beállításai

* A Site Recovery konfigurálása a helyszíni alkalmazások közötti adatszinkronizálás védelme
* A VMM-kiszolgáló a Hyper-V fürt kiszolgálók és a virtuális gépek tartalmazó konfigurált, négy felhők rendelkezik.

| Elsődleges VMM-felhő | Védett virtuális gépek | Replikációs gyakoriság | A további helyreállítási pontok |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 perc |None |
| PrimaryCloudRpo30s |47 |30 másodperc |None |
| PrimaryCloudRpo30sArp1 |47 |30 másodperc |1 |
| PrimaryCloudRpo5m |235 |5 perc |Nincs |

### <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A táblázat összefoglalja a metrikák és a központi telepítésben lévő volt mért számlálókat.

| Metrika | A számláló |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Rendelkezésre álló memória |\Memory\Available memória (MB) |
| IO |\PhysicalDisk(_Total)\Disk Transfers/sec |
| Virtuális gép olvasási művelet/mp (IOPS) |\Hyper-V virtuális tárolóeszköz (<VHD>) \Read művelet/mp |
| Virtuális gép (IOPS) írási művelet/mp |\Hyper-V virtuális tárolóeszköz (<VHD>) \Write műveletek/mp |
| Olvassa el az átviteli sebesség VM |\Hyper-V virtuális tárolóeszköz (<VHD>) \Read bájtok/s |
| Virtuális gép teljesítménye |\Hyper-V virtuális tárolóeszköz (<VHD>) \Write bájtok/s |

## <a name="next-steps"></a>További lépések

[A replikáció beállítása](hyper-v-vmm-disaster-recovery.md)
