---
title: A StorSimple 8000 sorozatú eszköz figyelése |} A Microsoft Docs
description: A StorSimple-Eszközkezelő szolgáltatás használatát a használatot, az i/o-teljesítmény és a tárolókapacitás kihasználtságát ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634562"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>A StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz figyelése

## <a name="overview"></a>Áttekintés
A StorSimple-Eszközkezelő szolgáltatás segítségével a StorSimple-megoldásokra belül meghatározott eszközök figyelését. Hozzon létre egyéni diagramokat i/o teljesítmény, tárolókapacitás kihasználtságát, hálózati átviteli sebesség és teljesítmény-mérőszámok eszköz alapján, és rögzítheti az irányítópulton. További információért ugorjon [testre szabhatja a portál irányítópultján](../azure-portal/azure-portal-dashboards.md).

Az Azure Portalon egy adott eszközhöz tartozó a figyelési adatok megtekintéséhez válassza ki a StorSimple-Eszközkezelő szolgáltatásban. Eszközök listájából válassza ki az eszközt, és folytassa a **figyelő**. Ezután megjelenik a **kapacitás**, **használati**, és **teljesítmény** diagramokon a kijelölt eszközre vonatkozóan.

## <a name="capacity"></a>Kapacitás
**Kapacitás** nyomon követi a kiosztott lemezterület és a hely az eszközön van. A fennmaradó kapacitás majd a helyileg rögzített vagy rétegzett jelenik meg.

A kiépített és a fennmaradó kapacitás rétegzett és a helyileg rögzített kötetek további oszlanak meg. Az egyes kötetek a kiosztott kapacitást, és a fennmaradó kapacitás az eszközön jelenik meg.

![IO-kapacitás](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Használat
**Használati** nyomon követi a kötetek, kötettárolók, vagy az eszköz által használt adatok tárolási terület mennyisége kapcsolatos metrikákat. A tárolókapacitás kihasználtságát az elsődleges tárolási, a felhőalapú tárolás vagy az eszköz tárhelyén alapuló jelentéseket hozhat létre. Kapacitás kihasználtsága egy adott kötet, egy adott kötettároló vagy minden kötettároló mérhető.
Alapértelmezés szerint a használat az elmúlt 24 órában jelentett. Módosítsa az időtartamot, amelyen a használati jelentett, válassza ki a diagram szerkesztheti:
* Az elmúlt 24 óra
* Elmúlt 7 nap
* Elmúlt 30 nap
* Elmúlt 90 nap
* Az elmúlt év

Két fő metrices, a növekedési és a tartomány jelenti, hogy a használat diagramok. Hivatkozik a maximális érték és a használat, a kijelölt időintervallumban (fő példány, elmúlt 7 napon) keresztül jelentett minimális értékét.

Növekedési használat növelése az első nap az utolsó napjáig keresztül a kijelölt időintervallumban hivatkozik. 

Növekedés és a tartomány a következő egyenletek is ábrázolható:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Az elsődleges, felhőalapú és a felhasznált helyi tár leírása a következő lehet:

### <a name="primary-storage-usage"></a>Elsődleges tárhely kihasználtsága
Ezek a diagramok megjelenítése előtt az adatok tömörítve és deduplikált kötetek a StorSimple írt adatok mennyisége. Megtekintheti az összes kötet kötettároló vagy egyetlen kötetén által használt elsődleges tároló. Használt elsődleges tároló továbbá által használt elsődleges rétegzett tárhely lebontva, és az elsődleges gyors helyi tár használatban.

A következő diagramon megjelenik egy StorSimple-eszköz előtt és után egy felhőbeli pillanatkép használt elsődleges tároló. Mivel ez csak a mennyiségi adatok, egy felhőbeli pillanatkép nem kell módosítani az elsődleges tárolási. Amint láthatja, a diagram mutatja eredményeként felhőbeli pillanatkép használt elsődleges rétegzett vagy helyileg rögzített tároló nincs különbség. A felhőbeli pillanatképet kezdtem körülbelül 11:50 pm, hogy az eszközről.

![Felhőbeli pillanatkép utáni elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Ha a gazdagép csatlakozik a StorSimple-eszköz most már futtatása IO, látni fogja az elsődleges rétegzett tárhely növekedése vagy elsődleges gyors helyi tár használatban attól függően (vagy a helyileg rögzített rétegzett) kötetek esetén akkor írja az adatokat. Az alábbiakban az elsődleges tárolási használati diagramok egy StorSimple-eszköz. Az eszközön a lépések a kiszolgáló StorSimple gazdagép, az írási műveleteknél körülbelül 2:30 = 1997031213 a rétegzett kötetek az eszközön. Láthatja, hogy az írási bájt/s-megfelelő a gazdagépen futó az i/o csúcsértéke.

![Amikor futó i/o-rétegzett köteteket teljesítmény](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Az elsődleges rétegzett tár használatban tekinti meg, ha, amely rendelkezik lekérnénk az összesítést, mivel az elsődleges gyors helyi használat változatlan marad, mert nincsenek rendereléséhez szükséges a helyileg rögzített kötetek az eszközön nincs írási.

![Elsődleges kapacitáskihasználás IO a rétegzett kötetek futtatásakor](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Ha futtatja 3-as frissítés vagy újabb verziója szükséges, akkor is felosztania az elsődleges tárolókapacitás kihasználtságát az egyedi kötet, minden kötet, az összes rétegzett kötetek és minden helyileg rögzített kötet alább látható módon. Minden helyileg rögzített kötetek érvénytelenítésével lehetővé teszi, gyorsan listájáért, mekkora a helyi rétegen használja.

![Az összes rétegzett kötet elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Az összes helyileg rögzített kötet elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Az egyes kötetek a listában kattintson a Tovább, és a megfelelő használati.

![Az összes helyileg rögzített kötet elsődleges kapacitáskihasználás](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Cloud storage használata
Ezek a diagramok megjelenítése használt felhőbeli tárhely. Ezek az adatok deduplikált, és a tömörített. Ez a mennyiség magában foglalja a felhőbeli pillanatképekkel nem tükröződnek bármelyik elsődleges kötet és célokra megőrzési örökölt vagy szükséges tárolt adatok tartalmazhatnak. Hasonlítsa össze az elsődleges és a storage fogyasztási adatokat, hogy képet kapjon a csökkentési sebessége, a felhőalapú, bár a szám nem lesz pontos.

A következő diagramok megjelenítése a a StorSimple eszköz cloud storage felhasználása, ha egy felhőbeli pillanatkép.

* A felhőbeli pillanatképet elindítva: körülbelül 11:50:00, hogy az eszközről, és láthatja, hogy a felhőbeli pillanatkép, mielőtt volt nem használt felhőbeli tárhelyhez. 
* Egyszer a felhőbeli pillanatképet befejeződött, a felhő tárterület kihasználtsága elejtett 0,89 GB fel. 
* Miközben folyamatban volt a felhőbeli pillanatképet, emellett van egy megfelelő csúcsértéke az eszközről a felhőbe az i/o.

    ![Felhőbeli pillanatkép előtti felhőalapú tárterület kihasználtsága](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Tárterület kihasználtsága felhőbeli pillanatképet készít a felhő](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Eszközről a felhőbe során felhőbeli pillanatkép IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Helyi tárhely kihasználtsága
Ezek a diagramok megjelenítése a teljes kihasználtság az eszközhöz, amely több, mint az elsődleges tárolási felhasználásának lesz, mert azokat az lineáris SSD-réteghez. A csomag tartalmaz egy is létezik a adatok mennyisége az eszköz a többi szint. A lineáris SSD-réteg kapacitása nem szakad meg, így amikor új adat érkezik, a régi helyez át adatokat a HDD-réteg (ekkor deduplikált és tömörített), és ezt követően a felhőben.

Idő, az elsődleges tárolási keresztül használt használt és a helyi tároló nagy valószínűséggel megnöveli a együtt mindaddig, amíg a felhőbe helyezhető elkezdi az adatok. Ezen a ponton a felhasznált helyi tár tetőzni valószínűleg megkezdődik, de a használt elsődleges tároló megnő, a rendszer több adatot ír.

A következő diagramon megjelenik az elsődleges tárolási használhatók a StorSimple eszköz abban az esetben, ha egy felhőbeli pillanatkép. A felhőbeli pillanatkép: 00:50 óra, és a helyi tároló ekkor csökken. Felhasznált helyi tár csökkent 1.445 GB-tól 1.09 GB. Ez jelzi, hogy nagy valószínűséggel a tömörítetlen adatok a lineáris SSD-réteg lett deduplikált, tömörített, és a HDD-réteget helyezve. Vegye figyelembe, hogy ha az eszköz már rendelkezik egy nagy mennyiségű adatot az SSD és HDD-szinten, nem jelenhet meg ez csökkenése. Ebben a példában az eszköz rendelkezik egy kisebb mennyiségű adatot.

![Felhőbeli pillanatképet készít a helyi tárterület kihasználtsága](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Teljesítmény
**Teljesítmény** nyomon követi az olvasások száma kapcsolatos metrikákat, és az írási műveletek között, vagy az iSCSI-kezdeményező felületek a gazdagép-kiszolgálón és az eszköz vagy az eszköz és a felhőben. A teljesítmény mérhető egy meghatározott kötete, egy adott kötettároló vagy minden kötettároló. Teljesítményét a CPU-kihasználtság és hálózati átviteli sebesség az eszközön a különböző hálózati adaptereket is tartalmaz.

### <a name="io-performance-for-initiator-to-device"></a>Kezdeményező – eszköz i/o-teljesítmény
Az alábbi diagram bemutatja a kezdeményező – az eszköz egy éles eszköz a kötetek az i/o. A metrikák rajzolódik ki vannak írási és olvasási bájt / másodperc. Is olvasási, írási és a szálankénti függőben lévő i/o diagram vagy olvasási és írási késése.

![Kezdeményező – eszköz i/o-teljesítmény](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>A felhő-eszköz i/o-teljesítmény
Az ugyanazon az eszközön az i/o-műveletek az adatok az eszközről a felhőbe a kötettárolókat ábrázolási. Az eszközön az adatok csak a lineáris szinten van, és semmi nem rendelkezik kiömlött a felhőbe. Nincsenek az eszközről a felhőbe előforduló olvasási és írási műveletek. Emiatt a csúcsok, a diagram egy 5 perces időközönként, amely megfelel a gyakoriságot, amellyel a szívverés jelölőnégyzet be van jelölve az eszköz és a szolgáltatás között lehet.

Ugyanazon az eszközön, az egy felhőbeli pillanatkép mennyiségi adatok 11:50-kor kezdődik. Ennek következtében adatforgalmát, az eszközről a felhőbe. Írás a felhőbe ennek az időtartamnak a szolgálja ki a is. Az i/o-diagram az írási bájt/s az idő, amikor a pillanatkép megfelelő csúcsértéke mutatja meg.

![Eszközről a felhőbe során felhőbeli pillanatkép IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Eszköz hálózati adapterek a hálózati átviteli sebesség
**Hálózati átviteli sebesség** nyomon követi az iSCSI-kezdeményező hálózati interfész az a gazdagép és az eszköz és az eszköz és a felhő között továbbított adatok mennyisége kapcsolatos metrikákat. Ez a metrika az iSCSI-hálózati adapterek mindegyike figyelheti az eszközön.

A következő diagramok megjelenítése a hálózat átviteli sebessége az adatok 0, 1 1 GbE hálózati az eszközön, amely mindkét felhőalapú engedélyezett (alapértelmezett) és az iSCSI-kompatibilis. Az eszköz körülbelül 9-kor, június 14-én, adatokat a felhőbe volt rétegzett (nem felhőbeli pillanatképek származnak rétegezést folyamatban van az adatok áthelyezése a felhőbe a mechanizmust mutat, amely jelenleg) amely szolgál ki, a felhő IO eredményezett. A megfelelő csúcsot a hálózati átviteli sebesség Graph időben, és a hálózati forgalmat a legtöbb kimenő a felhőbe.

![Data 0 hálózati átviteli sebesség](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Ha megnézzük a Data 1 felület átviteli diagram, egy másik 1 GbE hálózati adapter, mely csak iSCSI-kompatibilis, majd szinte semmilyen hálózati forgalom az ezen időtartam történt.

![Hálózati átviteli sebesség Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Az eszköz CPU-kihasználtság
**CPU-kihasználtság** nyomon követi a Processzor, az eszközön felhasznált kapcsolatos metrikákat. Az alábbi ábra a Processzor kihasználtsága stats eszköz éles jeleníti meg.

![Az eszköz CPU-kihasználtság](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [használata a StorSimple-Eszközkezelő szolgáltatás irányítópultját](storsimple-device-dashboard.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-manager-service-administration.md).

