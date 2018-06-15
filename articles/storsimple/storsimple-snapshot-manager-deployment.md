---
title: A StorSimple Snapshot Manager központi telepítése |} Microsoft Docs
description: Megtudhatja, hogyan töltse le és telepítse a StorSimple Snapshot Manager MMC beépülő modulként StorSimple adatok védelmét és biztonsági mentését szolgáltatások kezeléséhez.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876319"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>A StorSimple Snapshot Manager MMC beépülő modul telepítése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja, amely leegyszerűsíti az adatvédelem és biztonsági mentési kezelése a Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager kezelheti a Microsoft Azure StorSimple a helyszíni és felhőbeli tárhelyén, mintha egy teljesen integrált tárolórendszer, így biztonsági mentési és helyreállítási folyamatok egyszerűsítése, és csökkenti a költségeket. 

Ez az oktatóanyag konfigurációs követelményeinek, valamint telepítése, eltávolítása és frissítése a StorSimple Snapshot Manager eljárásait ismerteti.

> [!NOTE]
> * StorSimple Snapshot Manager kezelése a Microsoft Azure StorSimple virtuális tömbök (más néven StorSimple a helyszíni virtuális eszköz) nem használható.
> * Ha a StorSimple eszköz StorSimple 2. frissítés telepítését tervezi, ügyeljen arra, hogy a StorSimple Snapshot Manager legújabb verziójának letöltéséhez és telepítéséhez **StorSimple 2. frissítés telepítése előtt**. A StorSimple Snapshot Manager legújabb verziójára visszamenőlegesen kompatibilis, és együttműködik a Microsoft Azure StorSimple kiadott verziói. A StorSimple Snapshot Manager korábbi verzióját használja, ha szüksége lesz a frissítést (nem kell eltávolítani a korábbi verzióját, az új verzió telepítése előtt).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager telepítése
StorSimple Snapshot Manager a Windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2 operációs rendszert futtató számítógépeken telepíthető. Windows 2008 R2 rendszert futtató kiszolgálókon is telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0.

Ahhoz, hogy telepíteni vagy frissíteni a StorSimple Snapshot Manager beépülő modul a Microsoft Management Console (MMC), győződjön meg arról, hogy a Microsoft Azure StorSimple eszköz és a gazdagép server helyesen vannak-e konfigurálva.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
Az alábbi lépések nyújtanak a konfigurációs feladatok magas szintű áttekintését, hogy el kell végeznie a StorSimple Snapshot Manager telepítése előtt. A Microsoft Azure StorSimple konfigurációs és telepítési információk, beleértve a telepítési rendszerkövetelményeket és a részletes útmutatást lásd [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Mielőtt elkezdené, tekintse át a [üzembehelyezési konfigurációs ellenőrzőlista](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) és és [telepítésének előfeltételei](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager telepítése előtt
1. Csomagolja ki, csatlakoztathatják és csatlakozás a Microsoft Azure StorSimple eszközön a [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md) vagy [a StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md).
2. Győződjön meg arról, hogy a gazdaszámítógép a következő operációs rendszerek egyikét futtatja:
   
   * Windows Server 2008 R2 (a kiszolgálók Windows 2008 R2 fut, telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     A StorSimple virtuális eszköz a gazdagép egy Microsoft Azure virtuális gépen kell lennie.
3. Győződjön meg arról, hogy teljesülnek-e a Microsoft Azure StorSimple konfigurációs követelményeinek. További információkért lépjen [telepítésének előfeltételei](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Csatlakoztassa az eszközt a gazdagép és a kezdeti konfigurálása. További információkért lépjen [az üzembe helyezés lépései](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Hozzon létre köteteket az eszközön, rendelje őket a gazdagéphez, és győződjön meg arról, hogy a gazdagép csatlakoztathatja és használhatja őket. StorSimple Snapshot Manager a következő kötetek-típusokat támogatja:
   
   * Alapszintű kötetek
   * Egyszerű kötetek
   * A dinamikus kötetek
   * Tükrözött dinamikus kötetek (RAID 1)
   * Fürt megosztott kötetei
     
     A StorSimple eszköz vagy a StorSimple virtuális eszköz a kötetek létrehozásával kapcsolatos információkért látogasson el [6. lépés: kötet létrehozása](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume), a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Egy új StorSimple Snapshot Manager telepítése
StorSimple Snapshot Manager telepítése előtt győződjön meg arról, hogy a kötetek létrehozta a StorSimple eszköz vagy a StorSimple virtuális eszköz csatlakoztatása, inicializálása, és leírtaknak megfelelően formázott [Előfeltételek konfigurálása](#configure-prerequisites).

> [!IMPORTANT]
> * A StorSimple virtuális eszköz a gazdagép egy Microsoft Azure virtuális gépen kell lennie. 
> * A gazdagép Windows 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 futnia kell. Ha a Windows Server 2008 R2 rendszer fut, telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0.
> * Egy iSCSI-kapcsolat a gazdagép és a StorSimple eszköz be kell állítania, mielőtt az csatlakozna az eszköz StorSimple Snapshot Manager.

Kövesse az alábbi lépéseket a StorSimple Snapshot Manager új telepítést végrehajtani. Amennyiben a frissítés telepítésekor [frissítéséhez, vagy telepítse újra a StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* 1. lépés: A StorSimple Snapshot Manager telepítése 
* 2. lépés: Csatlakozás a StorSimple Snapshot Manager eszköz 
* 3. lépés: Az eszköz való kapcsolódás ellenőrzése. 

### <a name="step-1-install-storsimple-snapshot-manager"></a>1. lépés: A StorSimple Snapshot Manager telepítése
Az alábbi lépések segítségével StorSimple Snapshot Manager telepítéséhez.

#### <a name="to-install-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager telepítése
1. A StorSimple Snapshot Manager szoftver letöltése (Ugrás [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) a Microsoft Download Center), és mentse helyileg a gazdagépen.
2. A Fájlkezelőben kattintson a jobb gombbal a tömörített mappa, és kattintson **az összes kibontása**.
3. Az a **bontsa ki a tömörített (Zipped) mappák** ablakban, a a **válassza ki a célhelyet, és bontsa ki a fájlt** mezőbe írja be vagy tallózással keresse meg az elérési út, hová szeretné kinyerni fájlt.
   
    > [!IMPORTANT]
    > StorSimple Snapshot Manager kell telepítenie a C: meghajtón.
    
4. Válassza ki a **megjelenítése kibontott fájlokat, ha a teljes** jelölőnégyzetet, majd kattintson a **kibontása**.
   
    ![Bontsa ki a fájlok párbeszédpanel](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Amikor befejeződött az kibontásával, a célmappa megnyitása. Kattintson duplán az alkalmazást telepítő ikonra a rendeltetési mappára.
6. Ha a **beállítása sikeres** üzenet jelenik meg, kattintson a **Bezárás**. A StorSimple Snapshot Manager ikonra az asztalon megjelenik.
   
    ![asztali ikon](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>2. lépés: Csatlakozás a StorSimple Snapshot Manager eszköz
Az alábbi lépések segítségével a StorSimple eszköz StorSimple Snapshot Manager csatlakozni.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Egy eszköz StorSimple Snapshot Manager kapcsolódni
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager ablak. Az ablak tartalmaz egy **hatókör** ablaktáblán egy **eredmények** ablaktáblán, és egy **műveletek** ablaktáblán. 
   
    ![StorSimple Snapshot Manager felhasználói felülete](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * A **hatókör** ablaktáblán (bal oldali ablaktábla) vannak rendszerezve fastruktúrában csomópontok listáját tartalmazza. Válasszon egy nézetet, vagy ahhoz a csomóponthoz kapcsolódó adatokat az egyes csomópontok bővítheti. Kattintson a nyíl ikonra kibontása vagy összecsukása egy csomópont. Kattintson a jobb gombbal egy elemére a **hatókör** ablaktáblán, a cikk elérhető műveletek listájának megtekintéséhez.
   * A **eredmények** ablaktábláján (középső ablaktábla) tartalmazza a csomópontot, a nézet vagy a kiválasztott adatok vonatkozó részletes állapotadatokat a **hatókör** ablaktáblán.
   * A **műveletek** ablaktábla listázza a műveleteket hajthat végre a csomópontra, a nézet vagy a kiválasztott adatokat a **hatókör** ablaktáblán.
     
     A StorSimple Snapshot Manager felhasználói felület teljes körű ismertetését lásd: [StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md).
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **eszközök** csomópontra, majd **eszköz konfigurálása**. A **eszköz konfigurálása** párbeszédpanel jelenik meg.
   
    ![Egy eszköz konfigurálása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Az a **eszköz** listán, válassza ki a Microsoft Azure StorSimple eszköz vagy a virtuális eszköz IP-címét. Az a **jelszó** szöveg mezőbe írja be a StorSimple Snapshot Manager jelszavát, amelyet az eszköz az Azure-portálon létrehozott. Kattintson az **OK** gombra.
4. StorSimple Snapshot Manager megkeresi az azonosított eszközre. Ha az eszköz nem érhető el, a StorSimple Snapshot Manager hozzáadja a kapcsolat. Is [ellenőrizni a kapcsolatot az eszköz](#to-verify-the-connection) annak ellenőrzéséhez, hogy a kapcsolat felvétele sikeresen megtörtént.
   
    Ha az eszköz a bármilyen okból nem érhető el, a StorSimple Snapshot Manager hibaüzenetet ad vissza. Kattintson a **OK** zárja be a hibaüzenetet, és kattintson **Mégse** bezárásához a **eszköz konfigurálása** párbeszédpanel megnyitásához.
5. Amikor az eszköz csatlakozik, a StorSimple Snapshot Manager importálja minden kötet csoport van konfigurálva. az eszköznek, feltéve, hogy a kötet csoporthoz társított biztonsági másolatok. Kötet csoportok, amelyek nem rendelkeznek társított biztonsági másolatok nincsenek importálva. Emellett a kötet csoport létrehozott biztonsági mentési házirendek nincsenek importálva. Az importált csoportok megtekintéséhez kattintson a jobb gombbal a childwindow- **kötet csoportok** csomópontja a **hatókör** ablaktáblán, majd kattintson **importált csoportok átváltása**.

### <a name="step-3-verify-the-connection-to-the-device"></a>3. lépés: Az eszköz való kapcsolódás ellenőrzése.
Az alábbi lépések segítségével győződjön meg arról, hogy a StorSimple Snapshot Manager a StorSimple eszköz csatlakozik-e.

#### <a name="to-verify-the-connection"></a>A kapcsolat ellenőrzése
1. Az a **hatókör** ablaktáblán kattintson a **eszközök** csomópont.
   
    ![StorSimple Snapshot Manager eszköz állapota](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Ellenőrizze a **eredmények** panelen: 
   
   * Ha az eszköz ikonja megjelenik egy zöld jelző és **elérhető** jelenik meg a **állapot** oszlopban, majd az eszköz csatlakoztatva van. 
   * Ha egy piros jelző jelenik meg az eszköz ikonjára, és nem érhető el megjelenik a **állapot** oszlopban, majd az eszköz nincs csatlakoztatva. 
   * Ha **frissítés** jelenik meg a **állapot** oszlopban, majd a StorSimple Snapshot Manager beolvassa a kötet csoportok és a csatlakoztatott eszközhöz társított biztonsági másolatok.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Frissítés, vagy telepítse újra a StorSimple Snapshot Manager
Távolítsa el a StorSimple Snapshot Manager teljesen előtt frissítse, vagy telepítse újra az alkalmazást. 

StorSimple Snapshot Manager újratelepítése, előtt készítsen biztonsági másolatot a meglévő StorSimple Snapshot Manager-adatbázis az állomáson. Ez menti a biztonsági mentési házirendek és konfigurációs információt, hogy visszaállíthatja az adatokat a biztonsági másolatból.

Ha frissíteni vagy újratelepíteni a StorSimple Snapshot Manager kövesse az alábbi lépéseket:

* 1. lépés: Távolítsa el a StorSimple Snapshot Manager 
* 2. lépés: A StorSimple Snapshot Manager-adatbázis biztonsági mentése 
* 3. lépés: Telepítse újra a StorSimple Snapshot Manager, és állítsa vissza az adatbázist 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>1. lépés: Távolítsa el a StorSimple Snapshot Manager
Az alábbi lépések segítségével távolítsa el a StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager eltávolítása
1. Az állomáson, nyissa meg a **Vezérlőpult**, kattintson a **programok**, és kattintson a **programok és szolgáltatások**.
2. Kattintson a bal oldali ablaktáblában **eltávolítása vagy módosítása egy program**.
3. Kattintson a jobb gombbal **StorSimple Snapshot Manager**, és kattintson a **Eltávolítás**.
4. Ekkor elindul a StorSimple Snapshot Manager telepítő programját. Kattintson a **módosítása telepítő**, és kattintson a **Eltávolítás**.
   
   > [!NOTE]
   > Ha a háttérben futó MMC folyamatok, például a StorSimple Snapshot Manager vagy a Lemezkezelés eszközben, az eltávolítás sikertelen lesz, és zárja be az MMC előtt távolítsa el a program üzenetet fog kapni. Válassza ki **automatikusan zárja be az alkalmazásokat, és újraindításuk megkísérlése telepítés befejezése után**, és kattintson a **OK**.
   > 
   > 
5. Ha az eltávolítási folyamat befejeződött, egy **beállítása sikeres** üzenet jelenik meg. Kattintson a **Bezárás** gombra.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>2. lépés: A StorSimple Snapshot Manager-adatbázis biztonsági mentése
Az alábbi lépések segítségével létrehozza és menti a StorSimple Snapshot Manager adatbázisát.

#### <a name="to-back-up-the-database"></a>Az adatbázis biztonsági mentése
1. A Microsoft a StorSimple szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   3. Az a **szolgáltatások** lapon jelölje be **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
      
        ![A StorSimple Device Manager szolgáltatás leállítása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Tallózással keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData mappa rejtett.
  
3. A katalógus XML-fájl található, másolja a fájlt, és a példány tárolja biztonságos helyen, vagy a felhőben.
   
    ![A StorSimple biztonsági mentési katalógusfájl](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Indítsa újra a Microsoft a StorSimple szolgáltatás: 
   
   1. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   2. Az a **szolgáltatások** lapon jelölje be a **Microsoft StorSimple felügyeleti szolgáltatás**e.
   3. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>3. lépés: Telepítse újra a StorSimple Snapshot Manager, és állítsa vissza az adatbázist
StorSimple Snapshot Manager újratelepítéséhez kövesse [telepítése egy új StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Ezt követően a következő eljárással a StorSimple Snapshot Manager-adatbázis helyreállítására.

#### <a name="to-restore-the-database"></a>Az adatbázis visszaállítása
1. A Microsoft a StorSimple szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   3. Az a **szolgáltatások** lapon jelölje be **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
2. Tallózással keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData mappa rejtett.
   > 
   > 
3. Törölje a katalógus XML-fájlt, és cserélje le a korábban mentett verziót.
4. Indítsa újra a Microsoft a StorSimple szolgáltatás: 
   
   1. A Kiszolgálókezelő irányítópultján a a **eszközök** menü **szolgáltatások**.
   2. Az a **szolgáltatások** lapon jelölje be **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában a **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**.

## <a name="next-steps"></a>Következő lépések
* További információ a StorSimple Snapshot Manager Ugrás [Mi az StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* A StorSimple Snapshot Manager felhasználói felület kapcsolatos további tudnivalókért keresse fel [StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md).
* StorSimple Snapshot Manager használatával kapcsolatos további tudnivalókért keresse fel [használja a StorSimple Snapshot Manager felügyelete a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).

