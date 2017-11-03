---
title: "A StorSimple 8000 series biztonsági mentési házirendek kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan használhatja a StorSimple Device Manager szolgáltatás létrehozásához és kezeléséhez manuális biztonsági mentések, a biztonsági mentési ütemezés és a biztonsági másolatok megőrzésének a StorSimple 8000 series eszközön."
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 569dbfdeb7dcd526cb5a54b487ea1bfb59b13cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>A StorSimple Device Manager szolgáltatás használata Azure-portálon kezelheti a biztonsági mentési házirendek


## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan használhatja a StorSimple Device Manager szolgáltatást **biztonsági mentési házirend** panel biztonsági mentési folyamatokat és a StorSimple-köteteket a biztonsági másolatok megőrzésének szabályozására. Azt is bemutatja, hogyan hajthatja végre manuális biztonsági mentés.

Biztonsági mentését egy kötetet, ha szeretné, hozzon létre egy helyi pillanatfelvétel vagy egy felhőalapú pillanatfelvétel. Ha biztonsági mentést egy helyileg rögzített kötet, javasoljuk, meg kell adnia egy felhőalapú pillanatfelvétel. Helyi pillanatképek készítése, amely rendelkezik a nagy mennyiségű forgalom adatkészlet alapján kialakulhat egy helyileg rögzített kötet nagy mennyiségű véve azt eredményezi, hogy olyan helyzet, amelyben gyorsan futtathatja helyi elfogyott. Ha helyi pillanatképek választja, azt javasoljuk, hogy a legutóbbi állapota, készítsen biztonsági másolatot a megőrizni kívánt őket egy napon kevesebb napi pillanatfelvételeket, és törölje őket.

Amikor a pillanatképet egy felhőalapú, egy helyileg rögzített kötetet, csak a megváltozott adatok másolása a felhőbe, amennyiben deduplikált és tömörített.

## <a name="the-backup-policy-blade"></a>A biztonsági mentési házirend panel

A **biztonsági mentési házirend** a StorSimple eszköz paneljén lehetővé teszi a biztonsági mentési házirendek kezelése és ütemezése helyi és felhőalapú pillanatfelvételek. Biztonsági mentési házirendek segítségével konfigurálhatja a biztonsági mentési ütemezés és a biztonsági másolatok megőrzésének kötetek gyűjteményét. Biztonsági mentési házirendek lehetővé teszik a pillanatkép készítése több kötet egy időben. Ez azt jelenti, hogy a biztonsági mentési házirend által létrehozott biztonsági mentéseket kell-e a összeomlás-konzisztens másolja.

A biztonsági mentési házirendek táblázatos felsorolása lehetővé teszi egy vagy több a következő mezőket a meglévő biztonsági mentési házirendek szűrése:

* **Házirend neve** – a házirendhez társított név. A különböző típusú házirendek a következők:

  * Ütemezett házirendek, amelyek kifejezetten a felhasználó által létrehozott.
  * Importált házirendek, amelyeket a StorSimple Snapshot Manager lettek létrehozva. A StorSimple Snapshot Manager gazdagép, a házirendek az importált címkéjét ezek rendelkezik.

  > [!NOTE]
  > Automatikus vagy alapértelmezett biztonsági mentési házirendek már nincs engedélyezve a kötet létrehozása idején.

* **Utolsó sikeres biztonsági mentés** – a dátum és idő, a legutóbbi sikeres készült biztonsági másolat az ehhez a szabályzathoz.

* **Következő biztonsági mentés** – a dátum és idő, a következő ütemezett biztonsági mentés, hogy ez a házirend indul.

* **Kötetek** – a házirendhez társított kötetek. A biztonsági mentési házirend társított összes kötet egy csoportba kerülnek, biztonsági mentések létrehozásakor.

* **Ütemezések** – a biztonsági mentési házirend kapcsolódó száma.

A gyakran használt műveletek a biztonsági mentési házirendek hajthat végre a következők:

* Biztonsági mentési szabályzat hozzáadása
* Adja hozzá vagy ütemezésének módosítása
* Hozzáadni vagy eltávolítani egy kötet
* A biztonsági mentési házirend törlése
* Manuális biztonsági mentés készítése

## <a name="add-a-backup-policy"></a>Biztonsági mentési szabályzat hozzáadása

Adja hozzá a biztonsági mentési házirend automatikusan ütemezni a biztonsági másolatokat. Először hozzon létre egy kötetet, ha nincs a kötethez társított alapértelmezett biztonsági mentési házirend. Szeretné hozzáadni, és rendelje hozzá a kötet adatainak védelme érdekében biztonsági mentési házirend.

Hajtsa végre a következő lépéseket a StorSimple eszköz biztonsági mentési házirend hozzáadása az Azure portálon. Miután hozzáadta a házirendet, definiálhat egy ütemezés (lásd: [hozzáadása vagy ütemezésének módosítása](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Adja hozzá vagy ütemezésének módosítása

Adja hozzá, vagy módosítsa egy ütemezést, amely egy meglévő biztonsági mentési házirend, a StorSimple eszköz csatlakozik. A következő lépésekkel adja hozzá vagy ütemezésének módosítása az Azure portálon.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Hozzáadni vagy eltávolítani egy kötet

Adja hozzá, vagy távolítsa el a kötetet a biztonsági mentési házirend, a StorSimple eszköz rendelt. A következő lépésekkel lehet hozzáadni vagy eltávolítani egy kötetet az Azure portálon.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>A biztonsági mentési házirend törlése

Hajtsa végre az alábbi lépéseket az Azure-portálon a StorSimple eszköz biztonsági mentési házirend törlése.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése

Hajtsa végre az alábbi lépéseket az Azure-portálon egyetlen kötetén igény szerinti (manuális) biztonsági másolat létrehozásához.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Következő lépések

További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

