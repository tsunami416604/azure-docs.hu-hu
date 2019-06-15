---
title: A StorSimple Snapshot Manager és a kötetek |} A Microsoft Docs
description: Megtekintheti és kezelheti a köteteket és biztonsági mentések konfigurálása a StorSimple Snapshot Manager MMC beépülő modul használatát ismerteti.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 260dfdd4b8fe7c277358fa5773029ea9a532740a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61078307"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>A StorSimple Snapshot Manager segítségével megtekintheti, és a kötetek kezelése
## <a name="overview"></a>Áttekintés
Használhatja a StorSimple Snapshot Manager **kötetek** csomópont (az a **hatókör** ablaktáblán) válassza ki a köteteket, és a velük kapcsolatos információk megtekintéséhez. A kötetek jelennek meg a gazdagép által csatlakoztatott kötetek, amelyek megfelelnek meghajtókon. A **kötetek** csomópont helyi köteteken, többek között a kötetek felderített iSCSI és a egy eszközt a StorSimple által támogatott kötettípusok jeleníti meg. 

További információ a támogatott köteteket, nyissa meg [támogatása több kötet](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Eredmények ablaktábla listáján kötet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

A **kötetek** csomópont is lehetővé teszi ismételt vizsgálat vagy kötetek törlése után a StorSimple Snapshot Manager észleli őket. 

Ez az oktatóanyag azt ismerteti, hogyan lehet csatlakoztatása, inicializálása, és formázza a köteteket és majd a StorSimple Snapshot Manager használata:

* Kötetek adatainak megtekintése 
* Kötetek törlése
* Kötetek ismételt vizsgálata 
* Egyszerű kötet konfigurálása, és készítsen biztonsági másolatot
* A dinamikus tükrözött kötetek konfigurálni, és készítsen biztonsági másolatot

> [!NOTE]
> Összes a **kötet** csomópont műveletek is rendelkezésre áll a **műveletek** ablaktáblán.
> 
> 

## <a name="mount-volumes"></a>Kötetek csatlakoztatása
A következő eljárás használatával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket. Ez az eljárás használja a Lemezkezelés, merevlemezek és a megfelelő köteteket vagy partíciókat kezelésére szolgál. Lemezkezelés kapcsolatos további információkért látogasson el [Lemezkezelés](https://technet.microsoft.com/library/cc770943.aspx) a Microsoft TechNet webhelyen.

#### <a name="to-mount-volumes"></a>Kötetek csatlakoztatása
1. A gazdaszámítógépen indítsa el a Microsoft iSCSI-kezdeményező.
2. Adjon meg egy, a célportál IP-címek felület vagy felderítési IP-címet, és csatlakozni az eszközhöz. Az eszköz csatlakoztatása után a köteteket a Windows rendszerhez elérhető lesz. A Microsoft iSCSI-kezdeményező használatával kapcsolatos további információkért látogasson el a szakasz "Csatlakozik egy iSCSI-tárolóeszköz" a [telepítése és konfigurálása a Microsoft iSCSI-kezdeményező][1].
3. Indítsa el a Lemezkezelés használja a következő beállítások bármelyikét:
   
   * Írja be a Diskmgmt.msc a **futtatása** mezőbe.
   * Indítsa el a Kiszolgálókezelőt, bontsa ki a **tárolási** csomópontot, és válassza ki **Lemezkezelés**.
   * Indítsa el **felügyeleti eszközök**, bontsa ki a **számítógép-kezelés** csomópontot, és válassza ki **Lemezkezelés**. 
     
     > [!NOTE]
     > Futtassa a rendszergazdai jogosultságokkal kell használnia.
     > 
     > 
4. Online hajtsa végre a következő kötet(ek):
   
   1. A Lemezkezelés alkalmazásban kattintson a jobb gombbal bármelyik kötet megjelölve **Offline**.
   2. Kattintson a **lemez újraaktiválása**. A lemez kell megjelölni **Online** után a lemez újraaktiválják.
5. A következő kötet(ek) inicializálása:
   
   1. Kattintson a jobb gombbal a felderített köteteket.
   2. Válassza a menü **lemez inicializálása**.
   3. Az a **lemez inicializálása** párbeszédpanel, válassza ki a lemezeket, inicializálja, és kattintson **OK**.
6. Egyszerű kötet formázása:
   
   1. Kattintson a jobb gombbal a formázni kívánt kötet.
   2. Válassza a menü **új egyszerű kötet**.
   3. Használja az új egyszerű kötet varázslóban a kötet formázása:
      
      * Adja meg a kötet méretét.
      * Adjon meg egy meghajtóbetűjelet.
      * Válassza ki az NTFS fájlrendszerrel.
      * Adjon meg 64 KB-os lemezfoglalásiegység-méretet.
      * Hajtson végre egy gyorsformázást.
7. Több partíció kötetek formázása. Útmutatásért lépjen az "A partíciók és a kötetek" szakaszban az [Lemezkezelés végrehajtási](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>A kötetek adatainak megtekintése
Az alábbi eljárás segítségével a helyi és az Azure StorSimple-kötetek kapcsolatos információk megtekintéséhez.

#### <a name="to-view-volume-information"></a>Mennyiségi információk megtekintése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához. 
2. Az a **hatókör** ablaktáblán kattintson a **kötetek** csomópont. Megjelenik az összes Azure StorSimple-köteteket, beleértve a helyi és csatlakoztatott kötetek listáját a **eredmények** ablaktáblán. Az oszlopokat a **eredmények** panelen is konfigurálható. (Kattintson a jobb gombbal a **kötetek** csomópontban jelölje ki **nézet**, majd válassza ki **oszlopok hozzáadása és eltávolítása**.)
   
    ![Az oszlopok konfigurálása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Eredmények oszlop | Leírás |
   |:--- |:--- |
   |  Name (Név) |A **neve** oszlop tartalmazza minden egyes felderített kötetre rendelt meghajtóbetűjelet. |
   |  Eszköz |A **eszköz** oszlop tartalmazza a számítógép csatlakozik az eszköz IP-címét. |
   |  Eszköz kötet neve |A **kötet eszköznév** oszlopnak a neve, az eszköz a kötet, amely a kijelölt kötet tartozik. Ez az a kötet neve, az Azure Portalon, a megadott kötethez tartozó definiálva. |
   |  Elérési utak |A **elérési utak** oszlopban megjelenik az elérési út a kötetre. Ez az a meghajtó meghajtóbetűjelét vagy csatlakoztatási pontját, amikor a kötet nem érhető el a gazdagépen. |

## <a name="delete-a-volume"></a>Kötet törlése
Az alábbi eljárás segítségével a StorSimple Snapshot Manager-kötet törlése.

> [!NOTE]
> A kötet nem lehet törölni, ha minden kötet csoport tagja. (A törlési beállítás nem áll rendelkezésre, amelyek tagjai egy kötetcsoport kötetek esetén.) Törölnie kell a teljes kötet csoport törölni a kötetet.

#### <a name="to-delete-a-volume"></a>Kötet törlése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a **kötetek** csomópont. 
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal a kötetre, amelyet törölni szeretne.
4. Kattintson a menü **törlése**. 
   
    ![Kötet törlése](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. A **kötet törlése** párbeszédpanel jelenik meg. Típus **megerősítése** a szövegmezőbe, és kattintson a **OK**.
6. Alapértelmezés szerint a StorSimple Snapshot Managerrel biztonsági másolatot készít egy kötet törlése előtt. Az eszközeikről védheti meg az adatvesztést, ha a törlés nem volt szándékos. Megjeleníti a StorSimple Snapshot Manager- **automatikus pillanatfelvétel-készítési** állapotüzenete, amíg ez a kötet biztonsági mentését. 
   
    ![Automatikus pillanatfelvétel-készítési üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Kötetek ismételt vizsgálata
A következő eljárás használatával ellenőrizze újra a StorSimple Snapshot Manager csatlakoztatott kötet.

#### <a name="to-rescan-the-volumes"></a>A kötetek ismételt
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **kötetek**, és kattintson a **ismételt vizsgálat kötetek**.
   
    ![Kötetek ismételt vizsgálata](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ez az eljárás szinkronizálja a kötet lista a StorSimple Snapshot Managerrel. Módosításokat, például új kötetekre vagy törölt kötetek, az eredményeket fog szerepelni.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurálja és biztonsági mentése egyszerű kötet
A következő eljárás használatával egy egyszerű kötet biztonsági másolatának konfigurálásához és majd a biztonsági mentés azonnali elindítása vagy ütemezett biztonsági mentések szabályzat létrehozása.

### <a name="prerequisites"></a>Előfeltételek
Előkészületek:

* Győződjön meg arról, hogy a StorSimple eszköz- és fogadó számítógép megfelelően vannak-e konfigurálva. További információért ugorjon [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-u2.md).
* Telepítse és konfigurálja a StorSimple Snapshot Manager. További információért ugorjon [üzembe helyezése a StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Egyszerű kötet biztonsági másolatának konfigurálásához.
1. Hozzon létre egy alapszintű kötetet a StorSimple eszközön.
2. Csatlakoztassa, inicializálja és formázza a kötetet, leírtak szerint [kötetek csatlakoztatása](#mount-volumes). 
3. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager ablak. 
4. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** csomópontot, és válassza ki **ismételt vizsgálat kötetek**. Ha az ellenőrzés befejeződött, kötetek listáját meg kell jelennie a **eredmények** ablaktáblán. 
5. Az a **eredmények** panelen kattintson a jobb gombbal a kötetre, majd válassza ki **kötet-csoport létrehozása**. 
   
    ![Kötet-csoport létrehozása](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Az a **kötet-csoport létrehozása** párbeszédpanelen adja meg a kötet csoport nevét, kötetek hozzárendelése, és kattintson **OK**.
7. Az a **hatókör** ablaktáblán bontsa ki a **kötet csoportok** csomópont. Az új kötet csoport alatt meg kell jelennie a **kötet csoportok** csomópont. 
8. Kattintson a jobb gombbal a kötetre csoport nevét.
   
   * Interaktív (igény szerinti) biztonsági mentési feladat indításához kattintson **igénybe biztonsági mentési**. 
   * Az automatikus biztonsági mentés ütemezése, kattintson a **biztonsági mentési szabályzat létrehozása**. Az a **általános** lapra, jelölje be egy kötet csoportot a listából. Az a **ütemezés** lap, adja meg az ütemezés adatait. Ha elkészült, kattintson a **OK**. 
9. Győződjön meg arról, hogy a biztonsági mentési feladat elindult, bontsa ki a **feladatok** csomópontja a **hatókör** ablaktáblán, és kattintson a **futó** csomópont. A jelenleg futó feladatok listája jelenik meg a **eredmények** ablaktáblán. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurálja, és készítsen biztonsági másolatot a dinamikus tükrözött kötetek
Hajtsa végre az alábbi lépések végrehajtásával konfigurálhatja a dinamikus tükrözött kötetek biztonsági mentése:

* 1\. lépés: Használja a dinamikus tükrözött kötetek létrehozására. 
* 2\. lépés: A backup konfigurálása a StorSimple Snapshot Manager.

### <a name="prerequisites"></a>Előfeltételek
Előkészületek:

* Győződjön meg arról, hogy a StorSimple eszköz- és fogadó számítógép megfelelően vannak-e konfigurálva. További információért ugorjon [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).
* Telepítse és konfigurálja a StorSimple Snapshot Manager. További információért ugorjon [üzembe helyezése a StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Két kötet konfigurálása a StorSimple eszközön. (A példákban az elérhető kötetek vannak **lemez 1** és **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>1\. lépés: Hozzon létre egy dinamikus tükrözött kötetet a Lemezkezelés használatával
Lemezkezelés szolgál kezeléséhez, merevlemezek és a köteteket vagy partíciókat tartalmaz. Lemezkezelés kapcsolatos további információkért látogasson el [Lemezkezelés](https://technet.microsoft.com/library/cc770943.aspx) a Microsoft TechNet webhelyen.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>A dinamikus tükrözött kötetek létrehozása
1. Indítsa el a Lemezkezelés használja a következő beállítások bármelyikét: 
   
   * Nyissa meg a **futtatása** mezőbe írja be **Diskmgmt.msc**, és nyomja le az Enter billentyűt.
   * Indítsa el a Kiszolgálókezelőt, bontsa ki a **tárolási** csomópontot, és válassza ki **Lemezkezelés**. 
   * Indítsa el **felügyeleti eszközök**, bontsa ki a **számítógép-kezelés** csomópontot, és válassza ki **Lemezkezelés**. 
2. Győződjön meg arról, hogy rendelkezik-e két elérhető kötetek a StorSimple eszközön. (A példában az elérhető kötetek vannak **lemez 1** és **Disk 2**.) 
3. A jobb oldali oszlopban, kisebb ablaktábla, a Lemezkezelés ablakban kattintson a jobb gombbal **lemez 1** válassza **tükrözött új kötet**. 
   
    ![Új tükrözött kötetek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Az a **tükrözött új kötet** varázsló lapja, kattintson a **tovább**.
5. A a **lemezek kiválasztása** lapra, jelölje be **Disk 2** a a **kijelölt** ablaktáblán kattintson a **Hozzáadás**, és kattintson a **következő**. 
6. Az a **meghajtóbetűjel hozzárendelése vagy elérési út** lapon elfogadhatja az alapértelmezett beállításokat, és kattintson a **tovább**. 
7. Az a **kötet formázásának** lap a **lemezfoglalási egység mérete** jelölje ki **64K**. Válassza ki a **hajtson végre egy gyorsformázást** jelölőnégyzetet, majd kattintson a **tovább**. 
8. Az a **a tükrözött új kötet varázsló befejezése** lapon tekintse át a beállításokat, és kattintson a **Befejezés**. 
9. Megjelenik egy üzenet jelzi, hogy az alaplemez lesz átalakítva a dinamikus lemezeket. Kattintson a **Yes** (Igen) gombra.
   
    ![Dinamikus lemez átalakítás üzenet](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. A Lemezkezelés beépülő modulban győződjön meg arról, hogy a lemez 1. és 2 lemez látható dinamikus tükrözött kötetek. (**Dinamikus** meg kell jelennie az Állapot oszlopban, és a kapacitás sáv színe kell módosítani a piros, a tükrözött kötetek jelzi.) 
    
    ![Lemez tükrözött felügyeleti dinamikus lemezek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>2\. lépés: Használja a StorSimple Snapshot Manager biztonsági mentés konfigurálása
Az alábbi eljárás segítségével dinamikus tükrözött kötetek konfigurálni és majd a biztonsági mentés azonnali elindítása vagy ütemezett biztonsági mentések szabályzat létrehozása.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Konfigurálhatja a dinamikus tükrözött kötetek biztonsági mentése
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager ablak. 
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **kötetek** csomópontra, és válassza **ismételt vizsgálat kötetek**. Ha az ellenőrzés befejeződött, kötetek listáját meg kell jelennie a **eredmények** ablaktáblán. A dinamikus tükrözött kötetek szerepel egy kötetet. 
3. Az a **eredmények** ablaktáblán kattintson a jobb gombbal a dinamikus tükrözött kötetek, és kattintson **kötet-csoport létrehozása**. 
4. Az a **kötet-csoport létrehozása** párbeszédpanelen adja meg a kötet csoport nevét, a dinamikus tükrözött kötetek hozzárendelése ehhez a csoporthoz, és kattintson **OK**. 
5. Az a **hatókör** ablaktáblán bontsa ki a **kötet csoportok** csomópont. Az új kötet csoport alatt meg kell jelennie a **kötet csoportok** csomópont. 
6. Kattintson a jobb gombbal a kötetre csoport nevét. 
   
   * Interaktív (igény szerinti) biztonsági mentési feladat indításához kattintson **igénybe biztonsági mentési**. 
   * Az automatikus biztonsági mentés ütemezése, kattintson a **biztonsági mentési szabályzat létrehozása**. Az a **általános** lapon, válassza ki a kötetet a listából. Az a **ütemezés** lap, adja meg az ütemezés adatait. Ha elkészült, kattintson a **OK**. 
7. Figyelheti a biztonsági mentési feladat futása során. A a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontban, és kattintson **futtató**, a feladat részletei jelennek meg a **eredmények** ablaktáblán. Ha a biztonsági mentési feladat befejeződött, a részletek átkerülnek a **elmúlt 24** óra feladat listája. 

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [létrehozása és kezelése a StorSimple Snapshot Manager használata](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
