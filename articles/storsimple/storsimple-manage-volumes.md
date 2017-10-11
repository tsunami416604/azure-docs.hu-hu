---
title: "A StorSimple-köteteket kezelése |} Microsoft Docs"
description: "Elmagyarázza, hozzáadása, módosítása, figyeléséhez, és törölje a StorSimple-köteteket, és hogyan offline állapotba rájuk szükség."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ccabd859-590c-4568-a81d-6ff38f125be3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2016
ms.author: v-sharos
ms.openlocfilehash: 31ed9dad8ba56a3746873b7b35e678e97743fbfe
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>A StorSimple Manager szolgáltatással kötetek kezelése
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti, hogyan hozhatja létre és kezelheti a StorSimple eszköz és a StorSimple virtuális eszköz a kötetek a StorSimple Manager szolgáltatás.

A StorSimple Manager szolgáltatás a klasszikus Azure portálra, amely lehetővé teszi a StorSimple megoldásban egy egyetlen webes felhasználói felületen keresztüli kezelését kiterjesztése. Kötetek kezelése, valamint használhatja a StorSimple Manager szolgáltatás létrehozása és a StorSimple-szolgáltatások kezelése, megtekintése és eszközök kezeléséhez, megtekintheti a riasztásokat, és megtekintéséhez és kezeléséhez biztonsági mentési házirendek és a biztonságimásolat-katalógus.

> [!NOTE]
> Az Azure StorSimple csak dinamikusan kiosztott köteteket hozhat létre. Nem hozható létre teljesen vagy részben kiosztott köteteket az Azure StorSimple rendszeren.
> 
> Dinamikus kiosztás egy virtualizációs technológia, amely rendelkezésre álló tár hosszabb legyen, mint a fizikai erőforrásokat jelenik meg. Ahelyett, hogy elegendő tárhely előre foglalással, Azure StorSimple használja a dinamikus kiosztás aktuális követelményeknek éppen elegendő lemezterületet foglaljon le. Felhőalapú tárolás rugalmas jellege elősegíti ezt a módszert használja, mert Azure StorSimple növelheti vagy csökkentheti a felhőalapú tárolást tartalomkérései változó igényeinek kielégítéséhez.
> 
> 

## <a name="the-volumes-page"></a>A kötetek lap
A **kötetek** lap lehetővé teszi a tárolókötetek törlődnek a Microsoft Azure StorSimple eszközön a kezdeményezőktől (kiszolgálóktól) való kezelését. A StorSimple eszköz kötetek listáját megjeleníti.

 ![Kötetek lap](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

A kötet attribútumainak állnak:

* **Név** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a kötetet. Ez a név monitoring jelentések, amikor egy adott köteten szűrheti is szerepel.
* **Állapot** – lehet online vagy offline állapotú. Ha egy köteten, ha a kapcsolat nélküli, nincs látható kezdeményezőktől (kiszolgálóktól), amelyek hozzáférhetnek a kötetet használja.
* **Kapacitás** – adja meg, hogy nagy a kötet van, a kezdeményező (kiszolgáló) által érzékelt. Kapacitás a kezdeményező (kiszolgáló) tárolt adatok teljes mennyisége határozza meg. Kötetek kiosztása, és a deduplikált adatok. Ez azt jelenti, hogy az eszköz nem előre lefoglalni fizikai tárolási kapacitás, belsőleg vagy a felhő megfelelően konfigurált kötet kapacitása. A kötet kapacitása lefoglalt és felhasznált igény szerint.
* **Típus** – a kötet típusa rétegzett és archiválási lehet (a altípusa Szintezett)
* **Hozzáférés** – Itt adhatja meg, amelyek számára engedélyezett a kötet elérésének kezdeményezőktől (kiszolgálóktól). Kezdeményezők, amelyek nem tagjai a hozzáférés-vezérlési rekordot (ACR) a kötet társított nem jelenik meg a kötet.
* **Figyelési** – adja meg a kötet figyel-e. Egy kötet lesz figyelési létrehozásakor alapértelmezés szerint engedélyezett. Lesz, azonban figyelés, a kötet másolat le kell tiltani. Kötet figyelésének engedélyezéséhez kövesse a figyelő egy kötet.

A leggyakoribb feladatokat hozzárendelt egy kötetet a következők:

* Kötet hozzáadása
* A kötet módosítása
* Kötet törlése
* A kötet offline állapotba helyezése
* A kötet figyelése

## <a name="add-a-volume"></a>Kötet hozzáadása
Ön [kötet létrehozott](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) a StorSimple megoldás üzembe helyezése során. Hasonló eljárással hozzáadna egy kötetet.

### <a name="to-add-a-volume"></a>Kötet hozzáadása
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. Válassza ki a kötettároló, és kattintson a nyílra az adott sorban a tárolóhoz társított kötetek elérésére.
3. Kattintson a **Hozzáadás** az oldal alján. A Hozzáadás egy kötet varázsló elindul.
   
     ![Vegye fel a kötet varázsló alapbeállítások](./media/storsimple-manage-volumes/AddVolume1.png)
4. A Kötet hozzáadása varázslóban az **Alapbeállítások** területen tegye a következőket:
   
   1. Adjon **nevet** a kötetnek.
   2. Adja meg a kötethez tartozó **Kiosztott kapacitást** GB-ban vagy TB-ban. A kapacitás egy fizikai eszköz 1 GB és 64 TB-os között kell lennie. A maximális kapacitás, amely egy olyan kötetre a StorSimple virtuális eszköz kiépítése 30 TB.
   3. Válassza ki a **használat típusa** a köteten. Ha a rétegzett kötetet archív adatokhoz használ, válassza a **kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet vált az a kötet deduplikációs adattömbméret 512 KB. Ha nem ezt a beállítást, a megfelelő rétegzett kötet 64 KB adattömbméretet fogja használni. Egy nagyobb deduplikációs adattömbméret lehetővé teszi, hogy az eszközt, hogy a nagy mennyiségű archiválási adatok a felhőben való továbbítását. (Rétegzett kötetek volt neve korábban elsődleges kötet.)
   4. Kattintson a nyíl ikonra ![nyíl ikonra](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)gomba a **további beállításokat** lap.
      
        ![Vegye fel a kötet varázsló további beállításai](./media/storsimple-manage-volumes/AddVolume2.png)
5. A **további beállításokat**, adja hozzá egy új hozzáférés-vezérlési rekordot (ACR):
   
   1. A legördülő listából válassza ki a hozzáférés-vezérlési rekordot (ACR). Másik megoldásként egy új ACR is hozzáadhat. ACRs határozza meg, hogy mely gazdagépek a kötetek férhetnek hozzá az állomás IQN-Nevének megfelelő vele a rekord szerepel.
   2. Javasoljuk, hogy engedélyezze az alapértelmezett biztonsági mentést az **Alapértelmezett biztonsági mentés engedélyezése ehhez a kötethez** jelölőnégyzet bejelölésével.
   3. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-volumes/HCS_CheckIcon.png) a kötet létrehozása a megadott beállításokkal.

Az új kötet már készen áll a használatra.

## <a name="modify-a-volume"></a>A kötet módosítása
Bontsa ki, vagy módosítsa a gazdagépek, amelyek a kötethez kell egy kötet módosításával.

> [!IMPORTANT]
> * Ha módosítja a kötet méretét, az eszközön, a kötet mérete kell módosítani a gazdagépen.
> * A gazdagép-oldali itt leírt lépésekre a Windows Server 2012 (2012R2). Eljárások a Linux vagy más állomás operációs rendszerek eltérőek lesznek. Ha módosítja a kötet olyan operációs rendszert futtató másik gazdagépre tekintse meg a gazdagép operációs rendszer utasításokat.
> 
> 

### <a name="to-modify-a-volume"></a>A kötet módosítása
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettároló** fülre. Ezen a lapon táblázatos formában sorolja fel a kötet minden olyan tárolók, amelyek az eszközhöz kapcsolódnak.
2. Válassza ki a kötettároló, és kattintson rá a tárolóban a kötetek listájának megjelenítéséhez.
3. Az a **kötetek** lapon jelöljön ki egy kötetet, majd kattintson **módosítás**.
4. A módosítás kötet varázslóban a **alapbeállítások**, a következőket teheti:
   
   * Szerkesztés a **neve** és **típus** választásával rétegzett kötetet archív kötetre módosítani kívánja a **kötet használata ritkábban használt archív adatokhoz** melletti jelölőnégyzetet, hogy módosítsa a deduplikációs adattömbméret a kötethez tartozó 512 KB.
   * Növelje a **kapacitás kiosztása**. A **kiosztott kapacitást** pedig csak növelni. A kötet nem zsugorítható, létrehozás után.
     
     > [!NOTE]
     > A kötettároló nem módosítható, miután egy kötet van hozzárendelve.
     > 
     > 
5. A **további beállításokat**, a következőket teheti:
   
   * Módosítsa a ACRs, feltéve, hogy a kötet offline állapotban. Ha a kötet online állapotban, akkor offline állapotba először. Tekintse meg a lépéseket [kötet offline állapotba](#take-a-volume-offline) az ACR módosítása előtt.
   * ACRs listájának módosítása után a kötet offline állapotban.
     
     > [!NOTE]
     > Nem módosítható a **engedélyezése ehhez a kötethez alapértelmezett biztonsági mentés** beállítást a kötethez.
     > 
     > 
6. A módosítások mentéséhez kattintson a pipa ikonra ![pipa ikon](./media/storsimple-manage-volumes/HCS_CheckIcon.png). A klasszikus Azure portálra egy frissítési kötet üzenetet jelenít meg. Egy üzenetet, jelenít meg, ha a kötet sikeresen frissítve.
7. Ha egy kötet bővíti, végezze el az alábbi lépéseket a Windows-állomás számítógépen:
   
   1. Ugrás a **számítógép-kezelés** ->**felügyeleti lemez**.
   2. Kattintson a jobb gombbal **Lemezkezelés** válassza **lemezek újraellenőrzése**.
   3. A lemezek, jelölje ki a kötetet, amelyeket azért frissített, kattintson a jobb gombbal, és válassza **kötet kiterjesztése**. A kötet kiterjesztése varázsló elindul. Kattintson a **Tovább** gombra.
   4. Fejezze be a varázslót, az alapértelmezett értékek elfogadásával. A varázsló befejezése után, a kötet mérete nagyobb kell megjelenítenie.

![Videó elérhető](./media/storsimple-manage-volumes/Video_icon.png) **Videó elérhető**

Bemutató videó bemutatja, hogyan bontsa ki a kötetet, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="take-a-volume-offline"></a>A kötet offline állapotba helyezése
Szükség lehet egy kötet offline állapotba, ha azt tervezi, hogy módosítsa vagy törölje azt. Ha egy kötet offline állapotban, nincs olvasási és írási hozzáférése érhető el. Szüksége lesz a kötet offline érvénybe az állomáson, valamint az eszközön. A következő lépésekkel kötet offline állapotba.

### <a name="to-take-a-volume-offline"></a>A kötet offline állapotba
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

### <a name="to-delete-a-volume"></a>Kötet törlése
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. Válassza ki a kötettároló, amely a törölni kívánt kötet rendelkezik. A kötettároló eléréséhez kattintson a **kötetek** lap.
3. Ez a tároló társított összes kötet táblázatos formában jelennek meg. A törölni kívánt kötet állapotának ellenőrzéséhez. Ha törli a kötet nem offline állapotban, offline állapotba először lépéseit [kötet offline állapotba](#take-a-volume-offline).
4. Miután a kötet offline állapotban, kattintson **törlése** az oldal alján.
5. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Program törli a kötet és a **kötetek** lapon megjelenik a frissített listáját a tárolóban lévő köteteket.

## <a name="monitor-a-volume"></a>A kötet figyelése
Mennyiségi figyelési lehetővé teszi, hogy a kötet I/O-hez kapcsolódó Statisztika gyűjtése. Figyelés az Ön által létrehozott első 32 kötetekhez alapértelmezés szerint engedélyezve van. A további kötetek figyelés alapértelmezés szerint le van tiltva. A klónozott kötetek figyelését is alapértelmezés szerint nincs engedélyezve.

A következő lépésekkel engedélyezhető vagy tiltható le a kötet figyelését.

### <a name="to-enable-or-disable-volume-monitoring"></a>Engedélyezheti vagy tilthatja le a kötet figyelése
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **Kötettárolók** fülre.
2. Válassza ki a kötettároló, ahol a köteten van, és kattintson a eléréséhez a kötettároló a **kötetek** lap.
3. Ebben a tárolóban társított összes kötet szerepelnek táblázatos megjelenítéséhez. Kattintson, és válassza ki a kötet vagy a kötet Klónozás.
4. Kattintson a lap alján **módosítás**.
5. A kötet módosítása varázslóban a **alapbeállítások**, jelölje be **engedélyezése** vagy **tiltsa le a** a a **figyelés** legördülő listából választhatja ki.
   
    ![A kötet alapvető beállítások módosítása](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [klónozza a StorSimple-kötet](storsimple-clone-volume.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

