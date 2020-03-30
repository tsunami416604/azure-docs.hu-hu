---
title: StorSimple 8000 sorozatú biztonsági mentési házirendek kezelése | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja a StorSimple Eszközkezelő szolgáltatást manuális biztonsági mentések, biztonsági mentési ütemezések és biztonsági mentési megőrzések létrehozásához és kezeléséhez egy StorSimple 8000 sorozatú eszközön.
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
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267793"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>A StorSimple Eszközkezelő szolgáltatás használata az Azure Portalon a biztonsági mentési szabályzatok kezeléséhez


## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja a StorSimple Eszközkezelő szolgáltatás **biztonsági mentési házirend** paneljének biztonsági mentési folyamatainak és a StorSimple-kötetek biztonsági mentési megtartásának szabályozásához. Azt is leírja, hogyan kell elvégezni a manuális biztonsági mentést.

Kötet biztonsági felvételekor helyi pillanatképet vagy felhőbeli pillanatképet hozhat létre. Ha helyileg rögzített kötetről készít biztonsági másolatot, javasoljuk, hogy adjon meg egy felhőbeli pillanatképet. A helyileg rögzített kötet helyi rögzített kötetek nagyszámú helyi pillanatképek egy adathalmaz, amely sok lemorzsolódás eredményez olyan helyzetet, amelyben gyorsan elfogy a helyi terület. Ha úgy dönt, hogy a helyi pillanatképek, azt javasoljuk, hogy kevesebb napi pillanatképek biztonsági másolatot készítsen a legutóbbi állapot, megtartva őket egy napra, majd törölje őket.

Amikor egy helyileg rögzített kötet felhőbeli pillanatképét készíti, csak a módosított adatokat másolja a felhőbe, ahol a deduplikált és tömörített.

## <a name="the-backup-policy-blade"></a>A Biztonsági mentési házirend panel

A StorSimple-eszköz **biztonsági mentési házirend** panelje lehetővé teszi a biztonsági mentési szabályzatok kezelését és a helyi és felhőbeli pillanatképek ütemezését. A biztonsági mentési házirendek a biztonsági mentési ütemezések és a kötetek gyűjteményének biztonsági mentési megtartásának konfigurálására szolgálnak. A biztonsági mentési házirendek lehetővé teszik, hogy egyszerre több kötetről készítsen pillanatképet. Ez azt jelenti, hogy a biztonsági mentési házirend által létrehozott biztonsági mentések összeomlás-konzisztens másolatok lesznek.

A biztonsági mentési házirendek táblázatos listázása lehetővé teszi a meglévő biztonsági mentési házirendek szűrését a következő mezők közül egy vagy több:

* **Házirend neve** – a házirendhez társított név. A különböző típusú házirendek a következők:

  * Ütemezett házirendek, amelyeket a felhasználó kifejezetten létrehoz.
  * Importált házirendek, amelyek eredetileg a StorSimple Snapshot Manager. Ezek egy címkével rendelkeznek, amely leírja a StorSimple Snapshot Manager gazdagép, amely a házirendek importálták.

  > [!NOTE]
  > Az automatikus vagy alapértelmezett biztonsági mentési házirendek már nincsenek engedélyezve a kötetlétrehozásakor.

* **Utolsó sikeres biztonsági mentés** – A házirenddel készített utolsó sikeres biztonsági mentés dátuma és időpontja.

* **Következő biztonsági mentés** – A házirend által kezdeményezett következő ütemezett biztonsági mentés dátuma és időpontja.

* **Kötetek** – a házirendhez társított kötetek. A biztonsági mentési házirendhez társított kötetek a biztonsági mentések létrehozásakor egy csoportba vannak csoportosítva.

* **Ütemezések** – A biztonsági mentési házirendhez társított ütemezések száma.

A biztonsági mentési házirendekhez gyakran használt műveletek a következők:

* Biztonsági mentési szabályzat hozzáadása
* Ütemezés hozzáadása vagy módosítása
* Kötet hozzáadása vagy eltávolítása
* Biztonságimentési házirend törlése
* Kézi biztonsági mentés készítése

## <a name="add-a-backup-policy"></a>Biztonsági mentési szabályzat hozzáadása

Adjon hozzá egy biztonsági mentési házirendet a biztonsági mentések automatikus ütemezéséhez. Amikor először hoz létre kötetet, nincs alapértelmezett biztonsági mentési házirend társítva a kötethez. A kötetadatok védelme érdekében biztonsági mentési házirendet kell hozzáadnia és hozzárendelnie.

Hajtsa végre az alábbi lépéseket az Azure Portalon a StorSimple-eszköz biztonsági mentési szabályzatának hozzáadásához. A házirend hozzáadása után megadhat ütemezést [(lásd: Ütemezés hozzáadása vagy módosítása).](#add-or-modify-a-schedule)

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Ütemezés hozzáadása vagy módosítása

Hozzáadhat vagy módosíthat egy ütemezést, amely a StorSimple-eszközön meglévő biztonsági mentési szabályzathoz van csatolva. Az alábbi lépéseket az Azure Portalon ütemezés hozzáadásához vagy módosításához hajtsa végre.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Kötet hozzáadása vagy eltávolítása

Hozzáadhat vagy eltávolíthat egy biztonsági mentési házirendhez rendelt kötetet a StorSimple-eszközön. A kötet hozzáadásához vagy eltávolításához hajtsa végre az alábbi lépéseket az Azure Portalon.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Biztonságimentési házirend törlése

Hajtsa végre az alábbi lépéseket az Azure Portalon a StorSimple-eszközön lévő biztonsági mentési szabályzat törléséhez.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Kézi biztonsági mentés készítése

Hajtsa végre az alábbi lépéseket az Azure Portalon egy igény szerinti (manuális) biztonsági mentés létrehozásához egyetlen kötethez.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>További lépések

További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

