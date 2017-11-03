---
title: "A StorSimple 8000 Series kötet klónozása |} Microsoft Docs"
description: "Ismerteti a különböző Klónozás típusok és a használati, és elmagyarázza, hogyan használhatja a biztonságimásolat-készletet úgy klónozni egy egyéni köteten a StorSimple 8000 series eszközön."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/26/2017
ms.author: alkohli
ms.openlocfilehash: 70c85bcb2c26d2ad3d0515d24e028f84495634c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>A StorSimple Device Manager szolgáltatás segítségével az Azure portál egy kötet klónozása

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja, hogyan használható egy biztonságimentés-készlet keresztül az egyes kötetek klónozása a **biztonságimásolat-katalógus** panelen. Azt is bemutatja a különbség a között *átmeneti* és *állandó* klónozásával készíti. Ez az oktatóanyag az útmutató minden StorSimple 8000 series futtató eszköznek az Update 3-as vagy újabb vonatkozik.

A StorSimple Device Manager szolgáltatás **biztonságimásolat-katalógus** csempe megjeleníti a biztonsági mentés, amely jönnek létre, ha manuális vagy automatikus biztonsági mentés készül. Ezután egy kötet kiválaszthatja egy biztonságimentés-készlet klónozását.

 ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>A kötet klónozásával kapcsolatos szempontok

Vegye figyelembe a következő adatokat, amikor egy kötet klónozása.

- A Klónozás rendszeres kötetként ugyanúgy viselkedik. A Klónozás minden művelet, amely lehet egy olyan köteten érhető el.

- Figyelés és az alapértelmezett biztonsági mentés automatikusan le vannak tiltva a klónozott köteten. Minden biztonsági másolatának klónozott kötet konfigurálása kellene.

- Egy helyileg rögzített kötet egy rétegzett kötet klónozták. Ha módosítania kell a klónozott kell helyileg rögzített kötetet, átválthat a klón egy helyileg rögzített kötet a Klónozási művelet sikeres végrehajtása után. A rétegzett kötet konvertálása egy helyileg rögzített kötet kapcsolatos információkért látogasson el [kötet típusának módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Ha a klónozott kötet konvertálása próbál rétegzett a helyileg rögzített közvetlenül után a Klónozás (ha az még átmeneti másolat), a konvertálás sikertelen a következő hiba miatt:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Ez a hiba csak akkor, ha egy másik eszközön be vannak a Klónozás érkezik. Sikeresen átválthat a köteten a helyileg rögzített, ha az átmeneti Klónozás konvertál egy állandó klónnak. Felhő pillanatkép készítése a átmeneti klón konvertálni kell egy állandó Klónozás.

## <a name="create-a-clone-of-a-volume"></a>A kötet klónozhatja

Hozhat létre a klónozott ugyanarra az eszközre, egy másik eszközön vagy akár egy felhőalapú készülék a helyi vagy felhőalapú pillanatfelvétel.

Az alábbi eljárás ismerteti, hogyan klónozhatja a biztonságimásolat-katalógusból.  Klónozás kezdeményezése alternatív módszert, hogy lépjen **kötetek**, jelöljön ki egy kötetet, majd kattintson a jobb gombbal a helyi menü el, és válassza ki a **Klónozás**.

A következő lépésekkel klónozhatja a kötet a biztonságimásolat-katalógusból.

#### <a name="to-clone-a-volume"></a>Kötet klónozása

1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és kattintson a **biztonságimásolat-katalógus**.

2. Válassza ki a biztonságimásolat-készletet a következőképpen:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki szeretne biztonsági másolatot az kötet vagy a biztonsági mentési házirend kiválasztásához.
   3. Adja meg azt az időtartományt.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.

    A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.
   
    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezek a kötetek offline állapotba kell helyezni a gazdagépet és az eszköz visszaállítani őket. A kötetek elérését a a **kötetek** panelen található az eszközre, majd kövesse a lépéseket [kötet offline állapotba](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) offline állapotba.
   
   > [!IMPORTANT]
   > Győződjön meg arról, hogy elvégezte a kötetek offline állapotba a gazdagépen, mielőtt az eszköz a kötetek offline állapotba. Ha a gazdagép nem hajtja végre a kötetek offline, potenciálisan előfordulhat, hogy sérült adatokat.
   
4. Lépjen vissza a **biztonságimásolat-katalógus** és jelöljön ki egy kötetet a biztonságimásolat-készletben. Kattintson a jobb gombbal, és a helyi menüből válassza **Klónozás**.

   ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Az a **Klónozás** panelen tegye a következőket:
   
    1. Azonosítsa a céleszközön. Ez az a hely, ahol létrejön-e a Klónozás. Válassza ki az adott eszközt, vagy adjon meg egy másik eszközt.

      > [!NOTE]
      > Győződjön meg arról, hogy a Klónozás szükséges kapacitást alacsonyabb, mint a céleszközön rendelkezésre álló kapacitás.
       
    2. Adja meg a klónozott egyedi kötet nevét. A név 3 és 127 karakterből állhat.
      
        > [!NOTE]
        > A **Klónozás kötet mint** mező kitöltése **rétegzett** akkor is, ha meg vannak Klónozás egy helyileg rögzített kötet. Ez a beállítás; nem módosítható azonban ha a klónozott kötet helyileg is rögzítve van szüksége, átválthat a klón egy helyileg rögzített kötet a Klónozás sikeres létrehozását követően. A rétegzett kötet konvertálása egy helyileg rögzített kötet kapcsolatos információkért látogasson el [kötet típusának módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. A **csatlakozó állomások**, adjon meg egy hozzáférés-vezérlési rekordot (ACR) a klón. Adja hozzá egy új ACR, vagy válassza ki a meglévő listáról. A ACR mely állomásokat férhetnek hozzá ehhez a klónhoz határozza meg.
      
        ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Kattintson a **Klónozás** elvégezni a műveletet.

4. A Klónozás feladat indítható, és értesítést kap a Klónozás sikeres létrehozását. Kattintson a feladat értesítési vagy Ugrás **feladatok** panelt, és a Klónozás feladat figyeléséhez.

    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. A Klónozás feladat befejezése után nyissa meg az eszközre, és kattintson a **kötetek**. A kötetek listáját meg kell jelennie a klón, amely rendelkezik a forráskötet kötet tárolóhoz az újonnan létrehozott.

    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Az így létrehozott klón átmeneti másolat. Klónozás típusok kapcsolatos további információkért lásd: [átmeneti és végleges klónok](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Átmeneti és végleges klónok összehasonlítása
Átmeneti klónok jönnek létre, csak akkor, ha egy másik eszközre klónozását. Egy adott köteten beállítása egy másik eszközön, a StorSimple eszköz Manager által kezelt mentésből tud klónozni. Az átmeneti Klónozás adatokra hivatkozásokat tartalmaz az eredeti köteten, és használja az adatok olvasását és írását helyileg a céleszközön.

Egy felhő-pillanatfelvételt a átmeneti klón elvégzése után az eredményül kapott Klónozás van egy *állandó* Klónozás. A folyamat során az adatok másolatát a felhő jön létre, és ezek az adatok másolása az idő határozza meg az adatok és az Azure késések (Ez az az Azure-Azure másolatot) mérete. A folyamat eltarthat nap, hét. Az átmeneti Klónozás állandó másolat válik, és nem rendelkezik az eredeti kötet adatait, amely a klónozása megtörtént mutató hivatkozásokat.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Átmeneti és végleges klónok forgatókönyvei
A következő szakaszok ismertetik például olyan helyzetekben, ahol átmeneti és végleges klónok is használható.

### <a name="item-level-recovery-with-a-transient-clone"></a>Elemszintű helyreállítás az átmeneti másolat
Egy éves Microsoft PowerPoint bemutatót fájlhelyreállítás kell. A rendszergazda a biztonsági másolat kezdve azonosítja, szűrőt, és a kötetet. A rendszergazda ezután a kötet klónozásával készíti, talál a fájlt, amely a keresett, és itt meg. Ebben a forgatókönyvben egy átmeneti Klónozás szolgál.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Az éles környezetben az állandó másolat tesztelése
Vissza kell igazolnia egy tesztelési hiba a termelési környezetben. Az éles környezetben klónozhatja a kötet, illetve az ezekkel a felhő-pillanatfelvételt a klón egy független klónozott kötet létrehozásához. Ebben a forgatókönyvben egy állandó Klónozás szolgál.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple-kötet visszaállítása egy biztonságimásolat-készlet](storsimple-8000-restore-from-backup-set-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

