---
title: A StorSimple Snapshot Manager kötet csoportok |} A Microsoft Docs
description: A StorSimple Snapshot Manager MMC beépülő modul létrehozása és kezelése a használatát ismerteti.
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
ms.openlocfilehash: e84bc790ac577796e91be010deecc8c5cea1b010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303098"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Létrehozása és kezelése a StorSimple Snapshot Manager használata
## <a name="overview"></a>Áttekintés
Használhatja a **kötet csoportok** csomópontjához a **hatókör** kötetek hozzárendelése kötet csoportokhoz, egy kötetcsoport adatainak megtekintése ablaktábla a biztonsági mentések ütemezéséhez és mennyiségi csoportok szerkesztése.

Kötet-csoportok tartoznak a készletek a kapcsolódó kötetek használja annak érdekében, hogy alkalmazáskonzisztens biztonsági mentés. További információkért lásd: [kötetek és a kötet csoportok](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) és [integráció a Windows a kötet árnyékmásolata szolgáltatás](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Egy kötet csoportban található összes kötetet egyetlen felhőszolgáltató kell származnia.
> * Kötet csoportok konfigurálásakor ne keverje a fürt megosztott kötetei (CSV-k) és a CSV kötetet ugyanabba a csoportba. A StorSimple Snapshot Manager nem támogatja a megosztott fürtkötetek és a CSV pillanatképeket.

![Kötet-csoportok csomópont](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**1. ábra: A StorSimple Snapshot Manager kötet-csoportok csomópont** 

Ez az oktatóanyag azt ismerteti, hogyan használhatja a StorSimple Snapshot Manager:

* A kötet csoportok adatainak megtekintése
* Hozzon létre egy kötetet csoportot
* Készítsen biztonsági másolatot egy kötetcsoport
* Kötet csoport szerkesztése
* Kötet csoport törlése

Ezek a műveletek mindegyike is elérhetők a **műveletek** ablaktáblán.

## <a name="view-volume-groups"></a>Kötet csoportok megtekintése
Kattintva a **kötet csoportok** csomópontra, a **eredmények** panel megjeleníti az egyes kötetcsoport kapcsolatos alábbi információkat, attól függően, a oszlopkiválasztásokat győződjön meg. (Az oszlopok a **eredmények** panelen is konfigurálható. Kattintson a jobb gombbal a **kötetek** csomópontban jelölje ki **nézet**, majd válassza ki **oszlopok hozzáadása és eltávolítása**.)

| Eredmények oszlop | Leírás |
|:--- |:--- |
| Name (Név) |A **neve** oszlop tartalmazza a kötet csoport neve. |
| Alkalmazás |A **alkalmazások** az oszlopban látható a VSS-írók jelenleg telepítve van és fut a Windows-gazdagépen. |
| Kiválasztva |A **kijelölt** oszlop a mennyiségi csoportban található kötetek számát jeleníti meg. Nulla (0) azt jelzi, hogy nincs alkalmazás társítva a köteteket a kötet csoportban. |
| Importálva |A **importált** oszlop importált kötetek számát jeleníti meg. Ha a beállítása **igaz**, ez az oszlop azt jelzi, hogy egy kötetcsoport importált az Azure Portalról, és nem jött létre a StorSimple Snapshot Managerben. |

> [!NOTE]
> A StorSimple Snapshot Manager kötet csoport is megjelenik a a **biztonsági mentési házirendek** lap az Azure Portalon.
> 
> 

## <a name="create-a-volume-group"></a>Hozzon létre egy kötetet csoportot
A következő eljárással hozhat létre egy kötetcsoport.

#### <a name="to-create-a-volume-group"></a>Kötet csoport létrehozása
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **kötet csoportok**, és kattintson a **kötet-csoport létrehozása**.
   
    ![Kötet-csoport létrehozása](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    A **hozzon létre egy kötetcsoport** párbeszédpanel jelenik meg.
   
    ![Hozzon létre egy kötetet csoport párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Adja meg a következő információkat:
   
   1. Az a **neve** mezőbe írja be egy egyedi nevet az új kötet csoport.
   2. Az a **alkalmazások** mezőt, a köteteket, a kötet csoporthoz ad társított jelölje be alkalmazásokat.
      
       A **alkalmazások** box listák csak ezeket az alkalmazásokat, amelyek a StorSimple-köteteket használ, és rendelkezik a VSS-írók engedélyezve van a számukra. A VSS-író engedélyezve van, csak ha tisztában az író minden kötet StorSimple-köteteket. Ha az alkalmazások mező üres, nem használhatja az Azure StorSimple-köteteket, és a VSS-írók rendelkezik támogatott alkalmazások vannak telepítve. (Jelenleg az Azure StorSimple támogatja a Microsoft Exchange és az SQL Server.) További információ a VSS-írót használ: [integráció a Windows a kötet árnyékmásolata szolgáltatás](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Ha kiválaszt egy alkalmazást, azzal társított összes kötetet automatikusan ki van jelölve. Ezzel szemben ha egy adott alkalmazáshoz társított kötetek választja, az alkalmazás automatikusan ki van jelölve az a **alkalmazások** mezőbe. 
   3. Az a **kötetek** jelölje ki a StorSimple-köteteket a kötet csoportba való felvételéhez. 
      
      * Egy vagy több partícióval rendelkező köteteket is megadhat. (Több partíció kötetek lehetnek dinamikus lemezek vagy az alaplemezek több partícióval rendelkező.) A kötet, amely több partíciót tartalmaz egyetlen egységként számít. Ebből következően ad hozzá a partíciók közül csak egy kötet csoportot, ha az összes többi partíció automatikusan a kötet csoportba kerülnek egyszerre. Több partíció kötet hozzáadása egy kötetcsoport, miután a több partíció-kötet továbbra is egyetlen egységként kell kezelni.
      * Nem rendelhet hozzájuk a köteteket a kötet üres csoportot is létrehozhat. 
      * Ne keverje a fürt megosztott kötetei (CSV-k) és a CSV kötetet ugyanabba a csoportba. A StorSimple Snapshot Manager nem támogatja a megosztott fürtkötetek és-CSV köteteket a pillanatképeket.
4. Kattintson a **OK** menteni a kötet csoportot.

## <a name="back-up-a-volume-group"></a>Készítsen biztonsági másolatot egy kötetcsoport
A következő eljárás használatával készítsen biztonsági másolatot egy kötetcsoport.

#### <a name="to-back-up-a-volume-group"></a>Biztonsági mentése egy kötetcsoport
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **kötet csoportok** csomópontot, kattintson a jobb gombbal a kötet csoport nevét, és kattintson **igénybe biztonsági mentés**.
   
    ![Kötetcsoport azonnal készíteni](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Az a **igénybe biztonsági mentés** párbeszédpanelen jelölje ki **helyi pillanatkép** vagy **Felhőbeli pillanatkép**, és kattintson a **létrehozás**.
   
    ![Igénybe vehet a biztonsági mentési párbeszédpanel](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Győződjön meg arról, hogy fut-e a biztonsági mentés, bontsa ki a **feladatok** csomópontban, és kattintson **futó**. A biztonsági mentés szerepelnie kell.
5. A befejezett pillanatképének megtekintéséhez bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoport nevét, és kattintson **helyi pillanatkép** vagy **Felhőbeli pillanatkép**. A biztonsági mentés fog szerepelni, ha sikeresen befejeződött.

## <a name="edit-a-volume-group"></a>Kötet csoport szerkesztése
Az alábbi eljárás segítségével kötet csoport szerkesztéséhez.

#### <a name="to-edit-a-volume-group"></a>Kötet csoportok szerkesztése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **kötet csoportok** csomópontot, kattintson a jobb gombbal a kötet csoport nevét, és kattintson **szerkesztése**.
3. A ** hozzon létre egy kötetcsoport ** párbeszédpanel jelenik meg. Módosíthatja a **neve**, **alkalmazások**, és **kötetek** bejegyzéseket.
4. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-a-volume-group"></a>Kötet csoport törlése
Az alábbi eljárás segítségével kötet csoport törlése. 

> [!WARNING]
> Ez a kötet gazdagépcsoporthoz tartozó összes biztonsági mentés is törli.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Kötet csoport törlése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **kötet csoportok** csomópontot, kattintson a jobb gombbal a kötet csoport nevét, és kattintson **törlése**.
3. A **Kötetcsoport törlése** párbeszédpanel jelenik meg. Típus **megerősítése** a szövegmezőbe, és kattintson a **OK**.
   
    A törölt kötetcsoport eltűnik a listából a **eredmények** ablaktábla és a kötet csoporthoz rendelt összes biztonsági mentés törlődni fog a biztonságimásolat-katalógust.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [hozhat létre, és a biztonsági mentési házirendek kezelése a StorSimple Snapshot Manager használata](storsimple-snapshot-manager-manage-backup-policies.md).

