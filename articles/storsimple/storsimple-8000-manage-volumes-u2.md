---
title: StorSimple-kötetek kezelése (3. frissítés)
description: A StorSimple-kötetek hozzáadásának, módosításának, figyelésének és törlésének, valamint szükség esetén offline állapotba hozásának útmutatója.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254767"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Kötetek kezelése a StorSimple Eszközkezelő szolgáltatással (3. vagy újabb frissítés)

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan hozhat létre és kezelhet köteteket a StorSimple 8000 sorozatú 3- as és újabb frissítést futtató storSimple sorozatú eszközökön.

A StorSimple Device Manager szolgáltatás egy bővítmény az Azure Portalon, amely lehetővé teszi a StorSimple-megoldás egyetlen webes felületről történő kezelését. Az Azure Portal használatával kezelheti a kötetek az összes eszközén. StorSimple-szolgáltatásokat is létrehozhat és kezelhet, kezelheti az eszközöket, a biztonsági mentési házirendeket és a biztonsági mentési katalógust, és megtekintheti a riasztásokat.

## <a name="volume-types"></a>Kötettípusok

A StorSimple kötetek a következők lehetnek:

* **Helyileg rögzített kötetek:** Az ezekben a kötetekben lévő adatok mindig a helyi StorSimple eszközön maradnak.
* **Rétegzett kötetek:** Az ezekben a kötetekben lévő adatok kiömlehetnek a felhőbe.

Az archiválási kötet a rétegzett kötetek egy típusa. Az archiválási kötetekhez használt nagyobb deduplikációs adattömbméret lehetővé teszi, hogy az eszköz nagyobb adatszegmenseket továbbítson a felhőbe.

Szükség esetén módosíthatja a kötet típusát helyiről rétegzettre vagy rétegzettről helyire. További információ: [A kötettípus módosítása](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Helyileg rögzített kötetek

A helyileg rögzített kötetek teljesen kiépített kötetek, amelyek nem rétegezik az adatokat a felhőbe, így helyi garanciákat biztosítanak az elsődleges adatok számára, függetlenül a felhőalapú kapcsolattól. A helyileg rögzített köteteken lévő adatok nem deduplikált és tömörített; azonban a helyileg rögzített kötetek pillanatképei deduplikálva lesznek. 

A helyileg rögzített kötetek teljesen ki vannak építve; ezért a létrehozáskor elegendő hellyel kell rendelkeznie a készüléken. A StorSimple 8100-as eszközön legfeljebb 8 TB méretű, a 8600-as eszközön pedig 20 TB méretű helyileg rögzített köteteket építhet ki. A StorSimple lefoglalja a fennmaradó helyi területet az eszközön a pillanatképek, a metaadatok és az adatfeldolgozás számára. A helyileg rögzített kötet méretét növelheti a rendelkezésre álló maximális helyre, de a kötet eklétrehozása után nem csökkentheti a kötet méretét.

Helyileg rögzített kötet létrehozásakor a rétegzett kötetek létrehozásához rendelkezésre álló terület csökken. Ennek fordítottja is igaz: ha meglévő rétegzett kötetek, a rendelkezésre álló hely a helyileg rögzített kötetek létrehozása alacsonyabb lesz, mint a fent megadott maximális korlátok. A helyi kötetekről a [helyileg rögzített kötetekkel kapcsolatos gyakori kérdésekről](storsimple-8000-local-volume-faq.md)talál.

### <a name="tiered-volumes"></a>Rétegzett kötetek

Rétegzett kötetek vékonyan kiépített kötetek, ahol a gyakran használt adatok helyi marad az eszközön, és a ritkábban használt adatok automatikusan rétegzett a felhőbe. A vékony kiépítés egy virtualizációs technológia, amelyben a rendelkezésre álló tárterület láthatóan meghaladja a fizikai erőforrásokat. Ahelyett, hogy elegendő tárhelyet előre lefoglalna, a StorSimple vékony kiépítést használ, hogy csak elegendő helyet foglaljon le az aktuális igényeknek megfelelően. A felhőalapú tárolás rugalmas jellege megkönnyíti ezt a megközelítést, mivel a StorSimple növelheti vagy csökkentheti a felhőalapú tárolást a változó igények nek megfelelően.

Ha a rétegzett kötetet archiválási adatokhoz használja, jelölje be a **Kötet használata a ritkábban használt archiválási adatokhoz** jelölőnégyzetet, ha a kötet deduplikációs adattömbméretét 512 KB-ra szeretné módosítani. Ha nem választja be ezt a beállítást, a megfelelő rétegzett kötet 64 KB-os adattömbméretet fog használni. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.


### <a name="provisioned-capacity"></a>Kiépített kapacitás

Az egyes eszközök és kötettípusok maximális kiosztott kapacitását az alábbi táblázatban tájékozott. (Vegye figyelembe, hogy a helyileg rögzített kötetek nem érhetők el virtuális eszközön.)

|  | Maximális rétegzett kötetméret | Maximális helyileg rögzített kötetméret |
| --- | --- | --- |
| **Fizikai eszközök** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuális eszközök** | | |
| 8010 |30 TB |N/A |
| 8020 |64 TB |N/A |

## <a name="the-volumes-blade"></a>A kötetek panel

A **Kötetek** panel lehetővé teszi a microsoft Azure StorSimple-eszközön a kezdeményezői (kiszolgálók) számára kiépített tárolókötetek kezelését. A szolgáltatáshoz csatlakoztatott StorSimple eszközök köteteinek listáját jeleníti meg.

 ![Kötetek lap](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

A kötet attribútumok sorozatából áll:

* **Kötet neve** – Egyedi nek és a kötet azonosításának segít. Ez a név a figyelési jelentésekben is használatos, ha egy adott kötetre szűr. A kötet létrehozása után nem nevezhető át.
* **Állapot** – Lehet online vagy offline. Ha egy kötet offline állapotban van, akkor az nem látható azon kezdeményezői (kiszolgálók) számára, amelyek számára engedélyezett a kötet használata.
* **Kapacitás** – megadja a kezdeményező (kiszolgáló) által tárolható adatok teljes mennyiségét. A helyileg rögzített kötetek teljesen ki vannak építve, és a StorSimple-eszközön találhatók. A rétegzett kötetek vékonyan vannak kiépítve, és az adatok duplikálva vannak. A vékonyan kiépített kötetek esetén az eszköz nem osztja le előre a fizikai tárolókapacitást belsőleg vagy a felhőben a konfigurált kötetkapacitásnak megfelelően. A kötetkapacitás igény szerint van lefoglalva és felhasználásra kerül.
* **Típus** – Azt jelzi, hogy a kötet **rétegzett** (alapértelmezett) vagy **helyileg rögzített.**

Az oktatóanyag utasításaival hajtsa végre a következő feladatokat:

* Kötet hozzáadása 
* Kötet módosítása 
* A kötettípus módosítása
* Kötet törlése 
* Kötet kapcsolat nélküli üzemmódba váltása 
* Kötet figyelése 

## <a name="add-a-volume"></a>Kötet hozzáadása

A StorSimple 8000 sorozatú eszköz telepítése során [létrehozott egy kötetet.](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) A kötet hozzáadása hasonló eljárás.

#### <a name="to-add-a-volume"></a>Kötet hozzáadása

1. Az **Eszközök** panelen szereplő eszközök táblázatos listájából válassza ki az eszközt. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Új kötet hozzáadása](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. A **Kötet hozzáadása** panelen:
   
    1. Az **Eszköz kiválasztása** mezőt a rendszer automatikusan kitölti a jelenlegi eszközzel.

    2. A legördülő listából válassza ki a kötettárolót, amelyhez kötetet szeretne adni.

    3.  Adja meg a kötet **Nevét**. A kötet létrehozása után nem nevezhető át a kötet.

    4. A legördülő listából adja meg a **Típus** beállítást a kötethez. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **Helyileg rögzített** kötetet. Minden más adathoz válasszon **Rétegzett** kötetet. Ha archív adatokhoz használja ezt a kötetet, jelölje be a **Kötet használata ritkábban használt archív adatokhoz** beállítást.
      
       A rétegzett kötetek kiosztása dinamikus, ezért gyorsan létrehozhatók. Archív adatokhoz használt rétegzett kötet esetében a **Kötet használata ritkábban használt archív adatokhoz** beállítás bejelölésével 512 KB-ra módosíthatja a kötet deduplikációs adattömbméretét. Ha ez a mező nincs bejelölve, az érintett rétegzett kötet 64 KB-os adattömbméretet használ. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.
       
       Egy helyileg rögzített kötet kiosztása állandó, és biztosítja, hogy a kötet elsődleges adatai helyben tárolódnak az eszközön, és nem kerülnek a felhőbe.  Ha gyors helyi kötetet hoz létre, az eszköz ellenőrzi a helyi rétegeken elérhető tárhelyet a kívánt méretű kötet kiosztásához. A helyileg rögzített kötet létrehozásához hozzátartozhat az eszközön levő adatok felhőbe küldése is, így a kötet létrehozása hosszú időt vehet igénybe. A teljes időtartam a kiosztott kötet méretétől, az elérhető hálózati sávszélességtől és az eszközön lévő adatoktól függ.

    5. Adja meg a kötethez tartozó **Kiosztott kapacitást**. Jegyezze fel a kiválasztott kötettípus alapján elérhető kapacitást. A megadott kötetméret nem haladhatja meg a rendelkezésre álló területet.
      
       A 8100-as eszközön gyors helyi kötetekhez legfeljebb 8,5 TB, rétegzett kötetekhez legfeljebb 200 TB területet oszthat ki. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki. A rétegzett kötetek munkakészletének üzemeltetéséhez helyi terület szükséges az eszközön, ezért a gyors helyi kötetek létrehozása hatással van a rétegzett kötetek kiosztásához rendelkezésre álló területre. Ha tehát helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület lecsökken. Ha rétegzett kötetet hoz létre, akkor ehhez hasonlóan a gyors helyi kötetek létrehozásához rendelkezésre álló terület csökken.
      
       Ha a 8100-as eszközön kioszt egy 8,5 TB-os (vagyis a legnagyobb megengedett méretű) gyors helyi kötetet, azzal elfoglalja az összes rendelkezésre álló helyi területet az eszközön. Innentől kezdve nem hozhat létre további rétegzett köteteket, mivel nincs helyi terület az eszközön, amely üzemeltethetné a rétegzett kötet munkakészletét. A meglévő rétegzett kötetek is hatással vannak a rendelkezésre álló területre. Ha például egy 8100-as eszközhöz már tartozik körülbelül 106 TB rétegzett kötet, akkor csak 4 TB érhető el a gyors helyi kötetekhez.

    6. Kattintson a nyílra a **Csatlakoztatott gazdagépek** mezőben. A **Csatlakoztatott állomások** panelen válasszon egy meglévő ACR-t, vagy adjon hozzá egy új ACR-t. Ha új ACR-t választ, majd megadja az ACR **nevét,** adja meg a Windows-állomás **iSCSI minősített nevét** (IQN). Ha nem tudja az IQN-t, lépjen a Windows Server-gazdagép IQN nevének lekérése szakaszhoz. Kattintson **a Létrehozás gombra.** Létrejön egy kötet a megadott beállításokkal.

        ![Kattintson a Létrehozás gombra](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Az új kötet készen áll a használatra.

> [!NOTE]
> Ha létrehoz egy helyileg rögzített kötetet, majd közvetlenül utána létrehoz egy másik helyileg rögzített kötetet, a kötetlétrehozási feladatok egymás után futnak. Az első kötetlétrehozási feladatnak be kell fejeződnie, mielőtt a következő kötetlétrehozási feladat elkezdődhetne.

## <a name="modify-a-volume"></a>Kötet módosítása

Módosítsa a kötetet, ha ki kell bontania, vagy módosítania kell a kötetet elérő állomásokat.

> [!IMPORTANT]
> * Ha módosítja a kötet méretét az eszközön, a kötet méretét is módosítani kell az állomáson.
> * Az itt ismertetett állomásoldali lépések a Windows Server 2012 (2012R2) rendszerre szolgálnak. A Linux vagy más gazdaoperációs rendszerek relevandai eljárásai eltérőek lesznek. Ha egy másik operációs rendszert futtató gazdagépen módosítja a kötetet, olvassa el a gazdagép utasításait.

#### <a name="to-modify-a-volume"></a>Kötet módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amely rendelkezik a módosítani kívánt kötettel. Kattintson **a Beállítások > kötetek gombra.**

    ![Ugrás a Kötetek panelre](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza a **Kapcsolat nélküli mód kiválasztása** lehetőséget, ha a módosítandó kötetet offline állapotba szeretné helyezni.

    ![Hangerő kiválasztása és forgalomkapcsolat nélküli használata](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Az **Offline mód** kiválasztása panelen tekintse át a kötet offline állapotba hozásának hatását, és jelölje be a megfelelő jelölőnégyzetet. Győződjön meg arról, hogy a megfelelő kötet az állomáson először offline állapotban van. A StorSimple-hez csatlakoztatott gazdakiszolgálón a kötetek offline állapotba hozásáról az operációs rendszerre vonatkozó utasításokban tájékozódhat. Kattintson **a Kapcsolat nélküli mód**ra .

    ![A kötet offline állapotba vételének hatásának áttekintése](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Miután a kötet offline állapotba került (ahogy azt a kötet állapota mutatja), válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza **a Hangerő módosítása**lehetőséget.

    ![Kötet módosítása](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. A **Kötet módosítása** panelen a következő módosításokat hajthatja végre:
   
   1. A kötet **neve** nem szerkeszthető.
   2. A **típus** átalakítása helyileg rögzítettről rétegzettre vagy rétegzettről helyileg rögzítettre (további [információ: Kötettípus módosítása).](#change-the-volume-type)
   3. A **kiosztott kapacitás növelése**. A **kiépített kapacitás** csak növelhető. Létrehozása után nem zsugorítható kötet.
   4. A **Csatlakoztatott állomások**csoportban módosíthatja az ACR-t. Az ACR módosításához a kötetnek offline állapotban kell lennie.

       ![A kötet offline állapotba vételének hatásának áttekintése](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. A módosítások mentéséhez kattintson a **Mentés** gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Az Azure Portal on egy frissítő kötet üzenetet jelenít meg. A kötet sikeres frissítése után sikeres üzenetet jelenít meg.

    ![A kötet offline állapotba vételének hatásának áttekintése](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Ha kötetet bővít, hajtsa végre az alábbi lépéseket a Windows gazdaszámítógépén:
   
   1. Nyissa meg a Számítógép-kezelés lemezkezelése **(Számítógép-kezelés)** ->**lemezkezelés e-ezt.**
   2. Kattintson a jobb gombbal **a Lemezkezelés elemre,** és válassza **a Lemezek újraberendezése parancsot.**
   3. A lemezek listájában jelölje ki a frissített kötetet, kattintson a jobb gombbal, majd válassza **a Kötet kiterjesztése parancsot.** Elindul a Kötet meghosszabbítása varázsló. Kattintson a **Tovább** gombra.
   4. Töltse ki a varázslót az alapértelmezett értékek elfogadásával. A varázsló befejezése után a kötetnek meg kell jelennie a nagyobb méretnek.
      
      > [!NOTE]
      > Ha kibont egy helyileg rögzített kötetet, majd közvetlenül utána egy másik helyileg rögzített kötetet bont ki, a kötetbővítési feladatok egymás után futnak. Az első kötetbővítési feladatnak be kell fejeződnie, mielőtt a következő kötetbővítési feladat elkezdődhetne.
      

## <a name="change-the-volume-type"></a>A kötettípus módosítása

Módosíthatja a kötet típusát rétegzettről helyileg rögzítettre vagy helyileg rögzítettrétegzettről. Ez az átalakítás azonban nem lehet gyakori.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Helyi kötet-átalakítási szempontoknak megfelelően felvan egészülve

A kötetek rétegzettről helyileg rögzítettre történő átalakításának néhány oka a következő:

* Helyi garanciák az adatok rendelkezésre állásával és teljesítményével kapcsolatban
* A felhőbeli késések és a felhőbeli kapcsolódási problémák kiküszöbölése.

Ezek általában kis meglévő kötetek, amelyeket gyakran szeretne elérni. A helyileg rögzített kötet létrehozásakor teljesen ki van építve. 

Ha egy rétegzett kötetet helyileg rögzített kötetté konvertál, a StorSimple ellenőrzi, hogy elegendő hely áll-e rendelkezésre az eszközön a konvertálás megkezdése előtt. Ha nincs elegendő hely, hibaüzenetet kap, és a művelet megszakad. 

> [!NOTE]
> Mielőtt elkezdené a többszintűről a helyileg rögzítettre történő átalakítást, vegye figyelembe a többi számítási feladat helyigényét. 

A rétegzett kötetről helyileg rögzített kötetre való átalakítás hátrányosan befolyásolhatja az eszköz teljesítményét. Ezenkívül a következő tényezők növelhetik az átalakítás befejezéséhez szükséges időt:

* Nincs elegendő sávszélesség.
* Nincs aktuális biztonsági mentés.

A tényezők hatásainak minimalizálása érdekében:

* Tekintse át a sávszélesség-szabályozási házirendeket, és győződjön meg arról, hogy egy dedikált 40 Mb/s sávszélesség áll rendelkezésre.
* Az átalakítás ütemezése csúcsidőn kívülre.
* Az átalakítás megkezdése előtt készíthet felhőbeli pillanatképet.

Ha több kötetet konvertál (különböző számítási feladatokat támogat), akkor a kötetkonvertálást kell előnyben részesíteni, hogy először a nagyobb prioritású kötetek konvertálódása legyen. Például konvertálnia kell a virtuális gépeket (VM-eket) vagy az SQL-munkaterheléssel rendelkező köteteket, mielőtt fájlmegosztási számítási feladatokat tartalmazó köteteket konvertálna.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Helyi– többszintű kötet-átalakítási szempontok

Előfordulhat, hogy módosítani szeretné a helyileg rögzített kötetet egy rétegzett kötetre, ha további helyre van szüksége más kötetek kiépítéséhez. Ha a helyileg rögzített kötetet rétegzettre konvertálja, az eszközön rendelkezésre álló kapacitás a felszabaduló kapacitás méretével nő. Ha a kapcsolódási problémák megakadályozzák egy kötet helyi típusról rétegzett típusra történő átalakítását, a helyi kötet a konvertálás befejezéséig egy rétegzett kötet tulajdonságait fogja mutatni. Ennek az az oka, hogy egyes adatok a felhőbe kerülhettek. A kiömlött adatok továbbra is helyi helyet foglalnak el az eszközön, amely nem szabadítható fel, amíg a művelet újra nem indul és nem fejeződik be.

> [!NOTE]
> A kötet ekkonvertálása eltarthat egy ideig, és a konvertálás nem szakítható meg az indítás után. A kötet online marad az átalakítás során, és biztonsági mentéseket készíthet, de nem bonthatja ki vagy állíthatja vissza a kötetet az átalakítás ideje alatt.


#### <a name="to-change-the-volume-type"></a>A kötettípus módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amely rendelkezik a módosítani kívánt kötettel. Kattintson **a Beállítások > kötetek gombra.**

    ![Ugrás a Kötetek panelre](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza **a Módosítás**lehetőséget.

    ![Módosítás kiválasztása a helyi menüből](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. A **Kötet módosítása** panelen módosítsa a kötet típusát a **Típus** legördülő listából az új típus kiválasztásával.
   
   * Ha a típus **helyileg rögzített,** StorSimple ellenőrzi, hogy van-e elegendő kapacitás.
   * Ha a típust **rétegzettre módosítja,** és ezt a kötetet fogja használni az archiválási adatokhoz, jelölje be a **Kötet használata a ritkábban használt archiválási adatokhoz** jelölőnégyzetet.
   * Ha egy helyileg rögzített kötetet rétegzettként vagy _fordítva_konfigurál, a következő üzenet jelenik meg.
   
     ![Kötettípus-üzenet módosítása](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kattintson a **Mentés** gombra a módosítások mentéséhez. Amikor megerősítést kér, kattintson az **Igen** gombra a konverziós folyamat elindításához. 

    ![Mentés és megerősítés](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Az Azure Portal on megjelenik egy értesítés a feladat létrehozásáról, amely frissíti a kötetet. Kattintson az értesítésre a kötetkonverziós feladat állapotának figyeléséhez.

    ![Feladat a volumenátalakításhoz](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Kötet kapcsolat nélküli üzemmódba váltása

Előfordulhat, hogy egy kötetet offline állapotba kell helyeznie, amikor módosítani vagy törölni szeretné a kötetet. Ha egy kötet offline állapotban van, nem érhető el írási és írási hozzáféréssel. A kötetet offline állapotba kell helyeznie a gazdagépen és az eszközön.

#### <a name="to-take-a-volume-offline"></a>Kötet kapcsolat nélküli üzemmódba váltása

1. Mielőtt offline állapotba hozná, győződjön meg arról, hogy a kérdéses kötet nincs használatban.
2. Először a kötetet állítsa offline állapotba a gazdagépen. Ez kiküszöböli a kötet adatsérülésének lehetséges kockázatát. A konkrét lépéseket a gazdaoperációs rendszerre vonatkozó utasításokban találja.
3. Miután a gazdagép offline állapotba került, a következő lépések végrehajtásával offline állapotba helyezi az eszközön lévő kötetet:
   
    1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amely rendelkezik a módosítani kívánt kötettel. Kattintson **a Beállítások > kötetek gombra.**

        ![Ugrás a Kötetek panelre](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza a **Kapcsolat nélküli mód kiválasztása** lehetőséget, ha a módosítandó kötetet offline állapotba szeretné helyezni.

        ![Hangerő kiválasztása és forgalomkapcsolat nélküli használata](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Az **Offline mód** kiválasztása panelen tekintse át a kötet offline állapotba hozásának hatását, és jelölje be a megfelelő jelölőnégyzetet. Kattintson **a Kapcsolat nélküli mód**ra . 

    ![A kötet offline állapotba vételének hatásának áttekintése](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Értesítést kap, ha a kötet offline állapotban van. A kötet állapota offline állapotra is frissül.
      
4. Ha egy kötet offline állapotban van, és a jobb gombbal kattint, a Helyi menüben elérhetővé válik az **Online állapot** megválasztása lehetőség.

> [!NOTE]
> Az **Offline mód ba parancs** kérést küld az eszköznek, hogy a kötetet offline állapotba kell helyezni. Ha az állomások továbbra is használják a kötetet, az megszakadt kapcsolatokat eredményez, de a kötet offline állapotba helyezése nem fog sikertelen.

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> A kötetet csak akkor törölheti, ha az offline állapotban van.

Kötet törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amely rendelkezik a módosítani kívánt kötettel. Kattintson **a Beállítások > kötetek gombra.**

    ![Ugrás a Kötetek panelre](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Ellenőrizze a törölni kívánt kötet állapotát. Ha a törölni kívánt kötet nem offline állapotú, először tegye offline állapotba. Kövesse a [Kötet offline állapotba helyezése](#take-a-volume-offline)című rész lépéseit.
4. Miután a kötet offline állapotban van, jelölje ki a kötetet, kattintson a jobb gombbal a helyi menü meghívásához, majd válassza a **Törlés parancsot.**

    ![Törlés kiválasztása a helyi menüből](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. A **Delete** panelen tekintse át és jelölje be a jelölőnégyzetet a kötet törlésének hatása ellen. Kötet törlésekor a köteten található összes adat elvész. 

    ![Módosítások mentése és megerősítése](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. A kötet törlése után a kötetek táblázatos listája frissül a törlés jelzésére.

    ![Frissített kötetlista](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Ha helyileg rögzített kötetet töröl, előfordulhat, hogy az új kötetek számára rendelkezésre álló terület nem frissül azonnal. A StorSimple Eszközkezelő szolgáltatás rendszeres időközönként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy várjon néhány percet, mielőtt megpróbálná létrehozni az új kötetet.
   >
   > Ezenkívül ha töröl egy helyileg rögzített kötetet, majd közvetlenül utána egy másik helyileg rögzített kötetet töröl, a kötettörlési feladatok egymás után futnak. Az első kötettörlési feladatnak be kell fejeződnie, mielőtt a következő kötettörlési feladat elkezdődhetne.

## <a name="monitor-a-volume"></a>Kötet figyelése

A kötetfigyelés lehetővé teszi a kötetI/O-val kapcsolatos statisztikák gyűjtését. A figyelés alapértelmezés szerint engedélyezve van az első 32 létrehozott köteten. A további kötetek figyelése alapértelmezés szerint le van tiltva. 

> [!NOTE]
> A klónozott kötetek figyelése alapértelmezés szerint le van tiltva.


A kötet figyelésének engedélyezéséhez vagy letiltásához hajtsa végre az alábbi lépéseket.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Kötetfigyelés engedélyezése vagy letiltása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki azt az eszközt, amely rendelkezik a módosítani kívánt kötettel. Kattintson **a Beállítások > kötetek gombra.**
2. A kötetek táblázatos listájából válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza **a Módosítás**lehetőséget.
3. A **Kötet módosítása** panelen a **Figyelés** csoportban válassza **az Engedélyezés** vagy **a Letiltás** lehetőséget a figyelés engedélyezéséhez vagy letiltásához.

    ![Monitorozás letiltása](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kattintson a **Mentés** gombra, és amikor megerősítést kér, kattintson az **Igen**gombra. Az Azure Portalon megjelenik egy értesítés a kötet frissítéséről, majd egy sikeres üzenetet, miután a kötet sikeresen frissült.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [klónozhat egy StorSimple kötetet.](storsimple-8000-clone-volume-u2.md)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)

