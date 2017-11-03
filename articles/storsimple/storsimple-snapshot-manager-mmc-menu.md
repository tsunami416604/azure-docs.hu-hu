---
title: "StorSimple Snapshot Manager MMC menü műveletek |} Microsoft Docs"
description: "A Microsoft Management Console (MMC) szokásos menü műveletek a StorSimple Snapshot Manager használatát ismerteti."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 48f439a566a8067e153aab4fb789937d2f91268d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Az MMC menü műveletek a StorSimple Snapshot Manager használatára

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager látni fogja az alábbi műveletek felsorolt összes művelet menü és az összes változatát a **műveletek** ablaktáblán.

* Nézet
* Itt új ablak 
* Frissítés 
* Lista exportálása 
* Súgó 

Ezek a műveletek része a Microsoft Management Console (MMC) és nem adott a StorSimple Snapshot Manager. Ez az oktatóanyag ismerteti ezeket a műveleteket, és ismerteti, hogyan azok a StorSimple Snapshot Manager.

## <a name="view"></a>Nézet
Használhatja a **nézet** módosításának lehetőségét a **eredmények** ablakban és a konzol ablakot nézet módosításához. 

#### <a name="to-change-the-results-pane-view"></a>Az eredmények ablaktáblán nézet módosítása
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemére a **eredmények** ablaktáblán, és kattintson a **nézet** lehetőséget. 
3. Szeretne felvenni vagy eltávolítani a megjelenő oszlopok a **eredmények** ablaktáblán kattintson a **oszlopok hozzáadása/eltávolítása**. A **oszlopok hozzáadása/eltávolítása** párbeszédpanel jelenik meg.
   
    ![Oszlop hozzáadása vagy eltávolítása az eredmények ablaktábláján](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Töltse ki az űrlapot az alábbiak szerint:
   
   * Válassza ki az elemeket a **elérhető** oszlopok listán, és kattintson **Hozzáadás** kattintva vegye fel a **megjelenő oszlopok** listája. 
   * Elemére a **megjelenő oszlopok** listában, és kattintson a **eltávolítása** kattintva távolítsa el őket a listából. 
   * Jelöljön ki egy elemet a a **megjelenő** oszlopok listán, és kattintson **fel** vagy **le** az elem áthelyezése felfelé vagy lefelé a listában. 
   * Kattintson a **alapértelmezett értékek visszaállítása** visszatér az alapértelmezett **eredmények** ablaktábla konfigurációjának. 
5. Ha elkészült, a beállításokat, kattintson a **OK**. 

#### <a name="to-change-the-console-window-view"></a>A konzol ablakot nézet módosítása
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában kattintson jobb gombbal a csomópontra, kattintson a **nézet**, és kattintson a **Testreszabás**. A **Testreszabás** párbeszédpanel jelenik meg.
   
    ![A konzolablakban testreszabása](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Válassza ki, vagy törölje a megjelenítendő vagy elrejtendő elemeket a konzolablakban jelölőnégyzetet. Ha elkészült, a beállításokat, kattintson a **OK**.

## <a name="new-window-from-here"></a>Itt új ablak
Használhatja a **új ablak innen** Nyisson új konzolablakot lehetőséget.

#### <a name="to-open-a-new-console-window"></a>Nyisson új konzolablakot számára
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópont, és kattintson a **új ablak innen**. 
   
    Egy új ablakban megjelenik az csak a hatókör kijelölt. Például, ha a jobb gombbal a **biztonsági mentési házirendek** csomópont, az új ablakban megjelenik csak a **biztonsági mentési házirendek** csomópontja a **hatókör** ablaktábla és a meghatározott a biztonsági mentési házirendek a **eredmények** ablaktáblán. Tekintse meg a következő példát.
   
    ![Itt új ablak](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Frissítés
Használhatja a **frissítése** a konzolablakban művelettel.

#### <a name="to-update-the-console-window"></a>A konzolablakban frissítése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemére a **eredmények** ablaktáblán, és kattintson **frissítése**. 

## <a name="export-list"></a>Lista exportálása
Használhatja a **lista exportálása** művelet listáját egy vesszővel tagolt (CSV) fájl mentéséhez. Például exportálhatja a biztonsági mentési házirendek vagy a biztonságimásolat-katalógus listáját. Egy táblázat alkalmazásba elemzéshez importálhatja a CSV-fájlt.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Egy listát egy vesszővel tagolt (CSV) fájl mentése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager. 
2. Az a **hatókör** ablaktáblában kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemére a **eredmények** ablaktáblán, és kattintson **lista exportálása**. 
3. A **lista exportálása** párbeszédpanel jelenik meg. Töltse ki az űrlapot az alábbiak szerint: 
   
   1. Az a **Fájlnév** mezőben adja meg a CSV-fájl nevét, vagy kattintson a nyílra kattintva válassza ki a legördülő listából.
   2. Az a **Fájltípus** mezőben, kattintson a nyílra, és jelölje ki a a legördülő listából.
   3. Csak a kijelölt elemek mentéséhez válassza ki azon sorait, és kattintson a **mentése csak a kijelölt sorok** jelölőnégyzetet. Minden exportált listák mentéséhez, vagy törölje a **mentése csak a kijelölt sorok** jelölőnégyzetet.
   4. Kattintson a **Save** (Mentés) gombra.
      
      ![Lista exportálása vesszővel tagolt-fájlként](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Súgó
Használhatja a **súgó** menüjéből érhető el az MMC és a StorSimple Snapshot Manager online súgójának megtekintéséhez.

#### <a name="to-view-available-online-help"></a>Rendelkezésre álló online súgó megtekintése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemére a **eredmények** ablaktáblán, és kattintson **súgó**. 

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md).
* További információ [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).

