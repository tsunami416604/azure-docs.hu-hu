---
title: "A StorSimple-köteteket (U2) kezelése |} Microsoft Docs"
description: "Elmagyarázza, hozzáadása, módosítása, figyeléséhez, és törölje a StorSimple-köteteket, és hogyan offline állapotba rájuk szükség."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 57896932-0aa5-4805-970c-d13403ae7551
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/28/2016
ms.author: alkohli
ms.openlocfilehash: a61c57cd74a0df8363648dd8df40e433b0e6489d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>A StorSimple Manager szolgáltatás használatával (2. frissítés) kötetek kezelése
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan a StorSimple Manager szolgáltatás létrehozásához és kezeléséhez a kötetet a StorSimple eszköz és a StorSimple virtuális eszköz a telepített frissítés 2.

A StorSimple Manager szolgáltatás a klasszikus Azure portálon, amely lehetővé teszi a StorSimple megoldásban egy egyetlen webes felhasználói felületen keresztüli kezelését bővítmény. Kötetek kezelése, valamint használhatja a StorSimple Manager szolgáltatás létrehozása és a StorSimple-szolgáltatások kezelése, megtekintése és eszközök kezeléséhez, megtekintheti a riasztásokat, és megtekintéséhez és kezeléséhez biztonsági mentési házirendek és a biztonságimásolat-katalógus.

## <a name="volume-types"></a>Kötet típusok
StorSimple-köteteket lehet:

* **Helyileg rögzített kötetek**: mindig a helyi StorSimple eszközön marad ezeken a köteteken lévő adatok.
* **Rétegzett kötet**: ezeken a köteteken lévő adatok kerülnek a felhőbe is.

Az archiválási köteten a rétegzett kötet típusú. Archiválási kötetek használt nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz nagyobb szegmensek az adatok átvitele a felhőben. 

Ha szükséges, módosíthatja a kötet típusa helyi rétegzett vagy rétegzett helyi. További információkért látogasson el [kötet típusának módosítása](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Helyileg rögzített kötetek
Helyileg rögzített kötetek, amelyek nem réteg adatok összekapcsolása a felhővel teljesen kiosztott köteteket, ezzel biztosítva a helyi biztosítja, hogy az elsődleges adatok, a felhő kapcsolat független. A helyileg rögzített kötetekhez adatok nem deduplikált, és tömörített; azonban a pillanatképeket a helyileg rögzített kötetek deduplikálásának. 

Helyileg rögzített kötetekhez teljesen kiépített; ezért rendelkeznie kell elegendő lemezterület az eszközön a létrehozott. Helyileg rögzített kötetekhez legfeljebb 8 TB, a StorSimple 8100 eszközön és az 8600 eszközön 20 TB maximális méretén építhető ki. StorSimple fenntartja a fennmaradó helyi területet az eszközön a pillanatképek, a metaadatok és az adatok feldolgozása. A rendelkezésre álló maximális lemezterület a helyileg rögzített kötet mérete növelheti, de a létrehozását követően a kötet mérete nem csökkenthető.

Ha helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület csökken. Fordítva is igaz: Ha a meglévő rétegzett kötetek, a szabad lemezterületet az létrehozása helyileg rögzített kötetek alacsonyabb, mint a fent megadott maximális határok lesz. A helyi köteteken további információkért tekintse meg a [gyakran ismételt kérdések a helyileg rögzített kötetekhez](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Rétegzett kötetek
Rétegzett kötetek rendszer dinamikusan kiosztott köteteket a ritkábban használt adatokhoz marad az eszközön helyi és a felhő automatikusan rétegzett a ritkábban használt adatok. Dinamikus kiosztás egy virtualizációs technológia, amely rendelkezésre álló tár hosszabb legyen, mint a fizikai erőforrásokat jelenik meg. Ahelyett, hogy elegendő tárhely előre foglalással, StorSimple használja a dinamikus kiosztás aktuális követelményeknek éppen elegendő lemezterületet foglaljon le. Felhőalapú tárolás rugalmas jellege elősegíti ezt a módszert használja, mert StorSimple növelheti vagy csökkentheti a felhőalapú tárolást tartalomkérései változó igényeinek kielégítéséhez.

Ha a rétegzett kötetet archív adatokhoz használ, válassza a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet vált az a kötet deduplikációs adattömbméret 512 KB. Ha nem ezt a beállítást, a megfelelő rétegzett kötet 64 KB adattömbméretet fogja használni. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.

> [!NOTE]
> A frissítés előtti StorSimple 2 verziójával létrehozott archiválási kötetek fog importálható, mert rétegzett az archiválási jelölőnégyzet bejelölésével.
> 
> 

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

## <a name="the-volumes-page"></a>A kötetek lap
A **kötetek** lap lehetővé teszi a tárolókötetek törlődnek a Microsoft Azure StorSimple eszközön a kezdeményezőktől (kiszolgálóktól) való kezelését. A StorSimple eszköz kötetek listáját megjeleníti.

 ![Kötetek lap](./media/storsimple-manage-volumes-u2/VolumePage.png)

A kötet attribútumainak állnak:

* **Kötet neve** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a kötetet. Ez a név monitoring jelentések, amikor egy adott köteten szűrheti is szerepel.
* **Állapot** – lehet online vagy offline állapotú. Ha egy kötet offline állapotban, nincs látható kezdeményezőktől (kiszolgálóktól), amelyek hozzáférhetnek a kötetet használja.
* **Kapacitás** – a kezdeményező (kiszolgáló) tárolt adatok teljes mennyisége határozza meg. Helyileg rögzített kötetek teljes törlődnek, és a StorSimple eszközön találhatók. Rétegzett kötetek kiosztása, és az adatok deduplikált. A dinamikusan kiosztott köteteket az eszköz nem előre lefoglalni fizikai tárolási kapacitás belső vagy a felhő megfelelően konfigurált kötet kapacitása. A kötet kapacitása lefoglalt és felhasznált igény szerint.
* **Típus** – azt jelzi, hogy a kötet **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.
* **Biztonsági mentési** – azt jelzi, hogy a kötet létezik alapértelmezett biztonsági mentési házirend.
* **Hozzáférés** – Itt adhatja meg, amelyek számára engedélyezett a kötet elérésének kezdeményezőktől (kiszolgálóktól). Kezdeményezők, amelyek nem tagjai a hozzáférés-vezérlési rekordot (ACR) a kötet társított nem jelenik meg a kötet.
* **Figyelési** – adja meg a kötet figyel-e. Egy kötet lesz figyelési létrehozásakor alapértelmezés szerint engedélyezett. Lesz, azonban figyelés, a kötet másolat le kell tiltani. Egy kötet figyelésének engedélyezéséhez kövesse az utasításokat a [kötet figyelése](#monitor-a-volume). 

Kövesse az utasításokat ebben az oktatóanyagban a következő feladatokat:

* Kötet hozzáadása 
* A kötet módosítása 
* Módosítsa a kötet típusa
* Kötet törlése 
* A kötet offline állapotba helyezése 
* A kötet figyelése 

## <a name="add-a-volume"></a>Kötet hozzáadása
Ön [kötet létrehozott](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) a StorSimple megoldás üzembe helyezése során. Hasonló eljárással hozzáadna egy kötetet.

#### <a name="to-add-a-volume"></a>Kötet hozzáadása
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. A kötettároló válasszon a listából, és kattintson duplán a tárolóhoz társított kötetek elérésére.
3. Kattintson a **Hozzáadás** az oldal alján. A Hozzáadás egy kötet varázsló elindul.
   
     ![Vegye fel a kötet varázsló alapbeállítások](./media/storsimple-manage-volumes-u2/TieredVolEx.png)
4. A Kötet hozzáadása varázslóban az **Alapbeállítások** területen tegye a következőket:
   
   1. Adjon **nevet** a kötetnek.
   2. Válassza ki a **használat típusa** a legördülő listából. Adatok helyileg megtalálható az eszköz mindig igénylő munkaterheléseknél válasszon **helyileg rögzített**. Minden más típusú adatok, válassza a **rétegzett**. (**Rétegzett** az alapértelmezett beállítás.)
   3. Ha a kiválasztott **rétegzett** a 2. lépésben, kiválaszthatja a **kötet használata ritkábban használt archív adatokhoz** melletti jelölőnégyzetet, hogy az archiválási kötet konfigurálása.
   4. Adja meg a **kiosztott kapacitást** a kötet GB-ban vagy TB. Lásd: [kapacitás kiosztása](#provisioned-capacity) az eszköz és a kötet típusonkénti maximális méret. Tekintse meg a **rendelkezésre álló kapacitásból** határozhatja meg, mennyi tárhelyre ténylegesen elérhető, az eszközön.
5. Kattintson a nyíl ikonra![Nyíl ikon](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Ha konfigurál egy helyileg rögzített kötetet, a következő üzenet jelenik meg.
   
    ![Módosítsa a kötet típusa üzenet](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
6. Kattintson a nyíl ikonra ![nyíl ikonra](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)újra ide a **további beállításokat** lap.
   
    ![Vegye fel a kötet varázsló további beállításai](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>
7. A **további beállításokat**, adja hozzá egy új hozzáférés-vezérlési rekordot (ACR):
   
   1. A legördülő listából válassza ki a hozzáférés-vezérlési rekordot (ACR). Másik megoldásként egy új ACR is hozzáadhat. ACRs határozza meg, hogy mely gazdagépek a kötetek férhetnek hozzá az állomás IQN-Nevének megfelelő vele a rekord szerepel. Ha nem adja meg az ACR, a következő üzenet jelenik meg.
      
        ![Adja meg az ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)
   2. Azt javasoljuk, hogy kiválassza a **engedélyezése ehhez a kötethez alapértelmezett biztonsági mentés** jelölőnégyzetet.
   3. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) a kötet létrehozása a megadott beállításokkal.

Az új kötet már készen áll a használatra.

> [!NOTE]
> Ha helyileg rögzített kötet létrehozása, és majd hozzon létre egy másik helyileg rögzített kötet azonnal ezek után a kötetek létrehozását feladatok egymás után futnak. Az első kötet-létrehozási feladatot a következő kötet-létrehozási feladat megkezdése előtt kell befejezni.
> 
> 

## <a name="modify-a-volume"></a>A kötet módosítása
Bontsa ki, vagy módosítsa a gazdagépek, amelyek a kötethez kell egy kötet módosításával.

> [!IMPORTANT]
> * Ha módosítja a kötet méretét, az eszközön, a kötet mérete kell módosítani a gazdagépen. 
> * A gazdagép-oldali itt leírt lépésekre a Windows Server 2012 (2012R2). Eljárások a Linux vagy más állomás operációs rendszerek eltérőek lesznek. Ha módosítja a kötet olyan operációs rendszert futtató másik gazdagépre tekintse meg a gazdagép operációs rendszer utasításokat. 
> 
> 

#### <a name="to-modify-a-volume"></a>A kötet módosítása
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. A kötettároló válasszon a listából, és a tárolóhoz társított kötetek megtekintéséhez kattintson rá duplán.
3. Jelöljön ki egy kötetet, és kattintson a lap alján **módosítás**. A módosítás kötet varázsló elindul.
4. A módosítás kötet varázslóban a **alapbeállítások**, a következőket teheti:
   
   * Szerkessze a **neve**.
   * Alakítsa át a **használat típusa** a helyileg rögzített a rétegzett vagy a rétegzett a helyileg rögzített (lásd: [kötet típusának módosítása](#change-the-volume-type) olvashat).
   * Növelje a **kapacitás kiosztása**. A **kiosztott kapacitást** pedig csak növelni. A kötet nem zsugorítható, létrehozás után.
5. A **további beállításokat**, módosíthatja a ACR, feltéve, hogy a kötet offline állapotban. Ha a kötet online állapotban, akkor offline állapotba először. Tekintse meg a lépéseket [kötet offline állapotba](#take-a-volume-offline) az ACR módosítása előtt.
   
   > [!NOTE]
   > Nem módosítható a **alapértelmezett biztonsági mentés engedélyezése** beállítást a kötethez.
   > 
   > 
6. A módosítások mentéséhez kattintson a pipa ikonra ![pipa ikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). A klasszikus Azure portálra egy frissítési kötet üzenetet jelenít meg. Egy üzenetet, jelenít meg, ha a kötet sikeresen frissítve.
7. Ha egy kötet bővíti, végezze el az alábbi lépéseket a Windows-állomás számítógépen:
   
   1. Ugrás a **számítógép-kezelés** ->**felügyeleti lemez**.
   2. Kattintson a jobb gombbal **Lemezkezelés** válassza **lemezek újraellenőrzése**.
   3. A lemezek, jelölje ki a kötetet, amelyeket azért frissített, kattintson a jobb gombbal, és válassza **kötet kiterjesztése**. A kötet kiterjesztése varázsló elindul. Kattintson a **Tovább** gombra.
   4. Fejezze be a varázslót, az alapértelmezett értékek elfogadásával. A varázsló befejezése után, a kötet mérete nagyobb kell megjelenítenie.
      
      > [!NOTE]
      > Ha bontsa ki a helyileg rögzített kötet, és bontsa ki egy másik helyileg rögzített kötet közvetlenül ezek után a kötet bővítése feladatok egymás után futnak. A kötet bővítése első feladat a következő kötet bővítése feladat megkezdése előtt kell befejezni.
      > 
      > 

![Videó elérhető](./media/storsimple-manage-volumes-u2/Video_icon.png) **Videó elérhető**

Bemutató videó bemutatja, hogyan bontsa ki a kötetet, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Módosítsa a kötet típusa
Módosíthatja a kötet típusa a helyileg rögzített a rétegzett, vagy a helyileg rögzített a rétegzett. Ez a konverzió azonban nem lehet egy gyakori előfordulásainak kívánt számát. Néhány ok, amiért a kötet alakításának a helyileg rögzített rétegzett vannak:

* Helyi garanciákat adatok rendelkezésre állását és teljesítményét
* Felhő késéseket és a felhő kapcsolódási problémák megszüntetését.

Ezek általában kicsi, amelyet gyakran használt meglévő kötetet. Egy helyileg rögzített kötet teljesen kiépítve, létrehozásakor. A rétegzett kötetek alakít át egy helyileg rögzített kötetet, ha a StorSimple ellenőrzi, hogy van elegendő hely az eszközön az átalakítás megkezdése előtt. Ha nincs elég hely van, hibaüzenetet kap, és megszakítja a műveletet. 

> [!NOTE]
> A helyileg rögzített rétegzett való átalakítás megkezdése előtt győződjön meg arról, hogy érdemes-e a lemezterület-igénye a többi munkaterhelését. 
> 
> 

Előfordulhat, hogy szeretné módosítani egy helyileg rögzített kötet rétegzett kötetet, ha más kötetek kiépítéséhez további területre van szükség. A helyileg rögzített kötet rétegzett, az eszköz növeli az elérhető kapacitás mérete elérhető kapacitást alakításakor. Ha csatlakozási problémák megakadályozzák a kötet a helyi-típusra történő konvertálást rétegzett típusra, a helyi kötet virágot fog hozni egy rétegzett kötet tulajdonságainak az átalakítás befejeződéséig. Ennek oka az, néhány adat előfordulhat, hogy rendelkezik kiömlött a felhőbe. Ezek az adatok kiömlött továbbra is elfoglalt helyi terület az eszközön, amely nem szabadítható, amíg a művelet újra, és befejeződött.

> [!NOTE]
> Egy kötet konvertálása eltarthat egy ideig, és a konvertálás nem vethető el, miután elindult. A kötet online állapotban marad az átalakítás során és a biztonsági másolatok készítése, de nem kibontása vagy a kötet visszaállítása, az átalakítás közben.  
> 
> 

A helyileg rögzített kötet egy rétegzett történő átalakítás kedvezőtlen hatással lehet a Teljesítmény eszköz. Emellett az alábbi tényezők növelheti a konvertálás befejezéséhez szükséges idő:

* Nincs elegendő sávszélesség.
* Nincs aktuális biztonsági másolat.

Ha csökkenteni szeretné a ezek a tényezők lehetnek:

* Tekintse át a sávszélesség-szabályozási házirendeket, és győződjön meg arról, hogy rendelkezésre áll-e a dedikált 40 MB/s sávszélességet.
* A konverzió kevesen ütemezni.
* A felhő pillanatképet az átalakítás előtt.

Ha több kötet (támogatja a különböző terhelésekhez), majd kell helyezi előtérbe a kötet átalakítás, hogy a magasabb prioritású kötetek először konvertálja. Például kell átalakítása üzemeltető virtuális gépek (VM) és az SQL-munkaterhelések kötetek fájl megosztási munkaterhelések kötetek átalakítása előtt.

#### <a name="to-change-the-volume-type"></a>A kötet típusa módosítása
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. A kötettároló válasszon a listából, és a tárolóhoz társított kötetek megtekintéséhez kattintson rá duplán.
3. Jelöljön ki egy kötetet, és kattintson a lap alján **módosítás**. A módosítás kötet varázsló elindul.
4. Az a **alapbeállítások** lapján jelölje ki az új származó használati típusának módosítása a **használat típusa** legördülő listából.
   
   * Ha módosítja a típus a **helyileg rögzített**, StorSimple ellenőrzi, hogy van-e elegendő kapacitása.
   * Ha módosítja a típus a **rétegzett** és archiválási adatok, jelölje be ezt a kötetet fog történni a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet.
     
       ![Archív jelölőnégyzet](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)
5. Kattintson a nyíl ikonra ![nyíl ikonra](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) gomba a **további beállításokat** lap. Ha konfigurál egy helyileg rögzített kötetet, a következő üzenet jelenik meg.
   
    ![Módosítsa a kötet típusa üzenet](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)
6. Kattintson a nyíl ikonra ![nyíl ikon](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) újra a műveletet.
7. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) az átalakítási folyamat elindításához. Az Azure portál egy frissítési kötet üzenetet jelenít meg. Egy üzenetet, jelenít meg, ha a kötet sikeresen frissítve.

## <a name="take-a-volume-offline"></a>A kötet offline állapotba helyezése
Szükség lehet egy kötet offline állapotba, ha azt tervezi, hogy módosítsa vagy törölje azt. Ha egy kötet offline állapotban, nincs olvasási és írási hozzáférése érhető el. Szüksége lesz a kötet offline érvénybe az állomáson, valamint az eszközön. 

#### <a name="to-take-a-volume-offline"></a>A kötet offline állapotba
1. Győződjön meg arról, hogy az adott kötet nem offline állapotba helyezése előtt használatban van.
2. Igénybe offline állapotba a kötet a gazdagépen első. Ezzel a megoldással a köteten adatsérülés lehetséges kockázatát. Tekintse meg az utasításokat a gazda operációs rendszer megadott lépéseket.
3. Miután a gazdagép offline állapotban, igénybe a kötet offline állapotba az eszközön az alábbi lépések elvégzésével:
   
   1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre. A **Kötettárolók** lapon táblázatos formában sorolja fel a kötet minden olyan tárolók, amelyek az eszközhöz kapcsolódnak.
   2. Válassza ki a kötettároló, és kattintson rá a tárolóban a kötetek listájának megjelenítéséhez.
   3. Jelöljön ki egy kötetet, majd **offline állapotba**.
   4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A kötet offline állapotban kell.
      
      Miután egy kötet offline állapotban, a **online állapotba hozás** összetevőn.

> [!NOTE]
> A **Offline állapotba** parancs kérést küld az eszközt, hogy a kötet offline állapotba. Gazdagépek még mindig használja a kötetet, ha az eredmény megszakadt kapcsolat, de a kötet offline állapotba helyezése nem sikertelen lesz. 
> 
> 

## <a name="delete-a-volume"></a>Kötet törlése
> [!IMPORTANT]
> Törölheti a kötet csak akkor, ha offline állapotban.
> 
> 

Az alábbi lépésekkel kötet törlése.

#### <a name="to-delete-a-volume"></a>Kötet törlése
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. Válassza ki a kötettároló, amely a törölni kívánt kötet rendelkezik. A kötettároló eléréséhez kattintson a **kötetek** lap.
3. Ez a tároló társított összes kötet táblázatos formában jelennek meg. A törölni kívánt kötet állapotának ellenőrzéséhez. Ha törli a kötet nem offline állapotban, offline állapotba először lépéseit [kötet offline állapotba](#take-a-volume-offline).
4. Miután a kötet offline állapotban, kattintson **törlése** az oldal alján.
5. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Program törli a kötet és a **kötetek** lapon megjelenik a frissített listáját a tárolóban lévő köteteket.
   
   > [!NOTE]
   > Ha töröl egy helyileg rögzített kötetet, a szabad lemezterület az új kötetekre esetleg nem azonnal frissíti. A StorSimple Manager szolgáltatás rendszeres időközönként frissíti a rendelkezésre álló helyi területet. Javasoljuk, hogy csak néhány perc elteltével az új kötet létrehozásakor várja.<br> Emellett ha töröl egy helyileg rögzített kötet, és törölje a másikra helyileg rögzített kötet azonnal ezt követően, a kötet törlési feladatok egymás után futnak. Az első kötet törlési feladat a következő kötet törlési feladat megkezdése előtt kell befejezni.
   > 
   > 

## <a name="monitor-a-volume"></a>A kötet figyelése
Mennyiségi figyelési lehetővé teszi, hogy a kötet I/O-hez kapcsolódó Statisztika gyűjtése. Figyelés az Ön által létrehozott első 32 kötetekhez alapértelmezés szerint engedélyezve van. A további kötetek figyelés alapértelmezés szerint le van tiltva. A klónozott kötetek figyelését is alapértelmezés szerint nincs engedélyezve.

A következő lépésekkel engedélyezhető vagy tiltható le a kötet figyelését.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Engedélyezheti vagy tilthatja le a kötet figyelése
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. Válassza ki a kötettároló, ahol a köteten van, és kattintson a eléréséhez a kötettároló a **kötetek** lap.
3. Ebben a tárolóban társított összes kötet szerepelnek táblázatos megjelenítéséhez. Kattintson, és válassza ki a kötet vagy a kötet Klónozás.
4. Kattintson a lap alján **módosítás**.
5. A kötet módosítása varázslóban a **alapbeállítások**, jelölje be **engedélyezése** vagy **tiltsa le a** a a **figyelés** legördülő listából választhatja ki.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [klónozza a StorSimple-kötet](storsimple-clone-volume.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

