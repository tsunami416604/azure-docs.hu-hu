---
title: A StorSimple-kötetek (Aktualizace 3) kezelése |} A Microsoft Docs
description: Azt ismerteti, hogyan hozzáadása, módosítása, figyelheti és törölni a StorSimple-köteteket, és hogyan offline állapotba őket szükség esetén.
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
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f7bfe41b4cdc9989c6b949011bc240275886b6f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093330"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>A StorSimple-Eszközkezelő szolgáltatás használata kezelheti a kötetek (Update 3 vagy újabb)

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti, hogyan hozhat létre és kezelhet a kötetek a StorSimple 8000-es sorozatú eszközökön Update 3 és újabb verzióit futtató a StorSimple-Eszközkezelő szolgáltatásban.

A StorSimple-Eszközkezelő szolgáltatásban egy bővítmény az Azure Portalon, amely lehetővé teszi, hogy a StorSimple megoldás egy közös webes felületről kezelheti. Az Azure portal segítségével kezelheti a kötetek az eszközön. Is is létrehozása és kezelése a StorSimple-szolgáltatások, eszközök, a biztonsági mentési szabályzatok és a biztonságimásolat-katalógus kezelése és megtekintheti a riasztásokat.

## <a name="volume-types"></a>Kötettípusok

A StorSimple-kötetek lehetnek:

* **A helyileg rögzített kötetekről**: Ezeken a köteteken lévő adatok mindig a helyi StorSimple-eszköz marad.
* **A rétegzett kötetek**: Ezeken a köteteken lévő adatok a felhőben is kerülnek.

Az archív kötetek olyan rétegzett kötet. Az archív kötetek esetén használt nagyobb deduplikációs adattömbméret lehetővé teszi, hogy az eszköz, nagyobb méretű szegmenseket, az adatok átvitelét a felhőbe.

Ha szükséges, módosíthatja a kötet rétegzett típusát a helyi vagy a rétegzett a helyi számítógépre. További információért ugorjon [a kötet típusának módosításához](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>A gyors helyi kötetek

Helyileg rögzített kötetek teljesen kiosztott köteteket, amelyeket nem csomag adatait a felhőbe, ezzel biztosítva a helyi garantálja, hogy az elsődleges adatokat, független a felhővel. Az adatok gyors helyi kötetek nem deduplikált és tömörített; azonban a helyileg rögzített kötetek pillanatképek deduplikálásának. 

A gyors helyi kötetek teljes felhasznált; Ezért kell rendelkeznie elég hely az eszközön azok létrehozásakor. Helyileg rögzített kötetekhez legfeljebb 8 TB-ra a StorSimple 8100 sorozatú eszköz és a 8600-as eszközön 20 TB-os maximális mérettel építhető ki. A StorSimple fenntartja a fennmaradó helyi terület az eszközön a pillanatképek, a metaadatokat és az adatok feldolgozása. Egy helyileg rögzített kötet a rendelkezésre álló maximális lemezterület méretének növeléséhez, de Miután létrehozott egy kötet mérete nem csökkenthető.

Ha helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület csökken. Ennek az ellenkezője is igaz: Ha a meglévő rétegzett kötetek, a szabad lemezterületet az létrehozása helyileg rögzített kötetekről alacsonyabb, mint a fenti korlátozásokat lesz. További információ a helyi köteteken, tekintse meg a [gyakori kérdések a helyileg rögzített kötetekhez](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Rétegzett kötetek

Rétegzett kötetek dinamikusan kiosztott köteteket, amelyben a gyakran használt adatok tárolódnak az eszközön, és a felhőbe automatikusan rétegzett a ritkábban használt adatok. A dinamikus kiosztás egy virtualizációs technológia, amely rendelkezésre álló tár meghaladja a fizikai erőforrásokat jelenik meg. Ahelyett, hogy elegendő tárhely az előzetesen lefoglalását, a StorSimple használja a dinamikus kiosztás aktuális követelményeinek éppen elegendő lemezterületet foglaljon le. Rugalmas jellegének köszönhetően felhőalapú tárolás Ez a megközelítés lehetővé teszi, mert StorSimple növelheti vagy csökkentheti a változó igényekkel összhangban a felhőalapú tárolás.

Ha a rétegzett kötetet archív adatokhoz használ, válassza ki a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzet bejelölésével módosíthatja a a kötet deduplikációs adattömbméret 512 KB. Ha nem ezt a beállítást, a megfelelő rétegzett kötet 64 KB-os adattömbméretet fogja használni. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.


### <a name="provisioned-capacity"></a>Kiosztott kapacitást

Tekintse meg a következő táblázat tartalmazza az egyes eszköz- és a maximális kiosztott kapacitást. (Vegye figyelembe, hogy a gyors helyi kötetek nem érhetők el a virtuális eszközön.)

|  | A rétegzett kötetek maximális mérete | Maximális helyileg rögzített kötet mérete |
| --- | --- | --- |
| **Fizikai eszközök** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuális eszközök** | | |
| 8010 |30 TB |– |
| 8020 |64 TB |– |

## <a name="the-volumes-blade"></a>A kötetek panel

A **kötetek** panel lehetővé teszi, hogy a tároló kötetek, a Microsoft Azure StorSimple eszközön a kezdeményezőktől (kiszolgálóktól) az üzembe helyezett kezelése. A StorSimple eszköz csatlakozik a szolgáltatáshoz a kötetek listáját megjeleníti.

 ![Kötetek lap](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Egy kötet attribútumainak áll:

* **Kötet neve** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a kötetet. Ez a név monitoring jelentések, ha egy adott köteten is szerepel. A kötet létrehozása után nem lehet átnevezni.
* **Állapot** – lehet online vagy offline állapotban van. Ha a kötet offline állapotban van, akkor sem, melyek hozzáférhetnek a kötetet használja kezdeményezőktől (kiszolgálóktól) számára látható.
* **Kapacitás** – Itt adhatja meg a kezdeményező (kiszolgáló) által tárolható adatok teljes mennyisége. Helyileg rögzített kötetek teljes felhasznált, és a StorSimple eszközön találhatók. Rétegzett kötetek kiosztása és az adatok deduplikált. A dinamikusan kiosztott köteteket, az eszköz nem előre fizikai tárolókapacitás foglalása belső vagy a felhő megfelelően konfigurált kötet kapacitása. A kötet kapacitása lefoglalt és felhasznált igény szerint.
* **Típus** – azt jelzi, hogy a kötet **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.

Ebben az oktatóanyagban az utasítások segítségével a következő feladatokat:

* Kötet hozzáadása 
* Kötet módosítása 
* A kötet típusának módosítása
* Kötet törlése 
* A kötet offline állapotba helyezése 
* Megfigyelési 

## <a name="add-a-volume"></a>Kötet hozzáadása

Ön [létrehozott egy kötetet](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) a StorSimple 8000 sorozatú eszköz üzembe helyezése során. A kötet hozzáadása nem hasonló eljárást.

#### <a name="to-add-a-volume"></a>Kötet hozzáadása

1. Az **Eszközök** panelen szereplő eszközök táblázatos listájából válassza ki az eszközt. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Új kötet hozzáadása](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. A **Kötet hozzáadása** panelen:
   
    1. Az **Eszköz kiválasztása** mezőt a rendszer automatikusan kitölti a jelenlegi eszközzel.

    2. A legördülő listából válassza ki a kötettárolót, amelyhez kötetet szeretne adni.

    3.  Adja meg a kötet **Nevét**. Ha a kötet létrejött, a kötet nem nevezhető át.

    4. A legördülő listából adja meg a **Típus** beállítást a kötethez. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **Helyileg rögzített** kötetet. Minden más adathoz válasszon **Rétegzett** kötetet. Ha archív adatokhoz használja ezt a kötetet, jelölje be a **Kötet használata ritkábban használt archív adatokhoz** beállítást.
      
       A rétegzett kötetek kiosztása dinamikus, ezért gyorsan létrehozhatók. Archív adatokhoz használt rétegzett kötet esetében a **Kötet használata ritkábban használt archív adatokhoz** beállítás bejelölésével 512 KB-ra módosíthatja a kötet deduplikációs adattömbméretét. Ha ez a mező nincs bejelölve, az érintett rétegzett kötet 64 KB-os adattömbméretet használ. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.
       
       Egy helyileg rögzített kötet kiosztása állandó, és biztosítja, hogy a kötet elsődleges adatai helyben tárolódnak az eszközön, és nem kerülnek a felhőbe.  Ha gyors helyi kötetet hoz létre, az eszköz ellenőrzi a helyi rétegeken elérhető tárhelyet a kívánt méretű kötet kiosztásához. A helyileg rögzített kötet létrehozásához hozzátartozhat az eszközön levő adatok felhőbe küldése is, így a kötet létrehozása hosszú időt vehet igénybe. A teljes időtartam a kiosztott kötet méretétől, az elérhető hálózati sávszélességtől és az eszközön lévő adatoktól függ.

    5. Adja meg a kötethez tartozó **Kiosztott kapacitást**. Jegyezze fel a kiválasztott kötettípus alapján elérhető kapacitást. A megadott kötetméret nem haladhatja meg a rendelkezésre álló területet.
      
       A 8100-as eszközön gyors helyi kötetekhez legfeljebb 8,5 TB, rétegzett kötetekhez legfeljebb 200 TB területet oszthat ki. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki. A rétegzett kötetek munkakészletének üzemeltetéséhez helyi terület szükséges az eszközön, ezért a gyors helyi kötetek létrehozása hatással van a rétegzett kötetek kiosztásához rendelkezésre álló területre. Ha tehát helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület lecsökken. Ha rétegzett kötetet hoz létre, akkor ehhez hasonlóan a gyors helyi kötetek létrehozásához rendelkezésre álló terület csökken.
      
       Ha a 8100-as eszközön kioszt egy 8,5 TB-os (vagyis a legnagyobb megengedett méretű) gyors helyi kötetet, azzal elfoglalja az összes rendelkezésre álló helyi területet az eszközön. Innentől kezdve nem hozhat létre további rétegzett köteteket, mivel nincs helyi terület az eszközön, amely üzemeltethetné a rétegzett kötet munkakészletét. A meglévő rétegzett kötetek is hatással vannak a rendelkezésre álló területre. Ha például egy 8100-as eszközhöz már tartozik körülbelül 106 TB rétegzett kötet, akkor csak 4 TB érhető el a gyors helyi kötetekhez.

    6. Kattintson a nyílra a **Csatlakoztatott gazdagépek** mezőben. Az a **csatlakoztatott gazdagépek** panelen válasszon egy meglévő ACR-t, vagy adjon hozzá egy új ACR. Ha úgy dönt, hogy egy új ACR, majd adjon meg egy **neve** az ACR, adja meg a **iSCSI Qualified nevet** (IQN) a Windows-gazdagép. Ha nem rendelkezik az IQN-t, látogasson el a Windows Server-gazdagép IQN Nevének lekérése. Kattintson a **Create** (Létrehozás) gombra. Létrejön egy kötet a megadott beállításokkal.

        ![Kattintson a Létrehozás gombra](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Az új kötet már készen áll a használatra.

> [!NOTE]
> Ha helyileg rögzített kötet létrehozása, és majd egy másik helyileg rögzített kötet létrehozása azonnal ezt követően a feladatok egymás után futnak a kötet létrehozása. Az első kötet-létrehozási feladatot a következő feladat a kötet létrehozásának megkezdése előtt kell be.

## <a name="modify-a-volume"></a>Kötet módosítása

Bontsa ki azt, vagy módosítsa a gazdagépeket, amelyek elérni a kötetet kell egy kötet módosítása

> [!IMPORTANT]
> * Az eszközön a kötet méretének módosítása esetén a kötet mérete a gazdagépen módosítani kell.
> * A gazdagép-oldali lépéseket az itt leírtak szerint vannak a Windows Server 2012, (2012 R2). Eljárások a Linux rendszerű vagy más állomás operációs rendszerek eltérő lesz. Ha módosítja a kötet egy másik operációs rendszert futtató gazdagépen tekintse meg a gazdagép operációs rendszer utasításokat.

#### <a name="to-modify-a-volume"></a>Kötet módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

    ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. A kötetek táblázatos listájában, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza ki **offline állapotba** módosítani fogja a kötet offline állapotba.

    ![Válassza ki, és a kötet offline állapotba helyezése](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Az a **offline állapotba** panelen tekintse át a kötet offline állapotba a hatását, és válassza ki a megfelelő jelölőnégyzetet. Győződjön meg arról, hogy a gazdagép a megfelelő kötet offline először. A gazdakiszolgálón StorSimple csatlakozik egy kötetet offline állapotrúra kapcsolatos információkért tekintse meg az operációs rendszer konkrét utasításokat. Kattintson a **offline állapotba**.

    ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Miután a kötet offline (ahogy azt a kötet állapota), válassza ki a kötetet, és meghívni a helyi menüben kattintson a jobb gombbal. Válassza ki **a kötet módosításának**.

    ![Válassza ki a kötet módosítása](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Az a **a kötet módosításának** panelen, a következő módosításokat végezheti el:
   
   1. A kötet **neve** nem szerkeszthető.
   2. Konvertálja a **típus** a helyileg rögzített a rétegzett, vagy a rétegzett a helyileg rögzített (lásd: [a kötet típusának módosításához](#change-the-volume-type) további információt).
   3. Növelje a **kiosztott kapacitás**. A **kiosztott kapacitást** pedig csak növelni. A létrehozást követően a kötet nem zsugorítható.
   4. A **csatlakoztatott gazdagépek**, az ACR-REL módosíthatja. Módosítja egy ACR-t, a kötet offline állapotban kell lennie.

       ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kattintson a **mentése** a módosítások mentéséhez. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Az Azure Portalon egy frissítési mennyiségi üzenetet jelenít meg. A sikert jelző üzenet jelenik meg, ha a kötet frissítése sikeresen befejeződött.

    ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Ha egy kötet bővíteni, hajtsa végre az alábbi lépéseket a Windows-állomás számítógépen:
   
   1. Lépjen a **számítógép-kezelés** ->**lemez kezelése**.
   2. Kattintson a jobb gombbal **Lemezkezelés** válassza **lemezek újraellenőrzése**.
   3. A lemezek listája, válassza ki a kötetet, amelyet a frissített, kattintson a jobb gombbal, és válassza ki **kötet kiterjesztése**. A kötet kiterjesztése varázsló elindul. Kattintson a **tovább**.
   4. Fejezze be a varázslót, az alapértelmezett értékek elfogadásával. A varázsló befejezése után a kötet megnövelt mérete kell megjelennie.
      
      > [!NOTE]
      > Ha bontsa ki a helyileg rögzített kötetet, és bontsa ki egy másik helyileg rögzített kötet azonnal ezt követően a kötet bővítése feladatok egymás után futnak. Az első kötet bővítése feladat a következő kötet bővítése feladat megkezdése előtt kell be.
      

## <a name="change-the-volume-type"></a>A kötet típusának módosítása

Módosíthatja a kötettípus rétegzettről a helyileg rögzített, illetve a helyileg rögzített a rétegzett. Az átalakítás azonban nem lehet egy gyakori előfordulás.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Rétegzettről helyi kötet átalakítás kapcsolatos szempontok

Néhány ok a kötet konvertálása rétegzettről helyi rögzített a következők:

* Helyi garanciákat adatok rendelkezésre állását és teljesítményét
* Felhőbeli késéseket és a felhőbeli kapcsolódási problémák megszüntetését.

Ezek általában kicsi, gyakran használt meglévő kötetet. Egy helyileg rögzített kötet létrehozásakor teljes kiépítése. 

Ha egy rétegzett kötet a helyileg rögzített kötetet, a StorSimple áll rendelkezésre elegendő lemezterület az eszközön az átalakítás megkezdése előtt ellenőrzi. Ha nincs elég hely van, hibaüzenetet kap, és megszakítja a műveletet. 

> [!NOTE]
> A helyileg rögzített rétegzett a konvertálást megkezdése előtt győződjön meg arról, hogy érdemes-e a szükséges lemezterület a többi alkalmazáshoz és. 

A helyileg rögzített kötetet egy rétegzett történő átalakítás eszközteljesítmény kedvezőtlen hatással lehet. Ezenkívül a következő tényezőket növelheti az átalakítás végrehajtásához szükséges idő:

* Nincs elegendő sávszélesség.
* Nincs aktuális biztonsági mentés.

Minimalizálása érdekében, hogy előfordulhat, hogy ezek a tényezők:

* Tekintse át a sávszélesség-szabályozási házirendek, és győződjön meg arról, hogy egy dedikált 40 MB/s sávszélesség érhető el.
* Csúcsidőszakon kívüli órán keresztül az átalakítás ütemezése.
* Felhőbeli pillanatkép készítése, az átalakítás előtt.

Ha több kötet (támogatja a különböző számítási feladatok), majd Ön előnyben részesíti a kötet átalakítását, hogy a magasabb prioritású kötetek először konvertálja. Például alakítsuk üzemeltető virtuális gépek (VM) és az SQL-tevékenységprofilokhoz kötetek előtt, alakítsa át a köteteket a fájl megosztási számítási feladatokkal.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Helyi és rétegzett kötet átalakítás kapcsolatos szempontok

Előfordulhat, hogy szeretné módosítani egy helyileg rögzített kötet a rétegzett kötetek, ha más kötetek kiépítéséhez további tárterületre van szükség. Amikor címkévé alakít a helyileg rögzített kötet rétegzett, az eszköz növeli az elérhető kapacitás mérete a rendelkezésre álló kapacitást. Ha kapcsolódási problémák megakadályozzák a típusról egy kötet a helyi a rétegzett típusú, a helyi kötet mindaddig, amíg a konvertálás befejeződött a rétegzett kötet tulajdonságait a mutatnak. Ennek az oka néhány adatot lehet, hogy rendelkezik kiömlött a felhőbe. Ez kiömlött adatai továbbra is helyi területet az eszközön, amely nem szabadítható fel addig, amíg a művelet újraindul, és befejeződött foglalhat el.

> [!NOTE]
> Egy kötet konvertálása eltarthat egy ideig, és a egy átalakítás nem szakítható meg, miután elindult. A kötet online állapotban marad az átalakítás során és a biztonsági másolatok készítése, de nem kibontása vagy az átalakítás lefolyása közben a kötet visszaállítása.


#### <a name="to-change-the-volume-type"></a>A kötet típusának módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

    ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A kötetek táblázatos listájában, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza ki **módosítása**.

    ![Válassza ki a helyi menüből módosítása](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Az a **a kötet módosításának** panelen, a kötet típusának módosításához jelölje ki a új, a **típusa** legördülő listából válassza ki.
   
   * Ha módosítja a típus a **helyileg rögzített**, a StorSimple ellenőrzi, hogy van-e elegendő kapacitással.
   * Ha módosítja a típus a **rétegzett** és archiválási adatok, válassza ezt a kötetet lesz a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet.
   * Ha helyileg rögzített kötetet állítja be, a rétegzett vagy _fordítva_, a következő üzenet jelenik meg.
   
     ![Változás kötet típusa üzenet](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kattintson a **Mentés** gombra a módosítások mentéséhez. Amikor a rendszer megerősítést kér, kattintson a **Igen** az átalakítási folyamat elindításához. 

    ![Mentse és megerősítése](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Az Azure Portalon a feladat létrehozásához, amely a kötet frissítené értesítést jelenít meg. Kattintson az értesítésre, figyelheti a feladat állapotát.

    ![Feladat kötet átalakításhoz](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>A kötet offline állapotba helyezése

Szükség lehet egy kötetet offline állapotba, ha azt tervezi, vagy a kötet törlése. Ha egy kötetet offline állapotban, akkor nem érhető olvasási és írási hozzáférése. Meg kell tennie a kötetet offline állapotrúra a gazdagép és az eszközt.

#### <a name="to-take-a-volume-offline"></a>A kötet offline állapotba

1. Győződjön meg arról, hogy az adott kötet nem használja, offline állapotba helyezése előtt.
2. Állítsa offline állapotba a gazdagépen első. Ezzel elkerülhető, hogy a köteten adatsérülés lehetséges kockázatát. Tekintse meg az utasításokat a gazda operációs rendszer megadott lépéseket.
3. Miután a gazdagép offline állapotban van, végre a kötet offline módban az eszközt a következő lépések végrehajtásával:
   
    1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

        ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. A kötetek táblázatos listájában, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza ki **offline állapotba** módosítani fogja a kötet offline állapotba.

        ![Válassza ki, és a kötet offline állapotba helyezése](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Az a **offline állapotba** panelen tekintse át a kötet offline állapotba a hatását, és válassza ki a megfelelő jelölőnégyzetet. Kattintson a **offline állapotba**. 

    ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Értesítést kap a kötet offline állapotban. Offline is frissíti a kötet állapota.
      
4. Miután egy kötet offline állapotban, ha a kötet, és kattintson a jobb gombbal, válassza ki **online állapotba hozás** lehetőség válik elérhetővé a helyi menüben.

> [!NOTE]
> A **Offline állapotba** parancs egy kérést küld az eszköz a kötet offline állapotba. Ha a gazdagépek továbbra is használja a kötetet, ennek hatására a megszakadt kapcsolatok, de a kötet offline állapotba helyezése nem sikertelen lesz.

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> A kötet törölheti csak akkor, ha a kapcsolat nélküli üzemmódban.

A következő lépéseket egy kötet törlése.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

    ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A törölni kívánt kötet állapotának ellenőrzéséhez. Ha törölni szeretné a kötet nem offline állapotban van, a hálózatról, először. Kövesse a [egy kötetet offline állapotba](#take-a-volume-offline).
4. Után a kötet offline állapotban, válassza ki a kötetet, kattintson a jobb gombbal indítja el a helyi menüt, és válassza ki **törlése**.

    ![Helyi menüből válassza delete](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Az a **törlése** panelen tekintse át, és jelölje be a kötet törlésének következményeit. Egy kötet törlésekor a köteten lévő összes adat elvész. 

    ![Mentés és a módosítások megerősítése](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. A kötet törlését követően a kötetek táblázatos listájában frissíti törlését jelzi.

    ![Frissített kötet listája](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Ha töröl egy helyileg rögzített kötetet, az új kötetek rendelkezésre álló terület nem frissíthető közvetlenül. A StorSimple-Eszközkezelő szolgáltatás rendszeres időközönként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy, várjon néhány percet, mielőtt az új kötet létrehozásakor.
   >
   > Ezenkívül ha törli a helyileg rögzített kötetet, majd törölje a másikra helyileg rögzített kötet azonnal ezt követően a kötet törlési feladatok egymás után futnak. Az első törlési feladat a következő feladat a kötet törlésének megkezdése előtt kell be.

## <a name="monitor-a-volume"></a>Megfigyelési

A kötet figyelésének lehetővé teszi egy kötet I/O-hez kapcsolódó statisztikákat gyűjthet. Figyelés az Ön által létrehozott első 32 kötetek alapértelmezés szerint engedélyezve van. További kötetek figyelés alapértelmezés szerint le van tiltva. 

> [!NOTE]
> Klónozott kötet figyelés alapértelmezés szerint le van tiltva.


A következő lépésekkel engedélyezhető vagy tiltható le, a kötet figyelésének.

#### <a name="to-enable-or-disable-volume-monitoring"></a>A kötet figyelésének letiltása és engedélyezése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.
2. A kötetek táblázatos listájában, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívásához. Válassza ki **módosítása**.
3. Az a **a kötet módosításának** panelen a **figyelés** kiválasztása **engedélyezése** vagy **letiltása** engedélyezni vagy letiltani a figyelést.

    ![Monitorozás letiltása](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kattintson a **mentése** és amikor a rendszer megerősítést kér, kattintson a **Igen**. Az Azure Portalon a kötetet, majd egy sikert jelző üzenettel, frissítésére, a kötet sikeres frissítése után egy értesítés jeleníti meg.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [StorSimple-kötet klónozása](storsimple-8000-clone-volume-u2.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

