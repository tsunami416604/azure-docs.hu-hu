---
title: StorSimple Snapshot Manager és kötetek | Microsoft dokumentumok
description: A StorSimple Snapshot Manager MMC beépülő modul használatával megtekintheti és kezelheti a köteteket, valamint a biztonsági másolatokat.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: twooley
ms.openlocfilehash: f09d4dd46a50f1794e51342a939b8919c5c523ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254650"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Kötetek megtekintése és kezelése a StorSimple Snapshot Manager segítségével
## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager **kötetek** csomópont (a **Hatókör** ablaktáblán) segítségével kiválaszthatköteteket, és megtekintheti a rájuk vonatkozó információkat. A kötetek meghajtóként jelennek meg, amelyek megfelelnek az állomás által csatlakoztatott kötetnek. A **Kötetek** csomópont a StorSimple által támogatott helyi köteteket és kötettípusokat jeleníti meg, beleértve az iSCSI és egy eszköz használatával felderített köteteket is. 

A támogatott kötetekről a [Több kötettípus támogatása](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)című részben talál további információt.

![Kötetlista az Eredmények ablaktáblában](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

A **Kötetek** csomópont lehetővé teszi a kötetek újravizsgálatát vagy törlését, miután a StorSimple Snapshot Manager felderíti azokat. 

Ez az oktatóanyag bemutatja, hogyan csatlakoztathat, inicializálhat és formázhat köteteket, majd hogyan használhatja a StorSimple Snapshot Managert a következőkhöz:

* Kötetek adatainak megtekintése 
* Kötetek törlése
* Kötetek újrabeszekveretése 
* Alapkötet konfigurálása és biztonsági másolatot ad róla
* Dinamikus tükrözött kötet konfigurálása és biztonsági másolatot

> [!NOTE]
> A **Kötet** csomópont összes művelete is elérhető a **Műveletek** ablaktáblán.
> 
> 

## <a name="mount-volumes"></a>Kötetek csatlakoztatása
A StorSimple kötetek csatlakoztatása, inicializálása és formázása az alábbi eljárással történik. Ez az eljárás a Lemezkezelés segédprogramot, a merevlemezek és a megfelelő kötetek vagy partíciók kezelését szolgáló rendszersegédprogramot használja. A Lemezkezelés szolgáltatásról a Microsoft TechNet webhely [lemezkezelése](https://technet.microsoft.com/library/cc770943.aspx) című webhelyén talál további információt.

#### <a name="to-mount-volumes"></a>Kötetek csatlakoztatása
1. A gazdaszámítógépen indítsa el a Microsoft iSCSI kezdeményezőjét.
2. Adja meg az egyik kapcsolat IP-címét célportálként vagy felderítési IP-címként, és csatlakozzon az eszközhöz. Az eszköz csatlakoztatása után a kötetek elérhetők lesznek a Windows rendszer számára. A Microsoft iSCSI-kezdeményező használatáról a [Microsoft iSCSI-kezdeményező telepítése és konfigurálása][1]című témakör "Csatlakozás iSCSI-tárolóeszközhöz" című részében talál további információt.
3. A Lemezkezelés segédprogram az alábbi lehetőségek valamelyikével indítható el:
   
   * Írja be a Diskmgmt.msc beírást a **Futtatás** mezőbe.
   * Indítsa el a Kiszolgálókezelőt, **bontsa** ki a Tároló csomópontot, és válassza a **Lemezkezelés**lehetőséget.
   * Indítsa el a **Felügyeleti eszközöket**, bontsa ki a **Számítógép-kezelés** csomópontot, és válassza a **Lemezkezelés**lehetőséget. 
     
     > [!NOTE]
     > A Lemezkezelés futtatásához rendszergazdai jogosultságokat kell használnia.
     > 
     > 
4. Vegye a hangerő(k) online:
   
   1. A Lemezkezelés csoportban kattintson a jobb gombbal bármelyik **Kapcsolat nélküli**jelölésű kötetre.
   2. Kattintson **a Lemez újraaktiválása gombra.** A lemez újraaktiválása után a lemezt **online** jelöléssel kell ellátva.
5. A térfogat(ok) inicializálása:
   
   1. Kattintson a jobb gombbal a felderített kötetekre.
   2. A menüben válassza a **Lemez inicializálása parancsot.**
   3. A **Lemez inicializálása** párbeszédpanelen jelölje ki az inicializálni kívánt lemezeket, majd kattintson az **OK**gombra.
6. Egyszerű kötetek formázása:
   
   1. Kattintson a jobb gombbal a formázni kívánt kötetre.
   2. A menüben válassza az **Új egyszerű kötet parancsot**.
   3. A kötet formázásához használja az Új egyszerű kötet varázslót:
      
      * Adja meg a kötet méretét.
      * Adja meg a meghajtó betűjelét.
      * Jelölje ki az NTFS fájlrendszert.
      * Adjon meg 64 KB-os lemezfoglalásiegység-méretet.
      * Hajtson végre egy gyorsformázást.
7. Többpartíciós kötetek formázása. További információt a [Lemezkezelés megvalósítása](https://msdn.microsoft.com/library/dd163556.aspx)című szakasz "Partíciók és kötetek" című részében talál.

## <a name="view-information-about-your-volumes"></a>A kötetekkel kapcsolatos információk megtekintése
Az alábbi eljárással megtekintheti a helyi és az Azure StorSimple-kötetekkel kapcsolatos információkat.

#### <a name="to-view-volume-information"></a>Kötetadatok megtekintése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához. 
2. A **Hatókör** ablaktáblán kattintson a **Kötetek** csomópontra. A helyi és csatlakoztatott kötetek listája, beleértve az összes Azure StorSimple kötetet, megjelenik az **Eredmények** ablaktáblán. **Az Eredmények** ablaktábla oszlopai konfigurálhatók. (Kattintson a jobb gombbal a **Kötetek** csomópontra, válassza a **Nézet parancsot,** majd válassza **az Oszlopok hozzáadása/eltávolítása parancsot.)**
   
    ![Oszlopok konfigurálása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Eredmények oszlop | Leírás |
   |:--- |:--- |
   |  Név |A **Név** oszlop az egyes felderített kötetekmeghajtó betűjelét tartalmazza. |
   |  Eszköz |Az **Eszköz** oszlop a gazdaszámítógéphez csatlakoztatott eszköz IP-címét tartalmazza. |
   |  Eszközkötet neve |Az **Eszközkötet neve** oszlop annak az eszközkötetnek a nevét tartalmazza, amelyhez a kijelölt kötet tartozik. Ez az adott kötethez az Azure Portalon definiált kötetnév. |
   |  Elérési utak |Az **Elérési útvonalak** oszlop megjeleníti a kötet elérési útvonalát. Ez az a meghajtóbetűjel vagy csatlakoztatási pont, ahol a kötet elérhető a gazdaszámítógépen. |

## <a name="delete-a-volume"></a>Kötet törlése
Az alábbi eljárással törölheti a kötetet a StorSimple Snapshot Managerből.

> [!NOTE]
> Nem törölhet kötetet, ha az bármely kötetcsoport része. (A törlési lehetőség nem érhető el olyan kötetek esetén, amelyek kötetcsoport tagjai.) A kötet törléséhez törölnie kell a teljes kötetcsoportot.

#### <a name="to-delete-a-volume"></a>Kötet törlése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a **Kötetek** csomópontra. 
3. Az **Eredmények** ablaktáblán kattintson a jobb gombbal a törölni kívánt kötetre.
4. Kattintson a menü **Törlés parancsára.** 
   
    ![Kötet törlése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Megjelenik **a Kötet törlése** párbeszédpanel. Írja be a Megerősítés szövegmezőbe a **Megerősítés** szöveget, majd kattintson az **OK**gombra.
6. Alapértelmezés szerint a StorSimple Snapshot Manager biztonsági másolatot ad egy kötetről a törlés előtt. Ez az óvintézkedés megvédi Önt az adatvesztéstől, ha a törlés nem volt szándékos. A StorSimple Snapshot Manager **egy automatikus pillanatkép-folyamatjelző** üzenetet jelenít meg, miközben biztonsági másolatot ad a kötetről. 
   
    ![Automatikus pillanatkép-üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Kötetek újrabeszekveretése
Az alábbi eljárással újrabeolvashatja a StorSimple Snapshot Managerhez csatlakoztatott köteteket.

#### <a name="to-rescan-the-volumes"></a>A kötetek újbóli besztatorálása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal **a Kötetek**elemre, majd kattintson **a Kötetek újrabehitelesítése parancsra.**
   
    ![Kötetek újrabeszekveretése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ez az eljárás szinkronizálja a kötetlistát a StorSimple Snapshot Manager rel. Az eredmények ben megjelennek minden változás, például az új kötetek vagy a törölt kötetek.

## <a name="configure-and-back-up-a-basic-volume"></a>Alapkötet konfigurálása és biztonsági másolatot ad
Az alábbi eljárással konfigurálhatja az alapkötet biztonsági másolatát, majd azonnal elindíthatja a biztonsági mentést, vagy létrehozhat egy házirendet az ütemezett biztonsági mentések számára.

### <a name="prerequisites"></a>Előfeltételek
Előkészületek:

* Győződjön meg arról, hogy a StorSimple eszköz és a gazdaszámítógép megfelelően van konfigurálva. További információért látogasson el a helyszíni [StorSimple-eszköz telepítése című webhelyre.](storsimple-deployment-walkthrough-u2.md)
* Telepítse és konfigurálja a StorSimple Snapshot Managert. További információ: [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Alapkötet biztonsági másolatának konfigurálása
1. Hozzon létre egy alapkötetet a StorSimple eszközön.
2. A kötet csatlakoztatása, inicializálása és formázása a [Kötetcsatlakoztatási kötetekben leírtak szerint.](#mount-volumes) 
3. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. Megjelenik a StorSimple Snapshot Manager ablak. 
4. A **Hatókör** ablaktáblán kattintson a jobb gombbal a **Kötetek** csomópontra, majd válassza **a Kötetek újrabehitelesítése parancsot**. Amikor a vizsgálat befejeződött, a kötetek listájának meg kell jelennie az **Eredmények** ablaktáblán. 
5. Az **Eredmények** ablaktáblán kattintson a jobb gombbal a kötetre, és válassza **a Kötetcsoport létrehozása parancsot.** 
   
    ![Kötetcsoport létrehozása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. A **Kötetcsoport létrehozása** párbeszédpanelen írja be a kötetcsoport nevét, rendeljen hozzá köteteket, majd kattintson az **OK**gombra.
7. A **Hatókör** ablaktáblában bontsa ki a **Kötetcsoportok** csomópontot. Az új kötetcsoportnak a Kötetcsoportok csomópont alatt kell **megjelennie.** 
8. Kattintson a jobb gombbal a kötetcsoport nevére.
   
   * Interaktív (igény szerinti) biztonsági mentési feladat elindításához kattintson a **Biztonsági mentés készítése**gombra. 
   * Az automatikus biztonsági mentés ütemezéséhez kattintson a **Biztonsági másolat létrehozása házirend gombra.** Az **Általános** lapon válasszon ki egy kötetcsoportot a listából. Az **Ütemezés** lapon adja meg az ütemezés részleteit. Amikor végzett, kattintson az **OK**gombra. 
9. Annak ellenőrzéséhez, hogy a biztonsági mentési feladat elindult-e, bontsa ki a **Feladatok** csomópontot a **Hatókör** ablaktáblában, majd kattintson a **Futó** csomópontra. Az éppen futó feladatok listája megjelenik az **Eredmények** ablaktáblán. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet konfigurálása és biztonsági másolatot ad
A dinamikus tükrözött kötet biztonsági mentésének konfigurálásához hajtsa végre az alábbi lépéseket:

* 1. lépés: Dinamikus tükrözött kötet létrehozásához használja a Lemezkezelés segédprogramot. 
* 2. lépés: A Biztonsági másolat konfigurálásához használja a StorSimple Snapshot Managert.

### <a name="prerequisites"></a>Előfeltételek
Előkészületek:

* Győződjön meg arról, hogy a StorSimple eszköz és a gazdaszámítógép megfelelően van konfigurálva. További információért látogasson el a helyszíni [StorSimple-eszköz telepítése című webhelyre.](storsimple-8000-deployment-walkthrough-u2.md)
* Telepítse és konfigurálja a StorSimple Snapshot Managert. További információ: [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Konfiguráljon két kötetet a StorSimple eszközön. (A példákban a rendelkezésre álló kötetek az **1.** és **2. lemez**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>1. lépés: Dinamikus tükrözött kötet létrehozása a Lemezkezelés segédprogramjával
A Lemezkezelés a merevlemezek és a benne lévő kötetek vagy partíciók kezelésére szolgáló rendszersegédprogram. A Lemezkezelés szolgáltatásról a Microsoft TechNet webhely [lemezkezelése](https://technet.microsoft.com/library/cc770943.aspx) című webhelyén talál további információt.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet létrehozása
1. A Lemezkezelés segédprogram az alábbi lehetőségek valamelyikével indítható el: 
   
   * Nyissa meg a **Futtatás** párbeszédpanelt, írja be a **Diskmgmt.msc**, és nyomja le az Enter billentyűt.
   * Indítsa el a Kiszolgálókezelőt, **bontsa** ki a Tároló csomópontot, és válassza a **Lemezkezelés**lehetőséget. 
   * Indítsa el a **Felügyeleti eszközöket**, bontsa ki a **Számítógép-kezelés** csomópontot, és válassza a **Lemezkezelés**lehetőséget. 
2. Győződjön meg arról, hogy két kötet érhető el a StorSimple eszközön. (A példában a rendelkezésre álló kötetek az **1.** és a **2.)** 
3. A Lemezkezelés ablak alsó ablakának jobb oldali oszlopában kattintson **New Mirrored Volume**a jobb gombbal az **1.** 
   
    ![Új tükrözött kötet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Az **Új tükrözött mennyiség varázsló** lapon kattintson a **Tovább**gombra.
5. A **Lemezek kijelölése** lapon válassza a **Add** **Next** **2.** **Selected** 
6. A **Meghajtóbetűjel vagy** a Görbe hozzárendelése lapon fogadja el az alapértelmezett értékeket, majd kattintson a **Tovább**gombra. 
7. A **Kötet formázása** lap **Felosztási egység mérete** mezőjében válassza a **64K**lehetőséget. Jelölje be a **Gyorsformázás végrehajtása** jelölőnégyzetet, majd kattintson a **Tovább**gombra. 
8. Az **Új tükrözött kötet befejezése** lapon tekintse át a beállításokat, majd kattintson a **Befejezés**gombra. 
9. Egy üzenet jelzi, hogy az alaplemez dinamikus lemezvé alakul át. Kattintson **az Igen gombra.**
   
    ![Dinamikus lemezkonverziós üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. A Lemezkezelés csoportban ellenőrizze, hogy az 1. (**A dinamikus** értéknek meg kell jelennie az állapotoszlopban, és a kapacitássáv színének pirosra kell változnia, tükrözött kötetet jelezve.) 
    
    ![Lemezkezelés tükrözött dinamikus lemezek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>2. lépés: A biztonsági mentés konfigurálásához használja a StorSimple Snapshot Managert
Az alábbi eljárással konfigurálhat egy dinamikus tükrözött kötetet, majd azonnal elindíthat egy biztonsági mentést, vagy létrehozhat egy házirendet az ütemezett biztonsági mentések számára.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet biztonsági másolatának beállítása
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. Megjelenik a StorSimple Snapshot Manager ablak. 
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal a **Kötetek** csomópontra, és válassza **a Kötetek újrabeszuka parancsot**. Amikor a vizsgálat befejeződött, a kötetek listájának meg kell jelennie az **Eredmények** ablaktáblán. A dinamikus tükrözött kötet egyetlen kötetként jelenik meg. 
3. Az **Eredmények** ablaktáblán kattintson a jobb gombbal a dinamikus tükrözött kötetre, majd kattintson a **Kötetcsoport létrehozása parancsra.** 
4. A **Kötetcsoport létrehozása** párbeszédpanelen írja be a kötetcsoport nevét, rendelje hozzá a dinamikus tükrözött kötetet ehhez a csoporthoz, majd kattintson az **OK**gombra. 
5. A **Hatókör** ablaktáblában bontsa ki a **Kötetcsoportok** csomópontot. Az új kötetcsoportnak a Kötetcsoportok csomópont alatt kell **megjelennie.** 
6. Kattintson a jobb gombbal a kötetcsoport nevére. 
   
   * Interaktív (igény szerinti) biztonsági mentési feladat elindításához kattintson a **Biztonsági mentés készítése**gombra. 
   * Az automatikus biztonsági mentés ütemezéséhez kattintson a **Biztonsági másolat létrehozása házirend gombra.** Az **Általános** lapon válassza ki a kötetcsoportot a listából. Az **Ütemezés** lapon adja meg az ütemezés részleteit. Amikor végzett, kattintson az **OK**gombra. 
7. A biztonsági mentési feladat futásközben figyelhető. A **Hatókör** ablaktáblán bontsa ki a **Feladatok** csomópontot, majd kattintson a **Futás**gombra, a feladat részletei az **Eredmények** ablaktáblán jelennek meg. Amikor a biztonsági mentési feladat befejeződött, a részletek átkerülnek az **elmúlt 24** órás feladatlistára. 

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelheti a StorSimple snapshot kezelőt a StorSimple megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet kötetcsoportokat a StorSimple Snapshot Manager segítségével.](storsimple-snapshot-manager-manage-volume-groups.md)

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
