---
title: "A StorSimple 8000 series kötet klónozása |} Microsoft Docs"
description: "Ismerteti a különböző Klónozás típusok és mikor érdemes használni azokat, és elmagyarázza, hogyan használhatja a biztonságimásolat-készletet úgy klónozni egy egyéni köteten."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 070ac53e-7388-4c48-b8a5-8ed7f9108b2c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/26/2017
ms.author: alkohli
ms.openlocfilehash: 2b627250df62bbe3299869ef3812947afbb8f29f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>A StorSimple Manager szolgáltatás használatával (2. frissítés) kötet klónozása
[!INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Áttekintés
A StorSimple Manager szolgáltatás **biztonságimásolat-katalógus** lap megjeleníti a biztonsági mentés, amely jönnek létre, ha manuális vagy automatikus biztonsági mentés készül. Ezen a lapon a biztonsági mentési házirend, vagy egy kötetet, jelölje be a biztonsági másolatok felsorolása, vagy törölje a biztonsági mentések, vagy a biztonsági másolat használatával állítsa vissza vagy egy kötet klónozása.

![Biztonságimásolat-katalógus lap](./media/storsimple-clone-volume-u2/backupCatalog.png)  

Ez az oktatóanyag leírja, hogyan használhatja a biztonságimásolat-készletet úgy klónozni egy egyéni köteten. Azt is bemutatja a különbség a között *átmeneti* és *állandó* klónozásával készíti.

> [!NOTE]
> Egy helyileg rögzített kötet lesz klónozható egy rétegzett kötet. Ha módosítania kell a klónozott kell helyileg rögzített kötetet, átválthat a klón egy helyileg rögzített kötet a Klónozási művelet sikeres végrehajtása után. A rétegzett kötet konvertálása egy helyileg rögzített kötet kapcsolatos információkért látogasson el [kötet típusának módosítása](storsimple-manage-volumes-u2.md#change-the-volume-type).
> 
> Ha a klónozott kötet konvertálása próbál rétegzett a helyileg rögzített közvetlenül után a Klónozás (ha az még átmeneti másolat), az átalakítás sikertelen lesz, és a következő hibaüzenet:
> 
> `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
> 
> Ez a hiba csak akkor, ha egy másik eszközön be vannak a Klónozás érkezik. Sikeresen átválthat a köteten a helyileg rögzített, ha az átmeneti Klónozás konvertál egy állandó klónnak. Az átmeneti Klónozás átalakítása állandó másolat, pillanatképet egy felhőalapú, azt.
> 
> 

## <a name="create-a-clone-of-a-volume"></a>A kötet klónozhatja
Létrehozhat egy klónozott ugyanarra az eszközre, egy másik eszközt vagy egy virtuális gép még akkor is a helyi vagy felhőalapú pillanatfelvétel.

#### <a name="to-clone-a-volume"></a>Kötet klónozása
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus** lapot, és válasszon egy biztonságimásolat-készlet.
2. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Kattintson, és jelöljön ki egy kötetet a biztonsági mentési készletből.
   
     ![Kötet klónozása](./media/storsimple-clone-volume-u2/CloneVol.png) 
3. Kattintson a **Klónozás** a kijelölt kötetet a Klónozás megkezdéséhez.
4. A Klónozás kötet varázslóban a **név és hely megadása**:
   
   1. Azonosítsa a céleszközön. Ez az a hely, ahol létrejön-e a Klónozás. Válassza ki az adott eszközt, vagy adjon meg egy másik eszközt. Ha, válasszon olyan kötetet, egyéb felhőszolgáltatók társított (nem Azure), a legördülő listából válassza ki a cél eszköz csak akkor jelenik meg fizikai eszközöket. A virtuális eszközön egyéb felhőszolgáltatók társított kötet nem klónozható.
      
      > [!NOTE]
      > Győződjön meg arról, hogy a Klónozás szükséges kapacitást alacsonyabb, mint a céleszközön rendelkezésre álló kapacitás.
      > 
      > 
   2. Adja meg a klónozott egyedi kötet nevét. A név 3 és 127 karakterből állhat. 
      
      > [!NOTE]
      > A **Klónozás kötet mint** mező kitöltése **rétegzett** akkor is, ha meg vannak Klónozás egy helyileg rögzített kötet. Ez a beállítás; nem módosítható azonban ha a klónozott kötet helyileg is rögzítve van szüksége, átválthat a klón egy helyileg rögzített kötet a Klónozás sikeres létrehozását követően. A rétegzett kötet konvertálása egy helyileg rögzített kötet kapcsolatos információkért látogasson el [kötet típusának módosítása](storsimple-manage-volumes-u2.md#change-the-volume-type).
      > 
      > 
      
        ![Klónozás varázsló 1](./media/storsimple-clone-volume-u2/clone1.png) 
   3. Kattintson a nyíl ikonra ![nyíl ikon](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) a folytatáshoz a következő oldalra.
5. A **adja meg a gazdagépekhez, amelyek használhatják ezt a kötetet**:
   
   1. Adjon meg egy hozzáférés-vezérlési rekordot (ACR) a klón. Adja hozzá egy új ACR, vagy válassza ki a meglévő listáról.
      
        ![Klónozás varázsló 2. régiója](./media/storsimple-clone-volume-u2/clone2.png) 
   2. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)a művelet végrehajtásához.
6. A Klónozás feladat indul, és értesítést fog kapni, ha a Klónozás sikeres létrehozását. Kattintson a **feladat megtekintése** a Klónozás feladat figyelése a **feladatok** lap. Ha a Klónozás feladat befejeződött a következő üzenet jelenik meg:
   
    ![Klónozás üzenet](./media/storsimple-clone-volume-u2/CloneMsg.png) 
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
Átmeneti klónok jönnek létre, csak akkor, ha egy másik eszközre vannak a Klónozás. Egy adott köteten beállítása egy másik eszközön, a StorSimple Manager által kezelt mentésből tud klónozni. Az átmeneti Klónozás adatokra hivatkozásokat lesz az eredeti kötet, és írási és olvasási helyileg a céleszközön fogja az adatokat. 

Egy felhő-pillanatfelvételt a átmeneti klón elvégzése után az eredményül kapott Klónozás lesz egy *állandó* Klónozás. A folyamat során az adatok másolatát a felhő jön létre, és ezek az adatok másolása az idő határozza meg az adatok és az Azure késések (Ez az az Azure-Azure másolatot) mérete. A folyamat eltarthat nap, hét. Az átmeneti Klónozás állandó másolat így lesz, és nem rendelkezik az eredeti kötet adatait, amely a klónozása megtörtént mutató hivatkozásokat. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Átmeneti és végleges klónok forgatókönyvei
A következő szakaszok ismertetik például olyan helyzetekben, ahol átmeneti és végleges klónok is használható.

### <a name="item-level-recovery-with-a-transient-clone"></a>Elemszintű helyreállítás az átmeneti másolat
Egy éves Microsoft PowerPoint bemutatót fájlhelyreállítás kell. A rendszergazda a biztonsági másolat egy adott időszak azonosítja, szűrőt, és a kötetet. A rendszergazda ezután a kötet klónozásával készíti, talál a fájlt, amely a keresett, és itt meg. Ebben a forgatókönyvben egy átmeneti Klónozás szolgál. 

![Videó elérhető](./media/storsimple-clone-volume-u2/Video_icon.png) **Videó elérhető**

Bemutató videó bemutatja, hogyan használhatja a Klónozás és szolgáltatások a StorSimple törölt fájlok visszaállítása, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Az éles környezetben az állandó másolat tesztelése
Vissza kell igazolnia egy tesztelési hiba a termelési környezetben. Az éles környezetben klónozhatja a kötet, illetve az ezekkel a felhő-pillanatfelvételt a klón egy független klónozott kötet létrehozásához. Ebben a forgatókönyvben egy állandó Klónozás szolgál.  

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple-kötet visszaállítása egy biztonságimásolat-készlet](storsimple-restore-from-backup-set-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

