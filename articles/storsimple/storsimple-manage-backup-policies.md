---
title: "A StorSimple biztonsági mentési házirendek kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan használhatja a StorSimple Manager szolgáltatás létrehozásához és kezeléséhez manuális biztonsági mentés, a biztonsági mentési ütemezés és a biztonsági másolatok megőrzésének."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d1834bc8-d520-4463-82ae-3b32fabd021c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos
ms.openlocfilehash: c1e9d5d0450bab5d371aafb40fd7c5920d39dfdb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>A StorSimple Manager szolgáltatás segítségével biztonsági mentési házirendek kezelése
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti a StorSimple Manager szolgáltatással **biztonsági mentési házirendek** lap biztonsági mentési folyamatokat és a StorSimple-köteteket a biztonsági másolatok megőrzésének szabályozására. Azt is bemutatja, hogyan hajthatja végre manuális biztonsági mentés.

A **biztonsági mentési házirendek** lap lehetővé teszi a biztonsági mentési házirendek kezelése és ütemezése helyi és felhőalapú pillanatfelvételek. (Biztonsági mentési házirendek segítségével konfigurálhatja a biztonsági mentési ütemezés és a biztonsági másolatok megőrzésének kötetek gyűjteményét.) Biztonsági mentési házirendek lehetővé teszik a pillanatkép készítése több kötet egy időben. Ez azt jelenti, hogy a biztonsági mentési házirend által létrehozott biztonsági mentéseket kell-e a összeomlás-konzisztens másolja. Ez a lap felsorolja a biztonsági mentési házirendek, azok típusát, a társított kötetek, a megőrzött biztonsági mentéseinek számát és ezek a házirendek lehetővé teszi.

A **biztonsági mentési házirendek** lap is lehetővé teszi egy vagy több a következő mezőket a meglévő biztonsági mentési házirendek szűrése:

* **Házirend neve** – a házirendhez társított név. A különböző típusú házirendek a következők:
  
  * Ütemezett házirendek, amelyek kifejezetten a felhasználó által létrehozott.
  * Az automatikus házirendek, amelyek jönnek létre, ha a kötet beállítás az alapértelmezett biztonsági mentési kötet létrehozása idején volt engedélyezve. Ezek a házirendek, ahol kötetneve vonatkozik-e a neve a klasszikus Azure portálon a felhasználó által beállított StorSimple-kötet VolumeName_Default neve. Az automatikus házirendek 22:30 eszköz időpontban verziótól napi felhőalapú pillanatfelvételek eredményez.
  * Importált házirendek, amelyeket a StorSimple Snapshot Manager lettek létrehozva. A StorSimple Snapshot Manager gazdagép, a házirendek az importált címkéjét ezek rendelkezik.
* **Kötetek** – a házirendhez társított kötetek. A biztonsági mentési házirend társított összes kötet egy csoportba kerülnek, biztonsági mentések létrehozásakor.
* **Utolsó sikeres biztonsági mentés** – a dátum és idő, a legutóbbi sikeres készült biztonsági másolat az ehhez a szabályzathoz.
* **Következő biztonsági mentés** – a dátum és idő, a következő ütemezett biztonsági mentés, hogy ez a házirend indul.
* **Ütemezések** – a biztonsági mentési házirend kapcsolódó száma.

A gyakran használt, ezen a lapon elvégezhető műveletek a következők:

* Biztonsági mentési szabályzat hozzáadása 
* Adja hozzá vagy ütemezésének módosítása 
* A biztonsági mentési házirend törlése 
* Manuális biztonsági mentés készítése 
* Hozzon létre egy egyéni biztonsági mentési házirend több kötetek és ütemezése 

## <a name="add-a-backup-policy"></a>Biztonsági mentési szabályzat hozzáadása
Adja hozzá a biztonsági mentési házirend automatikusan ütemezni a biztonsági másolatokat. Hajtsa végre a következő lépéseket a klasszikus Azure portálon vegye fel a biztonsági mentési házirend beállítása a StorSimple eszközhöz. Miután hozzáadta a házirendet, definiálhat egy ütemezés (lásd: [hozzáadása vagy ütemezésének módosítása](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![Videó elérhető](./media/storsimple-manage-backup-policies/Video_icon.png) **Videó elérhető**

Bemutató videó bemutatja, hogyan hozzon létre egy helyi vagy felhőalapú biztonsági mentési házirend, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## <a name="add-or-modify-a-schedule"></a>Adja hozzá vagy ütemezésének módosítása
Adja hozzá, vagy módosítsa egy ütemezést, amely egy meglévő biztonsági mentési házirend, a StorSimple eszköz csatlakozik. Hajtsa végre a következő lépéseket a klasszikus Azure portálon hozzáadása vagy módosítása egy ütemezés szerint.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>A biztonsági mentési házirend törlése
Hajtsa végre a következő lépéseket a klasszikus Azure-portálon a StorSimple eszköz biztonsági mentési házirend törlése.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése
Hajtsa végre a következő lépéseket a klasszikus Azure portálon hozzon létre (egy igény szerinti manuális) biztonsági mentés egyetlen kötetén.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Hozzon létre egy egyéni biztonsági mentési házirend több kötetek és ütemezése
Hajtsa végre a következő lépéseket a klasszikus Azure portálon hozzon létre egy egyéni biztonsági mentési házirend, amelyen több kötetek és ütemezések.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]

## <a name="next-steps"></a>Következő lépések
További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

