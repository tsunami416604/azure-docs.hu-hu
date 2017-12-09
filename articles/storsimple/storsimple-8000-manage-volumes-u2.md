---
title: "StorSimple-köteteket (Update 3) kezelése |} Microsoft Docs"
description: "Elmagyarázza, hozzáadása, módosítása, figyeléséhez, és törölje a StorSimple-köteteket, és hogyan offline állapotba rájuk szükség."
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
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: c9c575f42e6c8730b9404c62fb60e710d9d3bc80
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>A StorSimple Device Manager szolgáltatás segítségével kezelheti a kötetek (Update 3-as vagy újabb)

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti, hogyan a StorSimple Device Manager szolgáltatás létrehozását és kezelését a StorSimple 8000 sorozat eszközeire 3 és újabb verzióit futtató a frissítés köteteket.

A StorSimple Device Manager szolgáltatás nem egy bővítmény, az Azure portálon, amely lehetővé teszi a StorSimple megoldásban egy egyetlen webes felhasználói felületen keresztüli kezelését. Kötetek az eszközök kezelését az Azure-portál használatával. Akkor is létrehozása és a StorSimple-szolgáltatások kezelése, kezelheti az eszközöket, a biztonsági mentési házirendek és a biztonságimásolat-katalógus, és megtekintheti a riasztásokat.

## <a name="volume-types"></a>Kötet típusok

StorSimple-köteteket lehet:

* **Helyileg rögzített kötetek**: mindig a helyi StorSimple eszközön marad ezeken a köteteken lévő adatok.
* **Rétegzett kötet**: ezeken a köteteken lévő adatok kerülnek a felhőbe is.

Az archiválási köteten a rétegzett kötet típusú. Archiválási kötetek használt nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz nagyobb szegmensek az adatok átvitele a felhőben.

Ha szükséges, módosíthatja a kötet típusa helyi rétegzett vagy rétegzett helyi. További információkért látogasson el [kötet típusának módosítása](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Helyileg rögzített kötetek

Helyileg rögzített kötetek, amelyek nem réteg adatok összekapcsolása a felhővel teljesen kiosztott köteteket, ezzel biztosítva a helyi biztosítja, hogy az elsődleges adatok, a felhő kapcsolat független. A helyileg rögzített kötetekhez adatok nem deduplikált, és tömörített; azonban a pillanatképeket a helyileg rögzített kötetek deduplikálásának. 

Helyileg rögzített kötetekhez teljesen kiépített; ezért rendelkeznie kell elegendő lemezterület az eszközön a létrehozott. Helyileg rögzített kötetekhez legfeljebb 8 TB, a StorSimple 8100 eszközön és az 8600 eszközön 20 TB maximális méretén építhető ki. StorSimple fenntartja a fennmaradó helyi területet az eszközön a pillanatképek, a metaadatok és az adatok feldolgozása. A rendelkezésre álló maximális lemezterület a helyileg rögzített kötet mérete növelheti, de a létrehozását követően a kötet mérete nem csökkenthető.

Ha helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület csökken. Fordítva is igaz: Ha a meglévő rétegzett kötetek, a szabad lemezterületet az létrehozása helyileg rögzített kötetek alacsonyabb, mint a fent megadott maximális határok lesz. A helyi köteteken további információkért tekintse meg a [gyakran ismételt kérdések a helyileg rögzített kötetekhez](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Rétegzett kötetek

Rétegzett kötetek rendszer dinamikusan kiosztott köteteket a ritkábban használt adatokhoz marad az eszközön helyi és a felhő automatikusan rétegzett a ritkábban használt adatok. Dinamikus kiosztás egy virtualizációs technológia, amely rendelkezésre álló tár hosszabb legyen, mint a fizikai erőforrásokat jelenik meg. Ahelyett, hogy elegendő tárhely előre foglalással, StorSimple használja a dinamikus kiosztás aktuális követelményeknek éppen elegendő lemezterületet foglaljon le. Felhőalapú tárolás rugalmas jellege elősegíti ezt a módszert használja, mert StorSimple növelheti vagy csökkentheti a felhőalapú tárolást tartalomkérései változó igényeinek kielégítéséhez.

Ha a rétegzett kötetet archív adatokhoz használ, válassza ki a **kötet használata ritkábban használt archív adatokhoz** melletti jelölőnégyzetet, hogy módosítsa a deduplikációs adattömbméret a kötethez tartozó 512 KB. Ha nem ezt a beállítást, a megfelelő rétegzett kötet 64 KB adattömbméretet fogja használni. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.


### <a name="provisioned-capacity"></a>Kiosztott kapacitást

Tekintse meg a következő táblázat az egyes eszköz és a kötet maximális kiosztott kapacitást. (Vegye figyelembe, hogy helyileg rögzített kötetekhez ne legyenek elérhetők a virtuális eszköz.)

|  | Maximális rétegzett kötet mérete | Maximális helyileg rögzített kötet mérete |
| --- | --- | --- |
| **Fizikai eszköz** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuális eszközök** | | |
| 8010 |30 TB |N/A |
| 8020 |64 TB |N/A |

## <a name="the-volumes-blade"></a>A kötetek panel

A **kötetek** panel lehetővé teszi a tárolókötetek törlődnek a Microsoft Azure StorSimple eszközön a kezdeményezőktől (kiszolgálóktól) való kezelését. A StorSimple eszköz a szolgáltatáshoz való kapcsolódás kötetek listáját megjeleníti.

 ![Kötetek lap](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

A kötet attribútumainak állnak:

* **Kötet neve** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a kötetet. Ez a név monitoring jelentések, amikor egy adott köteten szűrheti is szerepel. A kötet létrehozása után nem lehet átnevezni.
* **Állapot** – lehet online vagy offline állapotú. Ha egy kötet offline állapotban, nincs látható kezdeményezőktől (kiszolgálóktól), amelyek hozzáférhetnek a kötetet használja.
* **Kapacitás** – a kezdeményező (kiszolgáló) tárolt adatok teljes mennyisége határozza meg. Helyileg rögzített kötetek teljes törlődnek, és a StorSimple eszközön találhatók. Rétegzett kötetek kiosztása, és az adatok deduplikált. A dinamikusan kiosztott köteteket az eszköz nem előre lefoglalni fizikai tárolási kapacitás belső vagy a felhő megfelelően konfigurált kötet kapacitása. A kötet kapacitása lefoglalt és felhasznált igény szerint.
* **Típus** – azt jelzi, hogy a kötet **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.

Kövesse az utasításokat ebben az oktatóanyagban a következő feladatokat:

* Kötet hozzáadása 
* A kötet módosítása 
* Módosítsa a kötet típusa
* Kötet törlése 
* A kötet offline állapotba helyezése 
* A kötet figyelése 

## <a name="add-a-volume"></a>Kötet hozzáadása

Ön [kötet létrehozott](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) a StorSimple 8000 series eszköz telepítése során. Hasonló eljárással hozzáadna egy kötetet.

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

    6. Kattintson a nyílra a **Csatlakoztatott gazdagépek** mezőben. Az a **csatlakozó állomások** panelen válassza ki egy meglévő ACR, vagy adja hozzá egy új ACR. Ha úgy dönt, hogy egy új ACR, majd adjon meg egy **neve** az ACR, adja meg a **iSCSI Qualified Name** (IQN) a Windows-állomás. Ha nem tudja az IQN-t, lépjen a [Windows Server-gazdagép IQN nevének lekérése](#get-the-iqn-of-a-windows-server-host) szakaszhoz. Kattintson a **Létrehozás** gombra. Létrejön egy kötet a megadott beállításokkal.

        ![Kattintson a Létrehozás gombra](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Az új kötet már készen áll a használatra.

> [!NOTE]
> Ha helyileg rögzített kötet létrehozása, és majd hozzon létre egy másik helyileg rögzített kötet azonnal ezek után a kötetek létrehozását feladatok egymás után futnak. Az első kötet-létrehozási feladatot a következő kötet-létrehozási feladat megkezdése előtt kell befejezni.

## <a name="modify-a-volume"></a>A kötet módosítása

Bontsa ki, vagy módosítsa a gazdagépek, amelyek a kötethez kell egy kötet módosításával.

> [!IMPORTANT]
> * Ha módosítja a kötet méretét, az eszközön, a kötet mérete kell módosítani a gazdagépen.
> * A gazdagép-oldali itt leírt lépésekre a Windows Server 2012 (2012R2). Eljárások a Linux vagy más állomás operációs rendszerek eltérőek lesznek. Ha módosítja a kötet olyan operációs rendszert futtató másik gazdagépre tekintse meg a gazdagép operációs rendszer utasításokat.

#### <a name="to-modify-a-volume"></a>A kötet módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listája, jelölje ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

    ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. A kötetek táblázatos listája, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívni. Válassza ki **offline állapotba** módosítani fogja a kötet offline állapotba.

    ![Válassza ki, és helyezze offline állapotba a kötet](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Az a **offline állapotba** panelt, tekintse át a hatását, hogy a kötet offline állapotba helyezése, és válassza ki a megfelelő jelölőnégyzetet. Győződjön meg arról, hogy a megfelelő kötet a gazdagépen offline először. A gazdagép-kiszolgálón, a StorSimple csatlakozik egy kötet offline kapcsolatos információkért tekintse meg az operációs rendszer konkrét utasításokat. Kattintson a **offline állapotba**.

    ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Miután a kötet offline (ahogy a kötet állapot szerint), válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívni. Válassza ki **kötet módosítása**.

    ![Válassza ki a kötet módosítása](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Az a **kötet módosítása** panelen módosításokat végezheti el a következő:
   
   1. A kötet **neve** nem szerkeszthető.
   2. Alakítsa át a **típus** a helyileg rögzített a rétegzett vagy a rétegzett a helyileg rögzített (lásd: [kötet típusának módosítása](#change-the-volume-type) további információk).
   3. Növelje a **kapacitás kiosztása**. A **kiosztott kapacitást** pedig csak növelni. A kötet nem zsugorítható, létrehozás után.
   4. A **csatlakozó állomások**, módosíthatja a ACR. Egy ACR módosításához a kötet offline állapotban kell lennie.

       ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kattintson a **mentése** menti a módosításokat. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Az Azure portál egy frissítési kötet üzenetet jelenít meg. Egy üzenetet, jelenít meg, ha a kötet sikeresen frissítve.

    ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Ha egy kötet bővíti, végezze el az alábbi lépéseket a Windows-állomás számítógépen:
   
   1. Ugrás a **számítógép-kezelés** ->**felügyeleti lemez**.
   2. Kattintson a jobb gombbal **Lemezkezelés** válassza **lemezek újraellenőrzése**.
   3. A lemezek, jelölje ki a kötetet, amelyeket azért frissített, kattintson a jobb gombbal, és válassza **kötet kiterjesztése**. A kötet kiterjesztése varázsló elindul. Kattintson a **Tovább** gombra.
   4. Fejezze be a varázslót, az alapértelmezett értékek elfogadásával. A varázsló befejezése után, a kötet mérete nagyobb kell megjelenítenie.
      
      > [!NOTE]
      > Ha bontsa ki a helyileg rögzített kötet, és bontsa ki egy másik helyileg rögzített kötet közvetlenül ezek után a kötet bővítése feladatok egymás után futnak. A kötet bővítése első feladat a következő kötet bővítése feladat megkezdése előtt kell befejezni.
      

## <a name="change-the-volume-type"></a>Módosítsa a kötet típusa

Módosíthatja a kötet típusa a helyileg rögzített a rétegzett, vagy a helyileg rögzített a rétegzett. Ez a konverzió azonban nem lehet egy gyakori előfordulásainak kívánt számát.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Helyi kötet átalakítás megfontolandó rétegzett

Néhány ok, amiért a kötet alakításának a helyileg rögzített rétegzett vannak:

* Helyi garanciákat adatok rendelkezésre állását és teljesítményét
* Felhő késéseket és a felhő kapcsolódási problémák megszüntetését.

Ezek általában kicsi, amelyet gyakran használt meglévő kötetet. Egy helyileg rögzített kötet teljesen kiépítve, létrehozásakor. 

A rétegzett kötetek alakít át egy helyileg rögzített kötetet, ha a StorSimple ellenőrzi, hogy van elegendő hely az eszközön az átalakítás megkezdése előtt. Ha nincs elég hely van, hibaüzenetet kap, és megszakítja a műveletet. 

> [!NOTE]
> A helyileg rögzített rétegzett való átalakítás megkezdése előtt győződjön meg arról, hogy érdemes-e a lemezterület-igénye a többi munkaterhelését. 

A helyileg rögzített kötet egy rétegzett történő átalakítás kedvezőtlen hatással lehet a Teljesítmény eszköz. Emellett az alábbi tényezők növelheti a konvertálás befejezéséhez szükséges idő:

* Nincs elegendő sávszélesség.
* Nincs aktuális biztonsági másolat.

Ha csökkenteni szeretné a ezek a tényezők lehetnek:

* Tekintse át a sávszélesség-szabályozási házirendeket, és győződjön meg arról, hogy rendelkezésre áll-e a dedikált 40 MB/s sávszélességet.
* A konverzió kevesen ütemezni.
* A felhő pillanatképet az átalakítás előtt.

Ha több kötet (támogatja a különböző terhelésekhez), majd kell helyezi előtérbe a kötet átalakítás, hogy a magasabb prioritású kötetek először konvertálja. Például kell átalakítása üzemeltető virtuális gépek (VM) és az SQL-munkaterhelések kötetek fájl megosztási munkaterhelések kötetek átalakítása előtt.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Helyi rétegzett kötet átalakítás megfontolandó

Érdemes lehet módosítani egy helyileg rögzített kötet rétegzett kötetet, ha más kötetek kiépítéséhez további területre van szükség. A helyileg rögzített kötet rétegzett, az eszköz növeli az elérhető kapacitás mérete elérhető kapacitást alakításakor. Ha problémák megakadályozzák a kötet a helyi-típusra történő konvertálást rétegzett típusra, a helyi kötet virágot fog hozni egy rétegzett kötet tulajdonságainak csak az átalakítás befejeződése után. Ennek oka az, néhány adat előfordulhat, hogy rendelkezik kiömlött a felhőbe. Ezek az adatok kiömlött továbbra helyi terület az eszközön, amely nem szabadítható, amíg a művelet újra, és befejeződött.

> [!NOTE]
> Egy kötet konvertálása eltarthat egy ideig, és a konvertálás nem vethető el, miután elindult. A kötet online állapotban marad az átalakítás során és a biztonsági másolatok készítése, de nem kibontása vagy a kötet visszaállítása, az átalakítás közben.


#### <a name="to-change-the-volume-type"></a>A kötet típusa módosítása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listája, jelölje ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

    ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A kötetek táblázatos listája, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívni. Válassza ki **módosítása**.

    ![Válassza ki a helyi menüből módosítása](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. A a **kötet módosítása** panelen jelölje ki az új a kötet típusának módosítása a **típus** legördülő listában.
   
   * Ha módosítja a típus a **helyileg rögzített**, StorSimple ellenőrzi, hogy van-e elegendő kapacitása.
   * Ha módosítja a típus a **rétegzett** és archiválási adatok, jelölje be ezt a kötetet fog történni a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet.
   * Ha konfigurál egy helyileg rögzített kötetet, rétegzett vagy _viszont_, a következő üzenet jelenik meg.
   
    ![Változás kötet típus üzenet](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kattintson a **Mentés** gombra a módosítások mentéséhez. Amikor felszólítja a megerősítésre, kattintson **Igen** az átalakítási folyamat elindításához. 

    ![Mentse és megerősítése](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Az Azure-portál a feladat létrehozásához, amely a kötet frissítené értesítést jelenít meg. Kattintson az értesítésre kattinthat a kötet átalakítási feladat állapotának figyelésére.

    ![Kötet átalakítási feladat](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>A kötet offline állapotba helyezése

Szükség lehet egy kötet offline állapotba, ha azt tervezi, hogy módosítja vagy törli a kötetet. Ha egy kötet offline állapotban, nincs olvasási és írási hozzáférése érhető el. A kötet offline állapotba a gazdagép és az eszköz szükséges.

#### <a name="to-take-a-volume-offline"></a>A kötet offline állapotba

1. Győződjön meg arról, hogy az adott kötet nem offline állapotba helyezése előtt használatban van.
2. Igénybe offline állapotba a kötet a gazdagépen első. Ezzel a megoldással a köteten adatsérülés lehetséges kockázatát. Tekintse meg az utasításokat a gazda operációs rendszer megadott lépéseket.
3. Miután a gazdagép offline állapotban, igénybe a kötet offline állapotba az eszközön az alábbi lépések elvégzésével:
   
    1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listája, jelölje ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

        ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. A kötetek táblázatos listája, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívni. Válassza ki **offline állapotba** módosítani fogja a kötet offline állapotba.

        ![Válassza ki, és helyezze offline állapotba a kötet](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Az a **offline állapotba** panelt, tekintse át a hatását, hogy a kötet offline állapotba helyezése, és válassza ki a megfelelő jelölőnégyzetet. Kattintson a **offline állapotba**. 

    ![Tekintse át a kötet offline állapotba helyezése hatása](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Értesítést kap a kötet offline állapotban. A kötet állapota offline is frissíti.
      
4. Miután egy kötet nem érhető el, ha a kötet, és kattintson a jobb gombbal, **online állapotba hozás** beállítás akkor válik elérhetővé a helyi menü.

> [!NOTE]
> A **Offline állapotba** parancs kérést küld az eszközt, hogy a kötet offline állapotba. Gazdagépek még mindig használja a kötetet, ha az eredmény megszakadt kapcsolat, de a kötet offline állapotba helyezése nem sikertelen lesz.

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> Törölheti a kötet csak akkor, ha offline állapotban.

Az alábbi lépésekkel kötet törlése.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listája, jelölje ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.

    ![Ugrás a kötetek panel](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. A törölni kívánt kötet állapotának ellenőrzéséhez. Ha törli a kötet nem offline állapotban, offline állapotba először. Kövesse a [kötet offline állapotba](#take-a-volume-offline).
4. Után a kötet offline állapotban, jelölje ki a kötetet, kattintson a jobb gombbal a helyi menü meghívni, és jelölje ki **törlése**.

    ![Helyi menüből válassza törlése](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Az a **törlése** panelt, tekintse át, és jelölje be a kötet törlésekor hatását ellen. Ha töröl egy köteten, a köteten található összes adat elvész. 

    ![Mentse és módosításának megerősítése](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. A kötet törlését követően kötetek táblázatos listáját frissíti, a törlés jelzi.

    ![Frissített kötet listája](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Ha töröl egy helyileg rögzített kötetet, a szabad lemezterület az új kötetekre esetleg nem azonnal frissíti. A StorSimple Device Manager szolgáltatás rendszeres időközönként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy csak néhány perc elteltével az új kötet létrehozásakor várja.
   >
   > Emellett ha töröl egy helyileg rögzített kötet, és törölje a másikra helyileg rögzített kötet azonnal ezt követően, a kötet törlési feladatok egymás után futnak. Az első kötet törlési feladat a következő kötet törlési feladat megkezdése előtt kell befejezni.

## <a name="monitor-a-volume"></a>A kötet figyelése

Mennyiségi figyelési lehetővé teszi, hogy a kötet I/O-hez kapcsolódó Statisztika gyűjtése. Figyelés az Ön által létrehozott első 32 kötetekhez alapértelmezés szerint engedélyezve van. A további kötetek figyelés alapértelmezés szerint le van tiltva. 

> [!NOTE]
> A klónozott kötetek figyelés alapértelmezés szerint le van tiltva.


A következő lépésekkel engedélyezhető vagy tiltható le a kötet figyelését.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Engedélyezheti vagy tilthatja le a kötet figyelése

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listája, jelölje ki az eszközt, amely rendelkezik a kötetet, amelyet módosítani szeretne. Kattintson a **beállítások > kötetek**.
2. A kötetek táblázatos listája, válassza ki a kötetet, és kattintson a jobb gombbal a helyi menü meghívni. Válassza ki **módosítása**.
3. A a **kötet módosítása** panelen a **figyelés** kiválasztása **engedélyezése** vagy **tiltsa le a** engedélyezni vagy letiltani a figyelés.

    ![Tiltsa le a figyelése](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kattintson a **mentése** és megerősítést kér, amikor kattintson **Igen**. Az Azure-portálon frissítéséhez a kötetet és egy üzenetet, miután frissítése sikeres volt a kötet értesítést jelenít meg.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [klónozza a StorSimple-kötet](storsimple-8000-clone-volume-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

