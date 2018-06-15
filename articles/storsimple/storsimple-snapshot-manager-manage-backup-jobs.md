---
title: StorSimple Snapshot Manager biztonsági mentési feladatok |} Microsoft Docs
description: A StorSimple Snapshot Manager beépülő MMC-modulban történő megtekintését és kezelését ütemezett, folyamatban levő és befejezett biztonsági mentési feladatok használatát ismerteti.
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
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875815"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>StorSimple Snapshot Manager segítségével megtekintheti és kezelheti a biztonsági mentési feladatok

## <a name="overview"></a>Áttekintés
A **feladatok** csomópontja a **hatókör** látható a **ütemezett**, **utolsó 24 óra**, és **futtató** interaktív módon vagy a beállított házirend kezdeményezett biztonsági mentési feladatokat. 

Ez az oktatóanyag azt ismerteti, hogyan használhatja a **feladatok** csomópont ütemezett, a legutóbbi és a jelenleg futó biztonsági mentési feladatok adatainak megjelenítése. (A feladatok és a megfelelő információkkal listája jelenik meg a **eredmények** ablaktáblán.) Továbbá kattintson a jobb gombbal a listában szereplő feladatok, és tekintse meg a helyi menü, amely felsorolja az elérhető műveletek.

## <a name="view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
Az alábbi eljárás segítségével megtekintheti az ütemezett biztonsági mentési feladatot.

#### <a name="to-view-scheduled-jobs"></a>Ütemezett feladatok megtekintése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager. 
2. Az a **hatókör** ablaktáblában bontsa ki a **feladatok** csomópontot, majd kattintson **ütemezett**. A következő információ szerepel a **eredmények** panelen:
   
   * **Név** – az ütemezett pillanatkép neve
   * **Ezután futtassa** – a dátum és idő, a következő ütemezett pillanatképre
   * **Legutóbbi futtatás** – a dátum és idő, a legutóbbi ütemezett pillanatkép
     
     > [!NOTE]
     > Egyszeri csak pillanatképekhez a **következő futásra** és **utolsó futtatása** ugyanaz lesz.
     
     ![Ütemezett biztonsági mentési feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. További műveleteket hajt végre egy adott feladat, kattintson a jobb gombbal a projekt nevére a **eredmények** ablaktábla és a menüpontok közül választhat.

## <a name="view-recent-jobs"></a>Legutóbbi feladatok megtekintése
A következő eljárás segítségével megtekintheti a biztonsági mentési és visszaállítási feladatok az elmúlt 24 órában elvégzett.

#### <a name="to-view-recent-jobs"></a>Legutóbbi feladatok megtekintése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **feladatok** csomópontot, majd kattintson **utolsó 24 óra**. A **eredmények** ablaktábla megjeleníti azokat a biztonsági mentési feladatok, a legutóbbi 24 órában (a maximális 64 feladatok). A következő információ szerepel a **eredmények** ablaktáblán, attól függően, a **nézet** lehetőségek:
   
   * **Név** – az ütemezett pillanatképre nevét.
   * **Lépések** – dátum és idő, amikor a pillanatképet kezdete.
   * **Leállítva** – dátum és idő, amikor a pillanatképet befejeződött, vagy le lett állítva.
   * **Eltelt** – közötti idő a **elindítva** és **leállítva** alkalommal.
   * **Állapot** – a legutóbb befejeződött feladat állapotát. **Sikeres** azt jelzi, hogy a biztonsági másolat sikeresen létrejött-e. **Nem sikerült** azt jelzi, hogy a feladat futtatása sikertelen volt.
   * **Információ** – a hiba okát.
   * **Memória (MB) feldolgozott** – a kötet csoportból (MB) a feldolgozott adatok mennyisége. 
     
     ![Feladat futott az elmúlt 24 órában](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. További műveleteket hajt végre egy adott feladat, kattintson a jobb gombbal a projekt nevére a **eredmények** ablaktábla és a menüpontok közül választhat.
   
    ![Egy feladat törlése](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Jelenleg futó feladatok megtekintése
Az alábbi eljárással az éppen futó feladatok megtekintése.

#### <a name="to-view-currently-running-jobs"></a>Az éppen futó feladatok megtekintése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **feladatok** csomópontot, majd kattintson **futtató**. Attól függően a **nézet** beállításokat ad meg, a következő információ szerepel a **eredmények** panelen:
   
   * **Név** – az ütemezett pillanatképre nevét.
   * **Lépések** – dátum és idő, amikor a pillanatképet kezdete.
   * **Ellenőrzőpont** – a biztonsági mentés az aktuális művelet.
   * **Állapot** – készenléti százalék.
   * **Eltelt** – a biztonsági mentés óta eltelt idő mennyiségét. 
   * **Átlagos átviteli sebessége (MB)** – viszonyított aránya, valamint a teljes ideje (MB) feldolgozásának feldolgozott adatmennyiség bájtban.
   * **Memória (MB) feldolgozott** – az adatok feldolgozása (MB) bájtok teljes száma.
   * **(MB) írt bájtok** – az adatok írása (MB-ban) bájtok teljes száma. Tartalmazza az adatokat, valamint a metaadatok, és ezért a általában nagyobb, mint a bájtok feldolgozott.
     
     ![Jelenleg futó feladatok](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. További műveleteket hajt végre egy adott feladat, kattintson a jobb gombbal a projekt nevére a **eredmények** ablaktábla és a menüpontok közül választhat.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével kezelheti a biztonságimásolat-katalógus](storsimple-snapshot-manager-manage-backup-catalog.md).

