---
title: A StorSimple 8000 sorozat biztonsági mentési házirendek kezelése |} A Microsoft Docs
description: Ismerteti, hogyan használhatja a StorSimple-Eszközkezelő szolgáltatás létrehozásához és kezeléséhez a manuális biztonsági mentések, biztonsági mentési ütemezés és a StorSimple 8000 sorozatú eszköz a biztonsági másolatok.
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681588"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás segítségével biztonsági mentési házirendek kezelése


## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan használhatja a StorSimple-Eszközkezelő szolgáltatás **biztonsági mentési szabályzat** biztonsági mentési folyamatainak és a StorSimple-köteteket a biztonsági másolatok megőrzési panelen. Emellett bemutatja, hogyan lehet manuális biztonsági mentés végrehajtásához.

Amikor, biztonsági másolatot készíteni egy kötetről, választhat, helyi pillanatképet, vagy a felhőbeli pillanatkép létrehozásához. Biztonsági mentése helyileg rögzített kötetet, azt javasoljuk, hogy megad egy felhőbeli pillanatképet. Helyi pillanatképeivel ügyfélparancsfájl egy adatkészlet, amely rendelkezik a forgalom rengeteg helyileg rögzített kötetet nagy számú véve eredményez olyan helyzet, amelyben gyorsan futtathat helyi lemezterületét. Ha a helyi pillanatképek választja, azt javasoljuk, hogy kevesebb napi pillanatképek készítsen biztonsági másolatot a legutóbbi állapot megőrzése napi őket, és ezután törölheti őket.

Egy helyileg rögzített kötet felhőbeli pillanatkép készítése, ha csak a módosított adatokat a felhőben, ahol deduplikált és tömöríti, másolja.

## <a name="the-backup-policy-blade"></a>A biztonsági mentési házirend panelen

A **biztonsági mentési szabályzat** panelen a StorSimple eszköz lehetővé teszi, hogy a biztonsági mentési házirendek kezelése és ütemezheti a helyi és felhőalapú pillanatfelvételek. Biztonsági mentési szabályzatok segítségével biztonsági mentési ütemezéseket, és a biztonsági másolatok megőrzési köteteken gyűjteményét. Biztonsági mentési szabályzatok lehetővé teszik, hogy egyszerre több kötet pillanatkép készítése. Ez azt jelenti, hogy a biztonsági mentési szabályzat által létrehozott biztonsági mentéseket kell-e a összeomlás-konzisztens másolatot.

Biztonsági mentési szabályzatok táblázatos lehetővé teszi a meglévő biztonsági mentési szabályzatok közül egy vagy több az alábbi mezőkkel szűrheti:

* **Házirend neve** – a szabályzathoz társított nevét. A különböző típusú szabályzatok a következők:

  * Ütemezett házirendek, amelyek kifejezetten a felhasználó által létrehozott.
  * Importált szabályzatokat, amelyekkel a StorSimple Snapshot Managerben lettek létrehozva. Ezeknek kell egy címke, amely ismerteti a StorSimple Snapshot Manager-gazdagép, amely a házirendek történő importálásakor.

  > [!NOTE]
  > Automatikus vagy alapértelmezett biztonsági mentési szabályzatok már nincs engedélyezve a kötet létrehozása idején.

* **Utolsó sikeres biztonsági mentés** – dátuma és időpontja, a legutóbbi sikeres készült biztonsági másolat az ehhez a szabályzathoz.

* **Következő biztonsági mentés** – dátuma és időpontja, a következő ütemezett biztonsági mentés, amely ezt a házirendet kell kezdeményeznie.

* **Kötetek** – a szabályzathoz társított kötetek. A biztonsági mentési szabályzathoz társított összes kötetet a biztonsági mentések létrehozásakor együtt vannak csoportosítva.

* **Ütemezések** – a biztonsági mentési szabályzathoz társított ütemezések száma.

A gyakran használt műveletek a biztonsági mentési szabályzatok hajthat végre a következők:

* Biztonsági mentési szabályzat hozzáadása
* Adjon hozzá vagy ütemezésének módosítása
* Adja hozzá, vagy távolítsa el a kötet
* A biztonsági mentési szabályzat törlése
* Manuális biztonsági mentés készítése

## <a name="add-a-backup-policy"></a>Biztonsági mentési szabályzat hozzáadása

Adjon hozzá egy biztonsági mentési szabályzat automatikusan a a biztonsági mentések ütemezéséhez. Először hozzon létre egy kötetet, ha egy alapértelmezett biztonsági mentési házirend sem a kötethez társított. Adja hozzá, és a mennyiségi adatok védelme érdekében biztonsági mentési szabályzat hozzárendelése kell.

Hajtsa végre az alábbi lépéseket az Azure Portalon a StorSimple eszköz biztonsági mentési házirend hozzáadása. Miután hozzáadta a házirendet, megadhatja az ütemezés (lásd: [hozzáadása vagy módosítása egy ütemezés](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Adjon hozzá vagy ütemezésének módosítása

Adja hozzá, vagy egy meglévő biztonsági mentési szabályzat a StorSimple eszköz csatolt ütemezésének módosítása. A következő lépésekkel hozzáadása vagy ütemezésének módosítása az Azure Portalon.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Adja hozzá, vagy távolítsa el a kötet

Adja hozzá, vagy távolítsa el a kötetet, a StorSimple eszköz biztonsági mentési szabályzat rendelve. Hajtsa végre az alábbi lépéseket az Azure Portalon hozzáadásához, vagy távolítsa el a kötetet.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>A biztonsági mentési szabályzat törlése

Hajtsa végre az alábbi lépéseket az Azure Portalon a StorSimple eszköz biztonsági mentési szabályzat törlése.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése

A következő lépésekkel hozhat létre egy kötetet egy igény szerinti (manuális) biztonsági mentése az Azure Portalon.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>További lépések

Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

