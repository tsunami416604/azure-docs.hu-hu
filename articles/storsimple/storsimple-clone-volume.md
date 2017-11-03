---
title: "A StorSimple-kötet klónozása |} Microsoft Docs"
description: "Ismerteti a különböző Klónozás típusok és mikor érdemes használni azokat, és elmagyarázza, hogyan használhatja a biztonságimásolat-készletet úgy klónozni egy egyéni köteten."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b5d615f2-02a7-4222-9867-6c0385ce748c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 8f1936fac543f559a44ad0f9c35b30d1a92dce68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>A StorSimple Manager szolgáltatás segítségével egy kötet klónozása
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Áttekintés
A StorSimple Manager szolgáltatás **biztonságimásolat-katalógus** lap megjeleníti a biztonsági mentés, amely jönnek létre, ha manuális vagy automatikus biztonsági mentés készül. Ezen a lapon a biztonsági mentési házirend, vagy egy kötetet, jelölje be a biztonsági másolatok felsorolása, vagy törölje a biztonsági mentések, vagy a biztonsági másolat használatával állítsa vissza vagy egy kötet klónozása.

![Biztonságimásolat-katalógus lap](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

Ez az oktatóanyag leírja, hogyan használhatja a biztonságimásolat-készletet úgy klónozni egy egyéni köteten. Azt is bemutatja a különbség a között *átmeneti* és *állandó* klónozásával készíti. 

## <a name="create-a-clone-of-a-volume"></a>A kötet klónozhatja
Egy helyi vagy egy felhő-pillanatfelvételt hozhat létre a klónozott ugyanarra az eszközre, egy másik eszközt vagy egy virtuális gép még akkor is.

#### <a name="to-clone-a-volume"></a>Kötet klónozása
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus** lapot, és válasszon egy biztonságimásolat-készlet.
2. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Kattintson, és jelöljön ki egy kötetet a biztonsági mentési készletből.
   
     ![Kötet klónozása](./media/storsimple-clone-volume/HCS_Clone.png) 
3. Kattintson a **Klónozás** a kijelölt kötetet a Klónozás megkezdéséhez.
4. A Klónozás kötet varázslóban a **név és hely megadása**:
   
   1. Azonosítsa a céleszközön. Ez az a hely, ahol létrejön-e a Klónozás. Válassza ki az adott eszközt, vagy adjon meg egy másik eszközt. Ha, válasszon olyan kötetet, egyéb felhőszolgáltatók társított (nem Azure), a legördülő listából válassza ki a cél eszköz csak akkor jelenik meg fizikai eszközöket. A virtuális eszközön egyéb felhőszolgáltatók társított kötet nem klónozható.
      
      > [!NOTE]
      > Győződjön meg arról, hogy a Klónozás szükséges kapacitást alacsonyabb, mint a céleszközön rendelkezésre álló kapacitás.
      > 
      > 
   2. Adja meg a klónozott egyedi kötet nevét. A név 3 és 127 karakterből állhat.
   3. Kattintson a nyíl ikonra ![nyíl ikon](./media/storsimple-clone-volume/HCS_ArrowIcon.png) a folytatáshoz a következő oldalra.
5. A **adja meg a gazdagépekhez, amelyek használhatják ezt a kötetet**:
   
   1. Adjon meg egy hozzáférés-vezérlési rekordot (ACR) a klón. Adja hozzá egy új ACR, vagy válassza ki a meglévő listáról.
   2. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-clone-volume/HCS_CheckIcon.png)a művelet végrehajtásához.
6. A Klónozás feladat indul, és értesítést fog kapni, ha a Klónozás sikeres létrehozását. Kattintson a **feladat megtekintése** a Klónozás feladat figyelése a **feladatok** lap.
7. Miután a Klónozás feladat befejezése:
   
   1. Lépjen a **eszközök** lapon, és válassza ki a **Kötettárolók** fülre. 
   2. Válassza ki a kötettároló a forráskötet, hogy a klónozott társított. A kötetek listáját meg kell jelennie az újonnan létrehozott klón.

> [!NOTE]
> Figyelés és az alapértelmezett biztonsági mentés automatikusan le vannak tiltva a klónozott köteten.
> 
> 

Az így létrehozott klón átmeneti másolat. Klónozás típusok kapcsolatos további információkért lásd: [átmeneti és végleges klónok](#transient-vs-permanent-clones).

Ehhez a klónhoz most már a rendszeres kötet, és minden művelet, amely lehet egy olyan köteten a klón elérhető lesz. Szüksége lesz a kötetet a biztonsági mentés konfigurálása.

## <a name="transient-vs-permanent-clones"></a>Átmeneti és végleges klónok összehasonlítása
Átmeneti és végleges klónok jönnek létre, csak akkor, ha egy másik eszközön be vannak a Klónozás. Egy adott köteten egy biztonsági másolatból egy másik eszköz beállítása tud klónozni. Ily módon létrehozta másolat van egy *átmeneti* Klónozás. Az átmeneti Klónozás lesz az eredeti kötet mutató hivatkozásokat, és helyileg írásakor olvasni, hogy a köteten használja. 

Egy felhő-pillanatfelvételt a átmeneti klón elvégzése után az eredményül kapott Klónozás lesz egy *állandó* Klónozás. Az állandó Klónozás független, és nem rendelkezik az eredeti kötet, amely a klónozása megtörtént mutató hivatkozásokat.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Átmeneti és végleges klónok forgatókönyvei
A következő szakaszok ismertetik például olyan helyzetekben, ahol átmeneti és végleges klónok is használható.

### <a name="item-level-recovery-with-a-transient-clone"></a>Elemszintű helyreállítás az átmeneti másolat
Egy éves Microsoft PowerPoint bemutatót fájlhelyreállítás kell. A rendszergazda a biztonsági másolat egy adott időszak azonosítja, szűrőt, és a kötetet. A rendszergazda ezután a kötet klónozásával készíti, talál a fájlt, amely a keresett, és itt meg. Ebben a forgatókönyvben egy átmeneti Klónozás szolgál. 

![Videó elérhető](./media/storsimple-clone-volume/Video_icon.png)**Videó elérhető**

Bemutató videó bemutatja, hogyan használhatja a Klónozás és szolgáltatások a StorSimple törölt fájlok visszaállítása, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Az éles környezetben az állandó másolat tesztelése
Vissza kell igazolnia egy tesztelési hiba a termelési környezetben. Pillanatkép készítése a ehhez a klónhoz felhő másolat a kötet létrehozásához az éles környezetben. A klónozott kötet már független. Ebben a forgatókönyvben egy állandó Klónozás szolgál.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple-kötet visszaállítása egy biztonságimásolat-készlet](storsimple-restore-from-backup-set.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

