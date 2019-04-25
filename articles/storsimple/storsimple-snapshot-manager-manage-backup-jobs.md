---
title: A StorSimple Snapshot Manager biztonsági mentési feladatok |} A Microsoft Docs
description: Az ütemezett, az aktuálisan futó és a befejezett biztonsági mentési feladatok megtekintése és kezelése a StorSimple Snapshot Manager MMC beépülő modul használatát ismerteti.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: c34ff487f03d90b16b6660fbad77c3a16699e165
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303458"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>A biztonsági mentési feladatok megtekintése és kezelése a StorSimple Snapshot Manager használata

## <a name="overview"></a>Áttekintés
A **feladatok** csomópontja a **hatókör** panelen látható a **ütemezett**, **az elmúlt 24 órából**, és **futó** interaktív módon vagy a konfigurált szabályzattal kezdeményezett biztonsági mentési feladatokat. 

Ez az oktatóanyag azt ismerteti, hogyan használhatja a **feladatok** csomópont ütemezett, a legutóbbi és a jelenleg futó biztonsági mentési feladatok információit jeleníti meg. (A feladatok és a megfelelő információkkal listája jelenik meg a **eredmények** ablaktáblán.) Emellett kattintson a jobb gombbal a listában szereplő feladatok, és a egy helyi menü, amely felsorolja az elérhető műveletek megtekintéséhez.

## <a name="view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
Az alábbi eljárás segítségével ütemezett biztonsági mentési feladatok megtekintéséhez.

#### <a name="to-view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához. 
2. Az a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson **ütemezett**. Megjelenik a következő információkat a **eredmények** panelen:
   
   * **Név** – az ütemezett pillanatkép neve
   * **Következő futtatás** – a dátum és idő, a következő ütemezett pillanatképre
   * **Legutóbbi futtatás** – a dátum és idő a legutóbbi ütemezett pillanatkép
     
     > [!NOTE]
     > Egyszeri csak pillanatképekhez a **futtassa a következő** és **utolsó Futtatás** azonos lesz.
     
     ![Ütemezett biztonsági mentési feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. További műveleteket hajthat végre egy adott feladat, kattintson a jobb gombbal a projekt nevére a **eredmények** ablaktábla és a menüpontok közül választhat.

## <a name="view-recent-jobs"></a>Legutóbbi feladatok megtekintése
Az alábbi eljárás segítségével megtekintheti a biztonsági mentési és visszaállítási már az elmúlt 24 órában befejezett feladatok.

#### <a name="to-view-recent-jobs"></a>Legutóbbi feladatok megtekintése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson **az elmúlt 24 órából**. A **eredmények** ablaktáblán jelennek meg a biztonsági mentési feladatok az elmúlt 24 órában (a maximális 64-feladatok). Megjelenik a következő információkat a **eredmények** ablaktáblán, attól függően, a **nézet** megadott beállításokat:
   
   * **Név** – az ütemezett pillanatképre nevét.
   * **Lépések** – a dátum és idő, amikor a pillanatkép kezdete.
   * **Leállítva** – a dátum és idő, amikor a pillanatkép befejeződött, vagy meg lett szakítva.
   * **Eltelt idő** – közötti idő a **elindítva** és **leállítva** alkalommal.
   * **Állapot** – a nemrégiben befejezett feladat állapotát. **Sikeres** azt jelzi, hogy a biztonsági másolat sikeresen létrejött-e. **Nem sikerült** azt jelzi, hogy a feladat nem futott.
   * **Információk** – meg a hiba okát.
   * **Memória (MB) feldolgozott** – a kötet csoportból (a MB-ban) feldolgozott adatok mennyisége. 
     
     ![Feladatok futott az elmúlt 24 órában](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. További műveleteket hajthat végre egy adott feladat, kattintson a jobb gombbal a projekt nevére a **eredmények** ablaktábla és a menüpontok közül választhat.
   
    ![Feladat törlése](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Jelenleg futó feladatok megtekintése
Az alábbi eljárással az aktuálisan futó feladatok megtekintése.

#### <a name="to-view-currently-running-jobs"></a>Jelenleg futó feladatok megtekintése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, majd kattintson **futó**. Attól függően, a **nézet** beállításokat ad meg, megjelenik a következő információkat a **eredmények** panelen:
   
   * **Név** – az ütemezett pillanatképre nevét.
   * **Lépések** – a dátum és idő, amikor a pillanatkép kezdete.
   * **Ellenőrzőpont** – a biztonsági másolat az aktuális művelet.
   * **Állapot** – Befejezés aránya.
   * **Eltelt idő** –, amelyek a biztonsági mentés kezdete óta eltelt idő mennyiségét. 
   * **Átlagos átviteli sebesség (MB)** – arány (MB) feldolgozása teljes idő a feldolgozott adatok teljes mérete (bájtban).
   * **Memória (MB) feldolgozott** – az adatok feldolgozása (MB) bájtok teljes száma.
   * **(MB) írt bájtok** – az adatok (MB)-ben írt bájtok teljes száma. Biztosítja az adatok, valamint a metaadatok, és ezért általában nagyobb, mint a feldolgozott bájt.
     
     ![Jelenleg futó feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. További műveleteket hajthat végre egy adott feladat, kattintson a jobb gombbal a projekt nevére a **eredmények** ablaktábla és a menüpontok közül választhat.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [a biztonságimásolat-katalógus kezelése a StorSimple Snapshot Manager használatával](storsimple-snapshot-manager-manage-backup-catalog.md).

