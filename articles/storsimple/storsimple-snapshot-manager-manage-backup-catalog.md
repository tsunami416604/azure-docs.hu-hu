---
title: StorSimple Snapshot Manager biztonsági másolat készítési katalógus | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a StorSimple Snapshot Manager MMC beépülő modul használatával hogyan tekintheti meg és kezelheti a biztonsági másolat katalógusát.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 38ef7774263e4b28b7c316fd0870ca8f7b89d6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931703"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>A biztonságimásolat-katalógus kezelése a StorSimple Snapshot Manager segítségével

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager elsődleges funkciója az, hogy lehetővé tegye a StorSimple kötetek alkalmazáskonzisztens biztonsági másolatainak pillanatképek formájában történő létrehozását. A pillanatképek ezután egy biztonsági *másolatkatalógusnak*nevezett XML-fájlban jelennek meg. A biztonsági mentési katalógus kötetcsoport, majd helyi pillanatkép vagy felhőbeli pillanatkép szerint rendezi a pillanatképeket.

Ez az oktatóanyag bemutatja, hogyan használhatja a **Biztonsági másolat katalógus** csomópontját a következő feladatok elvégzésére:

* Kötet visszaállítása
* Kötet vagy kötetcsoport klónozása
* Biztonsági másolat törlése
* Fájl helyreállítása
* A Storsimple Snapshot Manager adatbázis visszaállítása

A biztonsági másolat katalógusát úgy tekintheti meg, hogy kibontja a **Biztonsági másolat katalógus** csomópontját a **Hatókör** ablaktáblában, majd kibontja a kötetcsoportot.

* Ha a kötetcsoport nevére kattint, az **Eredmények** ablaktábla a kötetcsoport számára elérhető helyi pillanatképek és felhőbeli pillanatképek számát jeleníti meg. 
* Ha a **Helyi pillanatkép** vagy a **Felhőbeli pillanatkép**elemre kattint, az **Eredmények** ablaktábla a következő információkat jeleníti meg az egyes biztonsági mentési pillanatképekről (a **Nézet** beállításaitól függően):
  
  * **Név** – a pillanatkép készítésének időpontja.
  * **Típus** – legyen szó helyi pillanatképről vagy felhőbeli pillanatképről.
  * **Tulajdonos** – a tartalom tulajdonosa. 
  * **Elérhető** – függetlenül attól, hogy a pillanatkép jelenleg elérhető-e. **Igaz** azt jelzi, hogy a pillanatkép elérhető, és visszaállítható; **A Hamis** azt jelzi, hogy a pillanatkép már nem érhető el. 
  * **Importálva** – a biztonsági másolat importálása. **A True** azt jelzi, hogy a biztonsági mentés importálása a StorSimple Eszközkezelő szolgáltatásból lett importálva az eszköz StorSimple Snapshot Manager ben való konfigurálásakor; **A Hamis** azt jelzi, hogy nem importálták, hanem a StorSimple Snapshot Manager hozta létre. (Az importált kötetcsoportot könnyen azonosíthatja, mert hozzáad egy utótagot, amely azonosítja azt az eszközt, amelyről a kötetcsoportot importálták.)
    
    ![Biztonsági másolat katalógusa](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Ha kibontja **a Helyi pillanatkép** vagy **a Felhőpillanatkép**csomópontot, majd egy adott pillanatkép nevére kattint, az **Eredmények** ablaktábla a következő információkat jeleníti meg a kiválasztott pillanatképről:
  
  * **Név** – a meghajtóbetűjel által azonosított kötet. 
  * **Helyi név** – a meghajtó helyi neve (ha elérhető). 
  * **Eszköz** – annak az eszköznek a neve, amelyen a kötet található. 
  * **Elérhető** – függetlenül attól, hogy a pillanatkép jelenleg elérhető-e. **Igaz** azt jelzi, hogy a pillanatkép elérhető, és visszaállítható; **A Hamis** azt jelzi, hogy a pillanatkép már nem érhető el. 

## <a name="restore-a-volume"></a>Kötet visszaállítása
A kötet biztonsági mentésből történő visszaállításához kövesse az alábbi eljárást.

#### <a name="prerequisites"></a>Előfeltételek
Ha még nem tette meg, hozzon létre egy kötet- és kötetcsoportot, majd törölje a kötetet. Alapértelmezés szerint a StorSimple Snapshot Manager biztonsági másolatot ad egy kötetről, mielőtt engedélyezné a törlést. Ez az óvintézkedés megakadályozhatja az adatvesztést, ha a kötetet véletlenül törlik, vagy ha az adatokat bármilyen okból helyre kell állítani. 

A StorSimple Snapshot Manager a következő üzenetet jeleníti meg, miközben létrehozza az elővigyázatossági biztonsági mentést.

![Automatikus pillanatkép-üzenet](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Kötetcsoport részét használó kötet nem törölhető. A törlési lehetőség nem érhető el. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Kötet visszaállítása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához. 
2. A **Hatókör** ablaktáblában bontsa ki a **Biztonsági másolat katalógus** csomópontját, bontsa ki a kötetcsoportot, majd kattintson a Helyi **pillanatképek** vagy **a Felhőbeli pillanatképek**elemre. A biztonsági mentési pillanatképek listája megjelenik az **Eredmények** ablaktáblán.
3. Keresse meg a visszaállítani kívánt biztonsági másolatot, kattintson a jobb gombbal, majd kattintson **a Visszaállítás parancsra.**
   
    ![Biztonsági másolat katalógusának visszaállítása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. A megerősítő lapon tekintse át a részleteket, írja be a **Megerősítés**, majd kattintson az **OK gombra.** A StorSimple Snapshot Manager a biztonsági mentés segítségével állítja vissza a kötetet.
   
    ![Megerősítő üzenet visszaállítása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. A visszaállítási művelet futásközben figyelhető. A **Hatókör** ablaktáblán bontsa ki a **Feladatok** csomópontot, majd kattintson a **Futás**gombra. A feladat részletei az **Eredmények** ablaktáblán jelennek meg. Amikor a visszaállítási feladat befejeződött, a feladat részletei átkerülnek az **Elmúlt 24 óra** listára.

## <a name="clone-a-volume-or-volume-group"></a>Kötet vagy kötetcsoport klónozása
Az alábbi eljárással hozhat létre egy kötet vagy kötetcsoport duplikált (klónozását).

#### <a name="to-clone-a-volume-or-volume-group"></a>Kötet vagy kötetcsoport klónozása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblában bontsa ki a **Biztonsági másolat katalógus** csomópontját, bontsa ki a kötetcsoportot, majd kattintson a **Felhőbeli pillanatképek parancsra.** A biztonsági mentések listája megjelenik az **Eredmények** ablaktáblán.
3. Keresse meg a klónozni kívánt kötetet vagy kötetcsoportot, kattintson a jobb gombbal a kötet vagy kötetcsoport nevére, és válassza a **Klónozás (Klónozás) parancsra.** Megjelenik **a Klónfelhő-pillanatkép** párbeszédpanel.
   
    ![Felhőbeli pillanatkép klónozása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Töltse ki a **Clone Cloud Snapshot** párbeszédpanelt az alábbiak szerint: 
   
   1. A **Név** mezőbe írja be a klónozott kötet nevét. Ez a név jelenik meg a **Kötetek** csomópontban. 
   2. (Nem kötelező) válassza a **Meghajtó**lehetőséget, majd válasszon egy meghajtóbetűjelet a legördülő listából.
   3. (Nem kötelező) válassza a **Mappa (NTFS)** lehetőséget, és írjon be egy mappa elérési útját, vagy kattintson a Tallózás gombra, és adja meg a mappa helyét. 
   4. Kattintson **a Létrehozás gombra.**
5. Amikor a klónozási folyamat befejeződött, inicializálnia kell a klónozott kötetet. Indítsa el a Kiszolgálókezelőt, majd indítsa el a Lemezkezelés segédprogramot. A részletes utasításokat a [Kötetek csatlakoztatása című témakörben találja.](storsimple-snapshot-manager-manage-volumes.md#mount-volumes) Az inicializálás után a kötet a **Hatókör** ablaktábla **Kötetek** csomópontja alatt jelenik meg. Ha nem látja a listában szereplő kötetet, frissítse a kötetek listáját (kattintson a jobb gombbal a **Kötetek** csomópontra, majd kattintson a **Frissítés parancsra).**

## <a name="delete-a-backup"></a>Biztonsági másolat törlése
Az alábbi eljárással törölheti a pillanatképet a biztonsági másolat katalógusából. 

> [!NOTE]
> A pillanatkép törlése törli a pillanatképhez társított biztonsági másolatot. Az adatok felhőből történő tisztításának folyamata azonban eltarthat egy ideig.<br>


#### <a name="to-delete-a-backup"></a>Biztonsági másolat törlése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblában bontsa ki a **Biztonsági másolat katalógus** csomópontját, bontsa ki a kötetcsoportot, majd kattintson a Helyi **pillanatképek** vagy **a Felhőbeli pillanatképek**elemre. A pillanatképek listája megjelenik az **Eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a törölni kívánt pillanatképre, majd kattintson a **Törlés parancsra.**
4. Amikor megjelenik a megerősítő üzenet, kattintson **az OK**gombra.

## <a name="recover-a-file"></a>Fájl helyreállítása
Ha egy fájlt véletlenül törölegy kötetről, a fájlt helyreállíthatja úgy, hogy lekéri a törlést megelőző pillanatképet, a pillanatkép segítségével létrehozza a kötet klónozását, majd a fájlt a klónozott kötetről az eredeti kötetre másolja.

#### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik-e a kötetcsoport aktuális biztonsági másolatával. Ezután törölje a kötetcsoport egyik kötetén tárolt fájlt. Végül az alábbi lépésekkel állítsa vissza a törölt fájlt a biztonsági másolatból. 

#### <a name="to-recover-a-deleted-file"></a>Törölt fájl helyreállítása
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. Megjelenik a StorSimple Snapshot Manager konzolablaka. 
2. A **Hatókör** ablaktáblában bontsa ki a **Biztonsági másolat katalógus** csomópontját, és keresse meg a törölt fájlt tartalmazó pillanatképet. Általában ki kell választania egy pillanatképet, amely közvetlenül a törlés előtt jött létre.
3. Keresse meg a klónozni kívánt kötetet, kattintson a jobb gombbal, és válassza a **Klónozás (Klónozás) parancsra.** Megjelenik **a Klónfelhő-pillanatkép** párbeszédpanel.
   
    ![Felhőbeli pillanatkép klónozása](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Töltse ki a **Clone Cloud Snapshot** párbeszédpanelt az alábbiak szerint: 
   
   1. A **Név** mezőbe írja be a klónozott kötet nevét. Ez a név jelenik meg a **Kötetek** csomópontban. 
   2. (Nem kötelező) Válassza a **Meghajtó**lehetőséget, majd jelöljön ki egy meghajtóbetűjelet a legördülő listából. 
   3. (Nem kötelező) Jelölje ki **a Mappa (NTFS)** lehetőséget, és írja be a mappa elérési útját, vagy kattintson **a Tallózás gombra,** és adja meg a mappa helyét. 
   4. Kattintson **a Létrehozás gombra.** 
5. Amikor a klónozási folyamat befejeződött, inicializálnia kell a klónozott kötetet. Indítsa el a Kiszolgálókezelőt, majd indítsa el a Lemezkezelés segédprogramot. A részletes utasításokat a [Kötetek csatlakoztatása című témakörben találja.](storsimple-snapshot-manager-manage-volumes.md#mount-volumes) Az inicializálás után a kötet a **Hatókör** ablaktábla **Kötetek** csomópontja alatt jelenik meg. 
   
    Ha nem látja a listában szereplő kötetet, frissítse a kötetek listáját (kattintson a jobb gombbal a **Kötetek** csomópontra, majd kattintson a **Frissítés parancsra).**
6. Nyissa meg a klónozott kötetet tartalmazó NTFS mappát, **bontsa** ki a Kötetek csomópontot, majd nyissa meg a klónozott kötetet. Keresse meg a helyreállítani kívánt fájlt, és másolja az elsődleges kötetre.
7. A fájl visszaállítása után törölheti a klónozott kötetet tartalmazó NTFS mappát.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>A StorSimple Snapshot Manager adatbázis visszaállítása
Rendszeresen biztonsági másolatot kell biztonsági másolatot kell készített a StorSimple Snapshot Manager adatbázisról a gazdaszámítógépen. Ha katasztrófa történik, vagy a gazdaszámítógép bármilyen okból meghibásodik, visszaállíthatja azt a biztonsági másolatból. Az adatbázis biztonsági mentésének létrehozása manuális folyamat.

#### <a name="to-back-up-and-restore-the-database"></a>Az adatbázis biztonsági mentése és visszaállítása
1. A Microsoft StorSimple felügyeleti szolgáltatás leállítása:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján válassza az **Eszközök** menü **Szolgáltatások parancsát.**
   3. A **Szolgáltatások** ablakban válassza a **Microsoft StorSimple Management Service lehetőséget.**
   4. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás leállítása**elemre.
2. A gazdaszámítógépen keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog mappát. 
   
   > [!NOTE]
   > A ProgramData mappa rejtett.
   > 
   > 
3. Keresse meg a katalógus XML-fájlját, másolja a fájlt, és tárolja a másolatot biztonságos helyen vagy a felhőben. Ha a gazdagép meghibásodik, ezzel a biztonsági másolat fájllal helyreállíthatja a StorSimple Snapshot Managerben létrehozott biztonsági mentési házirendeket.
   
    ![Azure StorSimple biztonsági másolat katalógusfájlja](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Indítsa újra a Microsoft StorSimple felügyeleti szolgáltatást: 
   
   1. A Kiszolgálókezelő irányítópultján válassza az **Eszközök** menü **Szolgáltatások parancsát.**
   2. A **Szolgáltatások** ablakban válassza a **Microsoft StorSimple Management Service lehetőséget.**
   3. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás újraindítása**elemre.
5. A gazdaszámítógépen keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog mappát. 
6. Törölje a katalógus XML-fájlját, és cserélje le a létrehozott biztonsági másolat verziójára. 
7. Kattintson az asztali StorSimple Snapshot Manager ikonra a StorSimple Snapshot Manager elindításához. 

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple Snapshot Manager használatával a StorSimple megoldás felügyeletéről.](storsimple-snapshot-manager-admin.md)
* További információ a [StorSimple Snapshot Manager feladatairól és munkafolyamatairól.](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows)

