---
title: A StorSimple 8000 sorozatú eszköz figyelése
description: Ez a témakör azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás használatával hogyan figyelheti a használatot, az I/O-teljesítményt és a kapacitáskihasználtságot.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277061"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz figyeléséhez

## <a name="overview"></a>Áttekintés
A StorSimple Eszközkezelő szolgáltatás segítségével figyelheti a StorSimple-megoldás on belüli eszközök figyelésére. Egyéni diagramokat hozhat létre az I/O-teljesítmény, a kapacitáskihasználás, a hálózati átviteli és az eszközteljesítmény-mérőszámok alapján, és rögzítheti azokat az irányítópulton. További információért keresse [testre a portál irányítópultját.](../azure-portal/azure-portal-dashboards.md)

Egy adott eszköz figyelési adatainak megtekintéséhez az Azure Portalon válassza ki a StorSimple Eszközkezelő szolgáltatást. Az eszközök listájában válassza ki az eszközt, majd válassza a **Monitor**lehetőséget. Ezután megtekintheti a kijelölt eszköz **Kapacitás**, **Használat**és **Teljesítmény** diagramjait.

## <a name="capacity"></a>Kapacitás
**A kapacitás** nyomon követi a kiosztott területet és az eszközön fennmaradó helyet. A fennmaradó kapacitás ezután helyileg rögzített vagy rétegzett ként jelenik meg.

A kiosztott és a fennmaradó kapacitás tovább bontható rétegzett és helyileg rögzített kötetek. Minden kötetnél megjelenik a kiosztott kapacitás és az eszközön fennmaradó kapacitás.

![Io-kapacitás](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Használat
**A használat** a kötetek, kötettárolók vagy eszköz által használt adattárterület mennyiségével kapcsolatos mérőszámokat követi nyomon. Jelentéseket hozhat létre az elsődleges tároló, a felhőalapú tárhely vagy az eszköztároló kapacitáskihasználása alapján. A kapacitáskihasználtság mérhető egy adott köteten, egy adott kötettárolón vagy az összes kötettárolón.
Alapértelmezés szerint az elmúlt 24 óra használatát jelenti a rendszer. A diagram szerkesztésével módosíthatja a használat jelentésének időtartamát a következők kiválasztásával:
* Az elmúlt 24 órában
* Az elmúlt 7 napban
* Az elmúlt 30 napban
* Az elmúlt 90 napban
* Elmúlt év

A használati diagramokhoz két fő mérőszám, a növekedés és a tartomány szerepel. A tartomány a kiválasztott időtartam alatt jelentett használat maximális értékére és minimális értékére vonatkozik (fo példány, Elmúlt 7 nap).

A növekedés a használat nak az első naptól az utolsó napig a kiválasztott időtartamon keresztül idotartama növekedését jelöli. 

A növekedést és a tartományt a következő egyenletek is képviselhetik:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

A használt elsődleges, felhős és helyi tároló a következőképpen írható le:

### <a name="primary-storage-usage"></a>Elsődleges tárhelyhasználat
Ezek a diagramok a StorSimple kötetekre írt adatok mennyiségét mutatják az adatok deduplikálása és tömörítése előtt. Megtekintheti az elsődleges tároló által használt összes kötet egy kötet tárolóban, vagy egy kötet. A felhasznált elsődleges tároló tovább bontható a használt elsődleges rétegzett tároló és a helyileg rögzített elsődleges rögzített tároló.

Az alábbi diagramok a StorSimple-eszköz elsődleges tárolóját mutatják be a felhőbeli pillanatkép készítése előtt és után. Mivel ez csak kötetadatok, a felhőbeli pillanatkép nem módosíthatja az elsődleges tároló. Mint látható, a diagram nem mutat különbséget az elsődleges rétegzett vagy helyileg rögzített tároló, amelyet a felhőbeli pillanatkép készítése eredményeként használ. A felhőbeli pillanatkép 23:50 körül kezdődött az adott eszközön.

![Elsődleges kapacitáskihasználás felhőbeli pillanatkép után](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Ha most fut az IO a StorSimple-eszközhöz csatlakoztatott gazdagépen, az elsődleges rétegzett tárterület vagy az elsődleges helyileg rögzített tároló növekedését fogja látni attól függően, hogy mely kötetek (rétegzett vagy helyileg rögzített) az adatokat írja. Az alábbiakban a StorSimple-eszköz elsődleges tárolási használati diagramjait olvashatja. Ezen az eszközön a StorSimple-állomás 14:30 körül kezdte el kiszolgálni az írásokat egy rétegzett köteten az eszközön. Láthatja a csúcsot az írási bájtban/s-ban, amely megfelel az állomáson futó I/-nek.

![Teljesítmény rétegzett köteteken futó io esetén](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Ha megnézzük az elsődleges rétegzett tároló használt, amely felkerült, mivel az elsődleges helyileg rögzített használat változatlan marad, mivel nincsenek írások kiszolgált helyileg rögzített kötetek az eszközön.

![Elsődleges kapacitáskihasználtság, ha az IO rétegzett köteteken fut](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Ha a 3-as vagy újabb frissítést futtatja, az elsődleges tárolókapacitás-kihasználtságot egy adott kötet, az összes kötet, az összes rétegzett kötet és az összes helyileg rögzített kötet szerint bonthatja le. Az összes helyileg rögzített kötetek lebontása lehetővé teszi, hogy gyorsan megállapítsa, hogy a helyi szint mekkora része van használva.

![Elsődleges kapacitáskihasználtság az összes rétegzett kötethez](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Elsődleges kapacitáskihasználtság az összes helyileg rögzített kötethez](./media/storsimple-8000-monitor-device/monitor-usage4.png)

A listában szereplő kötetekre további kattintva megtekintheti a megfelelő használatot.

![Elsődleges kapacitáskihasználtság az összes helyileg rögzített kötethez](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Felhőalapú tárhely használat
Ezek a diagramok a felhasznált felhőalapú tárhely mennyiségét mutatják. Ezek az adatok deduplikált és tömörített. Ez az összeg felhőbeli pillanatképeket tartalmaz, amelyek olyan adatokat tartalmazhatnak, amelyek nem jelennek meg egyetlen elsődleges köteten sem, és örökölt vagy szükséges megőrzési célokra tárolják. Összehasonlíthatja az elsődleges és a felhőbeli tárolási felhasználási adatokat, hogy képet kapjon az adatcsökkentési arányról, bár a szám nem lesz pontos.

Az alábbi diagramok egy StorSimple-eszköz felhőalapú tárolási kihasználtságát mutatják be, amikor egy felhőbeli pillanatkép készült.

* A felhőbeli pillanatkép 11:50 körül kezdődött az adott eszközön, és láthatja, hogy a felhőbeli pillanatkép előtt nem használt felhőalapú tárhelyet. 
* Miután a felhőbeli pillanatkép befejeződött, a felhőbeli tárolási kihasználtság 0,89 GB-ra nőtt. 
* Amíg a felhőbeli pillanatkép folyamatban volt, az I/O-ban is van egy megfelelő csúcs az eszközről a felhőre.

    ![Felhőalapú tárolási kihasználtság a felhőpillanatkép előtt](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Felhőalapú tárolási kihasználtság a felhőbeli pillanatkép után](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Io-t az eszközről a felhőbe egy felhőbeli pillanatfelvétel során](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Helyi tárhelyhasználat
Ezek a diagramok az eszköz teljes kihasználtságát mutatják, amely több lesz, mint az elsődleges tárolási használat, mert tartalmazza az SSD lineáris szintet. Ez a szint olyan mennyiségű adatot tartalmaz, amely az eszköz más szintjein is megtalálható. Az SSD lineáris réteg kapacitása úgy van, hogy amikor új adatok érkeznek, a régi adatok a HDD-szintre kerülnek (ekkor a deduplikálás és a tömörítés) és ezt követően a felhőbe kerül.

Idővel az elsődleges használt és a használt helyi tároló valószínűleg együtt növekszik, amíg az adatok a felhőbe való rétegződése meg nem kezdődik. Ezen a ponton a használt helyi tároló valószínűleg elkezd fennsíkra, de a felhasznált elsődleges tároló növekedni fog, ahogy több adat van írva.

Az alábbi diagramok a StorSimple-eszköz elsődleges tárolóját mutatják be, amikor egy felhőbeli pillanatkép készült. A felhőbeli pillanatkép 11:50-kor kezdődött, és a helyi tárhely akkor kezdett csökkenni. A használt helyi tárhely 1,445 GB-ról 1,09 GB-ra csökkent. Ez azt jelzi, hogy a lineáris SSD-rétegtömörítetlen adatait valószínűleg deduplikálta, tömörítette és áthelyezték a HDD-szintre. Vegye figyelembe, hogy ha az eszköz már rendelkezik nagy mennyiségű adatot mind az SSD és a HDD-rétegek, előfordulhat, hogy nem látja ezt a csökkenést. Ebben a példában az eszköz kis mennyiségű adat.

![Helyi tárolási kihasználtság felhőbeli pillanatkép után](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Teljesítmény
**A teljesítmény** nyomon követi a gazdakiszolgálón lévő iSCSI-kezdeményező felületek és az eszköz, illetve az eszköz és a felhő közötti olvasási és írási műveletek számával kapcsolatos mérőszámokat. Ez a teljesítmény egy adott kötethez, egy adott kötettárolóhoz vagy az összes kötettárolóhoz mérhető. A teljesítmény magában foglalja a processzorkihasználtságot és a hálózati átviteli teljesítményt az eszközön lévő különböző hálózati interfészekhez.

### <a name="io-performance-for-initiator-to-device"></a>I/O-teljesítmény a kezdeményező és az eszköz számára
Az alábbi táblázat az eszköz kezdeményezőjének I/O-ját mutatja be az éles eszköz összes kötetéhez. A ábrázolt metrikák olvasási és írási bájt másodpercenként. Írhat, írhat és kiváló an/o-t is ábrázolhat, illetve késéseket is írhat.

![Io-teljesítmény a kezdeményező és az eszköz számára](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>I/O-teljesítmény az eszköz től a felhőig
Ugyanahhoz az eszközhöz az I/O-műveletek et az eszközről a felhőbe az összes kötettároló adatainak nyomtatása történik. Ezen az eszközön az adatok csak a lineáris szinten vannak, és semmi sem ömlött ki a felhőbe. Nincsenek írási és olvasási műveletek az eszközről a felhőbe. Ezért a diagram csúcsai 5 perces időközzel vannak elválasztva, amely megfelel annak a gyakoriságnak, amelyen a szívverést ellenőrzik az eszköz és a szolgáltatás között.

Ugyanahhoz az eszközhöz egy felhőbeli pillanatkép készült a 11:50-től kezdődő kötetadatokhoz. Ez azt eredményezte, hogy az adatok az eszközről a felhőbe áramlottak. Ebben az időtartamban a felhőbe kézbesítették az írásokat. Az IO-diagram az Írási bájtok/s csúcsát mutatja, amely megfelel a pillanatkép készítésének időpontjának.

![Io-t az eszközről a felhőbe egy felhőbeli pillanatfelvétel során](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Az eszköz hálózati interfészei hálózati átviteli fókusa
**A hálózati átviteli teljesítmény** a gazdakiszolgálón és az eszközön, valamint az eszköz és a felhő között az iSCSI-kezdeményező hálózati összeköttetésekből átvitt adatok mennyiségére vonatkozó mérőszámokat követi nyomon. Ezt a mérőszámot az eszközön lévő minden iSCSI-hálózati csatoló esetében figyelheti.

Az alábbi diagramok a Data 0, 1 1 GbE hálózat hálózati átviteli áteresztődését mutatják be az eszközön, amely felhőalapú (alapértelmezett) és iSCSI-kompatibilis is volt. Ezen az eszközön június 14-én, 21:00 körül az adatok rétegezve voltak a felhőbe (akkor nem készült felhőbeli pillanatkép, amely arra utal, hogy a rétegezés az adatok felhőbe való áthelyezésének mechanizmusa), ami azt eredményezte, hogy az IO a felhőbe került. Van egy megfelelő csúcs a hálózati átviteli gráf ugyanabban az időben, és a legtöbb hálózati forgalom kimenő a felhőbe.

![Hálózati átviteli -átmenő adat 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Ha megnézzük a Data 1 interfész átviteli diagram, egy másik 1 GbE hálózati interfész, amely csak iSCSI-kompatibilis, akkor gyakorlatilag nem volt hálózati forgalom ebben az időtartamban.

![1. adat hálózati átviteli forgalma](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Cpu-kihasználtság az eszközhöz
**A CPU-kihasználtság** az eszközön használt PROCESSZORhoz kapcsolódó mérőszámokat követi nyomon. A következő diagram az éles környezetben lévő eszköz PROCESSZOR-kihasználtsági statisztikáit mutatja be.

![Cpu-kihasználtság az eszközhöz](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>További lépések
* További információ [a StorSimple Device Manager szolgáltatáseszköz irányítópultjának használatáról.](storsimple-device-dashboard.md)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-manager-service-administration.md)

