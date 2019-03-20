---
title: A StorSimple 8000 sorozat kötet klónozása |} A Microsoft Docs
description: A különböző Klónozás típusát és a használati ismerteti, és bemutatja, hogyan használhatja a biztonságimásolat-készletet a StorSimple 8000 sorozatú eszköz az egyedi kötet klónozásához.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097235"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás segítségével kötet klónozása

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja, hogyan használhatja egy biztonságimentés-készlet-n keresztül az egyedi kötet klónozásához a **biztonságimásolat-katalógus** panelen. Ezen kívül ismerteti a különbség a között *átmeneti* és *állandó* klónozza. Ebben az oktatóanyagban az útmutató összes a StorSimple 8000 sorozatú eszköz 3 vagy újabb frissítést futtató vonatkozik.

A StorSimple-Eszközkezelő szolgáltatás **biztonságimásolat-katalógus** panel jeleníti meg az összes biztonságimásolat-készletek, amelyek jönnek létre, ha a manuális vagy automatikus biztonsági másolatokat készít. Ezután egy kötet kiválaszthatja egy biztonságimentés-készletből történő klónozásához.

 ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Kötet klónozása szempontjai

Vegye figyelembe a következő adatokat, amikor egy kötet klónozása.

- A klón rendszeres kötetként ugyanúgy viselkedik. Minden művelet, amely egy köteten lehetséges a klón érhető el.

- Figyelés és az alapértelmezett biztonsági mentés automatikusan le vannak tiltva a klónozott köteten. Minden biztonsági klónozott kötet konfigurálása kell.

- Egy helyileg rögzített kötet a rétegzett kötetek, klónozták. Ha a klónozott kell helyileg rögzített kötetet, átválthat a klón egy helyileg rögzített kötet a Klónozás sikeres végrehajtása után. Információk egy rétegzett kötet konvertálása helyileg rögzített kötetet, [a kötet típusának módosításához](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Ha megpróbálja a klónozott kötet konvertálása rétegzettről után azonnal a Klónozás (Ha még egy átmeneti klón), a helyileg rögzített az átalakítás sikertelen a következő hiba miatt:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Csak akkor, ha a, a Klónozás be egy másik eszközön van ez a hiba érkezett. Sikeresen alakíthatja, ha először konvertálja a átmeneti klónon állandó klón a helyileg rögzített kötet. A konvertálni kell egy állandó klón átmeneti klónon felhőbeli pillanatkép készítése.

## <a name="create-a-clone-of-a-volume"></a>Kötet létrehozása

Létrehozhat egy klónozott ugyanarra az eszközre, egy másik eszközön vagy akár a felhőalapú berendezés segítségével egy helyi vagy felhőbeli pillanatkép.

Az alábbi eljárás ismerteti, hogyan lehet egy klón létrehozására a biztonsági mentési katalógusból.  Egy alternatív módszer a Klónozás kezdeményezni, hogy lépjen **kötetek**, válasszon olyan kötetet, majd kattintson a jobb gombbal indítja el a helyi menüt, és válassza ki **Klónozás**.

A következő lépésekkel klónozhatja a kötet a biztonságimásolat-katalógusból.

#### <a name="to-clone-a-volume"></a>A kötet klónozása

1. Keresse meg a StorSimple-Eszközkezelő szolgáltatást, és kattintson a **biztonságimásolat-katalógus**.

2. Válassza ki a biztonsági csoportot az alábbiak szerint:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki a biztonsági mentés, amelyet szeretne, válassza ki a kötet vagy a biztonsági mentési szabályzat.
   3. Megadhatja az időtartományt.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.

      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési szabályzat meg kell jelennie a biztonságimásolat-készletek listájában.
   
      ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Bontsa ki a biztonságimásolat-készlet megtekintése a társított kötetet, és válassza ki a kötet a biztonságimásolat-készletből. Kattintson a jobb gombbal, és a helyi menüből válassza ki **Klónozás**.

   ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Az a **Klónozás** panelen tegye a következőket:
   
   1. Azonosítsa a céleszközt. Ez az a hely, ahol jön létre a klónozott. Válassza ki az ugyanazon az eszközön, vagy adjon meg egy másik eszközt.

      > [!NOTE]
      > Győződjön meg arról, hogy a klónozott szükséges kapacitást alacsonyabb, mint a céleszköz elérhető kapacitása.
       
   2. Adja meg a klónozott kötet egyedi nevét. A név 3 – 127 karaktert kell tartalmaznia.
      
       > [!NOTE]
       > A **klónozott kötet,** mező lesz **rétegzett** akkor is, ha helyileg rögzített kötet vannak klónozása. Ezzel a beállítással; nem módosítható azonban ha módosítania kell a helyileg rögzített, valamint a klónozott kötet, átválthat a klón egy helyileg rögzített kötet a Klónozás sikeres létrehozása után. Információk egy rétegzett kötet konvertálása helyileg rögzített kötetet, [a kötet típusának módosításához](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. A **csatlakoztatott gazdagépek**, adjon meg egy hozzáférés-vezérlési rekord (ACR) a klón. Adjon hozzá egy új ACR, vagy válassza ki a meglévő listából. Az ACR-REL gazdagépek ezt klón hozzáférhet határozza meg.
      
       ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Kattintson a **Klónozás** befejezni a műveletet.

4. A Klónozás feladat, és értesítést kap a klón létrehozása sikerült. Kattintson az értesítés vagy az **feladatok** panelen a Klónozás feladat figyeléséhez.

    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. A Klónozás feladat befejeződése után nyissa meg az eszközt, és kattintson **kötetek**. A kötetek listáját megtekintheti a klón ugyanabban a tárolóban kötet, amely rendelkezik a forráskötet újonnan létrehozott.

    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Az így létrehozott klón egy átmeneti klón. Klónozás típusaival kapcsolatos további információkért lásd: [átmeneti és állandó klónok](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Átmeneti és állandó klónok
Átmeneti klónok jönnek létre, csak klónozásakor egy másik eszközre. Egy adott köteten egy biztonsági másolatból állítsa egy másik eszközt a StorSimple Device Manager által felügyelt klónozhat. A átmeneti klónon az adatok mutató hivatkozásokat tartalmaz az eredeti kötet, és használja az adatok olvasásához és írásához helyileg a céleszközön.

Miután vetett egy átmeneti klón felhőbeli pillanatképet, az eredményül kapott Klónozás van egy *állandó* Klónozás. Ez a folyamat során az adatok másolatát jön létre a felhőben, és az időt az adatok másolása az adatok és az Azure késések (Ez az egy Azure-ról Azure példány) határozza meg. Ez a folyamat napokat is igénybe vehet a héten. A átmeneti klónon állandó klón lesz, és nem rendelkezik az eredeti kötet adatait, a klónozott mutató hivatkozásokat.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Átmeneti és állandó klónok forgatókönyvei
A következő szakaszok ismertetik például olyan helyzetekben, amelyben az átmeneti és állandó klónok is használható.

### <a name="item-level-recovery-with-a-transient-clone"></a>Egy átmeneti klón az elemszintű helyreállítás
Kell helyreállítania, egy éves Microsoft PowerPoint-bemutató fájlba. A rendszergazda azonosítja a biztonsági mentési ettől, és ezután szűri a kötetet. A rendszergazda ezután klónozza a kötetet, megkeresi a fájlt, amely az Ön által keresett, és itt meg. Ebben a forgatókönyvben egy átmeneti klón szolgál.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Az éles környezetben az állandó klón tesztelése
Az éles környezetben egy tesztelési hiba ellenőriznie kell. Az éles környezetben klónozhatja a kötetet, és a egy független klónozott kötet létrehozása ezt klón felhőbeli pillanatképet megfelelő. Ebben a forgatókönyvben egy állandó klón szolgál.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [StorSimple-kötet visszaállítása a biztonságimásolat-készlet](storsimple-8000-restore-from-backup-set-u2.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

