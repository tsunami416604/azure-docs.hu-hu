---
title: "A StorSimple eszköz figyelése |} Microsoft Docs"
description: "A StorSimple Manager szolgáltatás i/o Teljesítményfigyelő, tárolókapacitás kihasználtságát, hálózati teljesítményt és a Teljesítmény eszköz használatát ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: bd4f7704-4f6f-47d0-927a-b1c91eabc453
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 0e1387bfb3487c148c8befcf3f8bf23bba35661f
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>A StorSimple eszközt a StorSimple Manager szolgáltatás segítségével
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a StorSimple Manager szolgáltatás segítségével figyelheti a StorSimple eszköz](storsimple-8000-monitor-device.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
A StorSimple Manager szolgáltatás használatával figyelheti az adott eszközökön belül a StorSimple megoldásban. I/o teljesítmény, tárolókapacitás használatát, hálózati teljesítményt és eszköz teljesítménymutatók alapján egyéni diagramokat hozhat létre. 

Egy adott eszköz a figyelési információinak megtekintése a klasszikus Azure portálon, válassza ki a StorSimple Manager szolgáltatás. Kattintson a **figyelő** lapot, és válassza ki az eszközök a listából. A **figyelő** lap a következő információkat tartalmazza.

## <a name="io-performance"></a>I/o-teljesítmény
**I/o-teljesítmény** nyomon követi az olvasások száma kapcsolatos metrikákat, és írási műveletek között, vagy az iSCSI-kezdeményező felületek a gazdagép-kiszolgálón és az eszköz vagy az eszközön, és a felhőben. A teljesítmény mérhető egy adott köteten, egy adott kötettároló vagy az összes kötet tároló.

Az alábbi táblázatban látható az i/o-a kezdeményező éles eszköz a kötetek az eszközre. A metrikák ábrázolt olvassa el és írási bájt / mp, olvasási és írási I/O műveletek másodpercenkénti számát, és olvasási és írási késések fordulnak elő.

![Az eszköz IO kezdeményező teljesítményét](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Az ugyanarra az eszközre az i/o-műveletek ábrázolási az adatok az eszközről a felhőbe a kötet tárolók. Ezen az eszközön csak a lineáris rétegében, illetve semmi nem rendelkezik kiömlött a felhőbe. Nincsenek az eszközről a felhőbe előforduló olvasási és írási műveletek. Emiatt a diagramon csúcsait egy 5 perces időközönként, amely megfelel a gyakoriság, amellyel a szívverés jelölőnégyzet be van jelölve az eszköz és a szolgáltatás között lehet. 

![IO-teljesítmény az eszközről a felhőbe](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)

Az ugyanarra az eszközre, a felhő során létrehozott pillanatképen 2:00 pm kezdődő tartozó. Ennek következtében az eszközről a felhőbe áramló adatokat. Olvasási / írási műveletek ennek az időtartamnak a volt a felhőben szolgáltatott. Az IO diagram csúcsot a különböző metrikákat, amikor a pillanatképet során létrehozott megfelelő mutatja. 

![Az eszköz felhő pillanatképet készít a felhő IO teljesítmény](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)

## <a name="capacity-utilization"></a>Kapacitáskihasználás
**Kapacitáskihasználás** nyomon követi a a kötetek, a kötettárolók vagy az eszköz által használt adatok tárolási terület mennyisége kapcsolatos metrikákat. A tárolókapacitás kihasználtságát elsődleges tárhelyét, a felhőalapú tárolást és az eszköz tárolóját alapján jelentéseket is létrehozhat. Kapacitáskihasználás egy adott köteten, egy adott kötettároló vagy az összes kötet tároló mérhető.

Az elsődleges, felhőhöz, és az eszköz tárolási kapacitás leírása a következő lehet:

### <a name="primary-storage-capacity-utilization"></a>Elsődleges tárolókapacitás kihasználtságát
A diagramokat megjelenítése előtt az adatok deduplikációja és tömörített StorSimple-köteteket írt adatok mennyiségét. Megtekintheti az elsődleges tárhely kihasználtságát összes kötet vagy egyetlen kötetén.

Ha az elsődleges tárolási kötet kapacitása az összes kötet és az egyes kötetek mindegyikén kihasználtsági diagramok megtekintése, és mindkét ezekben az esetekben az elsődleges adatok összesíteni, lehet nem egyeznek a két szám közé. A teljes összes kötet elsődleges adatai előfordulhat, hogy nem adja ki az elsődleges adatok az egyes kötetek összessége. Ennek oka a következők egyike lehet:

* **Pillanatkép-adatok tartalmazza az összes kötet**: Ez a viselkedés látható csak akkor, ha a frissítés 3-nál korábbi verzióját futtatja. Látható az összes kötet elsődleges adatai a minden kötet elsődleges adatai és a pillanatkép-adatok. Megjelenik egy adott kötet elsődleges adatai csak a lefoglalt a köteten lévő adatok mennyisége megfelel-e (és nem tartalmazza a megfelelő kötet pillanatfelvétel adatait).
  
    Ez is viszonylag az alábbi képlettel:
  
    *Elsődleges data (az összes kötegről) = összege (elsődleges adatok (kötet i) + (kötet i) pillanatkép-adatok mérete)*
  
    *Ha az elsődleges adatok (kötet i) lefoglalt kötetre i elsődleges adatok mérete =*
  
    A pillanatképek révén a szolgáltatás törlődnek, ha a törlés történik aszinkron módon történik a háttérben. Ez eltarthat egy ideig, frissítenie kell a pillanatkép törlését követően a mennyiségi adatok méretét. 
  
    Ha fut a frissítés, 3-as vagy újabb verzióját, majd a pillanatkép-adatok nem szerepel a kötet adatait. És az elsődleges kihasználtsági kiszámítása a következőképpen történik:
  
    * Elsődleges data (az összes kötegről) = (elsődleges adatok (kötet i) összege
  
    *Ha az elsődleges adatok (kötet i) lefoglalt kötetre i elsődleges adatok mérete =*
* **Kötetek figyelési le van tiltva az összes kötet**: Ha kötetek az eszközön, amelynek figyelési ki van kapcsolva, ezek egyéni kötetek vonatkozó figyelési adatokat nem lesz elérhető a diagramon. Azonban a diagramon az összes kötetek szerepel a köteteket, amelynek figyelési ki van kapcsolva. 
* **Tartalmazza az összes kötet élő társított biztonsági másolatok törlődik a kötetek**: Ha pillanatkép-adatokat tartalmazó kötetek törlődnek, de a társított pillanatképet továbbra is, az eltérés jelenhetnek meg.
* **Törli az összes kötet kötetek**: bizonyos esetekben régebbi kötet előfordulhat, hogy létezik, annak ellenére, hogy ezek törölték. Törlés hatásának nem látható, és az eszköz lehet, hogy megjelenítése alacsonyabb rendelkezésre álló kapacitásból. Kérje a Microsoft Support távolítsa el ezeket a köteteket szükség.

A következő diagram megjelenítése az elsődleges tárolókapacitás kihasználtságát a StorSimple eszköz, előtt és után egy felhő-pillanatfelvételt kerül. Ez ugyanis csak a kötet adatait, egy felhő-pillanatfelvételt nem szükséges az elsődleges tárolási módosítani. Ahogy látja, a diagram nincs különbség miatt felhő pillanatkép létrehozása van folyamatban az elsődleges tárolókapacitás kihasználtságát jeleníti meg. A felhő-pillanatfelvételt kezdete körülbelül 2:00 pm azokon az eszközökön.

![Elsődleges tárolókapacitás kihasználtságát felhőalapú pillanatfelvétel előtt](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Felhő pillanatkép utáni elsődleges kapacitáskihasználás](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Ha 2 frissítése, vagy magasabb oszthatja meg az elsődleges tárolókapacitás kihasználtságát egy egyéni köteten, az összes kötegről, összes rétegzett kötetek és minden helyileg rögzített kötetekhez alább látható módon. Minden helyileg rögzített kötetek bontásához lehetővé teszi gyorsan megállapítani, hány a helyi rétegen használ.

![Az összes helyileg rögzített kötet elsődleges kapacitáskihasználás](./media/storsimple-monitor-device/localvolumes.png)

### <a name="cloud-storage-capacity-utilization"></a>Felhő tárolókapacitás kihasználtságát
A diagramokat megjelenítése használt felhőbeli tárhely mennyiségét. Ezek az adatok deduplikációja és tömöríti. Ez a mennyiség magában foglalja a felhőalapú pillanatfelvételek, amelyek esetleg nem tükröződnek elsődleges kötet és az örökölt vagy szükséges megőrzési célra maradnak adatokat tartalmaz. Hasonlítsa össze az elsődleges és a felhőalapú tárolás fogyasztási adatokat, ha meg szeretne ismerkedni és az adatforgalom csökkentése érdekében, annak ellenére száma nem pontos. A következő diagram megjelenítése a felhő tárolókapacitás kihasználtságát a StorSimple eszköz előtt és után egy felhő-pillanatfelvételt kerül. A felhő-pillanatfelvételt elkezdődött: körülbelül 2:00 pm azokon az eszközökön, és megtekintheti a felhő tárolókapacitás kihasználtságát változásszinkronizálás egy időben, 4.04 GB 5.73 MB növelését.

![Felhő tárolókapacitás kihasználtságát felhőalapú pillanatfelvétel előtt](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![A felhő felhő pillanatképet készít a tárolókapacitás kihasználtságát](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)

### <a name="device-storage-capacity-utilization"></a>Eszköz tárolókapacitás kihasználtságát
Diagramokat jelenít meg az eszköz, amely több, mint az elsődleges tárhely kihasználtságát lesz, mert ez magában foglalja a lineáris SSD-réteghez a teljes kihasználtság. A réteg tartalmaz egy adatmennyiséget is megtalálható az eszközhöz tartozó más szintekre. A lineáris SSD-réteg kapacitása nem szakad meg, így ha új adatok, a régi adatok áthelyezése a HDD-réteg (amikor deduplikált és tömörített), és ezt követően a felhő.

Idővel elsődleges tárolókapacitás kihasználtságát és a tárolókapacitás kihasználtságát eszköz valószínűleg megnöveli a együtt mindaddig, amíg az adatok elkezdi helyezhető el a felhőbe. Ezen a ponton tetőzni valószínűleg megkezdődik, az eszköz tárolókapacitás használatát, de az elsődleges kapacitás nagyobb lesz, több adatot ír.

A következő diagram megjelenítése az elsődleges tárolókapacitás kihasználtságát a StorSimple eszköz, előtt és után egy felhő-pillanatfelvételt kerül. A felhő-pillanatfelvételt, 2:00 pm, és az eszköz tárolókapacitás kihasználtságát ekkor csökken. Az eszköz tárolókapacitás kihasználtságát csökkent 11.58 GB 7.48 GB. Ez azt jelzi, hogy nagy valószínűséggel a lineáris SSD-réteget a tömörítetlen adatokhoz lett deduplikált, tömörített, helyezi át a HDD-réteget. Vegye figyelembe, hogy az eszköz már egy nagy mennyiségű adatot a rétegek az SSD és HDD, nem jelenhet meg ezen csökken. Ebben a példában az eszköz regisztrációját kisebb mennyiségű adatot.

![Eszköz tárolókapacitás kihasználtságát felhőalapú pillanatfelvétel előtt](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Eszköz tárolókapacitás kihasználtságát felhő pillanatkép utáni](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)

## <a name="network-throughput"></a>Hálózati teljesítmény
**Hálózati teljesítmény** nyomon követi a gazdagép-kiszolgálón és az eszköz és az eszköz és a felhő között az iSCSI-kezdeményező hálózati interfész átvitt adatok mennyisége kapcsolatos metrikákat. Ez a metrika az egyes iSCSI hálózati adaptereihez figyelheti az eszközön.

A következő diagram megjelenítése a hálózati átviteli sebesség a Data 0 és adatok 4, két 1 GbE hálózati adapterek az eszközön. Ebben az esetben Data 0 volt felhő-engedélyezve van, mivel az adatok 4 volt az iSCSI-kompatibilis. A StorSimple eszköz láthatja az a bejövő és kimenő forgalmat egyaránt. A diagram du. 3:24-től kezdődő strukturálatlan sor miatt van, hogy a Microsoft csak 5 percenként adatokat gyűjt, és figyelmen kívül lesz hagyva. 

![Az Data4 tartozó hálózati teljesítmény](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Az Data4 tartozó hálózati teljesítmény](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)

## <a name="device-performance"></a>Eszköz teljesítmény
**Eszköz teljesítményének** nyomon követi az eszköz a teljesítménnyel kapcsolatos metrikákat. A következő diagram egy eszköz számára a Processzor kihasználtsága statisztikák éles környezetben jeleníti meg.

![Az eszköz CPU-felhasználás](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple Manager szolgáltatás eszközök irányítópult](storsimple-device-dashboard.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

