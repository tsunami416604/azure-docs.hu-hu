---
title: StorSimple Snapshot Manager kötetcsoportok | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a StorSimple Snapshot Manager MMC beépülő modul használatával hogyan hozhat létre és kezelhet kötetcsoportokat.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 5198729cf96fb48c5dcd05096c04ea4d77c26de5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931489"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Kötetcsoportok létrehozása és kezelése a StorSimple Snapshot Manager használatával
## <a name="overview"></a>Áttekintés
A **Hatókör** ablaktábla **Kötetcsoportok** csomópontjával köteteket rendelhet kötetcsoportokhoz, megtekintheti a kötetcsoport adatait, ütemezheti a biztonsági másolatokat, és szerkesztheti a kötetcsoportokat.

A kötetcsoportok a kapcsolódó kötetek készletei, amelyek biztosítják, hogy a biztonsági mentések alkalmazáskonzisztensek legyenek. További információt a [Kötetek és kötetcsoportok,](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) valamint [a Windows kötetárnyékmásolat-szolgáltatással való integráció című témakörben talál.](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)

> [!IMPORTANT]
> * A kötetcsoport ban lévő összes kötetnek egyetlen felhőszolgáltatótól kell származnia.
> * Kötetcsoportok konfigurálásakor ne keverje a fürtmegosztott köteteket és a nem csv-ket ugyanabban a kötetcsoportban. A StorSimple Snapshot Manager nem támogatja a csv-k és a nem csv-k kombinációját ugyanabban a pillanatképben.

![Kötetcsoportok csomópontja](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**1. ábra: StorSimple Snapshot Manager kötetcsoportok csomópontja** 

Ez az oktatóanyag bemutatja, hogyan használhatja a StorSimple Snapshot Managert a következőkre:

* A kötetcsoportokra vonatkozó információk megtekintése
* Kötetcsoport létrehozása
* Kötetcsoport biztonsági és biztonsági másolatot készül
* Kötetcsoport szerkesztése
* Kötetcsoport törlése

Mindezek a műveletek a **Műveletek** ablaktáblán is elérhetők.

## <a name="view-volume-groups"></a>Kötetcsoportok megtekintése
Ha a **Kötetcsoportok** csomópontra kattint, az **Eredmények** ablaktábla a következő információkat jeleníti meg az egyes kötetcsoportokról, attól függően, hogy milyen oszlopkijelölést hoz létre. (Az **Eredmények** ablaktábla oszlopai konfigurálhatók. Kattintson a jobb gombbal a **Kötetek** csomópontra, válassza a **Nézet**parancsot, majd válassza **az Oszlopok hozzáadása/eltávolítása parancsot.)**

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Név |A **Név** oszlop a kötetcsoport nevét tartalmazza. |
| Alkalmazás |Az **Alkalmazások** oszlop a Windows gazdagépen jelenleg telepített és futó VSS-írók számát mutatja. |
| Kiválasztva |A **Kijelölt** oszlop a kötetcsoportban található kötetek számát mutatja. A nulla (0) azt jelzi, hogy nincs alkalmazás társítva a kötetcsoport köteteihez. |
| Importált |Az **Importált** oszlop az importált kötetek számát mutatja. Ha **értéke Igaz,** ez az oszlop azt jelzi, hogy egy kötetcsoport lett importálva az Azure Portalról, és nem jött létre a StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager kötetcsoportok is megjelennek a **Biztonsági mentési szabályzatok** lapon az Azure Portalon.
> 
> 

## <a name="create-a-volume-group"></a>Kötetcsoport létrehozása
Kötetcsoport létrehozásához kövesse az alábbi eljárást.

#### <a name="to-create-a-volume-group"></a>Kötetcsoport létrehozása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal **a Kötetcsoportok**elemre, majd kattintson **a Kötetcsoport létrehozása parancsra.**
   
    ![Kötetcsoport létrehozása](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Megjelenik **a Kötetcsoport létrehozása** párbeszédpanel.
   
    ![Kötetcsoport létrehozása párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Adja meg a következő információkat:
   
   1. A **Név** mezőbe írja be az új kötetcsoport egyedi nevét.
   2. Az **Alkalmazások mezőben** válassza ki a kötetcsoporthoz hozzáadni kívánt kötetekhez társított alkalmazásokat.
      
       Az **Alkalmazások** mező csak azokat az alkalmazásokat sorolja fel, amelyek StorSimple köteteket használnak, és amelyekhez vss-írók engedélyezve vannak. A VSS-író csak akkor engedélyezett, ha az író által tudomásul lévő összes kötet StorSimple kötet. Ha az alkalmazások mező üres, akkor nincs olyan alkalmazások, amelyek Az Azure StorSimple kötetek és támogatott VSS-írók telepítve vannak. (Jelenleg az Azure StorSimple támogatja a Microsoft Exchange és az SQL Server.) A VSS-írókról további információt az [Integráció a Windows kötet árnyékmásolata szolgáltatással című témakörben talál.](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)
      
       Ha kiválaszt egy alkalmazást, a hozzá társított összes kötet automatikusan kiválasztásra kerül. Ezzel szemben, ha egy adott alkalmazáshoz társított köteteket választ ki, az alkalmazás automatikusan be lesz jelölve az **Alkalmazások** mezőben. 
   3. A **Kötetek mezőben** válassza a Kötetcsoporthoz hozzáadni kívánt StorSimple kötetek lehetőséget. 
      
      * Egy vagy több partícióval rendelkező köteteket is felvehet. (Több partíciókötet lehet dinamikus lemez vagy több partícióval rendelkező alaplemez.) A több partíciót tartalmazó köteteket a rendszer egyetlen egységként kezeli. Ennek következtében, ha csak az egyik partíciót adja hozzá egy kötetcsoporthoz, az összes többi partíció automatikusan hozzáadódik a kötetcsoporthoz. Miután több partíciókötetet adott hozzá egy kötetcsoporthoz, a több partíciókötet továbbra is egyetlen egységként lesz kezelve.
      * Üres kötetcsoportokat úgy hozhat létre, hogy nem rendel hozzájuk köteteket. 
      * Ne keverje a fürtmegosztott köteteket és a nem csv-ket ugyanabban a kötetcsoportban. A StorSimple Snapshot Manager nem támogatja a CSV-kötetek és a nem CSV-kötetek kombinációját ugyanabban a pillanatképben.
4. A kötetcsoport mentéséhez kattintson az **OK** gombra.

## <a name="back-up-a-volume-group"></a>Kötetcsoport biztonsági és biztonsági másolatot készül
Kötetcsoport biztonsági és biztonsági biztonsági másolatot készüléséhez kövesse n alábbi eljárással.

#### <a name="to-back-up-a-volume-group"></a>Kötetcsoport biztonsági és biztonsági leépítése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán bontsa ki a **Kötetcsoportok csomópontot,** kattintson a jobb gombbal egy kötetcsoport nevére, majd kattintson a **Biztonsági másolat készítése parancsra.**
   
    ![Kötetcsoport azonnali biztonsági és biztonsági leépítése](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. A **Biztonsági másolat készítése** párbeszédpanelen válassza a **Helyi pillanatkép** vagy a **Felhőbeli pillanatkép**lehetőséget, majd kattintson a **Létrehozás gombra.**
   
    ![Biztonsági mentés idikta](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. A biztonsági másolat futásának ellenőrzéséhez bontsa ki a **Feladatcsomópontot,** majd kattintson a **Futtatás**gombra. A biztonsági mentést fel kell sorolni.
5. A kész pillanatkép megtekintéséhez bontsa ki a **Biztonsági másolat katalógus** csomópontját, bontsa ki a kötetcsoport nevét, majd kattintson a Helyi pillanatkép vagy a **Felhőbeli pillanatkép parancsra.** **Local Snapshot** A biztonsági mentés megjelenik, ha sikeresen befejeződött.

## <a name="edit-a-volume-group"></a>Kötetcsoport szerkesztése
Kötetcsoport szerkesztéséhez kövesse az alábbi eljárást.

#### <a name="to-edit-a-volume-group"></a>Kötetcsoport szerkesztése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán bontsa ki a **Kötetcsoportok csomópontot,** kattintson a jobb gombbal egy kötetcsoport nevére, majd kattintson a **Szerkesztés parancsra.**
3. Megjelenik a **Kötetcsoport létrehozása **párbeszédpanel. Módosíthatja a **Name**, **Applications**és **Volumes** bejegyzéseket.
4. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-a-volume-group"></a>Kötetcsoport törlése
Kötetcsoport törléséhez kövesse az alábbi eljárást. 

> [!WARNING]
> Ezzel a kötetcsoporthoz társított összes biztonsági másolat is törlődik.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Kötetcsoport törlése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán bontsa ki a **Kötetcsoportok csomópontot,** kattintson a jobb gombbal egy kötetcsoport nevére, majd kattintson a **Törlés parancsra.**
3. Megjelenik a **Kötetcsoport törlése** párbeszédpanel. Írja be a Megerősítés szövegmezőbe a **Megerősítés** szöveget, majd kattintson az **OK**gombra.
   
    A törölt kötetcsoport eltűnik az **Eredmények** ablaktáblán lévő listából, és az adott kötetcsoporthoz társított összes biztonsági másolat törlődik a biztonsági másolat katalógusából.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelheti a StorSimple snapshot kezelőt a StorSimple megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet biztonsági mentési házirendeket a StorSimple Snapshot Manager segítségével.](storsimple-snapshot-manager-manage-backup-policies.md)

