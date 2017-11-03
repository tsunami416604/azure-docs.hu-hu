---
title: "StorSimple Snapshot Manager és a kötetek |} Microsoft Docs"
description: "Ismerteti, hogyan megtekintéséhez és kezeléséhez kötetek és a biztonsági mentések konfigurálása a StorSimple Snapshot Manager MMC beépülő modul segítségével."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 2c0b211bced99d272a73a7b018a22f99d8d58aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>StorSimple Snapshot Manager segítségével megtekintheti, és a kötetek kezelése
## <a name="overview"></a>Áttekintés
Használhatja a StorSimple Snapshot Manager **kötetek** csomópont (a a **hatókör** ablaktáblán) válassza ki a köteteket, és tekintse meg azokat. A kötetek meghajtóként, amelyek megfelelnek a kötetek, a gazdagép által csatlakoztatott jelennek meg. A **kötetek** csomópontban jelennek meg a helyi kötet és a StorSimple, beleértve az iSCSI és egy eszköz révén felderített kötetek által támogatott kötet típusok. 

Támogatott kötetek kapcsolatos további információkért látogasson el [többféle kötet támogatása](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Az eredmények ablaktábláján kötet listája](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

A **kötetek** csomópont emellett lehetővé teszi a futtasson újbóli keresést vagy kötetek törlése után a StorSimple Snapshot Manager deríti fel őket. 

Ez az oktatóanyag azt ismerteti, hogyan csatlakoztatása, inicializálása, és a kötetek formázásának és majd használni a StorSimple Snapshot Manager:

* Kötetek adatainak megtekintése 
* Kötetek törlése
* Kötetek újraellenőrzése 
* Egyszerű kötet konfigurálása és biztonsági másolatot készíteni
* A dinamikus tükrözött kötetek konfigurálása és biztonsági másolatot készíteni

> [!NOTE]
> Összes a **kötet** csomópont műveletek is rendelkezésre állnak a a **műveletek** ablaktáblán.
> 
> 

## <a name="mount-volumes"></a>Kötet csatlakoztatása
A következő eljárás segítségével csatlakoztassa, inicializálja és formázza a StorSimple-köteteket. Ez az eljárás használja a Lemezkezelés alkalmazásban merevlemezek és a megfelelő kötetek vagy partíciók kezelésére szolgál. A Lemezkezelés eszköz kapcsolatos további információkért látogasson el [Lemezkezelés](https://technet.microsoft.com/library/cc770943.aspx) a Microsoft TechNet webhelyen.

#### <a name="to-mount-volumes"></a>Kötet csatlakoztatása
1. A gazdaszámítógépen indítsa el a Microsoft iSCSI-kezdeményező.
2. Adjon meg egy illesztőfelület IP-címek a cél portál vagy felderítési IP-címet, és csatlakozzon az eszközhöz. Az eszköz csatlakoztatása után a kötetek lesznek elérhetők, a Windows rendszerben. A Microsoft iSCSI-kezdeményező használatával kapcsolatos további információkért látogasson el a szakasz "Csatlakozik az iSCSI-tárolóeszköz" a [telepítése és konfigurálása a Microsoft iSCSI-kezdeményező][1].
3. A Lemezkezelés eszköz elindításához használja a következő beállításokat:
   
   * Írja be a Diskmgmt.msc a **futtatása** mezőbe.
   * Indítsa el a Kiszolgálókezelőt, bontsa ki a **tárolási** csomópont, és válassza **Lemezkezelés**.
   * Start **felügyeleti eszközök**, bontsa ki a **számítógép-kezelés** csomópont, és válassza **Lemezkezelés**. 
     
     > [!NOTE]
     > A Lemezkezelés eszköz futtatásához rendszergazdai jogosultsággal kell használnia.
     > 
     > 
4. A kötetek online érvénybe:
   
   1. A Lemezkezelés beépülő modulban kattintson a jobb gombbal bármely megjelölt kötet **Offline**.
   2. Kattintson a **lemez újraaktiválása**. A lemez kell megjelölni **Online** után a lemez újraaktiválásakor.
5. A kötetek inicializálása:
   
   1. Kattintson a jobb gombbal a felderített köteteket.
   2. Válassza a menü **lemez inicializálása**.
   3. Az a **lemez inicializálása** párbeszédpanel megnyitásához, válassza ki, inicializálja a lemezeket, és kattintson **OK**.
6. Egyszerű kötetek formázása:
   
   1. Kattintson a jobb gombbal a kötet formázásához kívánt.
   2. Válassza a menü **új egyszerű kötet**.
   3. Az új egyszerű kötet varázsló segítségével formázza a kötetet:
      
      * Adja meg a kötet méretét.
      * Adjon meg egy meghajtóbetűjelet.
      * Válassza ki az NTFS fájlrendszerhez.
      * Adjon meg 64 KB-os lemezfoglalásiegység-méretet.
      * Hajtson végre egy gyorsformázást.
7. Több partíció kötetek formázása. Útmutatás, keresse meg a "Partíciók és kötetek" szakaszban [Lemezkezelés végrehajtási](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>A kötetek adatainak megtekintése
Az alábbi eljárás segítségével tekintse meg helyi és az Azure StorSimple-köteteket.

#### <a name="to-view-volume-information"></a>A kötet adatainak megtekintése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager. 
2. Az a **hatókör** ablaktáblán kattintson a **kötetek** csomópont. Helyi és csatlakoztatott köteteket, beleértve az összes Azure StorSimple-köteteket, megjelenik a **eredmények** ablaktáblán. Az oszlopok a **eredmények** ablaktáblán is konfigurálható. (Kattintson a jobb gombbal a **kötetek** csomópontban jelölje ki **nézet**, majd válassza ki **oszlopok hozzáadása/eltávolítása**.)
   
    ![Az oszlopok konfigurálása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Eredmények oszlop | Leírás |
   |:--- |:--- |
   |  Név |A **neve** oszlop tartalmaz minden felderített kötetre rendelt meghajtóbetűjelet. |
   |  Eszköz |A **eszköz** oszlop tartalmazza az eszköz csatlakoztatva van a számítógép IP-címét. |
   |  Eszköz kötet neve |A **kötet eszköznév** oszlop, amely a kijelölt kötet tartozik eszköz kötet nevét tartalmazza. Ez az adott köteten az Azure portálon meghatározott kötet neve. |
   |  Elérési utak |A **elérési utak** oszlopában láthatók az elérési útvonalat a köteten. Ez az a meghajtóbetűjel vagy csatlakozási pont, amikor a kötet nem érhető el a gazdagépen. |

## <a name="delete-a-volume"></a>Kötet törlése
A következő eljárással kötet a StorSimple Snapshot Manager törlése.

> [!NOTE]
> A kötet nem törölhető, ha valamelyik kötet csoport része. (A törlési beállítás nem érhető el, amelyek egy kötet csoport tagjai kötetek.) A teljes kötet csoport törlése a köteten kell törölnie.

#### <a name="to-delete-a-volume"></a>Kötet törlése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **kötetek** csomópont. 
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal a kötetet, amelyet törölni szeretné.
4. A menüben kattintson a **törlése**. 
   
    ![Kötet törlése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. A **kötet törlése** párbeszédpanel jelenik meg. Típus **megerősítése** a szövegmezőbe, majd **OK**.
6. Alapértelmezés szerint a StorSimple Snapshot Manager biztonsági mentést készít egy kötet törlése előtt. Ez okokból is védve az adatveszteség, ha a törlés nem volt szándékos. StorSimple Snapshot Manager megjelenít egy **automatikus pillanatkép** állapotüzenetet, amíg azt készít biztonsági másolatot a kötetet. 
   
    ![Automatikus pillanatkép üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Kötetek újraellenőrzése
A következő eljárással területén újra megvizsgálja a csatlakoztatott StorSimple Snapshot Manager köteteket.

#### <a name="to-rescan-the-volumes"></a>A kötetek újbóli ellenőrzéséhez
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **kötetek**, és kattintson a **ismételt vizsgálat kötetek**.
   
    ![Kötetek újraellenőrzése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ez az eljárás szinkronizálja a kötet lista és a StorSimple Snapshot Manager. A módosításokat, például az új kötetekre vagy törölt kötetek, az eredmények fog szerepelni.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurálja, és készítsen biztonsági másolatot alaplemezek
A következő eljárás segítségével konfigurálhatja egy alapvető kötet, és majd a biztonsági mentést azonnal elindítani vagy ütemezett biztonsági mentések házirendet hozhat létre.

### <a name="prerequisites"></a>Előfeltételek
Előzetes teendők

* Győződjön meg arról, hogy a StorSimple eszköz és a fogadó számítógép helyesen vannak-e konfigurálva. További információkért látogasson el [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).
* Telepítse és konfigurálja a StorSimple Snapshot Manager. További információkért látogasson el [StorSimple Snapshot Manager telepítése](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Egyszerű kötet biztonsági mentését konfigurálása
1. Egyszerű kötet létrehozása a StorSimple eszközön.
2. Csatlakoztassa, inicializálja és formázza a kötetet a [csatlakoztassa köteteket](#mount-volumes). 
3. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager ablak. 
4. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** csomópont, és válassza **ismételt vizsgálat kötetek**. Ha az ellenőrzés befejeződött, meg kell jelennie a kötetek listáját a **eredmények** ablaktáblán. 
5. Az a **eredmények** ablaktáblán kattintson a jobb gombbal a kötetre, és válassza ki **kötet csoport létrehozása**. 
   
    ![Kötet csoport létrehozása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Az a **kötet csoport létrehozása** párbeszédpanelen adja meg a kötet csoport nevét, kötetek hozzárendelése, és kattintson **OK**.
7. Az a **hatókör** ablaktáblában bontsa ki a **kötet csoportok** csomópont. Az új kötet csoport alatt kell megjelennie a **kötet csoportok** csomópont. 
8. Kattintson a jobb gombbal a kötet csoport nevét.
   
   * Interaktív (igény) biztonsági mentési feladat elindításához kattintson **érvénybe biztonsági mentési**. 
   * Az automatikus biztonsági mentés, kattintson a **biztonsági mentési házirend létrehozása**. Az a **általános** lapon, válassza ki a kötetet a listából. Az a **ütemezés** lapján írja be az ütemezési adatait. Ha elkészült, kattintson a **OK**. 
9. Győződjön meg arról, hogy elindult-e a biztonsági mentési feladat, bontsa ki a **feladatok** csomópontja a **hatókör** ablaktáblán, és kattintson a **futtató** csomópont. A jelenleg futó feladatok listája jelenik meg a **eredmények** ablaktáblán. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurálja, és készítsen biztonsági másolatot a dinamikus tükrözött kötetek
Az alábbi lépésekkel konfigurálhatja a dinamikus tükrözött kötetek biztonsági mentése:

* 1. lépés: A Lemezkezelés dinamikus tükrözött kötet létrehozásához. 
* 2. lépés: Használja a StorSimple Snapshot Manager konfigurálhatja.

### <a name="prerequisites"></a>Előfeltételek
Előzetes teendők

* Győződjön meg arról, hogy a StorSimple eszköz és a fogadó számítógép helyesen vannak-e konfigurálva. További információkért látogasson el [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).
* Telepítse és konfigurálja a StorSimple Snapshot Manager. További információkért látogasson el [StorSimple Snapshot Manager telepítése](storsimple-snapshot-manager-deployment.md).
* Két kötet konfigurálása a StorSimple eszközön. (A példákban az elérhető kötetek vannak **lemez 1** és **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>1. lépés: A Lemezkezelés dinamikus tükrözött kötet létrehozása
A Lemezkezelés eszköz szolgál merevlemezek és a kötetek vagy partíciókat tartalmaz. A Lemezkezelés eszköz kapcsolatos további információkért látogasson el [Lemezkezelés](https://technet.microsoft.com/library/cc770943.aspx) a Microsoft TechNet webhelyen.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Dinamikus tükrözött kötet létrehozása
1. A Lemezkezelés eszköz elindításához használja a következő beállításokat: 
   
   * Nyissa meg a **futtatása** mezőbe írja be **Diskmgmt.msc**, és nyomja le az ENTER billentyűt.
   * Indítsa el a Kiszolgálókezelőt, bontsa ki a **tárolási** csomópont, és válassza **Lemezkezelés**. 
   * Start **felügyeleti eszközök**, bontsa ki a **számítógép-kezelés** csomópont, és válassza **Lemezkezelés**. 
2. Győződjön meg arról, hogy rendelkezik-e elérhető két kötet a StorSimple eszközön. (A példában az elérhető kötetek vannak **lemez 1** és **Disk 2**.) 
3. A Lemezkezelőben, a jobb oldali oszlopban található az alsó ablaktáblán kattintson a jobb gombbal **lemez 1** válassza **új tükrözött kötet**. 
   
    ![Új tükrözött kötetek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Az a **új tükrözött kötet** varázslólap, kattintson a **következő**.
5. A a **lemez kiválasztása** lapon, válassza ki **Disk 2** a a **kijelölt** ablaktáblán kattintson a **Hozzáadás**, és kattintson a **tovább**. 
6. Az a **meghajtóbetűjel hozzárendelése vagy az elérési út** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **következő**. 
7. Az a **kötet formázásának** lap a **foglalásiegység-méret** mezőben válassza **64K**. Válassza ki a **gyorsformázást** jelölőnégyzetet, majd kattintson a **következő**. 
8. Az a **a tükrözött új kötet varázsló befejezése** lapon tekintse át a beállításokat, és kattintson a **Befejezés**. 
9. Megjelenik egy üzenet jelzi, hogy az alaplemez dinamikus lemezzé alakítja át. Kattintson a **Yes** (Igen) gombra.
   
    ![Dinamikus lemez átalakítás üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. A Lemezkezelés eszközben ellenőrizze, hogy lemez 1 és lemez 2 rendszer dinamikus tükrözött kötetek. (**Dinamikus** meg kell jelennie az Állapot oszlopban, és a kapacitás színe piros, a tükrözött kötetek jelző következőre kell módosítani.) 
    
    ![Lemez tükrözött felügyeleti dinamikus lemezek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>2. lépés: Használja StorSimple Snapshot Manager biztonsági mentés konfigurálása
A következő eljárás segítségével konfigurálhatja a dinamikus tükrözött kötet, és majd a biztonsági mentést azonnal elindítani vagy ütemezett biztonsági mentések házirendet hozhat létre.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Konfigurálhatja a dinamikus tükrözött kötetek biztonsági mentése
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager ablak. 
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** csomópont, és válassza **ismételt vizsgálat kötetek**. Ha az ellenőrzés befejeződött, meg kell jelennie a kötetek listáját a **eredmények** ablaktáblán. A dinamikus tükrözött kötet van megadva, csak egy kötet. 
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal a dinamikus tükrözött kötet, és kattintson a **kötet csoport létrehozása**. 
4. Az a **kötet csoport létrehozása** párbeszédpanelen adja meg a kötet csoport nevét, a dinamikus tükrözött kötet hozzárendelése ehhez a csoporthoz, és kattintson **OK**. 
5. Az a **hatókör** ablaktáblában bontsa ki a **kötet csoportok** csomópont. Az új kötet csoport alatt kell megjelennie a **kötet csoportok** csomópont. 
6. Kattintson a jobb gombbal a kötet csoport nevét. 
   
   * Interaktív (igény) biztonsági mentési feladat elindításához kattintson **érvénybe biztonsági mentési**. 
   * Az automatikus biztonsági mentés, kattintson a **biztonsági mentési házirend létrehozása**. Az a **általános** lapon, válassza ki a kötetet a listából. Az a **ütemezés** lapján írja be az ütemezési adatait. Ha elkészült, kattintson a **OK**. 
7. Figyelheti a biztonsági mentési feladat futtatása közben. Az a **hatókör** ablaktáblában bontsa ki a **feladatok** csomópontra, majd **futtató**, jelennek meg a feladat részleteit a **eredmények** ablaktáblán. Ha a biztonsági mentési feladat befejeződött, a rendszer továbbítja a részletek a **utolsó 24** óra feladat listája. 

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével kötet csoportok létrehozásához és kezeléséhez](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
