---
title: StorSimple Snapshot Manager kötet csoportok |} Microsoft Docs
description: Ismerteti a StorSimple Snapshot Manager MMC beépülő modul segítségével kötet csoportok létrehozásához és kezeléséhez.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>StorSimple Snapshot Manager segítségével kötet csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés
Használhatja a **kötet csoportok** csomópont a **hatókör** kötetek hozzárendelése kötet csoportok, a kötet csoport adatainak megtekintése ablaktábla mentéseket, és a kötet csoportok szerkesztése.

Kötet csoportok azonban a kapcsolódó kötetek, és győződjön meg arról, hogy biztonsági mentések alkalmazáskonzisztens használt. További információkért lásd: [kötetek és a kötet csoportok](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) és [integráció a Windows a kötet árnyékmásolata szolgáltatás](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Egy kötet csoportban található összes kötetet egy felhőszolgáltató kell származnia.
> * Kötet csoportok konfigurálásakor ne keverje a fürt megosztott kötetei (CSV-k) és a CSV kötetet ugyanabban a csoportban. StorSimple Snapshot Manager nem támogatja a CSV-k és a nem megosztott fürtkötetek a azonos pillanatkép.

![Kötet csoportok csomópont](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**1. ábra: A StorSimple Snapshot Manager kötet csoportok csomópont** 

Ez az oktatóanyag azt ismerteti, hogyan használhatja a StorSimple Snapshot Manager:

* A kötet csoportok adatainak megtekintése
* Kötet csoport létrehozása
* Készítsen biztonsági másolatot egy kötet csoport
* Kötet csoport szerkesztése
* Kötet csoport törlése

Ezek a műveletek mindegyike is elérhető a **műveletek** ablaktáblán.

## <a name="view-volume-groups"></a>Kötet csoportok megtekintése
Ha a **kötet csoportok** csomópont, a **eredmények** ablaktáblán minden kötet csoport a következő információkat jeleníti meg, attól függően, hogy a oszlopkiválasztásokat elvégezte. (Az oszlopokat az **eredmények** ablaktáblán is konfigurálható. Kattintson a jobb gombbal a **kötetek** csomópontban jelölje ki **nézet**, majd válassza ki **oszlopok hozzáadása/eltávolítása**.)

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Name (Név) |A **neve** oszlop a mennyiségi csoport nevét tartalmazza. |
| Alkalmazás |A **alkalmazások** oszlopban látható a száma, a VSS-író jelenleg telepítve van és fut a Windows-gazdagépen. |
| Kiválasztva |A **kijelölt** oszlop a mennyiségi csoport található kötetek számát jeleníti meg. A nulla (0) azt jelzi, hogy egyetlen alkalmazás a köteteket a kötet csoport társítva. |
| Importálva |A **importált** oszlop importált kötetek számát jeleníti meg. Ha beállítása **igaz**, ebben az oszlopban azt jelzi, hogy a kötet csoport az Azure-portálon lett importálva, és nem jött létre a StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager kötet csoport is megjelenik a a **biztonsági mentési házirendek** fülre az Azure portálon.
> 
> 

## <a name="create-a-volume-group"></a>Kötet csoport létrehozása
A következő eljárással hozzon létre egy kötet csoportot.

#### <a name="to-create-a-volume-group"></a>Kötet csoport létrehozása
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **kötet csoportok**, és kattintson a **kötet csoport létrehozása**.
   
    ![Kötet csoport létrehozása](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    A **hozzon létre egy kötet csoportot** párbeszédpanel jelenik meg.
   
    ![Hozzon létre egy kötet csoport párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Adja meg a következő információkat:
   
   1. Az a **neve** mezőbe írja be egy egyedi nevet az új kötet csoporthoz.
   2. Az a **alkalmazások** mezőben, válassza ki a köteteket a kötet-csoportba felvenni társított alkalmazások.
      
       A **alkalmazások** mezőben listák csak ezeket az alkalmazásokat, amelyek a StorSimple-köteteket használ, és rendelkezik a VSS-írók engedélyezve van, a számukra. A VSS-író szolgáltatás engedélyezve van, csak akkor, ha minden kötet, amely az író tisztában a StorSimple-köteteket. Ha az alkalmazások mező üres, amely Azure StorSimple-köteteket használ, és támogatott van-e VSS-író alkalmazás sem lesz telepítve. (Jelenleg Azure StorSimple támogatja a Microsoft Exchange, SQL Server.) További információ a VSS-író: [integráció a Windows a kötet árnyékmásolata szolgáltatás](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Ha kiválaszt egy alkalmazást, akkor hozzá társított összes kötet automatikusan ki van jelölve. Ezzel szemben, ha egy adott alkalmazáshoz társított kötetek választja, az alkalmazás automatikusan ki van jelölve a a **alkalmazások** mezőbe. 
   3. Az a **kötetek** jelölje ki a StorSimple-köteteket a kötet csoport hozzáadása. 
      
      * Megadhat egy vagy több partíciókkal rendelkező köteteket. (Több partíció kötetek lehetnek dinamikus lemezek vagy alaplemezek több partícióval rendelkező.) A kötet, amely több partíciót tartalmaznak a rendszer egyetlen egységként kezeli. Ezért ha a partíciók közül csak egy kötet csoporthoz ad hozzá, minden más partíció automatikusan, hogy kötet csoportba kerülnek egyszerre. Miután egy kötet csoporthoz több partíció-kötet, a több partíció kötet továbbra is egyetlen egységként kell kezelni.
      * Kötet üres csoportok nem rendel hozzájuk köteteket hozhat létre. 
      * Ne keverje a fürt megosztott kötetei (CSV-k) és a CSV kötetet ugyanabban a csoportban. StorSimple Snapshot Manager nem támogatja a megosztott fürtkötetek és nem CSV-köteteket a azonos pillanatkép.
4. Kattintson a **OK** menteni a kötet csoportot.

## <a name="back-up-a-volume-group"></a>Készítsen biztonsági másolatot egy kötet csoport
A következő eljárás használatával készítsen biztonsági másolatot egy kötet csoportot.

#### <a name="to-back-up-a-volume-group"></a>Biztonsági mentése egy kötet csoport
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **kötet csoportok** csomópontot, kattintson a jobb gombbal egy kötet felügyeleticsoport-nevet, és kattintson **érvénybe biztonsági mentés**.
   
    ![Készítsen biztonsági másolatot kötet csoport azonnal](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Az a **érvénybe biztonsági mentés** párbeszédpanelen jelölje ki **helyi pillanatfelvétel** vagy **felhőalapú pillanatfelvétel**, és kattintson a **létrehozása**.
   
    ![Igénybe vehet a biztonsági mentési párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Győződjön meg arról, hogy fut-e a biztonsági mentés, bontsa ki a **feladatok** csomópontra, majd **futtató**. A biztonsági mentést kell szerepelnie.
5. A befejezett pillanatképének megtekintéséhez bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoport nevét, és kattintson **helyi pillanatfelvétel** vagy **felhőalapú pillanatfelvétel**. A biztonsági mentés megjelenik, ha sikeresen befejeződött.

## <a name="edit-a-volume-group"></a>Kötet csoport szerkesztése
A következő eljárással kötet csoport szerkesztéséhez.

#### <a name="to-edit-a-volume-group"></a>A kötet csoport szerkesztése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **kötet csoportok** csomópontot, kattintson a jobb gombbal egy kötet felügyeleticsoport-nevet, és kattintson **szerkesztése**.
3. A ** hozzon létre egy kötet csoportot ** párbeszédpanel jelenik meg. Módosíthatja a **neve**, **alkalmazások**, és **kötetek** bejegyzéseket.
4. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-a-volume-group"></a>Kötet csoport törlése
A következő eljárással kötet csoport törlése. 

> [!WARNING]
> Ez a kötet csoporthoz rendelt összes biztonsági mentés is törli.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Kötet csoport törlése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **kötet csoportok** csomópontot, kattintson a jobb gombbal egy kötet felügyeleticsoport-nevet, és kattintson **törlése**.
3. A **kötet csoport törlése** párbeszédpanel jelenik meg. Típus **megerősítése** a szövegmezőbe, majd **OK**.
   
    A törölt kötet csoport eltűnik a listából a **eredmények** ablaktábla és az adott kötet csoporthoz rendelt összes biztonsági mentés a biztonsági mentési katalógusból törlődnek.

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével biztonsági szabályzatok létrehozása és kezelése](storsimple-snapshot-manager-manage-backup-policies.md).

