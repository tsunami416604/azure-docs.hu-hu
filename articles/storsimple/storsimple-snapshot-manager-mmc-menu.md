---
title: A StorSimple Snapshot Manager MMC menü műveletek |} A Microsoft Docs
description: Ismerteti, hogyan használhatja a Microsoft Management Console (MMC) szokásos menü műveletek a StorSimple Snapshot Managerben.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: be761f0a2e59878139ae54dba62634a53b172711
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302999"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Az MMC-menü műveletek használata a StorSimple Snapshot Managerben

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Managerben, látni fogja a következő műveletek felsorolt összes művelet menü, és minden változata a **műveletek** ablaktáblán.

* Nézet
* Itt új ablakban 
* Frissítés 
* Lista exportálása 
* Súgó 

Ezek a műveletek részei a Microsoft Management Console (MMC), és a nem konkrétan a StorSimple Snapshot Manager. Ez az oktatóanyag ismerteti ezeket a műveleteket, és ismerteti, hogyan azok a StorSimple Snapshot Managerben.

## <a name="view"></a>Nézet
Használhatja a **nézet** lehetőséggel módosíthatja a **eredmények** ablakban és a konzol ablakot nézetet. 

#### <a name="to-change-the-results-pane-view"></a>Az eredmények ablaktáblán nézet módosítása
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemet a **eredmények** ablaktáblán, és kattintson a **nézet** lehetőséget. 
3. Hozzáadása vagy eltávolítása a megjelenő oszlopok a **eredmények** ablaktáblán kattintson a **oszlopok hozzáadása és eltávolítása**. A **oszlopok hozzáadása és eltávolítása** párbeszédpanel jelenik meg.
   
    ![Oszlopok hozzáadása vagy törlése az eredmények ablaktáblán](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Töltse ki az űrlapot a következőképpen:
   
   * Válassza ki azokat az elemeket a **elérhető** oszlopok listában, és kattintson a **Hozzáadás** adja hozzá őket a **megjelenő oszlopok** listája. 
   * Kattintson a elemekre a **megjelenő oszlopok** listában, és kattintson a **eltávolítása** eltávolítja őket a listából. 
   * Válasszon ki egy elemet a a **megjelenő** oszlopok listában, és kattintson a **feljebb** vagy **lejjebb** az elem áthelyezése felfelé vagy lefelé a listában. 
   * Kattintson a **alapértelmezett értékek visszaállítása** térjen vissza az alapértelmezett **eredmények** konfigurációs panelen. 
5. Amikor végzett a a beállításokat, kattintson a **OK**. 

#### <a name="to-change-the-console-window-view"></a>A konzol ablakot nézetet
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal minden olyan csomópontot, kattintson a **nézet**, és kattintson a **Testreszabás**. A **Testreszabás** párbeszédpanel jelenik meg.
   
    ![A konzolablakban testreszabása](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Válassza ki, vagy törölje a jelet a jelölőnégyzetből, megjelenítése vagy elrejtése a konzolablakban elemek. Amikor végzett a a beállításokat, kattintson a **OK**.

## <a name="new-window-from-here"></a>Itt új ablakban
Használhatja a **itt új ablakban** lehetőséget egy új ablakot is megnyit.

#### <a name="to-open-a-new-console-window"></a>Egy új konzolablak megnyitása
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópont, és kattintson **itt új ablakban**. 
   
    Egy új ablakban megjelenik, a hatókör csak a kijelölt. Például, ha a jobb gombbal a **biztonsági mentési szabályzatok** csomópont, az új ablakban jelennek meg csak a **biztonsági mentési szabályzatok** csomópontja a **hatókör** ablaktábla és a egy meghatározott biztonsági mentés listája a szabályzatok a **eredmények** ablaktáblán. Tekintse meg a következő példát.
   
    ![Itt új ablakban](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Frissítés
Használhatja a **frissítése** művelettel a konzolablakot.

#### <a name="to-update-the-console-window"></a>A konzolablakban frissítése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemet a **eredmények** ablaktáblán, és kattintson **frissítése**. 

## <a name="export-list"></a>Lista exportálása
Használhatja a **lista exportálása** művelet listáját egy vesszővel tagolt (CSV) fájl mentéséhez. Ha például a biztonsági mentési szabályzatok és a biztonságimásolat-katalógus listája exportálhatja. Ezután importálhatja a CSV-fájl egy számolótábla alkalmazásba elemzés céljából.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Listáját egy vesszővel tagolt (CSV) fájl mentéséhez
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához. 
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemet a **eredmények** ablaktáblán, és kattintson **lista exportálása**. 
3. A **lista exportálása** párbeszédpanel jelenik meg. Töltse ki az űrlapot a következőképpen: 
   
   1. Az a **Fájlnév** mezőbe írja be a CSV-fájl nevét, vagy kattintson a nyílra kattintva válassza ki a legördülő listából.
   2. Az a **Fájltípus** mezőbe, kattintson a nyílra, és a legördülő listából válassza ki a fájl típusa.
   3. Csak a kijelölt elemek mentése, a sorok kiválasztásához, majd kattintson a **mentése csak a kijelölt sorok** jelölőnégyzetet. Mentse az összes exportált listák, törölje a jelet a **mentése csak a kijelölt sorok** jelölőnégyzetet.
   4. Kattintson a **Save** (Mentés) gombra.
      
      ![Lista exportálása egy vesszővel tagolt fájl](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Súgó
Használhatja a **súgó** menüben elérhető online a StorSimple Snapshot Manager és az MMC súgójának megtekintéséhez.

#### <a name="to-view-available-online-help"></a>Elérhető online súgójának megtekintéséhez
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal bármelyik csomópont, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemet a **eredmények** ablaktáblán, és kattintson **súgó**. 

## <a name="next-steps"></a>További lépések
* Tudjon meg többet a [StorSimple Snapshot Manager felhasználói felületén](storsimple-use-snapshot-manager.md).
* Tudjon meg többet [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).

