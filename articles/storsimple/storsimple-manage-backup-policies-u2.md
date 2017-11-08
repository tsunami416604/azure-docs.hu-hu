---
title: "A StorSimple biztonsági mentési házirendek kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan használhatja a StorSimple Manager szolgáltatás létrehozásához és kezeléséhez manuális biztonsági mentés, a biztonsági mentési ütemezés és a biztonsági másolatok megőrzésének."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 4a2db707-bbfc-425c-bfeb-bc5c97781873
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8bf90b0ca5e488b676b50c37b24202002824c99b
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>A StorSimple Manager szolgáltatás segítségével kezelheti a biztonsági mentési házirendek (2. frissítés)
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a StorSimple Manager szolgáltatás segítségével kezelheti a biztonsági mentési házirendek (2. frissítés)](storsimple-8000-manage-backup-policies-u2.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag ismerteti a StorSimple Manager szolgáltatással **biztonsági mentési házirendek** lap biztonsági mentési folyamatokat és a StorSimple-köteteket a biztonsági másolatok megőrzésének szabályozására. Azt is bemutatja, hogyan hajthatja végre manuális biztonsági mentés.

Biztonsági mentését egy kötetet, ha szeretné, hozzon létre egy helyi pillanatfelvétel vagy egy felhőalapú pillanatfelvétel. Ha biztonsági mentést egy helyileg rögzített kötet, javasoljuk, meg kell adnia egy felhőalapú pillanatfelvétel. Helyi pillanatképek készítése, amely rendelkezik a nagy mennyiségű forgalom adatkészlet alapján kialakulhat egy helyileg rögzített kötet nagy mennyiségű véve azt eredményezi, hogy olyan helyzet, amelyben gyorsan futtathatja helyi elfogyott. Ha helyi pillanatképek választja, azt javasoljuk, hogy a legutóbbi állapota, készítsen biztonsági másolatot a megőrizni kívánt őket egy napon kevesebb napi pillanatfelvételeket, és törölje őket.

Amikor a pillanatképet egy felhőalapú, egy helyileg rögzített kötetet, csak a megváltozott adatok másolása a felhőbe, amennyiben deduplikált és tömörített. 

## <a name="the-backup-policies-page"></a>A biztonsági mentési házirendek lap
A **biztonsági mentési házirendek** lap lehetővé teszi a biztonsági mentési házirendek kezelése és ütemezése helyi és felhőalapú pillanatfelvételek. (Biztonsági mentési házirendek segítségével konfigurálhatja a biztonsági mentési ütemezés és a biztonsági másolatok megőrzésének kötetek gyűjteményét.) Biztonsági mentési házirendek lehetővé teszik a pillanatkép készítése több kötet egy időben. Ez azt jelenti, hogy a biztonsági mentési házirend által létrehozott biztonsági mentéseket kell-e a összeomlás-konzisztens másolja. A **biztonsági mentési házirendek** lap felsorolja a biztonsági mentési házirendek, azok típusát, a társított kötetek, a megőrzött biztonsági mentéseinek számát és ezek a házirendek lehetővé teszi.

A **biztonsági mentési házirendek** lap is lehetővé teszi egy vagy több a következő mezőket a meglévő biztonsági mentési házirendek szűrése:

* **Házirend neve** – a házirendhez társított név. A különböző típusú házirendek a következők:
  
  * Ütemezett házirendek, amelyek kifejezetten a felhasználó által létrehozott.
  * Az automatikus házirendek, amelyek jönnek létre, ha a kötet beállítás az alapértelmezett biztonsági mentési kötet létrehozása idején volt engedélyezve. Ezek a házirendek megnevezett *VolumeName*_alapértelmezett ahol *VolumeName* neve a klasszikus Azure portálon a felhasználó által beállított StorSimple-kötet hivatkozik. Az automatikus házirendek 22:30 eszköz időpontban verziótól napi felhőalapú pillanatfelvételek eredményez.
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

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Videó elérhető](./media/storsimple-manage-backup-policies-u2/Video_icon.png)**Videó elérhető**

Bemutató videó bemutatja, hogyan hozzon létre egy helyi vagy felhőalapú biztonsági mentési házirend, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).

## <a name="add-or-modify-a-schedule"></a>Adja hozzá vagy ütemezésének módosítása
Adja hozzá, vagy módosítsa egy ütemezést, amely egy meglévő biztonsági mentési házirend, a StorSimple eszköz csatlakozik. Hajtsa végre a következő lépéseket a klasszikus Azure portálon hozzáadása vagy módosítása egy ütemezés szerint.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>A biztonsági mentési házirend törlése
Hajtsa végre a következő lépéseket a klasszikus Azure-portálon a StorSimple eszköz biztonsági mentési házirend törlése.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése
Hajtsa végre a következő lépéseket a klasszikus Azure portálon hozzon létre (egy igény szerinti manuális) biztonsági mentés egyetlen kötetén.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Hozzon létre egy egyéni biztonsági mentési házirend több kötetek és ütemezése
Hajtsa végre a következő lépéseket a klasszikus Azure portálon hozzon létre egy egyéni biztonsági mentési házirend, amelyen több kötetek és ütemezések.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## <a name="next-steps"></a>Következő lépések
További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

