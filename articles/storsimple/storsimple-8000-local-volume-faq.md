---
title: StorSimple helyileg rögzített kötetekről – gyakori kérdések |} A Microsoft Docs
description: A StorSimple a helyileg rögzített kötetek kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013789"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple helyileg rögzített kötetekről: gyakori kérdések (GYIK)
## <a name="overview"></a>Áttekintés
Az alábbiakban a kérdések és válaszok, lehetséges, hogy amikor, hozzon létre egy StorSimple helyileg rögzített kötetet, konvertálja a helyileg rögzített kötetet (és fordítva), a rétegzett kötetek vagy biztonsági mentése és visszaállítása egy helyileg rögzített kötet.

Kérdések és válaszok a következő kategóriákba vannak rendezve

* A gyors helyi kötet létrehozása
* Biztonsági mentése egy helyileg rögzített
* Egy helyileg rögzített kötet rétegzett kötet konvertálása
* Egy helyileg rögzített kötet visszaállítása
* Egy helyileg rögzített kötet feladatátadás

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet létrehozásával kapcsolatos kérdések
**K.** Mi az a maximális mérete egy helyileg rögzített kötet, amely képes vagyok létrehozni a 8000-es sorozatú eszközökön?

**A** StorSimple 8000 Series Update 3.0 rendszerű eszközökön helyezheti üzembe a helyileg rögzített kötetekhez legfeljebb 8,5 TB, rétegzett kötetekhez legfeljebb 200 TB a 8100-as eszközön. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki.

**K.** Nemrégiben frissített 8100-as eszközöm a Update 3.0 és jelenik meg a helyileg rögzített kötet létrehozásához, a maximális méret csak 6 TB, illetve nem 8,5 TB. Miért nem hozható létre egy 8,5 TB-os kötetet?

**A** Ha az eszköz fut update 3.0, helyezheti üzembe a helyileg rögzített kötetekhez legfeljebb 8,5 TB-os vagy rétegzett köteteket a 8100-as eszközön legfeljebb 200 TB. Ha az eszköz már rendelkezik rétegzett köteteket, a helyileg rögzített kötet létrehozásához rendelkezésre álló területre lesz arányosan alacsonyabb, mint a maximális korlátot. Például ha körülbelül 106 TB rétegzett kötet már kiépített a 8100-as eszközön (amely a rétegzett kapacitása fele), majd egy helyi kötet, amely a 8100-as eszközön létrehozhat maximális mérete ennek megfelelően csökken 4 TB-ig (körülbelül fele a maximális helyileg rögzített kötet kapacitása).

Mivel egyes helyi terület az eszközön, a rétegzett kötetek munkakészletének üzemeltetéséhez használt, ha az eszköz tartozik rétegzett kötet helyileg rögzített kötet létrehozásához a rendelkezésre álló területet csökken. Ezzel szemben arányosan helyileg rögzített kötet létrehozása csökkenti a rendelkezésre álló területet a rétegzett kötetek. Az alábbi táblázat foglalja össze a rendelkezésre álló rétegzett kapacitás a 8100-as és 8600-as eszközön gyors helyi kötetek létrehozásakor.

#### <a name="update-30"></a>Update 3.0 

| A gyors helyi kötetek kiosztott kapacitást | Ki kell építeni a rétegzett kötetek – 8100-as elérhető kapacitás | Ki kell építeni a rétegzett kötetek – 8600-as elérhető kapacitás |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8,5 TB |0 TB |311.1 TB |
| 10 TB |NA |277.8 TB |
| 15 TB |NA |166.7 TB |
| 22,5 TB |NA |0 TB |

**K.** Miért van a gyors helyi kötet létrehozása hosszú ideje futó művelet?

**V.** A gyors helyi kötetek nem dinamikus kiosztásúak. Az eszköz a helyi rétegeken terület létrehozásához néhány meglévő rétegzett kötetek adatainak előfordulhat, hogy lehet leküldeni a felhőbe a kiépítési folyamat során. És mivel ez attól függ, hogy az eszköz és a rendelkezésre álló sávszélességet, a felhőbe, a meglévő adatok kiépítése folyamatban, kötet méretét a helyi kötet létrehozásához szükséges idő előfordulhat, hogy több óra is lehet.

**K.** Mennyi ideig tart a helyileg rögzített kötet létrehozásához?

**V.** A gyors helyi kötetek nem dinamikus kiosztásúak, mivel a rétegzett kötetek néhány meglévő adatainak előfordulhat, hogy lehet leküldeni a felhőbe a kiépítési folyamat során. A helyileg rögzített kötet létrehozásához szükséges idő, ezért több tényezővel bővül, többek között a kötetet, az eszközön, és a rendelkezésre álló sávszélesség az adatok méretétől függ. Egy frissen telepített eszköz, amely rendelkezik a kötetek a helyileg rögzített kötet létrehozása ideje terabájt adat / nagyjából 10 percet. Azonban a helyi kötetek létrehozásához, amely használatban van egy eszközön a fent ismertetett tényezők alapján több óráig is eltarthat.

**K.** Szeretném a helyileg rögzített kötet létrehozásához. Vannak-e bármilyen kell figyelembe venni, ajánlott eljárások?

**V.** A gyors helyi kötetek alkalmasak használatához szükséges az adatok mindig helyi garanciákat, és a felhőbe az késleltetések bizalmas számítási feladatokhoz. Figyelembe véve a számítási feladatok bármelyikéhez helyi kötetek használatát, vegye figyelembe a következőket:

* A gyors helyi kötetek nem dinamikus kiosztásúak, és a helyi kötetek létrehozása hatással van a rétegzett kötetek esetében a rendelkezésre álló területet. Ezért javasoljuk, hogy kisebb méretű kötetek kezdődhet, és a vertikális felskálázás a a storage követelmény növekedése.
* Helyi kötetek kiépítése egy hosszú ideig futó művelet érintő rétegzett kötetek meglévő adatok leküldése a felhőben. Ennek eredményeképpen ezeken a köteteken alacsonyabb teljesítményt tapasztalhat.
* Helyi kötetek kiépítése egy olyan időigényes művelet. Az érintett tényleges idő több tényezőtől függ: a volume önköltséggel, az eszközön, és a rendelkezésre álló sávszélesség adatok méretét. Ha nem készített biztonsági másolatot a meglévő köteteket a felhőbe, majd a kötet létrehozása lassabb lesz. Javasoljuk, hogy a meglévő kötetek felhőbeli pillanatképek igénybe vehet egy helyi kötet kiépítése előtt.
* A gyors helyi kötetek alakíthatja át a meglévő rétegzett kötetek, és az átalakításhoz magában foglalja a kiépítés terület az eszközön, az eredményül kapott helyileg rögzített kötet (mellett a rétegzett adatok leállítása, ha bármely, a felhőben). Újra ez az egy hosszú ideig futó művelet, amely felett már beszéltünk tényezőtől függ. Javasoljuk, hogy készítsen biztonsági másolatot a meglévő köteteket a konvertálás előtt, a folyamat lassabb még akkor is, ha meglévő kötetek nem készül biztonsági másolat lesz. Az eszköz is előfordulhat, hogy tapasztalható teljesítménycsökkenés a folyamat során.

További információt az útmutató [helyileg rögzített kötet létrehozása](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**K.** Létrehozhatok több helyileg rögzített kötet egyszerre?

**V.** Igen, de a helyileg rögzített kötet létrehozása és bővítése feladatok feldolgozása sorrendben történik.

A gyors helyi kötetek nem dinamikus kiosztásúak, és ez megköveteli, hogy a helyi területet (ami azt eredményezheti, hogy a rétegzett kötetek lehet leküldeni a felhőbe a kiépítési folyamat során meglévő adatainak) létrehozását. Ezért egy üzembe helyezési művelet folyamatban van, ha más helyi kötet-létrehozási feladatok várólistára kerül, amíg a feladat nem fejeződött.

Hasonlóképpen ha meglévő helyi kötet alatt ki van bontva, vagy a rétegzett kötetek alakít át egy helyileg rögzített kötetet, majd egy új helyileg rögzített kötet létrehozása várólistára van állítva mindaddig, amíg az előző feladat befejeződött. A meglévő helyi területet az adott kötet bővítése egy helyileg rögzített kötet méretének foglalja magában. Átalakítás a helyileg rögzített egy rétegzett kötet is magában foglalja a helyi terület létrehozása a létrejövő helyileg rögzített kötet esetében. Mind a műveletek, a létrehozás vagy a helyi terület kiterjesztésének egy hosszú ideig fut. feladat.

Ezeket a feladatokat a **feladatok** a StorSimple-Eszközkezelő szolgáltatás paneljén. A feladat által aktívan feldolgozott folyamatosan frissül, és a hely kiépítése állapotát mutatja. A fennmaradó helyileg rögzített kötet feladatok futtatását, vannak megjelölve, de előrehaladás leállását, és azok útvonalról, a sorrendben, várólistára került.

**K.** Törölt egy helyileg rögzített kötet. Miért nem látom a regenerált terület megjelennek a rendelkezésre álló területet, amikor megpróbálom hozzon létre egy új kötetet?

**V.** Ha töröl egy helyileg rögzített kötetet, az új kötetek rendelkezésre álló terület nem frissíthető közvetlenül. A StorSimple-Eszközkezelő szolgáltatás körülbelül óránként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy az új kötet létrehozásakor egy órás várja.

**K.** A gyors helyi kötetek a felhőalapú berendezésen támogatottak?

**V.** A gyors helyi kötetek nem támogatottak a felhőalapú berendezésen (8010 és 8020-as eszközöket a StorSimple virtuális eszköz korábban nevezzük).

**K.** Használhatom az Azure PowerShell-parancsmagok létrehozása és kezelése a gyors helyi kötetek?

**V.** Nem, nem hozható létre a gyors helyi kötetek (rétegzett kötet hoz létre az Azure Powershellen keresztül) az Azure PowerShell-parancsmagok használatával. Emellett javasoljuk, hogy nem használja az Azure PowerShell-parancsmagok egy helyileg rögzített kötet bármely tulajdonságainak módosítása, a kötet típusának módosítása nem várt hatásának fog rendelkezni a rétegzett.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet biztonsági mentésével kapcsolatos kérdések
**K.** Helyi pillanatképei helyileg rögzített kötetek támogatott?

**V.** Igen, a helyileg rögzített kötetek a helyi pillanatképek is igénybe vehet. Azonban erősen javasoljuk, hogy rendszeresen biztonsági másolatot készíteni a felhőbeli pillanatképekkel, győződjön meg arról, hogy biztonságban tudhassa adatait egy vészhelyreállítási esetben helyileg rögzített köteteit.

Vegye figyelembe, hogy a helyi pillanatképek helyileg rögzített kötetek is is réteg ki a felhőbe, és nem biztos, hogy az eszköz helyi szinten marad.

**K.** Vannak-e tartalmi helyileg rögzített kötetek esetében a helyi pillanatképek kezeléséhez?

**V.** A gyakori helyi pillanatkép-készítés gyakorisága a helyileg rögzített kötet adatváltozás okozhat a helyi terület gyorsan elfogy, és a az adatokat a felhőbe való adatküldés rétegzett kötetek az eszközön. Ezért javasoljuk, hogy a helyi pillanatképek számának minimálisra csökkenthető.

**K.** Figyelmezteti, hogy helyileg rögzített kötetek saját helyi pillanatképei érvénytelenítve előfordulhat, hogy riasztást kapott. Ha ez fordulhat elő?

**V.** A gyakori helyi pillanatkép-készítés gyakorisága a helyileg rögzített kötet adatváltozás okozhat a helyi terület gyorsan elfogy az eszközön. Ha az eszköz a helyi rétegeken használt funkcióknak, elhőbeli kimaradás eredményezheti, hogy az eszköz megtelnek, és bejövő írása a kötet azt eredményezheti, hogy a pillanatképek érvénytelenítése (mivel nincs szabad hely frissíteni a pillanatképek tekintse meg a régebbi blokk létezik adatok, amely felül lett írva). Ebben az esetben a kötetet az írási műveletek továbbra is szeretne kiszolgálni, de lehet, hogy a helyi pillanatképek érvénytelen. Nincs nem érinti a meglévő felhőbeli pillanatképeket.

A riasztás – figyelmeztetés, hogy Önt, hogy ez a helyzet merülnek fel, és Ön változataiban azonos időben ritkábban forduljanak a helyi pillanatképek a helyi pillanatképek ütemezését áttekintése vagy régebbi a helyi pillanatképek, amelyek már nem szükséges törlése.

Ha a helyi pillanatképek érvénytelenné válnak, kapni fog egy információs riasztás arról tájékoztatja, hogy az adott biztonsági mentési szabályzat helyi pillanatképei érvénytelenítve lett a helyi pillanatképek érvénytelenített időbélyegeket listája mellett. Ezek a pillanatképek automatikusan törölve, és már nem megtekintheti őket a a **Backup katalógusok** panel az Azure Portalon.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Kérdések a helyileg rögzített kötet rétegzett kötet konvertálása
**K.** Vagyok, ha az egyes lassúsága az eszközön helyileg rögzített kötetet egy rétegzett kötet konvertálása során észlelt problémát. Miért várható a változás?

**V.** Az átalakítási folyamat két lépésből áll:

1. Kiépítés terület az eszközön a hamarosan-az--konvertálható helyileg a rögzített kötet.
2. Garantálja a rétegzett adatok letöltése a felhőből helyi biztosításához.

Mindkettő az alábbi lépések hosszúak futó műveletek, amelyek az átalakítandó adat az eszközön, és a rendelkezésre álló sávszélesség kötet mérete függenek. Néhány meglévő rétegzett kötetek adatainak a előfordulhat, hogy a kiépítési folyamat részeként kerülnek a felhőbe, mivel az eszköz ez idő alatt teljesítménycsökkenés is szembesülhet. Emellett az átalakítási folyamat lassabb lehet, ha:

* Meglévő kötetek nem készült biztonsági a felhőben ezért javasoljuk, hogy biztonsági másolatot egy kezdeményezése előtt a köteteket.
* A sávszélesség-szabályozási házirendek alkalmazása, amely vendégenként a rendelkezésre álló sávszélességet, a felhőben ezért javasoljuk egy dedikált 40 MB/s, vagy több kapcsolat van, a felhőbe.
* A fenti; több tényező miatt több órát is igénybe vehet a az átalakítási folyamat ezért javasoljuk, hogy nem csúcsokat időszakokban vagy a hétvégi a végfelhasználók a fogyasztók gyakorolt hatás elkerülése érdekében a művelet elvégzéséhez.

További információt az útmutató [egy helyileg rögzített kötet rétegzett kötet konvertálása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**K.** A kötet átalakítási műveletet lemondható?

**V.** Nem a Mégse gombra a átalakítási művelet elindítása után. Mivel az előző kérdésnél leírt, vegye figyelembe, hogy előfordulhat, hogy a folyamat során előforduló, és kövesse az ajánlott eljárásokat, ha azt tervezi, hogy az átalakítás a fent felsorolt a lehetséges teljesítményproblémák.

**K.** Mi történik a kötetet, ha az átalakítási művelet meghiúsul?

**V.** Kötet átalakítás felhőbeli kapcsolódási problémák miatt meghiúsulhat. Az eszköz leállíthatja a rétegzett adatok a felhőből a sikertelen kísérletek sorozatát követő végül leállíthatja az átalakítási folyamat. Ilyen esetben a kötet típusának, továbbra is az átalakítás előtt forrás kötettípus és:

* Egy kritikus riasztást generál, amelyek figyelmeztetik, a kötet konvertálási hiba. További információ a [gyors helyi kötetek kapcsolatos riasztások](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Ha egy rétegzett a helyileg rögzített kötetet, a kötet a rétegzett kötet tulajdonságait a számára, ahogy adatok előfordulhat, hogy továbbra is megtalálhatók a felhő továbbra is. Javasoljuk, hogy a kapcsolati problémák elhárításához, és ismételje meg az átalakítási műveletet.
* Hasonlóképpen egy helyileg rögzített való átalakítás egy rétegzett kötet meghibásodása esetén, azonban a kötet lesznek megjelölve helyileg rögzített kötetet, ha azt fog működni, a rétegzett kötetek (mivel az adatokat a felhőbe sikerült rendelkezik kiömlött). Azonban továbbra is az eszköz a helyi rétegeken tárhelyet foglalnak. Ez a terület nem lesz elérhető, a többi helyileg rögzített kötetekhez. Javasoljuk, hogy újra megpróbálja a műveletet, győződjön meg arról, hogy a kötet konvertálása befejeződött, és az eszközön a helyi terület szabadítható.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet visszaállításával kapcsolatos gyakori kérdésekre
**K.** A gyors helyi kötetek azonnal vissza?

**V.** Igen, a gyors helyi kötetek azonnal lettek visszaállítva. Amint a kötethez tartozó metaadat-információkat a visszaállítási művelet részeként lehívja a felhőben, a kötet online állapotba kerül, és a gazdagép által elérhető lesz. Azonban a kötet helyi garanciákat, adatok nem lesznek jelen, amíg az összes adat letöltötte a felhőből, és előfordulhat, csökkenteni a visszaállítás időtartama a kötetek teljesítménye.

**K.** Mennyi ideig tart egy helyileg rögzített kötet visszaállítása?

**V.** Gyors helyi kötetek azonnal vissza és, amint a kötet metaadatait veszi át a felhőbe, a mennyiségi adatok továbbra is le kell tölteni a háttérben online állapotba. Ez utóbbi része a visszaállítási művelet – a mennyiségi adatok--visszaállítani a helyi garanciákat egy hosszú ideig futó művelet, és el kell végezni helyi újra az adatok több órát is igénybe vehet. Azonos végrehajtásához szükséges idő attól függ, hogy több tényezővel bővül, például folyamatban van a kötet méretét és a rendelkezésre álló sávszélességen. Ha az eredeti kötet, amely kerül törölve lett, további időt megnyílik hozhat létre a helyi területet az eszközön a visszaállítási művelet részeként.

**K.** Kell a meglévő helyileg rögzített kötet visszaállítása egy korábbi pillanatkép (elvégezni, ha a kötet rétegzett volt). A kötet visszaáll, ebben az esetben a rétegzett?

**V.** Nem, a kötet lesz visszaállítva, egy helyileg rögzített kötet. Bár az idő, amikor a kötet rétegzett volt, a pillanatkép dátumát való visszaállítása során meglévő kötetek a StorSimple mindig kötet típusát a lemez jelenleg meglévő.

**K.** Nemrég kiterjesztett szeretnék a helyileg rögzített kötetet, de most kell visszaállítani az adatokat egy időpontot, amikor a kötet mérete kisebb volt. Visszaállítási átméretezi a jelenlegi kötetet, és ezeket kell-e a kötet kiterjesztése, a visszaállítás befejezése után?

**V.** Igen, a visszaállítás átméretezi a kötetet, és kiterjesztheti a kötet méretét, a visszaállítás befejezése után kell.

**K.** Módosíthatom a kötet típusa visszaállítás során?

**V.** Nem, visszaállítás során a kötet típusa nem módosítható.

* A típus a pillanatképben tárolt kötetek törölt visszaállítását végzi.
* Meglévő kötetek visszaállítása azok aktuális típusától, a pillanatképben tárolt típusától függően (lásd az előző két kérdések).

**K.** A helyileg rögzített kötet visszaállítása kell, de szeretnék egy helytelen pont kivételezett idő pillanatfelvétel. A jelenlegi visszaállítási műveletet lemondható?

**V.** Igen, megszakíthatja, egy folyamatban visszaállítási művelet. A kötet állapotát elején. a visszaállítási állapotba vissza lesz állítva. Azonban miközben folyamatban volt a visszaállítás a köteten végrehajtott írási elvesznek.

**K.** Egy visszaállítási művelet a gyors helyi kötetek egyik kezdtem, és most már láthatók egy pillanatképet a saját mappájában várakozó fájlok számát-katalógus, amely I nem recollect létrehozása. Ez mire való?

**V.** Ez az ideiglenes pillanatképre, jön létre a visszaállítási művelet előtt és a visszaállítás szolgál abban az esetben a visszaállítás megszakadt, vagy sikertelen lesz. Ne törölje a pillanatkép; Ez automatikusan törli a helyreállítás befejezésekor. Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik.

**K.** Klónozhat a helyileg rögzített kötetet?

**V.** Igen. Azonban a helyileg rögzített kötet klónozza, a rétegzett kötetek alapértelmezés szerint. További információt az útmutató [helyileg rögzített kötet klónozása](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet feladatátadás kapcsolatos kérdések
**K.** Van szükségem egy másik fizikai eszköz az eszköz feladatátvételét. A gyors helyi kötetek sikertelen lesz, a helyileg rögzített vagy rétegzett több mint?

**V.** A gyors helyi kötetek feladatátvétel történt a helyileg rögzített a Ha a céleszközön fut-e a StorSimple 8000 series update 3 vagy nagyobb.

További információ a [feladatátvétel és Vészhelyreállítás a helyileg rögzített kötetekről verziója között](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**K.** A gyors helyi kötetek azonnal visszaállítása során a vészhelyreállítás (DR)?

**V.** Igen, a gyors helyi kötetek azonnal a feladatátvétel során állítja vissza. Amint a kötethez tartozó metaadat-információkat a felhőből kéri le a feladatátvételi művelet részeként, a kötet online állapotba kerül a céleszközön, és a gazdagép által elérhető lesz. Ugyanakkor továbbra is a mennyiségi adatok letöltését a háttérben, és ezeken a köteteken a feladatátvétel időtartama csökkentett teljesítményt tapasztalhat.

**K.** A feladatátvételi feladat befejeződött, hogyan tudok nyomon követheti a helyileg rögzített kötet, amely kerül a céleszközön meg?

**V.** A feladatátvételi művelet során a feladatátvételi feladat van megjelölve, végezze el egyszer az összes a köteteket a feladatátvételi csoportban kell azonnal visszaállítva, és a céleszköz online állapotú. Ez magában foglalja a gyors helyi köteteket, előfordulhat, hogy feladatátadása megtörtént; azonban az adatok helyi garanciákat csak akkor érhető el, ha a kötet minden adatát lett letöltve. Ez a folyamat minden helyileg rögzített kötet, amely a megfelelő, a feladatátvétel részeként létrehozott helyreállítási feladatok figyelése át lett nem tudja követni. Ezek az egyes visszaállítási feladat csak a helyileg rögzített kötetekhez hozhatók létre.

**K.** Módosíthatom a kötet típusa feladatátvétel során?

**V.** Nem, a kötet típusa nem módosítható a feladatátvétel alatt. Ha Ön nem működik keresztül egy másik fizikai eszközt, amelyen fut a StorSimple 8000 series 3-as frissítés, a kötetek feladatátvételekor a pillanatképben tárolt kötet típusa alapján.

**K.** Is lehet feladatátvételt beállítani a helyileg rögzített köteteit a felhőalapú készülék egy kötettárolót?

**V.** Igen. A gyors helyi kötetek kerül átadásra a rétegzett kötetek. További információ a [feladatátvétel és Vészhelyreállítás a helyileg rögzített kötetekről verziója között](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

