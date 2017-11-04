---
title: "StorSimple helyileg rögzített kötetek – gyakori kérdések |} Microsoft Docs"
description: "A StorSimple helyileg rögzített kötetek gyakran feltett kérdésekre adott válaszok biztosít."
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: 
ms.assetid: 7a2f4b1a-4ac9-4ce1-9359-bd40a9cbbb5d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/11/2017
ms.author: manuaery
ms.openlocfilehash: 22eb95bf0e3a20893acbb860ad10cfe6a3bcf088
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple helyileg rögzített kötetek: gyakori kérdések (GYIK)
## <a name="overview"></a>Áttekintés
Az alábbi táblázat kérdések és válaszok lehetséges, hogy hozzon létre egy StorSimple helyileg rögzített kötetet, alakítsa át a rétegzett kötetek egy helyileg rögzített kötet (és fordítva), vagy biztonsági mentéséhez és a visszaállításhoz egy helyileg rögzített kötet.

Kérdések és válaszok az alábbi kategóriákba vannak rendezve

* Egy helyileg rögzített kötet létrehozása
* Biztonsági mentése egy helyileg rögzített
* Egy helyileg rögzített kötet egy rétegzett kötet konvertálása
* Egy helyileg rögzített kötet visszaállítása
* Egy helyileg rögzített kötet feladatátvételét

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet létrehozásával kapcsolatos kérdések
**K.** Mi az a maximális méretét, amely képes vagyok létrehozni a 8000 sorozat eszközeire a helyileg rögzített kötet?

**A** -t a StorSimple 8000 Series Update 3.0 futtató eszközökön építhető helyileg rögzített kötetekhez legfeljebb 8.5 TB, rétegzett kötetekhez legfeljebb 200 TB a 8100-as eszközhöz. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki.    
A StorSimple 8000 Series Update verziót futtató eszközökön 2.x, megadhat helyileg rögzített kötetekhez legfeljebb 8 TB, rétegzett kötetekhez legfeljebb 200 TB a 8100-as eszközhöz. A nagyobb 8600-as eszközön helyileg rögzített kötetekhez legfeljebb 20 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki.   

**K.** Nemrég frissítette a 8100-as eszközhöz a frissítés 2.0 és a helyileg rögzített kötet létrehozásához, ha a maximális méret csak 6 TB és nem 8 TB. Miért nem hozható létre egy 8 TB-os kötet?

**A** Ha az eszköz fut frissítés 2.0, oszthat helyileg rögzített kötetekhez legfeljebb 8 TB vagy rétegzett kötet legfeljebb 200 TB a 8100-as eszközhöz. Ha az eszköz már rendelkezik rétegzett kötet, akkor a szabad lemezterületet egy helyileg rögzített kötet létrehozásához arányosan alacsonyabb, mint a maximális korlát lesz. Például ha 100 TB rétegzett kötetek már kiépített a 8100-as eszközön (amely a rétegzett kapacitás felének), majd a maximális méretét, amelyet a 8100-as eszközön létrehozhat egy kötet ennek megfelelően csökken 4 TB-os (körülbelül fele a maxi a MUM helyileg rögzített kötet kapacitása).

A rétegzett kötetek munkakészletének üzemeltetéséhez helyi helyet a az eszköz használatával, ha az eszköz rendelkezik rétegzett kötet helyileg rögzített kötet létrehozása a rendelkezésre álló terület csökken. Ezzel szemben helyileg rögzített kötet arányosan létrehozása csökkenti a rétegzett kötetek rendelkezésre álló terület. Az alábbi táblázatok a 8100-as és 8600-eszközökön elérhető rétegzett kapacitás foglalja össze, helyileg rögzített kötetek létrehozásakor.

####<a name="update-30"></a>Frissítés 3.0 
| Helyileg rögzített kötetekhez kiosztott kapacitást | Ki kell építeni rétegzett kötetek - 8100 elérhető kapacitás | Ki kell építeni rétegzett kötetek - 8600 elérhető kapacitás |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |NA |277.8 TB |
| 15 TB |NA |166.7 TB |
| 22.5 TB |NA |0 TB |

####<a name="update-2x"></a>2.x frissítése  
 | Helyileg rögzített kötetekhez kiosztott kapacitást | Ki kell építeni rétegzett kötetek - 8100 elérhető kapacitás | Ki kell építeni rétegzett kötetek - 8600 elérhető kapacitás |  
 | --- | --- | --- |  
 | 0 |200 TB |500 TB |  
 | 1 TB |25 TB |475 TB |  
 | 4 TB |100 TB |400 TB |  
 | 8 TB |0 TB |300 TB |  
 | 10 TB |NA |250 TB |  
 | 15 TB |NA |125 TB |  
 | 20 TB |NA |0 TB |   

**K.** Miért van helyileg rögzített kötet létrehozása hosszú ideig futó művelet? 

**V.** Helyileg rögzített kötetek vannak kiosztása. Szabadítson fel helyet a helyi rétegeken az eszköz, adatai a meglévő rétegzett kötetek előfordulhat, hogy leküldött a felhőbe a telepítési folyamat során. És mivel ez a kötet kiépítése folyamatban, az eszköz és a rendelkezésre álló sávszélesség a felhőbe, a meglévő adatok méretétől függ a helyi kötet létrehozásához szükséges idő lehet, hogy több óra is lehet.

**K.** Mennyi időt vesz igénybe a helyileg rögzített kötet létrehozásához?

**V.** Helyileg rögzített kötet kiosztása van, mert néhány meglévő adatok a rétegzett kötetek előfordulhat, hogy leküldött a felhőbe a telepítési folyamat során. Ezért a helyileg rögzített kötet létrehozásához szükséges idő több tényezővel, például a köteten, az eszköz és a rendelkezésre álló sávszélesség lévő adatok méretétől függ. Egy frissen telepített eszköz, amely rendelkezik a kötet a helyileg rögzített kötet létrehozásához ideje / adatok terabájt körülbelül 10 perc. A helyi kötetek létrehozása azonban olyan eszköz, amely használatban van a fenti tényezők alapján több óráig is eltarthat.

**K.** Szeretném a helyileg rögzített kötet létrehozásához. Vannak-e be kell ismernie a bevált gyakorlatokat?

**V.** Helyileg rögzített kötetek alkalmasak, amely mindig adatok helyi garanciákat igényelnek és bizalmas késések felhőbe. Figyelembe véve az összes, a munkaterhelés helyi kötetek használatát, vegye figyelembe a következőket:

* Helyileg rögzített kötet kiosztása, és helyi kötetek létrehozása hatással van a rétegzett kötetek esetében a rendelkezésre álló területet. Ezért javasoljuk, hogy kisebb méretű kötetek kezdődnie, és a a tárolási követelmény növekedése növelheti.
* A helyi kötetek egy hosszú ideig futó művelet, például előfordulhat, hogy az kérdez le a meglévő adatokat a rétegzett kötetek a felhőbe. Ennek eredményeképpen ezeken a köteteken a csökkentett teljesítményt tapasztalhat.
* A helyi kötetek egy időigényes művelet. A tényleges idő érintett több tényezőtől függ: a kiépítése folyamatban köteten, az eszközön, és a rendelkezésre álló sávszélesség adatok méretét. Ha nem készített biztonsági másolatot a meglévő köteteket a felhőbe, a kötet létrehozása akkor lassabb. Javasoljuk, hogy a meglévő kötetek felhőalapú pillanatfelvételek igénybe vehet egy kötet kiépítése előtt.
* A meglévő rétegzett kötetek átalakítása helyileg rögzített kötetekhez, és az átalakításhoz magában foglalja a terület az eszközön az eredményül kapott helyileg rögzített kötet (mellett leáll rétegzett adatok [esetleges] a felhőből) való kiépítése. Ebben az esetben ez az egy hosszú ideig futó művelet, amely azt korábban már korábban említettük tényezőktől függ. Javasoljuk, hogy biztonsági másolatot a meglévő köteteket átalakítás előtti, a folyamat lassabb még akkor is, ha meglévő kötetek nem készül biztonsági mentés lesz. Az eszköz is előfordulhatnak teljesítménycsökkenés a folyamat során.

További tájékoztatást [helyileg rögzített kötet létrehozása](storsimple-manage-volumes-u2.md#add-a-volume)

**K.** Hozható létre több helyileg rögzített kötetekhez egyszerre?

**V.** Igen, de a helyileg rögzített kötet létrehozása és bővítése feladat feldolgozása sorrendben történik.

Helyileg rögzített kötet kiosztása, és ez megköveteli, hogy helyi terület az eszközön (ami a meglévő adatokat és a telepítési folyamat során a felhőbe leküldött rétegzett kötetek) létrehozását. Ezért ha egy üzembe helyezési feladat van folyamatban, más helyi kötet-létrehozási feladatok, várósorba kerülnek, amíg a feladat nem fejeződött.

Hasonlóképpen ha egy meglévő helyi kötet alatt ki van bontva, vagy a rétegzett kötetek alakít át egy helyileg rögzített kötetet, majd egy új helyileg rögzített kötet létrehozásához várólistára van állítva az előző feladat befejezéséig. Egy helyileg rögzített kötet méretének magában foglalja a meglévő helyi terület, hogy a kötet kiterjesztését. Átalakítás egy rétegzett helyileg rögzített kötet is magában foglalja a helyi terület létrehozása a a létrejövő helyileg rögzített kötet. Mind a műveletek, a létrehozása vagy a helyi terület bővítése long fut. feladat.

Ezek a feladatok megtekintéséhez a **feladatok** az Azure StorSimple Manager szolgáltatás lapján. A feladat által aktívan feldolgozott állapotát mutatja terület kiépítés folyamatosan frissül. A fennmaradó helyileg rögzített kötet feladatok futtatását, jelölésű, de a folyamat leállt, és azokat a ahhoz, azok sorba állított leltárhoz.

**K.** Törölt egy helyileg rögzített kötet. Miért nem látom, hogy hozzon létre egy új kötetet közben a szabad terület kerülnek regenerált helyet? 

**V.** Ha töröl egy helyileg rögzített kötetet, a szabad lemezterület az új kötetekre esetleg nem azonnal frissíti. A StorSimple Manager szolgáltatás körülbelül óránként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy várja meg a egy óraba az új kötet létrehozásakor.

**K.** Helyileg rögzített kötetekhez a felhő-készüléken támogatottak?

**V.** A felhő-készüléken (8010 és 8020 eszközök korábban néven a StorSimple virtuális eszköz) nem támogatottak a helyileg rögzített kötetekhez.

**K.** Használhatom az Azure PowerShell-parancsmagok létrehozásához és kezeléséhez a helyileg rögzített kötetekhez? 

**V.** Nem, nem hozható létre (Azure PowerShell használatával hoz létre kötet Szintezett) Azure PowerShell-parancsmagok segítségével helyileg rögzített kötetekhez. Emellett javasoljuk, hogy nem használja az Azure PowerShell-parancsmagok egy helyileg rögzített kötet bármely tulajdonságainak módosítása mert módosítása a kötet típusa nem kívánt hatását a rétegzett.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet biztonsági mentésével kapcsolatos kérdések
**K.** Azok a támogatott helyileg rögzített kötetek helyi pillanatképeket?

**V.** Igen, a helyileg rögzített kötetek helyi pillanatképeket is igénybe vehet. Azonban ajánlott rendszeresen biztonsági másolatot annak érdekében, hogy az adatok egy olyan vészhelyzet esetén esetben védett felhő pillanatfelvételekkel a helyileg rögzített kötetekhez.

**K.** Vannak-e bármiféle irányelv, amely a helyileg rögzített kötetekhez helyi pillanatképek kezeléséhez?

**V.** A helyileg rögzített kötet adatforgalommal nagy mértékű mellett gyakori helyi pillanatképeket okozhat helyi terület gyorsan használni, és a az adatokat a rétegzett kötetek alatt leküldött a felhőbe az eszközön. Ezért javasoljuk, hogy helyi pillanatképek számának minimalizálása érdekében.

**K.** Kaptam arról, hogy a helyi pillanatképeket helyileg rögzített kötetek érvényteleníti előfordulhat, hogy riasztást. Ha ez fordulhat?

**V.** A helyileg rögzített kötet adatforgalommal nagy mértékű mellett gyakori helyi pillanatképeket okozhat helyi terület gyorsan fel az eszközön. Az eszköz a helyi rétegeken fokozottan használata esetén kiterjesztett felhő kimaradás esetén az eszköz teljenek előfordulhat, és a bejövő írási műveleteket ad ki a kötet eredményezhet a pillanatképek érvénytelenítése (módon nem lehet szóköz létezik-e frissíteni a régebbi adatblokkok, amely felül lett írva, tekintse meg a pillanatképek). Ilyen esetben a írási műveleteket ad ki a kötet továbbra is jeleníthető meg, de lehet, hogy a helyi pillanatképeket érvénytelen. A meglévő felhőalapú pillanatfelvételek hatással van.

A figyelmeztetést, hogy tájékoztassuk Önt, hogy ez a helyzet merülnek fel, és győződjön meg arról, hogy címét időben vagy a helyi pillanatképeket ütemezésekkel ritkábban helyi pillanatképek áttekintése, vagy régebbi a már nem szükségesek helyi pillanatképek törlésével.

Ha a helyi pillanatképeket érvénytelenné válnak, egy értesítés, hogy a helyi pillanatképeket a meghatározott biztonsági házirend érvénytelenítve lett, amely érvénytelenítve lett a helyi pillanatképek időbélyegeket listája mellett információ riasztást fog kapni. Ezeket a pillanatképeket a automatikusan törölve lesz, és már nem fogja megtekintheti azokat a **biztonsági mentés katalógusok** a klasszikus Azure portálra a lap.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>A rétegzett kötet konvertálása egy helyileg rögzített kötet kapcsolatos kérdések
**K.** Vagyok, ha az egyes lassúsága az eszközön helyileg rögzített kötet egy rétegzett kötet konvertálása során tartják be. Miért van ennek az oka? 

**V.** Az átalakítási folyamat két lépésből áll: 

1. Az eszközön a leghamarabb-az--alakítható helyileg a lemezterület üzembe rögzített kötet.
2. Biztosítja a rétegzett adatok letöltése a helyi biztosításához a felhőből.

Ezek hosszúak konvertálni, adatok, az eszközön, és a rendelkezésre álló sávszélesség a kötet mérete függő műveletek futtatása. A meglévő rétegzett kötetek adatai a telepítési folyamat részeként előfordulhat, hogy kerülnek a felhőbe, mert az eszköz ebben az időszakban, a teljesítménycsökkenés is szembesülhet. Emellett az átalakítási folyamat lassabb lehet, ha:

* Meglévő kötetek nem mentett a felhőbe; ezért javasoljuk, hogy a kötetek konverzió kezdeményezése előtt készítsen biztonsági másolatot.
* Sávszélesség-szabályozási házirendeket alkalmaztak, ami korlátozhatja a rendelkezésre álló sávszélesség a felhőbe; ezért javasoljuk, hogy az egy dedikált 40 MB/s- vagy kapcsolatban a felhőbe.
* A fenti; több tényezők miatt több órát is igénybe vehet a az átalakítási folyamat ezért javasoljuk, hogy a művelet esetén nem csúcsait vagy záró fogyasztók gyakorolt hatás elkerülése érdekében hétvégi hajtható végre.

További tájékoztatást [rétegzett kötet konvertálása egy helyileg rögzített kötet](storsimple-manage-volumes-u2.md#change-the-volume-type)

**K.** A kötet átalakítási műveletet is megszakítja?

**V.** Nem, nem lehet a Mégse gombra az átalakítási műveletet egyszer kezdeményezett. Lásd az előző kérdéssel a, vegye figyelembe, hogy előfordulhat, hogy a folyamat során előforduló, és kövesse az ajánlott eljárásokat, ha azt tervezi, hogy a konverzió a fent felsorolt a lehetséges teljesítményproblémák.

**K.** Mi történik a kötet, ha sikertelen az átalakítási műveletet?

**V.** Kötet átalakítás felhő csatlakozási problémák miatt sikertelen lehet. Az eszköz végül leállíthatja az átalakítási folyamat leállíthatja a rétegzett adatokat a felhőből sikertelen kísérletet követően. Ilyen esetben a kötet típusa továbbra is a kötet típusa az átalakítás előtti és:

* Egy kritikus riasztást generál, hogy a kötet típuskonverziós hiba tájékoztassuk. További információ a [helyileg rögzített kötetekhez kapcsolatos riasztások](storsimple-manage-alerts.md#locally-pinned-volume-alerts)
* Átalakításkor egy rétegzett egy helyileg rögzített kötetet, ha a kötet továbbra is mutatnak rétegzett kötet tulajdonságai, mivel adatok előfordulhat, hogy továbbra is a felhőre. Javasoljuk, hogy a kapcsolódási problémák megoldásához, és ismételje meg az átalakítási műveletet.
* Hasonlóképpen ha rétegzett kötetet a helyileg rögzített történő átalakítás sikertelen lesz, bár a kötet egy helyileg rögzített kötet lesz megjelölve, működik egy rétegzett kötet (mivel az adatok sikerült rendelkezik kiömlött a felhőbe). Azonban továbbra is az eszköz a helyi rétegeken területet foglalnak. Ezen a helyen nem lesz elérhető az egyéb helyileg rögzített kötetek esetében. Javasoljuk, hogy ez a művelet csak győződjön meg arról, hogy a kötet konvertálása befejeződött, és az eszközön a helyi terület szabadítható fel újra.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet visszaállításával kapcsolatos gyakori kérdésekre
**K.** Azonnal vissza helyileg rögzített kötetek?

**V.** Igen, azonnal visszaállnak helyileg rögzített kötetekhez. Amint a kötethez tartozó metaadat-információkat a visszaállítási művelet részeként a felhőben van lekért, a kötet online állapotba kerül, és a gazdagép elérhető legyen. Azonban az adatok nem lesznek megtalálhatók, amíg az adatok a felhőből le van töltve, és problémákat tapasztalhat a kötet helyi garanciákat csökkenteni teljesítmény ezeken a köteteken a visszaállítás alatt.

**K.** Mennyi időt vesz igénybe egy helyileg rögzített kötet visszaállítása?

**V.** Helyileg rögzített kötetek azonnal vissza és a hálózatra, amint a kötet metaadatok veszi át a felhőbe, közben a kötet adatait le kell tölteni a háttérben folytatódik. A visszaállítási művelet--beolvasása vissza a helyi garanciákat az kötet--ez utóbbi részét hosszú ideig futó művelet, és az összes elvégzendő helyi újra több órát is igénybe vehet. Az azonos végrehajtásához szükséges idő több tényezőtől függ, például a visszaállítandó kötet méretét és a rendelkezésre álló sávszélességet. Ha törölték az eredeti kötetre visszaállítása folyamatban van, további időt megnyílik a visszaállítási művelet részeként hozza létre a helyi terület az eszközön.

**K.** A meglévő helyileg rögzített kötet visszaállítása egy korábbi pillanatkép (hajtja végre, ha a kötet volt Szintezett) kell. A kötet visszaállnak, ebben az esetben rétegzett?

**V.** Nem, a kötet vissza lesznek állítva egy helyileg rögzített kötet. Bár a pillanatkép dátumokat az idő, amikor a kötet volt rétegzett, meglévő kötetek visszaállítása közben StorSimple mindig kötet a lemezen, mert jelenleg létezik.

**K.** Nemrég kiterjesztett I a helyileg rögzített kötetet, de most kell állítsa vissza az adatokat, amikor a kötet mérete kisebb volt idő. Visszaállítás átméretezi-e az aktuális kötet, és fog kell-e a kötet méretének növelése a visszaállítás befejezése után?

**V.** Igen, a visszaállítás átméretezi-e a kötet, és kiterjesztése a kötet mérete, a visszaállítás befejezése után kell.

**K.** Módosítható visszaállítás során egy kötet?

**A.**nem, nem módosíthatja a kötet típusa visszaállítás során.

* Törölt kötetek visszaállítását végzi el a pillanatkép tárolt típusát.
* Meglévő kötetek visszaállítása az aktuális típusa, függetlenül a pillanatkép tárolt típus alapján (lásd az előző két kérdések).

**K.** Állítsa vissza a helyileg rögzített kötetet kell, de egy helytelen pont I mennyi idő pillanatfelvétel. Az aktuális visszaállítási művelet is megszakítja?

**V.** Igen, megszakíthatja a folyamatban lévő helyreállítási művelet. A kötet állapota program visszaállítja elején. a visszaállítási állapotba. Azonban a visszaállítás közben a kötet végrehajtott bármilyen írás el fog veszni.

**K.** A visszaállítási művelet a helyileg rögzített kötetekhez egyik kezdődik, és most a várakozó-katalógus létrehozása recollect I nem látom pillanatképet. Ez alkalmazott?

**V.** Ez az az ideiglenes pillanatképet, amely a visszaállítási művelet előtt jön létre, és a visszaállításhoz használt abban az esetben a visszaállítás megszakadt, vagy nem sikerül. Ne törölje a pillanatkép; akkor lesz automatikusan törlődik a visszaállítás befejeződött. Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik.

**K.** Egy helyileg rögzített kötetet tud klónozni?

**V.** Igen, akkor is. Azonban a helyileg rögzített kötet lesz klónozható egy rétegzett kötet alapértelmezés szerint. További tájékoztatást [egy helyileg rögzített kötet klónozása](storsimple-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Egy helyileg rögzített kötet feladatátvételét kapcsolatos kérdések
**K.** Az eszköz egy másik fizikai eszközre feladatátvételt kell. A helyileg rögzített kötetekhez sikertelen lesz, a helyileg rögzített vagy rétegzett több mint?

**V.** Attól függően, hogy a figyelt eszköz szoftverének verziójával helyileg rögzített kötetekhez átvétele fog megtörténni mint:

* Helyileg rögzített, ha a céleszközt fut-e a StorSimple 8000 series update 2
* Rétegzett, ha a céleszközt fut-e a StorSimple 8000 series 1.x frissítése
* A felhő készülék esetén is a céleszközt rétegzett (szoftverfrissítés verzió 2 vagy 1.x módosítása)

További információ a [feladatátvétel és a vész-Helyreállítási helyileg rögzített kötetek verziója között](storsimple-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**K.** Helyileg rögzített kötetekhez azonnal visszaállnak vészhelyreállítás (DR) során?

**V.** Igen, a helyileg rögzített kötetek visszaállítása azonnal a feladatátvétel során. Amint a kötethez tartozó metaadat-információkat a felhő lekért a feladatátvételi művelet részeként, a kötet online állapotba kerül a céleszközön, és a gazdagép elérhető legyen. Eközben a kötet adatait letöltése a háttérben folytatódik, és ezeken a köteteken a feladatátvétel időtartama csökkentett teljesítmény.

**K.** A feladatátvételi feladatban fejeződött be, hogyan követhető nyomon a helyileg rögzített kötetet, amelyek helyreállítása előrehaladását a céleszközön meg?

**V.** A feladatátvételi művelet során a feladatátvételi feladatban van megjelölve végezze el egyszer az összes feladatátvételi készletében kötetek rendelkezik azonnal visszaállítva és online állapotba a céleszközön. Ez magában foglalja a helyileg rögzített kötetekhez keresztül; sikertelen lehet, hogy azonban az adatok helyi garanciákat csak akkor érhető el a kötet összes adatát letöltésekor. Nyomon követheti a folyamat egyes át a megfelelő, a feladatátvétel részeként létrehozott helyreállítási feladatok figyelése nem volt helyileg rögzített kötetekhez. Ezek a feladatok egyes visszaállítási csak a helyileg rögzített kötetekhez hozhatók létre.

**K.** Módosítható a feladatátvétel során egy kötet?

**V.** Nem, a kötet típusa nem módosítható egy feladatátvétel során. Ha Ön nem működnek keresztül egy másik fizikai eszközt, hogy fut a StorSimple 8000 series frissítse 2, a kötetek átvétele fog megtörténni a pillanatkép tárolt kötettípus alapján. Feladatátadás a más eszköz verziója, tekintse meg a fenti kérdés a kötettípus egy feladatátvétel után.

**K.** Tudom a feladatátvétel és a felhő készülék helyileg rögzített kötetek a kötettároló?

**V.** Igen, akkor is. A helyileg rögzített kötetekhez a rétegzett kötetek átvétele fog megtörténni. További információ a [feladatátvétel és a vész-Helyreállítási helyileg rögzített kötetek verziója között](storsimple-device-failover-disaster-recovery.md#considerations-for-device-failover)

