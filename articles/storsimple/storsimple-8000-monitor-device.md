---
title: "A StorSimple 8000 series eszköz figyelése |} Microsoft Docs"
description: "Ismerteti a StorSimple Device Manager szolgáltatás a figyelheti a használati, i/o-teljesítmény és kapacitás kihasználtságát."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 679c1fc8775ad4481bc99c9aea79fe16e9bcac8f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>A StorSimple Device Manager szolgáltatással a StorSimple eszköz figyelése

## <a name="overview"></a>Áttekintés
A StorSimple Device Manager szolgáltatás használatával figyelheti az adott eszközökön belül a StorSimple megoldásban. Hozzon létre egyéni diagramok i/o teljesítmény, tárolókapacitás használatát, hálózati teljesítményt és eszköz teljesítménymutatók alapján, és rögzítheti az irányítópulton azokat. További információkért látogasson el [testre szabhatja a portál irányítópultján](../azure-portal/azure-portal-dashboards.md).

Az Azure portálon egy adott eszköz a figyelési adatok megtekintéséhez válassza ki a StorSimple Device Manager szolgáltatást. Az eszközök listájában válassza ki azt az eszközt, és folytassa a **figyelő**. Ezután láthatja a **kapacitás**, **használati**, és **teljesítmény** diagramokon a kijelölt eszközre vonatkozóan.

## <a name="capacity"></a>Kapacitás
**Kapacitás** nyomon követi a kiépített és az eszközön rendelkezésre területet. A fennmaradó kapacitás majd jelenik meg helyileg rögzített vagy rétegzett.

A kiépített és a fennmaradó kapacitás további bontásban rétegzett és helyileg rögzített kötetek. Az egyes kötetek a kiosztott, és a fennmaradó kapacitás az eszközön jelenik meg.

![IO-kapacitás](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Használat
**Használati** nyomon követi a a kötetek, a kötettárolók vagy az eszköz által használt adatok tárolási terület mennyisége kapcsolatos metrikákat. A tárolókapacitás kihasználtságát elsődleges tárhelyét, a felhőalapú tárolást és az eszköz tárolóját alapján jelentéseket is létrehozhat. Kapacitáskihasználás egy adott köteten, egy adott kötettároló vagy az összes kötet tároló mérhető.
Alapértelmezés szerint az elmúlt 24 órában használatát jelenti. Módosíthatja a diagram az időtartam, amelyen keresztül a használati való kiválasztással jelentett módosítása:
* Elmúlt 24 órában
* Elmúlt 7 nap
* Elmúlt 30 nap
* Elmúlt 90 nap
* Elmúlt év

Két fő metrices, a növekedési és a tartomány jelenti, hogy a használati diagramok. Hivatkozik a maximális érték és a használat során kiválasztott (a fő példány, az elmúlt 7 nap) jelentette a minimális értékek.

Növekedési hivatkozik a használati növekedése utolsó napja első napjától kijelölt időtartama alatt. 

Növekedés és a tartomány a következő egyenleteket is ábrázolható:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Az elsődleges, felhőhöz, és helyi tárhelyet leírása a következő lehet:

### <a name="primary-storage-usage"></a>Elsődleges tárhely kihasználtsága
A diagramokat megjelenítése előtt az adatok deduplikációja és tömörített StorSimple-köteteket írt adatok mennyiségét. A kötettároló, vagy csak egy kötet összes kötet által használt elsődleges tárolási tekintheti meg. A használt elsődleges tárolási további által használt elsődleges rétegzett tárhely lebontva és elsődleges helyileg rögzített használt tároló.

A következő diagram megjelenítése a StorSimple eszköz előtt és után egy felhő-pillanatfelvételt készült használt elsődleges tárolására. Ez ugyanis csak a kötet adatait, egy felhő-pillanatfelvételt nem szükséges az elsődleges tárolási módosítani. Ahogy látja, a diagram nincs különbség miatt felhő pillanatképének használt elsődleges rétegzett vagy helyileg rögzített tároló jeleníti meg. A felhő-pillanatfelvételt lépések du. körülbelül 11:50 azokon az eszközökön.

![Felhő pillanatkép utáni elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Ha a gazdagép csatlakozik a StorSimple eszköz IO most futtatja, látni fogja az elsődleges rétegzett tárolás növekedése vagy elsődleges helyileg rögzített használt tároló függően (Szintezett vagy helyileg rögzített) kötetek esetén írhat az adatokat. Az alábbiakban a StorSimple eszköz elsődleges tárolási használati diagramokat. Ezen az eszközön a StorSimple gazdagépen elindította a szolgáltató írása, körülbelül 2:30 pm a rétegzett kötetek az eszközön. A maximális írási bájt/s a IO az állomáson futó megfelelő tekintheti meg.

![Ha a futó IO rétegzett kötet teljesítmény](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

A használt elsődleges rétegzett tárhely tekinti meg, ha, amely állapotba került, mivel az elsődleges helyileg rögzített használati változatlan marad, mivel nincs írási műveleteket az eszközön helyileg rögzített kötetek számára szolgáltatott.

![Elsődleges tárolókapacitás kihasználtságát IO a rétegzett kötetek futtatásakor](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Ha futtatja 3. frissítéssel vagy magasabb oszthatja meg az elsődleges tárolókapacitás kihasználtságát egy egyéni köteten, az összes kötegről, összes rétegzett kötetek és minden helyileg rögzített kötetekhez alább látható módon. Minden helyileg rögzített kötetek bontásához lehetővé teszi gyorsan megállapítani, hány a helyi rétegen használ.

![Az összes rétegzett kötet elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Az összes helyileg rögzített kötet elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Minden részt vevő kötetet a listában kattintson a Tovább, és a megfelelő használati.

![Az összes helyileg rögzített kötet elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>A felhőalapú tároló használata
A diagramokat megjelenítése használt felhőbeli tárhely mennyiségét. Ezek az adatok deduplikációja és tömöríti. Ez a mennyiség magában foglalja a felhőalapú pillanatfelvételek, amelyek esetleg nem tükröződnek elsődleges kötet és az örökölt vagy szükséges megőrzési célra maradnak adatokat tartalmaz. Hasonlítsa össze az elsődleges és a felhőalapú tárolás fogyasztási adatokat, ha meg szeretne ismerkedni és az adatforgalom csökkentése érdekében, annak ellenére száma nem pontos.

A következő diagram megjelenítése a felhőbeli tárhely kihasználtságát a StorSimple eszköz, ha egy felhő-pillanatfelvételt kerül.

* A felhő-pillanatfelvételt elkezdődött: körülbelül 11:50-kor az eszközön, és láthatja, hogy a pillanatkép-felhő, mielőtt nincs nem használt felhőbeli tárhelyhez. 
* Egyszer a felhő-pillanatfelvételt befejeződött, a felhőbeli tárhely kihasználtságát változásszinkronizálás 0,89 GB fel. 
* Miközben folyamatban volt a felhő-pillanatfelvételt, nincs is megfelelő csúcs a IO eszközről a felhőbe.

    ![Felhőbeli tárhely kihasználtságát a felhő-pillanatfelvételt előtt](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Felhőbeli tárhely kihasználtságát felhő pillanatkép utáni](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Az eszközről a felhőbe során egy felhő-pillanatfelvételt IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Helyi tárterület-használat
Diagramokat jelenít meg az eszköz, amely több, mint az elsődleges tárhelyhasználatot lesz, mert ez magában foglalja a lineáris SSD-réteghez a teljes kihasználtság. A réteg tartalmaz egy adatmennyiséget is megtalálható az eszközhöz tartozó más szintekre. A lineáris SSD-réteg kapacitása nem szakad meg, így ha új adatok, a régi adatok áthelyezése a HDD-réteg (amikor deduplikált és tömörített), és ezt követően a felhő.

Idő, elsődleges tárolási keresztül használt és helyi tárhelyet valószínűleg megnöveli a együtt mindaddig, amíg az adatok elkezdi helyezhető el a felhőbe. Ezen a ponton használja a helyi tároló tetőzni valószínűleg megkezdődik, de a használt elsődleges tároló növeli, mivel a rendszer több adatot ír.

A következő diagram megjelenítése a StorSimple eszköz használhatók, ha egy felhő-pillanatfelvételt készült elsődleges tárolására. A felhő-pillanatfelvételt: 00:50 óra, és a helyi tároló ekkor csökken. A helyi tárhelyet csökkent 1.445 GB 1.09 GB. Ez azt jelzi, hogy nagy valószínűséggel a lineáris SSD-réteget a tömörítetlen adatokhoz lett deduplikált, tömörített, helyezi át a HDD-réteget. Vegye figyelembe, hogy az eszköz már egy nagy mennyiségű adatot a rétegek az SSD és HDD, nem jelenhet meg ezen csökken. Ebben a példában az eszköz regisztrációját kisebb mennyiségű adatot.

![Felhő pillanatképet készít a helyi tárhely kihasználtságát](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Teljesítmény
**Teljesítmény** nyomon követi az olvasások száma kapcsolatos metrikákat, és írási műveletek között, vagy az iSCSI-kezdeményező felületek a gazdagép-kiszolgálón és az eszköz vagy az eszközön, és a felhőben. A teljesítmény mérhető egy adott köteten, egy adott kötettároló vagy az összes kötet tároló. Teljesítmény is CPU-használat és a hálózat átviteli sebességét, a különböző hálózati adaptereken, az eszközön.

### <a name="io-performance-for-initiator-to-device"></a>I/o-teljesítmény kezdeményező eszközre
Az alábbi táblázatban látható az i/o-a kezdeményező éles eszköz a kötetek az eszközre. A metrikák ábrázolt vannak írási és olvasási bájt / mp. Is diagram olvasási, írási és függőben lévő I/O vagy olvasási és írási késések fordulnak elő.

![IO-teljesítmény kezdeményező eszközre](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Felhőbe eszköz i/o-teljesítmény
Az ugyanarra az eszközre az i/o-műveletek ábrázolási az adatok az eszközről a felhőbe a kötet tárolók. Ezen az eszközön csak a lineáris rétegében, illetve semmi nem rendelkezik kiömlött a felhőbe. Nincsenek az eszközről a felhőbe előforduló olvasási és írási műveletek. Emiatt a diagramon csúcsait egy 5 perces időközönként, amely megfelel a gyakoriság, amellyel a szívverés jelölőnégyzet be van jelölve az eszköz és a szolgáltatás között lehet.

Az ugyanarra az eszközre egy felhőalapú során létrehozott pillanatképen: 00:50 óra kezdő tartozó. Ennek következtében az eszközről a felhőbe áramló adatokat. Írás ennek az időtartamnak a volt a felhőben szolgáltatott. IO ábrázolja a legmagasabb írási bájt/s az idő, amikor során létrehozott pillanatképen megfelelő.

![Az eszközről a felhőbe során egy felhő-pillanatfelvételt IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Az eszköz hálózati illesztők tartozó hálózati teljesítmény
**Hálózati teljesítmény** nyomon követi a gazdagép-kiszolgálón és az eszköz és az eszköz és a felhő között az iSCSI-kezdeményező hálózati interfész átvitt adatok mennyisége kapcsolatos metrikákat. Ez a metrika az egyes iSCSI hálózati adaptereihez figyelheti az eszközön.

A következő diagram megjelenítése a hálózati átviteli sebesség a Data 0, 1 1 GbE hálózati az eszközön, amely mindkét felhő engedélyezve (alapértelmezett) és az iSCSI-kompatibilis. Az eszközön. június 14-körülbelül 9 du. adatok kiterjeszti a felhőbe lett rétegzett (a felhő pillanatképek nem adott időpontban, amely alatt a mechanizmust, amely kiterjeszti a felhőbe helyezze át az adatokat rétegezéséhez mutat vették) amely a felhőbe kiszolgált IO eredményezett. Nincs megfelelő csúcs a hálózati átviteli kijelző egy időben, a hálózati forgalom nagy részét pedig kimenő a felhőbe.

![A Data 0 hálózati teljesítményt](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Ha úgy tekintünk, az adatok 1 felület átviteli diagram, egy másik 1 GbE hálózati adapter, amely volt az iSCSI-kompatibilis csak, majd ennek az időtartamnak a hálózati forgalom gyakorlatilag történt.

![Az adatok 1 tartozó hálózati teljesítmény](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Az eszköz CPU-felhasználás
**CPU-felhasználás** nyomon követi a CPU-használata az eszközön kapcsolatos metrikákat. A következő diagram egy eszköz számára a Processzor kihasználtsága statisztikák éles környezetben jeleníti meg.

![Az eszköz CPU-felhasználás](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple Device Manager szolgáltatás eszközök irányítópult](storsimple-device-dashboard.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-manager-service-administration.md).

