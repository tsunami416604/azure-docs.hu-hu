---
title: StorSimple Snapshot Manager MMC menü műveletek | Microsoft dokumentumok
description: A cikk a Microsoft Management Console (MMC) szabványos menüműveletek használatát ismerteti a StorSimple Snapshot Manager programban.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: ced075395c4f2a51308d5d3b0cbe4c0153a17816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931469"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Az MMC menüműveletek használata a StorSimple Snapshot Managerben

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager ben a következő műveletek jelennek meg az összes műveletmenüben és a **Műveletek** ablaktábla összes változatában.

* Nézet
* Új ablak innen 
* Frissítés 
* Lista exportálása 
* Súgó 

Ezek a műveletek a Microsoft Management Console (MMC) részét képezik, és nem a StorSimple Snapshot Managerre vonatkoznak. Ez az oktatóanyag ismerteti ezeket a műveleteket, és elmagyarázza, hogyan használhatja őket a StorSimple Snapshot Managerben.

## <a name="view"></a>Nézet
A **Nézet** beállítással módosíthatja az **Eredmények** ablaktábla nézetét, és módosíthatja a konzolablak nézetét. 

#### <a name="to-change-the-results-pane-view"></a>Az Eredmények ablaktábla nézetének módosítása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal valamelyik csomópontra, vagy bontsa ki a csomópontot, és kattintson a jobb gombbal egy elemre az **Eredmények** ablaktáblán, majd kattintson a **Nézet** parancsra. 
3. Az **Eredmények** ablaktáblán megjelenő oszlopok hozzáadásához vagy eltávolításához kattintson az **Oszlopok hozzáadása/eltávolítása gombra.** Megjelenik az **Oszlopok hozzáadása/eltávolítása** párbeszédpanel.
   
    ![Oszlopok hozzáadása és eltávolítása az Eredmények ablaktáblából](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Töltse ki az űrlapot az alábbiak szerint:
   
   * Jelölje ki az Elemeket az **Elérhető** oszlopok listából, és a **Hozzáadás** gombra kattintva vegye fel őket a **Megjelenített oszlopok** listájára. 
   * Kattintson a **Megjelenített oszlopok** lista elemeire, majd az **Eltávolítás** gombra kattintva távolítsa el őket a listából. 
   * Jelöljön ki egy elemet a **Megjelenített** oszlopok listában, majd a **Fel** vagy **a Le gombra** kattintva mozgassa az elemet felfelé vagy lefelé a listában. 
   * Az **Eredmények** ablaktábla alapértelmezett konfigurációjához való visszatéréshez kattintson az **Alapértelmezések visszaállítása** gombra. 
5. Ha végzett a beállításokkal, kattintson az **OK**gombra. 

#### <a name="to-change-the-console-window-view"></a>A konzolablak nézetének módosítása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal valamelyik csomópontra, kattintson a **Nézet**parancsra, majd kattintson a **Testreszabás parancsra.** Megjelenik a **Testreszabás** párbeszédpanel.
   
    ![A konzolablak testreszabása](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Jelölje be a jelölőnégyzeteket, vagy törölje belőle a jelet, ha meg szeretné jelenlennie vagy el szeretné rejteni az elemeket a konzolablakban. Ha végzett a beállításokkal, kattintson az **OK**gombra.

## <a name="new-window-from-here"></a>Új ablak innen
Az Új **ablak innen** lehetőséggel új konzolablakot nyithat meg.

#### <a name="to-open-a-new-console-window"></a>Új konzolablak megnyitása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal valamelyik csomópontra, majd kattintson **az Új ablak innen parancsra.** 
   
    Egy új ablak jelenik meg, amely csak a kiválasztott hatókört jeleníti meg. Ha például a jobb gombbal a **Biztonsági mentési házirendek** csomópontra kattint, az új ablakban csak a **Biztonsági másolat házirendek** csomópont jelenik meg a **Hatókör** ablaktáblában, és a definiált biztonsági mentési házirendek listája az **Eredmények** ablaktáblában. Tekintse meg a következő példát.
   
    ![Új ablak innen](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Frissítés
A **Frissítés** művelettel frissítheti a konzolablakot.

#### <a name="to-update-the-console-window"></a>A konzolablak frissítése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal valamelyik csomópontra, vagy bontsa ki a csomópontot, kattintson a jobb gombbal egy elemre az **Eredmények** ablaktáblán, majd kattintson a **Frissítés parancsra.** 

## <a name="export-list"></a>Lista exportálása
A Lista **exportálása** művelettel vesszővel tagolt (CSV) fájlba mentheti a listát. Exportálhatja például a biztonsági mentési házirendek listáját vagy a biztonsági másolat katalógusát. Ezután importálhatja a CSV-fájlt egy táblázatkezelő alkalmazásba elemzésre.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Lista mentése vesszővel tagolt értékfájlba (CSV)
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához. 
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal valamelyik csomópontra, vagy bontsa ki a csomópontot, kattintson a jobb gombbal egy elemre az **Eredmények** ablaktáblán, majd kattintson a **Lista exportálása parancsra.** 
3. Megjelenik **a Lista exportálása** párbeszédpanel. Töltse ki az űrlapot az alábbiak szerint: 
   
   1. A **Fájlnév** mezőbe írja be a CSV-fájl nevét, vagy kattintson a legördülő listából kiválasztható nyílra.
   2. A **Fájltípus** mezőben kattintson a nyílra, és válasszon egy fájltípust a legördülő listából.
   3. Ha csak a kijelölt elemeket szeretné menteni, jelölje ki a sorokat, majd kattintson a **Csak a kijelölt sorok mentése** jelölőnégyzetre. Az összes exportált lista mentéséhez törölje a jelet a **Csak a kijelölt sorok mentése** jelölőnégyzetből.
   4. Kattintson a **Mentés** gombra.
      
      ![Lista exportálása vesszővel tagolt értékfájlként](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Súgó
A **Súgó** menüsegítségével megtekintheti a StorSimple Snapshot Manager és az MMC elérhető online súgóját.

#### <a name="to-view-available-online-help"></a>Az elérhető online súgó megtekintése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal valamelyik csomópontra, vagy bontsa ki a csomópontot, kattintson a jobb gombbal egy elemre az **Eredmények** ablaktáblán, majd kattintson a **Súgó parancsra.** 

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple Snapshot Manager felhasználói felületéről.](storsimple-use-snapshot-manager.md)
* További információ a [StorSimple Snapshot Manager használatával a StorSimple megoldás felügyeletéről.](storsimple-snapshot-manager-admin.md)

