---
title: "StorSimple Snapshot Manager biztonságimásolat-katalógus |} Microsoft Docs"
description: "Ismerteti a StorSimple Snapshot Manager MMC beépülő modul segítségével megtekintheti, és a biztonságimásolat-katalógus kezelése."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Használja a StorSimple Snapshot Manager a biztonságimásolat-katalógus kezelése

## <a name="overview"></a>Áttekintés
Az elsődleges a StorSimple Snapshot Manager funkciója lehetővé teszi a StorSimple-köteteket alkalmazáskonzisztens biztonsági másolatot készíteni a pillanatfelvételek formájában. A pillanatképek majd szereplő nevű XML-fájl egy *biztonságimásolat-katalógus*. A biztonságimásolat-katalógus rendezi a pillanatképek a kötet csoportot, majd a helyi pillanatfelvétel és felhőbeli pillanatfelvétel.

Ez az oktatóanyag leírja, hogyan használhatja a **biztonságimásolat-katalógus** csomópont a következő feladatok elvégzéséhez:

* A kötet visszaállítása
* Egy kötet vagy kötet klónozása
* Biztonsági
* A fájl helyreállításához
* A Storsimple Snapshot Manager-adatbázis visszaállítása

A biztonságimásolat-katalógus kibontásával megtekintheti a **biztonságimásolat-katalógus** csomópontja a **hatókör** ablaktáblán, és a kötet csoport majd bővíteni.

* Ha a kötet csoport nevére kattint a **eredmények** ablaktábla megjeleníti azokat a helyi pillanatképeket és felhőbeli pillanatképeket kötet csoport számát. 
* Ha **helyi pillanatfelvétel** vagy **felhőalapú pillanatfelvétel**, a **eredmények** ablaktábla megjeleníti azokat a következő információkat minden egyes biztonsági mentési pillanatképet (attól függően a  **Megtekintése** beállítások):
  
  * **Név** – az idő során létrehozott pillanatképen.
  * **Típus** – hogy ez-e a helyi pillanatfelvétel vagy egy felhőalapú pillanatfelvétel.
  * **Tulajdonos** – a tartalom tulajdonosa. 
  * **Rendelkezésre álló** – hogy jelenleg rendelkezésre áll a pillanatkép. **Igaz** jelzi, hogy a pillanatkép érhető el, és visszaállítása végezhető el; **Hamis** azt jelzi, hogy a pillanatkép nem érhető el. 
  * **Importált** – hogy a biztonsági mentés lett importálva. **Igaz** azt jelzi, hogy a biztonsági mentés a StorSimple Device Manager szolgáltatásból importálta az eszköz konfigurálása a StorSimple Snapshot Manager; időpontjában **Hamis** azt jelzi, hogy nem lett importálva, de a StorSimple Snapshot Manager által lett létrehozva. (Egyszerűbb azonosítani egy importált kötet csoport egy utótagot ad hozzá, amely azonosítja az eszközt, amelyből a kötet csoport lett importálva, mert.)
    
    ![Biztonságimásolat-katalógus](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Ha kibontja **helyi pillanatfelvétel** vagy **felhőalapú pillanatfelvétel**, az egyes pillanatkép nevét, majd kattintson a **eredmények** ablaktábla megjeleníti azokat a következő információkat a pillanatkép kijelölt:
  
  * **Név** – a kötet meghajtóbetűjellel azonosított. 
  * **Helyi** – a meghajtó (ha elérhető) helyi nevét. 
  * **Eszköz** – a név az eszköz, amelyen a köteten található. 
  * **Rendelkezésre álló** – hogy jelenleg rendelkezésre áll a pillanatkép. **Igaz** jelzi, hogy a pillanatkép érhető el, és visszaállítása végezhető el; **Hamis** azt jelzi, hogy a pillanatkép nem érhető el. 

## <a name="restore-a-volume"></a>A kötet visszaállítása
A következő eljárással egy kötetet biztonsági másolatból történő visszaállítását.

#### <a name="prerequisites"></a>Előfeltételek
Ha még nem tette meg, hozzon létre egy kötet és a kötet csoport, és törölje a kötet. Alapértelmezés szerint a StorSimple Snapshot Manager biztonsági mentést készít egy kötet előtt, amely lehetővé teszi annak törölhető. A okokból előfordulhat, hogy az adatveszteség, ha a kötet véletlenül törölték, vagy ha az adatokat helyre kell állítani a bármilyen okból kell. 

StorSimple Snapshot Manager a következő üzenetet jeleníti meg, amikor létrehozza a megelőző biztonsági mentés.

![Automatikus pillanatkép üzenet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> A kötet olyan kötet csoport részét képező nem törölhető. A törlési beállítás nem érhető el. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>A kötet visszaállítása
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager. 
2. Az a **hatókör** ablaktáblában bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoportot, és kattintson a **helyi pillanatképeket** vagy **felhőalapú pillanatfelvételek**. A biztonsági mentési pillanatképek listája jelenik meg a **eredmények** ablaktáblán.
3. Keresse meg a biztonsági mentésből szeretné visszaállítani, kattintson a jobb gombbal, és kattintson **visszaállítása**.
   
    ![Állítsa vissza a biztonságimásolat-katalógus](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. A jóváhagyó lapon tekintse át a részletes adatait, típus **megerősítése**, és kattintson a **OK**. StorSimple Snapshot Manager a biztonsági másolat használatával állítsa vissza a kötetet.
   
    ![Állítsa vissza a jóváhagyást kérő üzenet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Figyelheti a visszaállítási művelet futtatása közben. Az a **hatókör** ablaktáblában bontsa ki a **feladatok** csomópontra, majd **futtató**. A feladat részletei megjelennek a **eredmények** ablaktáblán. Ha a visszaállítási feladat befejeződött, a feladat adatai átkerülnek a **utolsó 24 óra** listája.

## <a name="clone-a-volume-or-volume-group"></a>Egy kötet vagy kötet klónozása
A következő eljárással hozhat létre egy kötet vagy a kötet csoport duplikált (Klónozás).

#### <a name="to-clone-a-volume-or-volume-group"></a>Egy kötet vagy kötet klónozása
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoportot, és kattintson a **felhőalapú pillanatfelvételek**. A biztonsági mentések listája jelenik meg a **eredmények** ablaktáblán.
3. Keresse meg a kötet vagy klónozáshoz, kattintson a jobb gombbal a kötetre vagy a kötet csoport nevét, majd kattintson a kívánt kötet csoportot **Klónozás**. A **klón felhő pillanatkép** párbeszédpanel jelenik meg.
   
    ![Egy felhőalapú pillanatfelvétel klónozása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Fejezze be a **klón felhő pillanatkép** párbeszédpanel az alábbiak szerint: 
   
   1. Az a **neve** szövegmezőbe írja be a klónozott kötet nevét. Ez a név fog megjelenni a **kötetek** csomópont. 
   2. (Nem kötelező) jelölje ki **meghajtó**, és a legördülő listából válassza ki egy meghajtóbetűjelet.
   3. (Nem kötelező) jelölje ki **mappa (NTFS)**, és írja be a mappa elérési útját vagy kattintson a Tallózás gombra, és jelölje ki a mappa helyét. 
   4. Kattintson a **Create** (Létrehozás) gombra.
5. A Klónozási folyamat befejezésekor inicializálnia kell a klónozott kötet. Indítsa el a Kiszolgálókezelőt, és indítsa el a Lemezkezelés eszközben. Részletes útmutatásért lásd: [csatlakoztassa köteteket](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Az inicializált, miután a kötet alatt jelenik meg a **kötetek** csomópontja a **hatókör** ablaktáblán. Ha nem látja a kötet felsorolt, a kötetek listájának frissítése (kattintson a jobb gombbal a **kötetek** csomópontra, majd **frissítése**).

## <a name="delete-a-backup"></a>Biztonsági
A következő eljárással pillanatkép törlése a biztonságimásolat-katalógusból. 

> [!NOTE]
> A pillanatképek törlésével törli a pillanatkép társított a biztonsági másolatban szereplő adatokkal. Azonban törli az adatokat a felhőből az a folyamat eltarthat egy ideig.<br>


#### <a name="to-delete-a-backup"></a>Biztonsági
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblában bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoportot, és kattintson a **helyi pillanatképeket** vagy **felhőalapú pillanatfelvételek**. A pillanatképek listája jelenik meg a **eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a pillanatkép törlése, és kattintson a kívánt **törlése**.
4. A jóváhagyást kérő üzenet megjelenésekor kattintson **OK**.

## <a name="recover-a-file"></a>A fájl helyreállításához
Ha egy fájl véletlenül törli a kötetről, helyreállíthatja a fájl beolvasása, amely a törlés előtti dátumokat pillanatkép, a pillanatkép használatával klónozhatja a kötet, és majd a fájl másolása a klónozott kötetről, az eredeti köteten.

#### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a kötet csoport aktuális biztonsági másolata. Törölje az egyik kötet csoport köteten tárolt fájl. Végezetül a következő lépésekkel visszaállítani a törölt fájlt a biztonsági mentésből. 

#### <a name="to-recover-a-deleted-file"></a>A törölt fájl helyreállításához
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager console ablakban jelenik meg. 
2. Az a **hatókör** ablaktáblában bontsa ki a **biztonságimásolat-katalógus** csomópont, és keresse meg a törölt fájlt tartalmazó pillanatképet. Ki kell jelölni általában csak a törlés előtt készült pillanatképet.
3. A kötet, amelyet a klón található kattintson a jobb gombbal, majd kattintson **Klónozás**. A **klón felhő pillanatkép** párbeszédpanel jelenik meg.
   
    ![Egy felhőalapú pillanatfelvétel klónozása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Fejezze be a **klón felhő pillanatkép** párbeszédpanel az alábbiak szerint: 
   
   1. Az a **neve** szövegmezőbe írja be a klónozott kötet nevét. Ez a név fog megjelenni a **kötetek** csomópont. 
   2. (Választható) Válassza ki **meghajtó**, és a legördülő listából válassza ki egy meghajtóbetűjelet. 
   3. (Választható) Válassza ki **mappa (NTFS)**, és írja be a mappa elérési útját, vagy kattintson a **Tallózás** és a mappa helyének kiválasztására. 
   4. Kattintson a **Create** (Létrehozás) gombra. 
5. A Klónozási folyamat befejezésekor inicializálnia kell a klónozott kötet. Indítsa el a Kiszolgálókezelőt, és indítsa el a Lemezkezelés eszközben. Részletes útmutatásért lásd: [csatlakoztassa köteteket](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Az inicializált, miután a kötet alatt jelenik meg a **kötetek** csomópontja a **hatókör** ablaktáblán. 
   
    Ha nem látja a kötet felsorolt, a kötetek listájának frissítése (kattintson a jobb gombbal a **kötetek** csomópontra, majd **frissítése**).
6. Nyissa meg a klónozott kötetet tartalmazó NTFS-mappába, és bontsa ki a **kötetek** csomópont, és nyissa meg a klónozott kötet. Keresse meg a helyreállítani kívánt fájlt, és másolja az elsődleges köteten.
7. A fájl visszaállítása után törölheti a klónozott kötetet tartalmazó NTFS-mappába.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>A StorSimple Snapshot Manager-adatbázis visszaállítása
Rendszeresen készítsen biztonsági másolatot a StorSimple Snapshot Manager-adatbázis az állomáson. Ha katasztrófa történik, vagy a számítógép bármilyen okból nem sikerül, majd visszaállíthatja a biztonsági mentésből. Az adatbázis biztonsági másolatot készít az egy manuális folyamat.

#### <a name="to-back-up-and-restore-the-database"></a>Készítsen biztonsági másolatot, és állítsa vissza az adatbázist
1. A Microsoft a StorSimple szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   3. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
2. Az állomáson tallózással C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData mappa rejtett.
   > 
   > 
3. A katalógus XML-fájl található, másolja a fájlt, és a példány tárolja biztonságos helyen, vagy a felhőben. Ha a gazdagép meghibásodik, a biztonságimásolat-fájl segítségével létrehozott StorSimple Snapshot Manager biztonsági mentési házirend helyreállítását.
   
    ![Az Azure StorSimple biztonsági mentési katalógusfájl](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Indítsa újra a Microsoft a StorSimple szolgáltatás: 
   
   1. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   2. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**.
5. Az állomáson tallózással C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Törölje a katalógus XML-fájlt, és cserélje le a biztonsági másolatot, amelyet létrehozott. 
7. Kattintson a start StorSimple Snapshot Manager asztali StorSimple Snapshot Manager ikonra. 

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* További információ [StorSimple Snapshot Manager feladatok és a munkafolyamatok](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

