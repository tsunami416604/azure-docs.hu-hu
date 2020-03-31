---
title: StorSimple helyileg rögzített kötetek GYAKORI KÉRDÉSEK| Microsoft dokumentumok
description: Válaszokat a storSimple helyileg rögzített kötetekkel kapcsolatos gyakori kérdésekre.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: aa69d8b07d31b5cf0386e34c113475cbf4191891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319547"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple helyileg rögzített kötetek: gyakori kérdések (GYAKORI KÉRDÉSEK)
## <a name="overview"></a>Áttekintés
Az alábbiakban olyan kérdéseket és válaszokat, amelyek előfordulhat, hogy egy StorSimple helyileg rögzített kötet létrehozásakor, konvertálni egy rétegzett kötetet egy helyileg rögzített kötet (és fordítva), vagy biztonsági másolatot készíteni, és visszaállítani egy helyileg rögzített kötet.

A kérdések és válaszok a következő kategóriákba sorolhatók:

* Helyileg rögzített kötet létrehozása
* Helyileg rögzített biztonsági mentés
* Rétegzett kötet átalakítása helyileg rögzített kötetté
* Helyileg rögzített kötet visszaállítása
* Helyileg rögzített kötet feleslege

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Kérdések a helyileg rögzített kötetek létrehozásával kapcsolatban
**K.** Mekkora a helyileg rögzített kötetek maximális mérete, amelyet a 8000-es sorozatú eszközökön létrehozhatok?

**A** A StorSimple 8000-es sorozat 3.0-s frissítése taszthatói valama, és akár 8,5 TB-os vagy rétegzett köteteket is kiépíthet a 8100-as eszközön. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki.

**K.** Nemrég frissítettem a 8100-as eszközömet a 3.0 frissítésre, és amikor megpróbálok helyileg rögzített kötetet létrehozni, a maximális rendelkezésre álló méret csak 6 TB és nem 8,5 TB. Miért nem tudok 8,5 TB-os kötetet létrehozni?

**A** Ha az eszköz 3.0-s frissítése fut, a 8100-as eszközön akár 8,5 TB vagy rétegzett kötetek is üzembe helyezhető helyileg rögzítetteköteteket. Ha az eszköz már rendelkezik rétegzett kötetekkel, akkor a helyileg rögzített kötetek létrehozásához rendelkezésre álló terület arányosan alacsonyabb lesz, mint ez a maximális korlát. Ha például a 8100-as eszközön (amely a rétegzett kapacitás fele) már körülbelül 106 TB rétegzett kötetet létesített, akkor a 8100-as eszközön létrehozható helyi kötet maximális mérete ennek megfelelően 4 TB-ra csökken (nagyjából 4 TB-ra csökken a maximális helyileg rögzített térfogatkapacitás fele).

Mivel az eszközön lévő helyi terület egy része a rétegzett kötetek munkakészletének tárolására szolgál, a helyileg rögzített kötetek létrehozásához rendelkezésre álló terület csökken, ha az eszköz rétegzett kötetekkel rendelkezik. Ezzel szemben egy helyileg rögzített kötet létrehozása arányosan csökkenti a rétegzett kötetek számára rendelkezésre álló területet. Az alábbi táblázatok a 8100-as és 8600-as eszközökön rendelkezésre álló rétegzett kapacitást foglalja össze a helyileg rögzített kötetek létrehozásakor.

#### <a name="update-30"></a>3.0-s frissítés 

| Helyileg rögzített kötetek kiosztott kapacitás | Lépcsőzetes kötetek számára kiépíthető rendelkezésre álló kapacitás – 8100 | Többszintű kötetek számára kiépíthető rendelkezésre álló kapacitás – 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |NA |277,8 TB |
| 15 TB |NA |166,7 TB |
| 22,5 TB |NA |0 TB |

**K.** Miért van a helyileg rögzített kötet létrehozása egy hosszú ideig futó művelet?

**A.** A helyileg rögzített kötetek vastagon vannak kiépítve. Ha helyet szeretne létrehozni az eszköz helyi szintjein, előfordulhat, hogy a meglévő rétegzett kötetek egyes adatait a kiépítési folyamat során a felhőbe kell lelökni. És mivel ez a kiépített kötet méretétől, az eszközön lévő meglévő adatoktól és a felhőhöz rendelkezésre álló sávszélességtől függ, a helyi kötet létrehozásához szükséges idő több óra is lehet.

**K.** Mennyi ideig tart egy helyileg rögzített kötet létrehozása?

**A.** Mivel a helyileg rögzített kötetek vastagon vannak kiépítve, előfordulhat, hogy a rétegzett kötetek egyes meglévő adatait a kiépítési folyamat során a felhőbe kell lelökni. Ezért a helyileg rögzített kötet létrehozásához szükséges idő több tényezőtől függ, beleértve a kötet méretét, az eszközön lévő adatokat és a rendelkezésre álló sávszélességet. Egy frissen telepített eszközön, amely nem rendelkezik kötetekkel, a helyileg rögzített kötet létrehozásához szükséges idő körülbelül 10 perc terabájtnyi adatonként. A helyi kötetek létrehozása azonban több órát is igénybe vehet a fent ismertetett tényezők alapján egy használatban lévő eszközön.

**K.** Helyileg rögzített kötetet szeretnék létrehozni. Van-e olyan bevált gyakorlat, amelyet tudnom kell?

**A.** A helyileg rögzített kötetek olyan számítási feladatokhoz alkalmasak, amelyek mindig helyi adatgaranciát igényelnek, és érzékenyek a felhőbeli késésekre. A helyi kötetek használata során kérjük, vegye figyelembe a következőket:

* A helyileg rögzített kötetek vastagon vannak kiépítve, és a helyi kötetek létrehozása hatással van a rétegzett kötetek rendelkezésre álló területére. Ezért azt javasoljuk, hogy kezdje a kisebb méretű kötetek és a méretezési a tárolási igény növekedésével.
* A helyi kötetek kiépítése egy hosszú ideig futó művelet, amely magában foglalhatja a meglévő adatok lelökése a rétegzett kötetek a felhőbe. Ennek eredményeképpen ezeken a köteteken csökkenhet a teljesítmény.
* A helyi kötetek kiépítése időigényes művelet. A tényleges idő több tényezőtől függ: a kiosztott kötet méretétől, az eszközön lévő adatoktól és a rendelkezésre álló sávszélességtől. Ha nem tett biztonsági másolatot a meglévő kötetekről a felhőbe, akkor a kötetek létrehozása lassabb. Azt javasoljuk, hogy a meglévő kötetek felhőbeli pillanatképeket a meglévő kötetek kiépítése előtt.
* A meglévő rétegzett köteteket helyileg rögzített kötetekké alakíthatja át, és ez az átalakítás magában foglalja a hely kiépítését az eszközön az eredményül kapott helyileg rögzített kötet számára (a rétegzett adatok lebontása mellett, ha vannak ilyenek, a felhőből). Ismét, ez egy hosszú ideig futó művelet, amely attól függ, hogy a fent tárgyalt tényezők. Javasoljuk, hogy a konvertálás előtt készüljön el a meglévő kötetekről, mivel a folyamat még lassabb lesz, ha a meglévő kötetekről nem készül biztonsági másolatot. Előfordulhat, hogy az eszköz teljesítménye is csökken a folyamat során.

További információ a [helyileg rögzített kötetek létrehozásáról](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**K.** Létrehozhatok egyszerre több helyileg rögzített kötetet?

**A.** Igen, de minden helyileg rögzített kötet-létrehozási és bővítési feladatok feldolgozása egymás után történik.

A helyileg rögzített kötetek vastagon vannak kiépítve, és ehhez helyi terület létrehozása szükséges az eszközön (ami azt eredményezheti, hogy a rétegzett kötetek meglévő adatait a kiépítési folyamat során a felhőbe kell lelökni). Ezért ha egy kiépítési feladat folyamatban van, a többi helyi kötet-létrehozási feladat a feladat befejezéséig várakozik.

Hasonlóképpen, ha egy meglévő helyi kötet ki van bontva, vagy egy rétegzett kötet et konvertál egy helyileg rögzített kötetre, akkor egy új helyileg rögzített kötet létrehozása a várólistára kerül, amíg az előző feladat befejeződik. A helyileg rögzített kötet méretének növelése magában foglalja az adott kötet meglévő helyi területének bővítését. A rétegzett kötetről a helyileg rögzített kötetre való átalakítás magában foglalja a helyi legrögzített kötet helyi területének létrehozását is. Mindkét műveletben a helyi terület létrehozása vagy bővítése hosszú ideig futó feladat.

Ezeket a feladatokat a StorSimple Eszközkezelő szolgáltatás **Feladatok** panelen tekintheti meg. A folyamatban lévő feladat folyamatosan frissül, hogy tükrözze a területkiépítése előrehaladását. A fennmaradó helyileg rögzített kötetfeladatok futóként vannak megjelölve, de a folyamatuk leállt, és a várólistára helyezett sorrendben lesz nekik.

**K.** Töröltem egy helyileg rögzített kötetet. Miért nem jelenik meg a visszanyert terület a rendelkezésre álló helyen, amikor új kötetet próbálok létrehozni?

**A.** Ha helyileg rögzített kötetet töröl, előfordulhat, hogy az új kötetek számára rendelkezésre álló terület nem frissül azonnal. A StorSimple Eszközkezelő szolgáltatás óránként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy várjon egy órát, mielőtt megpróbállétrehozni az új kötetet.

**K.** A helyileg rögzített kötetek támogatottak a felhőalapú készüléken?

**A.** A helyileg rögzített kötetek nem támogatottak a felhőalapú készüléken (8010 és 8020 eszközök, amelyeket korábban StorSimple virtuális eszköznek neveztek).

**K.** Használhatom az Azure PowerShell-parancsmagokat a helyileg rögzített kötetek létrehozásához és kezeléséhez?

**A.** Nem, nem hozhat létre helyileg rögzített köteteket az Azure PowerShell-parancsmagokon keresztül (az Azure PowerShell-en keresztül létrehozott kötetek rétegzettek). Azt is javasoljuk, hogy ne használja az Azure PowerShell-parancsmagokat a helyileg rögzített kötet ek tulajdonságainak módosítására, mivel nem kívánt hatással lesz a kötettípus rétegzettre történő módosításához.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Kérdések a helyileg rögzített kötetek biztonsági mentésével kapcsolatban
**K.** A helyileg rögzített kötetek helyi pillanatképei támogatottak?

**A.** Igen, helyi pillanatképeket készíthet a helyileg rögzített kötetekről. Azt javasoljuk azonban, hogy rendszeresen biztonsági másolatot készít a helyileg rögzített kötetekről felhőbeli pillanatképekkel annak érdekében, hogy az adatok biztonságban legyenek a katasztrófa esetén.

Vegye figyelembe, hogy a helyileg rögzített kötetek helyi pillanatképei is rétegezheti ki a felhőbe, és nem garantált, hogy az eszköz helyi rétegében marad.

**K.** Vannak-e irányelvek a helyileg rögzített kötetek helyi pillanatképeinek kezeléséhez?

**A.** A helyileg rögzített köteten az adatok nagy arányú gyakori helyi pillanatképei az eszköz helyi tárhelyének gyors felhasználását eredményezhetik, és a rétegzett kötetek adatainak a felhőbe való leküldése esetén előfordulhat, hogy a rendszer lenyomja a helyi adatokat. Ezért azt javasoljuk, hogy minimalizálja a helyi pillanatképek számát.

**K.** Riasztást kaptam arról, hogy a helyileg rögzített kötetek helyi pillanatképei érvénytelenné válhatnak. Mikor történhet meg ez?

**A.** A helyileg rögzített köteten az adatok nagy arányú nagy arányú gyakori helyi pillanatképei az eszköz helyi tárhelyének gyors felhasználását eredményezhetik. Ha az eszköz helyi szintjeit erősen használják, egy kiterjesztett felhőkimaradás az eszköz megteltek, és a kötetre érkező írások a pillanatképek érvénytelenítéséhez vezethetnek (mivel nincs hely a pillanatképek frissítéséhez, hogy a régebbi blokkokra hivatkozzon felülírt adatok). Ilyen esetben a kötetre történő írások továbbra is kilesznek szolgálva, de a helyi pillanatképek érvénytelenek lehetnek. Nincs hatással a meglévő felhőbeli pillanatképek.

A figyelmeztetés az, hogy értesíti Önt, hogy ilyen helyzet merülhet fel, és győződjön meg róla, hogy ugyanazt időben kezelni, vagy tekintse át a helyi pillanatképek ütemezése, hogy a kevésbé gyakori helyi pillanatképek, vagy a régebbi helyi pillanatképek törlése, amelyek már nem szükséges.

Ha a helyi pillanatképek érvénytelenítve vannak, egy információs riasztást kap, amely értesíti, hogy az adott biztonsági mentési szabályzat helyi pillanatképei érvénytelenültek az érvénytelenített helyi pillanatképek időbélyegeinek listája mellett. Ezek a pillanatképek automatikusan törlődnek, és a továbbiakban nem fogja tudni megtekinteni őket az Azure Portal **biztonsági másolatai** panelen.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Kérdések a rétegzett kötetek helyileg rögzített kötetté történő átalakításával kapcsolatban
**K.** Némi lassúságot észlelek az eszközön, miközben egy rétegzett kötetet helyileg rögzített kötetté alakítok át. Miért történik ez?

**A.** Az átalakítási folyamat két lépésből áll:

1. Hely kiépítése az eszközön a hamarosan konvertálni fogandó helyileg rögzített kötet számára.
2. A rétegzett adatok letöltése a felhőből a helyi garanciák biztosítása érdekében.

Mindkét lépés hosszú ideig futó művelet, amely a konvertált kötet méretétől, az eszközön lévő adatoktól és a rendelkezésre álló sávszélességtől függ. Mivel a meglévő rétegzett kötetek egyes adatai a kiépítési folyamat részeként kiömlehetnek a felhőbe, az eszköz teljesítménye csökkenhet ez idő alatt. Ezenkívül az átalakítási folyamat lassabb lehet, ha:

* A meglévő kötetekről nem lett biztonsági másolatom a felhőbe; ezért javasoljuk, hogy a konvertálás megkezdése előtt készítsen biztonsági másolatot a kötetekről.
* Sávszélesség-szabályozási házirendek lettek alkalmazva, amelyek korlátozhatják a rendelkezésre álló sávszélességet a felhőre; ezért javasoljuk, hogy van egy dedikált 40 Mbps vagy több kapcsolatot a felhő.
* Az átalakítási folyamat több órát is igénybe vehet a fent ismertetett többszörös tényezők miatt; ezért azt javasoljuk, hogy ezt a műveletet nem csúcsidőszakokban vagy hétvégén hajtsa végre, hogy elkerülje a végfelhasználókra gyakorolt hatást.

További információ [arétegzett kötetek helyileg rögzített kötetté történő konvertálásáról](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**K.** Megszakíthatom a kötetkonverziós műveletet?

**A.** Nem, a konverziós művelet et nem szakíthatja meg a kezdeményezésután. Az előző kérdésben tárgyaltak szerint kérjük, vegye figyelembe a folyamat során esetlegesen felmerülő teljesítményproblémákat, és kövesse a fent felsorolt ajánlott eljárásokat a konverzió megtervezésekor.

**K.** Mi történik a kötetemmel, ha az átalakítási művelet sikertelen?

**A.** A kötetkonvertálás sikertelen lehet a felhőbeli kapcsolódási problémák miatt. Az eszköz végül leállíthatja az átalakítási folyamatot, miután egy sor sikertelen kísérlet a rétegzett adatok leállítására a felhőből. Ilyen esetben a kötettípus a konvertálás előtt továbbra is a forráskötet típusa marad, és:

* A rendszer kritikus riasztást küld, amely értesíti a kötetkonverziós hibáról. További információ a [helyileg rögzített kötetekkel kapcsolatos riasztásokról](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Ha egy rétegzett egy helyileg rögzített kötetre konvertál, a kötet továbbra is egy rétegzett kötet tulajdonságait mutatja, mivel az adatok továbbra is előfordulhat, hogy a felhőben találhatók. Javasoljuk, hogy oldja meg a kapcsolódási problémákat, majd próbálkozzon újra az átalakítási művelettel.
* Hasonlóképpen, ha egy rétegzett kötetre helyileg rögzített átalakítás sikertelen, bár a kötet helyileg rögzített kötetként lesz megjelölve, rétegzett kötetként fog működni (mivel az adatok a felhőbe kerülhettek). Azonban továbbra is helyet foglalnak az eszköz helyi szintjein. Ez a hely nem lesz elérhető más helyileg rögzített kötetek esetén. Azt javasoljuk, hogy próbálja meg újra ezt a műveletet annak érdekében, hogy a kötet konvertálása befejeződött, és a helyi terület az eszközön visszanyerhető.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Kérdések a helyileg rögzített kötet visszaállításáról
**K.** A helyileg rögzített kötetek azonnal helyreállnak?

**A.** Igen, a helyileg rögzített kötetek azonnal visszaállnak. Amint a kötet metaadat-információi a visszaállítási művelet részeként lekérik a felhőből, a kötet online állapotba kerül, és a gazdagép hozzáférhet. A kötetadatok helyi garanciái azonban nem lesznek jelen, amíg az összes adatot le nem töltötte a felhőből, és a visszaállítás időtartama alatt csökkentett teljesítményt tapasztalhat ezeken a köteteken.

**K.** Mennyi ideig tart a helyileg rögzített kötet visszaállítása?

**A.** A helyileg rögzített kötetek azonnal visszaállnak, és online állapotba kerülnek, amint a kötet metaadat-információi lekérésre kerülnek a felhőből, miközben a kötetadatok továbbra is a háttérben töltődnek le. Ez utóbbi része a visszaállítási műveletnek – a kötetadatok helyi garanciáinak visszaszerzése – hosszú ideig futó művelet, és több órát is igénybe vehet, amíg az összes adat ismét helyivé lesz téve. Az azonos befejezéshez szükséges idő több tényezőtől függ, például a visszaállítandó kötet méretétől és a rendelkezésre álló sávszélességtől. Ha a visszaállítás alatt álló eredeti kötetet törölték, további idő szükséges a helyi terület létrehozásához az eszközön a visszaállítási művelet részeként.

**K.** Vissza kell állítanom a meglévő helyileg rögzített kötetet egy régebbi pillanatképre (a kötet rétegzett állapotában). Ebben az esetben a kötet rétegzettként lesz visszaállítva?

**A.** Nem, a kötet helyileg rögzített kötetként lesz visszaállítva. Bár a pillanatkép a kötet rétegzett időpontjára nyúlik vissza, miközben a meglévő kötetek visszaállítása, a StorSimple mindig a lemezen lévő kötet típusát használja, ahogy jelenleg létezik.

**K.** Nemrég kiterjesztettem a helyileg rögzített kötetemet, de most vissza kell állítanom az adatokat egy olyan időpontra, amikor a kötet kisebb méretű volt. Visszaállítja az aktuális kötet átméretezését, és a visszaállítás befejezése után ki kell terjesztenia a kötet méretét?

**A.** Igen, a visszaállítás átméretezi a kötetet, és a visszaállítás befejezése után ki kell terjesztenie a kötet méretét.

**K.** Módosíthatom a kötet típusát a visszaállítás során?

**A.** Nem, a kötet típusát a visszaállítás során nem módosíthatja.

* A törölt kötetek a pillanatképben tárolt típusként lesznek visszaállítva.
* A meglévő kötetek visszaállítása az aktuális típusuk alapján történik, függetlenül a pillanatképben tárolt típustól (lásd az előző két kérdést).

**K.** Vissza kell állítanom a helyileg rögzített kötetet, de helytelen időpont-pillanatképet választottam. Megszakíthatom az aktuális visszaállítási műveletet?

**A.** Igen, megszakíthat egy folyamatos visszaállítási műveletet. A kötet állapota a visszaállítás kezdetén visszaáll az állapotra. Azonban minden olyan írás, amely a köteten, miközben a visszaállítás folyamatban volt, el vész.

**K.** Elindítottam egy visszaállítási műveletet az egyik helyileg rögzített köteten, és most megjelenik egy pillanatkép a lemaradási katalógusomban, amelynem emlékszem a létrehozásra. Mire használják ezt?

**A.** Ez az ideiglenes pillanatkép, amely a visszaállítási művelet előtt jön létre, és visszaállításra szolgál, ha a visszaállítás megszakad, vagy sikertelen. Ne törölje ezt a pillanatképet; a visszaállítás befejezésekor automatikusan törlődik. Ez a viselkedés akkor fordulhat elő, ha a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és rétegzett kötetek vegyesen rendelkezik. Ha a visszaállítási feladat csak rétegzett köteteket tartalmaz, akkor ez a viselkedés nem fordul elő.

**K.** Klónozhatok helyileg rögzített kötetet?

**A.** Igen. A helyileg rögzített kötet azonban alapértelmezés szerint rétegzett kötetként lesz klónozva. További információ a [helyileg rögzített kötetek klónozásáról](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Kérdések a helyileg rögzített kötetek felborulásáról
**K.** Át kell adnom az eszközömet egy másik fizikai eszköznek. A helyileg rögzített kötetek helyileg rögzítettként vagy rétegzettként lesznek megdöntve?

**A.** A helyileg rögzített kötetek a helyileg rögzített ként feladatátvételre kerülnek, ha a céleszköz a StorSimple 8000 sorozat 3-as vagy újabb frissítését futtatja.

További információ a [helyileg rögzített kötetek feladatátvételéről és dr-jéről a verziók között](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**K.** A helyileg rögzített kötetek azonnal helyreállnak a vész-helyreállítási (DR) során?

**A.** Igen, a helyileg rögzített kötetek azonnal visszaállnak a feladatátvétel során. Amint a kötet metaadat-információi a feladatátvételi művelet részeként lekérik a felhőből, a kötet online állapotba kerül a céleszközön, és a gazdagép hozzáférhet. Eközben a kötetadatok továbbra is a háttérben töltődnek le, és ezeken a köteteken a feladatátvétel időtartama alatt csökkentett teljesítményt tapasztalhat.

**K.** Látom a feladatátvételi feladat befejeződött, hogyan követhető a helyileg rögzített kötet, amely a céleszközön van visszaállítva?

**A.** A feladatátvételi művelet során a feladatátvételi feladat akkor lesz készként megjelölve, ha a feladatátvételi készlet összes kötete azonnal helyreállt, és online állapotba került a céleszközön. Ez magában foglalja a helyileg rögzített köteteket, amelyek lehet, hogy feladatátvételt; az adatok helyi garanciái azonban csak akkor lesznek elérhetők, ha a kötet összes adatát letöltötték. Ezt a folyamatot nyomon követheti minden helyileg rögzített köteten, amely feladatátvételt kapott a feladatátvétel részeként létrehozott megfelelő visszaállítási feladatok figyelésével. Ezek az egyéni visszaállítási feladatok csak helyileg rögzített kötetekhez jönnek létre.

**K.** Módosíthatom a kötet típusát a feladatátvétel során?

**A.** Nem, a kötet típusát nem módosíthatja feladatátvétel közben. Ha egy másik fizikai eszközre, amely a StorSimple 8000 sorozat 3-as frissítése 3, a kötetek feladatátvétele a pillanatképben tárolt kötet típusa alapján.

**K.** Átvehetem a feladatátvételt egy kötettárolóhelyileg rögzített kötetekkel a felhőalapú készülékre?

**A.** Igen. A helyileg rögzített kötetek felzett kötetek lesznek felzett kötetek. További információ a [helyileg rögzített kötetek feladatátvételéről és dr-jéről a verziók között](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

