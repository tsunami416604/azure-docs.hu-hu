---
title: A StorSimple Snapshot Manager biztonságimásolat-katalógus |} A Microsoft Docs
description: Megtekintéséhez és a biztonságimásolat-katalógus kezelése a StorSimple Snapshot Manager MMC beépülő modul használatát ismerteti.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: dc24ebd59fd977ef35766c304aec5824e2c7bb4c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127196"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Használja a StorSimple Snapshot Manager, a biztonságimásolat-katalógus kezelése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager elsődleges funkciója, lehetővé téve a StorSimple-kötetek alkalmazáskonzisztens biztonsági másolatot készíteni a pillanatfelvételek formájában. A pillanatképek szerepelnek majd nevű XML-fájl egy *biztonságimásolat-katalógus*. A biztonságimásolat-katalógus rendszerezi a pillanatképek a kötet csoportot, majd a helyi pillanatfelvétel és felhőbeli pillanatkép.

Ez az oktatóanyag leírja, hogyan használhatja a **biztonságimásolat-katalógus** csomópont hajtsa végre a következő feladatokat:

* Egy kötet visszaállítása
* Egy kötet vagy kötet klónozása
* A biztonsági másolat törlése
* Fájl helyreállítása
* A Storsimple Snapshot Manager-adatbázis visszaállítása

A biztonságimásolat-katalógus kibontásával is megtekintheti a **biztonságimásolat-katalógus** csomópontja a **hatókör** panelre, és ezután kibontása a kötet csoport.

* Ha kattint, a kötet csoport nevét, a **eredmények** panelen a helyi pillanatképeket és felhőbeli pillanatképek a kötet csoport elérhető számát jeleníti meg. 
* Ha rákattint **helyi pillanatkép** vagy **Felhőbeli pillanatkép**, a **eredmények** panel megjeleníti az egyes biztonsági mentési pillanatképet a következő információkat (attól függően, a  **Megtekintése** beállításokat):
  
  * **Név** – az idő a pillanatkép.
  * **Típus** – Ez a helyi pillanatfelvétel és felhőbeli pillanatkép-e.
  * **Tulajdonos** – a tartalom tulajdonosához. 
  * **Rendelkezésre álló** – jelenleg érhető el a pillanatkép-e. **Igaz** jelzi, hogy a pillanatkép érhető el, és vissza tudja állítani; **Hamis** azt jelzi, hogy a pillanatkép már nem érhető el. 
  * **Importált** – a biztonsági mentés importálta-e. **Igaz** azt jelzi, hogy a biztonsági mentés időpontjában az eszközt a StorSimple Snapshot Managerben; lett konfigurálva a StorSimple-Eszközkezelő szolgáltatás szolgáltatásból importált **Hamis** azt jelzi, hogy azt nem lett importálva, de a StorSimple Snapshot Managerben lett létrehozva. (Könnyen azonosíthatja az importált kötetcsoport, mert egy utótagot ad hozzá, amely azonosítja, hogy az eszköz, amelyről a kötet csoport lett importálva.)
    
    ![a biztonságimásolat-katalógus](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Ha kibontja **helyi pillanatkép** vagy **Felhőbeli pillanatkép**, és a egy pillanatkép egyedi nevét, majd kattintson a **eredmények** panelen látható a következő információkat a pillanatkép a kijelölt:
  
  * **Név** – a kötet meghajtóbetűjellel azonosított. 
  * **Helyi név** – a meghajtó (ha elérhető) helyi nevét. 
  * **Eszköz** – az a név az eszköz, amelyen a köteten található. 
  * **Rendelkezésre álló** – jelenleg érhető el a pillanatkép-e. **Igaz** jelzi, hogy a pillanatkép érhető el, és vissza tudja állítani; **Hamis** azt jelzi, hogy a pillanatkép már nem érhető el. 

## <a name="restore-a-volume"></a>Egy kötet visszaállítása
A következő eljárással egy kötetet biztonsági másolatból történő visszaállítását.

#### <a name="prerequisites"></a>Előfeltételek
Ha még nem tette meg, hozzon létre egy kötetet és egy kötetcsoport, és ezután törölje a kötetet. Alapértelmezés szerint a StorSimple Snapshot Managerrel biztonsági másolatot készít egy kötet előtt, amely lehetővé teszi annak törölhető. Ez az eszközeikről is megakadályozni az adatvesztést, ha a kötet véletlenül törölnek, vagy ha bármilyen okból lehet helyreállítani az adatokat kell. 

A StorSimple Snapshot Manager a következő üzenetet jeleníti meg, amikor létrehozza a elővigyázatosság biztonsági mentés.

![Automatikus pillanatfelvétel-készítési üzenet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Egy kötet csoport részét képező kötet nem lehet törölni. A törlési beállítás nem érhető el. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>A kötet visszaállítása
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához. 
2. Az a **hatókör** ablaktáblán bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoportot, és kattintson **helyi pillanatképeket** vagy **Felhőbeli pillanatképek**. Megjelenik egy biztonsági mentési pillanatképek listáját a **eredmények** ablaktáblán.
3. Keresse meg a használt biztonsági másolatot vissza kívánja állítani, kattintson a jobb gombbal, és kattintson **visszaállítása**.
   
    ![A biztonságimásolat-katalógus visszaállítása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. A megerősítő oldalon tekintse át a részleteket, típus **megerősítése**, és kattintson a **OK**. A StorSimple Snapshot Manager használ a biztonsági mentés a kötet visszaállítása.
   
    ![Megerősítő üzenet visszaállítása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Figyelheti a visszaállítási művelet futása során. Az a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontban, és kattintson **futó**. A feladat részletei megjelennek a **eredmények** ablaktáblán. Ha a visszaállítási feladat befejeződött, a program átviszi a feladat részleteit a **az elmúlt 24 órából** listája.

## <a name="clone-a-volume-or-volume-group"></a>Egy kötet vagy kötet klónozása
Az alábbi eljárással hozhat létre egy kötetet vagy kötetcsoport ismétlődése (Klónozás).

#### <a name="to-clone-a-volume-or-volume-group"></a>Egy kötet vagy kötet klónozása
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoportot, és kattintson **Felhőbeli pillanatképek**. A biztonsági mentések egy lista jelenik meg a **eredmények** ablaktáblán.
3. Keresse meg a kötet vagy a klónozza, kattintson a jobb gombbal a kötetre vagy a kötet neve, és kattintson a kívánt kötetcsoport **Klónozás**. A **Klónozás Felhőbeli pillanatkép** párbeszédpanel jelenik meg.
   
    ![Klónozza a felhőbeli pillanatkép](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Végezze el a **Klónozás Felhőbeli pillanatkép** párbeszédpanel az alábbiak szerint: 
   
   1. Az a **neve** szövegmezőbe írjon be egy nevet a klónozott kötet. Ez a név fog megjelenni a **kötetek** csomópont. 
   2. (Választható) válassza **meghajtó**, és ezután válasszon egy meghajtóbetűjelet a legördülő listából.
   3. (Választható) válassza **mappa (NTFS)**, és írja be a mappa elérési útja, vagy kattintson a Tallózás gombra, és válassza ki a mappa helyét. 
   4. Kattintson a **Create** (Létrehozás) gombra.
5. A Klónozási folyamat befejezésekor inicializálnia kell a klónozott kötet. Indítsa el a Kiszolgálókezelőt, és indítsa el a Lemezkezelés eszközben. Részletes útmutatásért lásd: [kötetek csatlakoztatása](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Után inicializálva van, a kötet területen fog megjelenni a **kötetek** csomópontja a **hatókör** ablaktáblán. Ha nem látja, akkor a kötet felsorolt, a kötetek listájának frissítése (kattintson a jobb gombbal a **kötetek** csomópontban, és kattintson **frissítése**).

## <a name="delete-a-backup"></a>A biztonsági másolat törlése
Az alábbi eljárás segítségével egy pillanatképet törölni a biztonságimásolat-katalógust. 

> [!NOTE]
> Pillanatkép törlése törli a pillanatkép tartozó biztonsági adatokat. Azonban az adatok a felhőből törlése, a folyamat eltarthat egy ideig.<br>


#### <a name="to-delete-a-backup"></a>Biztonsági másolat törlése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán bontsa ki a **biztonságimásolat-katalógus** csomópontot, bontsa ki a kötet csoportot, és kattintson **helyi pillanatképeket** vagy **Felhőbeli pillanatképek**. A pillanatképek listája jelenik meg a **eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a pillanatkép törlése, és kattintson a kívánt **törlése**.
4. A megerősítést kérő üzenet megjelenésekor kattintson **OK**.

## <a name="recover-a-file"></a>Fájl helyreállítása
Ha véletlenül töröl egy fájlt a kötetről, helyreállíthatja a fájlt, amely a törlés előtti dátumok pillanatkép beolvasása, a pillanatkép a kötet egy klón létrehozására és majd a klónozott kötet a fájl az eredeti kötet másolását.

#### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy a kötet csoport aktuális biztonsági másolata. Törölje az egyik kötet benne a köteteken tárolt fájlhoz. Végül a következő lépések használatával állítsa vissza a törölt fájlt a biztonsági másolatból. 

#### <a name="to-recover-a-deleted-file"></a>Egy törölt fájl helyreállítása
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager konzolablakban jelenik meg. 
2. Az a **hatókör** ablaktáblán bontsa ki a **biztonságimásolat-katalógus** csomópontot, és tallózással keresse meg a törölt fájlt tartalmazó pillanatképet. Általában akkor válassza a törlés előtt készült pillanatképet.
3. Keresse meg a kötet, amelyet szeretne klónozza, kattintson a jobb gombbal, majd kattintson **Klónozás**. A **Klónozás Felhőbeli pillanatkép** párbeszédpanel jelenik meg.
   
    ![Klónozza a felhőbeli pillanatkép](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Végezze el a **Klónozás Felhőbeli pillanatkép** párbeszédpanel az alábbiak szerint: 
   
   1. Az a **neve** szövegmezőbe írjon be egy nevet a klónozott kötet. Ez a név fog megjelenni a **kötetek** csomópont. 
   2. (Nem kötelező) Válassza ki **meghajtó**, és ezután válasszon egy meghajtóbetűjelet a legördülő listából. 
   3. (Nem kötelező) Válassza ki **mappa (NTFS)**, és írja be a mappa elérési útját, vagy kattintson a **Tallózás** , és válassza ki a mappa helyét. 
   4. Kattintson a **Create** (Létrehozás) gombra. 
5. A Klónozási folyamat befejezésekor inicializálnia kell a klónozott kötet. Indítsa el a Kiszolgálókezelőt, és indítsa el a Lemezkezelés eszközben. Részletes útmutatásért lásd: [kötetek csatlakoztatása](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Után inicializálva van, a kötet területen fog megjelenni a **kötetek** csomópontja a **hatókör** ablaktáblán. 
   
    Ha nem látja, akkor a kötet felsorolt, a kötetek listájának frissítése (kattintson a jobb gombbal a **kötetek** csomópontban, és kattintson **frissítése**).
6. Nyissa meg az NTFS-mappába, amely tartalmazza a klónozott kötet, bontsa ki a **kötetek** csomópontot, és nyissa meg a klónozott kötet. Keresse meg a helyreállítani kívánt fájlt, és másolja az elsődleges kötet.
7. Miután visszaállította a fájlt, törölheti az NTFS-mappába, amely tartalmazza a klónozott kötet.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>A StorSimple Snapshot Manager-adatbázis visszaállítása
Rendszeresen készítsen biztonsági másolatot a StorSimple Snapshot Manager-adatbázis a gazdagépen. Ha katasztrófa történik, vagy a számítógép bármely okból meghiúsul, majd visszaállíthatja azt a biztonsági mentésből. Az adatbázis-másolat létrehozása egy olyan manuális folyamat.

#### <a name="to-back-up-and-restore-the-database"></a>Készítsen biztonsági másolatot, és állítsa vissza az adatbázist
1. A Microsoft StorSimple felügyeleti szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   3. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
2. Az állomáson keresse meg C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData mappa rejtett.
   > 
   > 
3. Keresse meg a katalógus XML-fájlt, másolja a fájlt, és a másolat tárolása egy biztonságos helyre, vagy a felhőben. Ha a gazdagép meghibásodik, használhatja a biztonságimásolat-fájlt a biztonsági mentési szabályzatok a StorSimple Snapshot Managerben létrehozott helyreállítását segíti.
   
    ![Az Azure StorSimple a biztonságimásolat-katalógus fájl](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. A Microsoft StorSimple felügyeleti szolgáltatás újraindítása: 
   
   1. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   2. Az a **szolgáltatások** ablakban válassza ki a **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**.
5. Az állomáson keresse meg C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Törölje a katalógus XML-fájlt, és cserélje le a biztonsági másolat verzióját, amelyet Ön hozott létre. 
7. Kattintson az asztali a StorSimple Snapshot Manager ikonra a StorSimple Snapshot Manager elindításához. 

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Tudjon meg többet [a StorSimple Snapshot Manager-feladatok és a munkafolyamatok](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

