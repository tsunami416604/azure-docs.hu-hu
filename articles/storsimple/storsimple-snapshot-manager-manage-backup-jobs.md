---
title: StorSimple Snapshot Manager biztonsági mentési feladatok | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a StorSimple Snapshot Manager MMC beépülő modul használatával hogyan tekintheti meg és kezelheti az ütemezett, jelenleg futó és befejezett biztonsági mentési feladatokat.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933337"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>A StorSimple Snapshot Manager segítségével megtekintheti és kezelheti a biztonsági mentési feladatokat

## <a name="overview"></a>Áttekintés
A **Hatókör** ablaktábla **Feladatok** csomópontja az **Ütemezett**, Az **elmúlt 24 órát**és az interaktívan vagy egy konfigurált házirend által kezdeményezett biztonsági mentési feladatok **futtatását** jeleníti meg. 

Ez az oktatóanyag bemutatja, hogyan használhatja a **Feladatok** csomópontot az ütemezett, a legutóbbi és a jelenleg futó biztonsági mentési feladatokra vonatkozó információk megjelenítéséhez. (A feladatok listája és a megfelelő információk megjelennek az **Eredmények** ablaktáblán.) Ezenkívül a jobb gombbal egy felsorolt feladatra kattinthat, és megtekinthető egy helyi menü, amely felsorolja az elérhető műveleteket.

## <a name="view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
Az ütemezett biztonsági mentési feladatok megtekintéséhez kövesse az alábbi eljárást.

#### <a name="to-view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához. 
2. A **Hatókör** ablaktáblán bontsa ki a **Feladatok** csomópontot, és kattintson az **Ütemezett gombra.** Az **Eredmények** ablaktáblán a következő információk jelennek meg:
   
   * **Név** – az ütemezett pillanatkép neve
   * **Következő futtatás** – a következő ütemezett pillanatkép dátuma és időpontja
   * **Utolsó futtatás** – a legutóbbi ütemezett pillanatkép dátuma és időpontja
     
     > [!NOTE]
     > Csak egyszeri pillanatképek esetén a **következő és** az **utolsó futtatás** ugyanaz lesz.
     
     ![Ütemezett biztonsági mentési feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Ha további műveleteket szeretne végrehajtani egy adott feladaton, kattintson a jobb gombbal a feladat nevére az **Eredmények** ablaktáblán, és válasszon a menüpontok közül.

## <a name="view-recent-jobs"></a>Legutóbbi feladatok megtekintése
Az alábbi eljárással megtekintheti az elmúlt 24 órában elvégzett biztonsági mentési és visszaállítási feladatokat.

#### <a name="to-view-recent-jobs"></a>Legutóbbi feladatok megtekintése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán bontsa ki a **Feladatok** csomópontot, és kattintson az **Elmúlt 24 óra**elemre. **Az Eredmények** ablaktábla az elmúlt 24 órában (legfeljebb 64 feladatig) biztonsági mentési feladatokat jeleníti meg. A megadott **nézetbeállításoktól** függően az **Eredmények** ablaktáblán a következő információk jelennek meg:
   
   * **Név** – az ütemezett pillanatkép neve.
   * **Elindítva** – a pillanatkép kezdő dátuma és időpontja.
   * **Leállítva** – az a dátum és időpont, amikor a pillanatkép befejeződött vagy megszakadt.
   * **Eltelt** – az **Elindított** és a **Leállított** idő közötti idő.
   * **Állapot** – a legutóbb befejezett feladat állapota. **A siker** azt jelzi, hogy a biztonsági mentés sikeresen létrejött. **A sikertelen** azt jelzi, hogy a feladat nem futott sikeresen.
   * **Információ** – a hiba oka.
   * **Feldolgozott bájtok (MB)** – a feldolgozott kötetcsoportból származó adatok mennyisége (MB-ban). 
     
     ![Az elmúlt 24 órában lefutott feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Ha további műveleteket szeretne végrehajtani egy adott feladaton, kattintson a jobb gombbal a feladat nevére az **Eredmények** ablaktáblán, és válasszon a menüpontok közül.
   
    ![Feladat törlése](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>A jelenleg futó feladatok megtekintése
Az alábbi eljárással megtekintheti a jelenleg futó feladatokat.

#### <a name="to-view-currently-running-jobs"></a>Az éppen futó feladatok megtekintése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán bontsa ki a **Feladatok** csomópontot, és kattintson a **Futás**gombra. A megadott **nézetbeállításoktól** függően az **Eredmények** ablaktáblán a következő információk jelennek meg:
   
   * **Név** – az ütemezett pillanatkép neve.
   * **Elindítva** – a pillanatkép kezdő dátuma és időpontja.
   * **Ellenőrzőpont** – a biztonsági mentés aktuális művelete.
   * **Állapot** – a készültségi százalék.
   * **Eltelt** – a biztonsági mentés kezdete óta eltelt idő. 
   * **Átlagos átviteli sebesség (MB)** – a feldolgozott adatok teljes bájtjainak és a feldolgozáshoz szükséges teljes idő (MB) arányának aránya.
   * **Feldolgozott bájtok (MB)** – a feldolgozott adatok teljes bájtjai (MB-ban).
   * **Írt bájt (MB)** – az adatok teljes bájtjai (MB-ban). Ez magában foglalja az adatokat, valamint a metaadatokat, és így általában nagyobb, mint a feldolgozott bájtok.
     
     ![Jelenleg futó feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Ha további műveleteket szeretne végrehajtani egy adott feladaton, kattintson a jobb gombbal a feladat nevére az **Eredmények** ablaktáblán, és válasszon a menüpontok közül.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelheti a StorSimple snapshot kezelőt a StorSimple megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)
* Ismerje meg, hogyan [kezelheti a Biztonságimásolat-katalógust a StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-backup-catalog.md)segítségével.

