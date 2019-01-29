---
title: A StorSimple Snapshot Manager üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan töltse le és telepítse a StorSimple Snapshot Managerben, egy MMC beépülő modul StorSimple védelmi és biztonsági funkciók kezeléséhez.
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
ms.openlocfilehash: ee17e4b69d1e6c9de465e4241ee2237361e320b7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168005"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>A StorSimple Snapshot Manager MMC beépülő modul telepítése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Managert egy Microsoft Management Console (MMC) beépülő modulja, amely leegyszerűsíti az adatok védelme és a biztonságimásolat-felügyelet a Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Managerrel kezelheti a Microsoft Azure StorSimple a helyszíni és felhőbeli tárhelyén, mintha egy teljesen integrált tárolási rendszer, így leegyszerűsíti a biztonsági mentési és helyreállítási folyamatok, és csökkenti a költségeket. 

Ebben az oktatóanyagban konfigurációkra, valamint telepítése, eltávolítása és frissítése a StorSimple Snapshot Manager eljárásait ismerteti.

> [!NOTE]
> * A StorSimple Snapshot Manager kezelése a Microsoft Azure StorSimple Virtual Arrayt (más néven a StorSimple helyszíni virtuális eszköz) nem használható.
> * Ha azt tervezi, a StorSimple eszköz StorSimple Update 2 telepítése, ügyeljen arra, hogy a StorSimple Snapshot Manager legújabb verzió letöltése és telepítése azt **2. frissítés telepítése előtt**. A legújabb verzióra a StorSimple Snapshot Manager visszamenőlegesen kompatibilis és együttműködik a Microsoft Azure StorSimple kiadott verzióihoz. A StorSimple Snapshot Manager korábbi verziót használ, ha (nem kell az új verzió telepítése előtt, távolítsa el az előző verzió) frissíteni kell.


## <a name="storsimple-snapshot-manager-installation"></a>A StorSimple Snapshot Manager-telepítés
A StorSimple Snapshot Manager a Windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2 operációs rendszert futtató számítógépeken telepíthető. Windows 2008 R2-t futtató kiszolgálókon is telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0.

Mielőtt telepíteni vagy frissíteni a StorSimple Snapshot Manager beépülő modul a Microsoft Management Console (MMC), ellenőrizze, hogy a Microsoft Azure StorSimple eszköz és a gazdagép-kiszolgáló megfelelően vannak-e konfigurálva.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
Az alábbi lépéseket, hogy a StorSimple Snapshot Manager telepítése előtt végrehajtandó konfigurációs feladatok magas szintű áttekintést biztosítanak. Teljes Microsoft Azure StorSimple-konfiguráció és telepítés kapcsolódó információk, beleértve a rendszerkövetelmények és lépésenkénti útmutatójáért lásd [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Mielőtt elkezdené, tekintse át a [üzembehelyezési konfigurációs ellenőrzőlista](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) és és [üzembe helyezési Előfeltételek](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager telepítése előtt
1. Csomagolja ki, csatlakoztathatja és a Microsoft Azure StorSimple-eszköz csatlakoztatása, leírtak szerint [telepítse a StorSimple 8100 sorozatú eszköz](storsimple-8100-hardware-installation.md) vagy [telepítse a StorSimple 8600 sorozatú eszköz](storsimple-8600-hardware-installation.md).
2. Győződjön meg arról, hogy a gazdaszámítógépet az alábbi operációs rendszerek egyikét futtatja:
   
   * A Windows Server 2008 R2 (a kiszolgálók, Windows 2008 R2 fut, telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     A StorSimple virtuális eszköz a gazdagép egy Microsoft Azure virtuális gépen kell lennie.
3. Győződjön meg arról, hogy teljesülnek-e a Microsoft Azure StorSimple konfigurációkra. További információkért lépjen [üzembe helyezési Előfeltételek](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Az eszköz csatlakoztatása a gazdagép és a kezdeti konfigurálása. További információkért lépjen [üzembe helyezési lépések](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Hozzon létre köteteket az eszközön, hozzárendelheti azokat a gazdagépre, és győződjön meg arról, hogy a gazdagép csatlakoztathatja és azok használatát. A StorSimple Snapshot Manager támogatja a következő típusú kötetek:
   
   * Alapszintű kötetek
   * Egyszerű kötet
   * A dinamikus kötetek
   * Tükrözött dinamikus kötetek (RAID 1)
   * Fürt megosztott köteteihez
     
     A StorSimple-eszköz vagy a StorSimple virtuális eszköz a kötetek létrehozásával kapcsolatos információkért keresse [6. lépés: Hozzon létre egy kötetet](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume), a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Egy új StorSimple Snapshot Manager telepítése
A StorSimple Snapshot Manager telepítése előtt győződjön meg arról, hogy a kötetek a StorSimple-eszköz vagy a StorSimple virtuális eszköz létrehozott vannak csatlakoztatva, inicializálása és leírtak szerint formázott [Előfeltételek konfigurálása](#configure-prerequisites).

> [!IMPORTANT]
> * A StorSimple virtuális eszköz a gazdagép egy Microsoft Azure virtuális gépen kell lennie. 
> * A gazdagép Windows 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 futnia kell. Ha a kiszolgáló Windows Server 2008 R2 fut, is telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0.
> * Mielőtt az csatlakozna az eszközt a StorSimple Snapshot Manager konfigurálnia kell egy iSCSI-kapcsolat a gazdagép és a StorSimple-eszköz.

Kövesse az alábbi lépéseket egy friss, a StorSimple Snapshot Manager telepítés befejezéséhez. Ha a frissítés telepítésekor, lépjen a [frissítése, vagy telepítse újra a StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* 1. lépés: Install StorSimple Snapshot Manager 
* 2. lépés: A StorSimple Snapshot Manager csatlakozni egy eszközhöz 
* 3. lépés: Az eszközön a kapcsolat ellenőrzése 

### <a name="step-1-install-storsimple-snapshot-manager"></a>1. lépés: Install StorSimple Snapshot Manager
A következő lépések segítségével telepítse a StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager telepítéséhez
1. A StorSimple Snapshot Manager szoftver letöltéséhez (Ugrás a [a StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) a Microsoft Download Center), és mentse helyileg a gazdagépen.
2. A Fájlkezelőben kattintson a jobb gombbal a tömörített mappa, és kattintson **az összes kibontása**.
3. Az a **bontsa ki a tömörített (Zipped) mappák** ablakban, a a **ki kell választania a célhelyet és a fájlok kibontásához** mezőbe írja be vagy tallózással keresse meg az elérési utat, ahol szeretné kinyerni a fájl.
   
    > [!IMPORTANT]
    > Telepítenie kell a StorSimple Snapshot Manager a C: meghajtón található.
    
4. Válassza ki a **Show kibontott fájlokat, amikor a teljes** jelölőnégyzetet, majd kattintson a **kinyerése**.
   
    ![Bontsa ki a fájlok párbeszédpanel](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Ha elkészült a kinyerés, megnyílik a rendeltetési mappára. Kattintson duplán az alkalmazás a telepítő ikonjára a célmappa jelenik meg.
6. Ha a **beállítása sikeres** üzenet jelenik meg, kattintson a **Bezárás**. A StorSimple Snapshot Manager ikonra az asztalon megjelenik.
   
    ![asztali ikonra](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>2. lépés: A StorSimple Snapshot Manager csatlakozni egy eszközhöz
Használja az alábbi lépéseket a StorSimple eszköz StorSimple Snapshot Manager csatlakozni.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Egy eszköz csatlakozni a StorSimple Snapshot Manager
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. A StorSimple Snapshot Manager ablak. Az ablak tartalmaz egy **hatókör** ablaktáblán egy **eredmények** ablaktáblán, és a egy **műveletek** ablaktáblán. 
   
    ![StorSimple Snapshot Manager felhasználói felületén](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * A **hatókör** ablaktáblán (bal oldali ablaktábla) vannak rendszerezve fastruktúrában csomópontok listáját tartalmazza. Válasszon ki egy nézetet, vagy ahhoz a csomóponthoz kapcsolódó adatokat az egyes csomópontok bővítheti. Kattintson a nyíl ikonra kibontása vagy összecsukása egy csomópontot. Kattintson a jobb gombbal egy elemet a **hatókör** ablaktáblán egy, a cikk elérhető műveletek listájának megtekintéséhez.
   * A **eredmények** panelen (a középső ablaktábla) tartalmazza a csomópont, a nézet vagy a kiválasztott adatok részletes állapotadatait a **hatókör** ablaktáblán.
   * A **műveletek** panel felsorolja a műveleteket hajthat végre a csomóponton, a nézet vagy a kiválasztott adatokat a **hatókör** ablaktáblán.
     
     A StorSimple Snapshot Manager felhasználói felület teljes leírását lásd: [StorSimple Snapshot Manager felhasználói felületén](storsimple-use-snapshot-manager.md).
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal a **eszközök** csomópontban, és kattintson **eszközök konfigurálása**. A **eszközök konfigurálása** párbeszédpanel jelenik meg.
   
    ![Eszközök konfigurálása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Az a **eszköz** listán, válassza ki a Microsoft Azure StorSimple-eszköz vagy a virtuális eszköz IP-címét. Az a **jelszó** szöveg írja be a StorSimple Snapshot Manager jelszavát, amelyet az eszköz az Azure Portalon létrehozott. Kattintson az **OK** gombra.
4. A StorSimple Snapshot Manager keresi meg az eszközt, hogy meghatározta. Ha az eszköz érhető el, a StorSimple Snapshot Manager hozzáadja a kapcsolatot. Is [az eszközhöz való csatlakozás ellenőrzéséhez](#to-verify-the-connection) annak ellenőrzéséhez, hogy a kapcsolat hozzáadása sikerült.
   
    Ha az eszköz valamilyen okból elérhetetlenné válik, a StorSimple Snapshot Manager hibaüzenetet ad vissza. Kattintson a **OK** zárja be a hibaüzenetet, és kattintson a **Mégse** gombra kattintva zárja be a **eszközök konfigurálása** párbeszédpanel bezárásához.
5. Amikor egy eszköz csatlakozik, a StorSimple Snapshot Manager importálja minden kötet csoport van konfigurálva. az eszközön, feltéve, hogy a kötet csoporthoz társított biztonsági másolatok. Kötet csoportok, amelyek nem rendelkeznek társított biztonsági másolatok nem lesznek importálva. Emellett a biztonsági mentési mennyiségi csoport létrehozott házirendek nem lesznek importálva. Az importált csoportokat megtekintéséhez kattintson a jobb gombbal a felső – a legtöbb **kötet csoportok** csomópontja a **hatókör** panelre, és kattintson **importált csoportok átváltása**.

### <a name="step-3-verify-the-connection-to-the-device"></a>3. lépés: Az eszközön a kapcsolat ellenőrzése
Az alábbi lépések segítségével győződjön meg arról, hogy a StorSimple Snapshot Manager a StorSimple-eszközhöz van csatlakoztatva.

#### <a name="to-verify-the-connection"></a>A kapcsolat ellenőrzése
1. Az a **hatókör** ablaktáblán kattintson a **eszközök** csomópont.
   
    ![A StorSimple Snapshot Manager eszköz állapota](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Ellenőrizze a **eredmények** panelen: 
   
   * Ha az eszköz ikonját megjelenik egy zöld jelző és **elérhető** jelenik meg a **állapot** oszlopot, majd az eszköz csatlakoztatva van. 
   * Ha az eszköz ikonja egy piros jelző jelenik meg, és nem érhető el megjelenik a **állapot** oszlopot, majd az eszköz nem kapcsolódik. 
   * Ha **frissítéssel** jelenik meg a **állapot** oszlopot, majd a StorSimple Snapshot Manager kötet csoportok és csatlakoztatott eszközhöz társított biztonsági másolatok beolvasása.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Frissítés, vagy telepítse újra a StorSimple Snapshot Managerrel
Előtt távolítsa el a StorSimple Snapshot Manager teljesen frissítése, vagy telepítse újra az alkalmazást. 

A StorSimple Snapshot Manager újratelepítése, előtt készítsen biztonsági másolatot a meglévő StorSimple Snapshot Manager-adatbázis a gazdagépen. Ezzel a biztonsági mentési szabályzatok és a konfigurációs adatok takaríthat meg, hogy könnyedén visszaállíthatók az adatok biztonsági másolatból.

Ha frissíteni vagy újratelepíteni a StorSimple Snapshot Manager kövesse az alábbi lépéseket:

* 1. lépés: Távolítsa el a StorSimple Snapshot Managerrel 
* 2. lépés: A StorSimple Snapshot Manager-adatbázis biztonsági mentése 
* 3. lépés: Telepítse újra a StorSimple Snapshot Managerben, és állítsa vissza az adatbázist 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>1. lépés: Távolítsa el a StorSimple Snapshot Managerrel
Az alábbi lépések segítségével távolítsa el a StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager eltávolítása
1. Az állomáson, nyissa meg a **Vezérlőpult**, kattintson a **programok**, és kattintson a **programok és szolgáltatások**.
2. A bal oldali ablaktáblán kattintson a **eltávolítása vagy módosítása egy program**.
3. Kattintson a jobb gombbal **a StorSimple Snapshot Manager**, és kattintson a **Eltávolítás**.
4. Ekkor elindul a StorSimple Snapshot Manager telepítő programját. Kattintson a **telepítés módosítása**, és kattintson a **Eltávolítás**.
   
   > [!NOTE]
   > Ha a háttérben futó MMC folyamatok, például a StorSimple Snapshot Manager vagy a Lemezkezelés eszközben, az eltávolítás sikertelen lesz, és megjelenik egy üzenet gombra kattintva zárja be az MMC összes példányát, mielőtt megkísérli a program eltávolítása. Válassza ki **automatikusan zárja be az alkalmazásokat, és próbálja meg újraindítani őket a telepítés befejezése után**, és kattintson a **OK**.
   > 
   > 
5. Az eltávolítási folyamat befejezését követően egy **beállítása sikeres** üzenet jelenik meg. Kattintson a **Bezárás** gombra.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>2. lépés: A StorSimple Snapshot Manager-adatbázis biztonsági mentése
A következő lépések segítségével hozzon létre és mentsen a StorSimple Snapshot Manager-adatbázis másolatát.

#### <a name="to-back-up-the-database"></a>Az adatbázis biztonsági mentése
1. A Microsoft StorSimple felügyeleti szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   3. Az a **szolgáltatások** lapon jelölje be **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
      
        ![A StorSimple-Eszközkezelő szolgáltatás leállítása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Keresse meg C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData mappa rejtett.
  
3. Keresse meg a katalógus XML-fájlt, másolja a fájlt, és a másolat tárolása egy biztonságos helyre, vagy a felhőben.
   
    ![A StorSimple biztonsági mentési katalógusfájl](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. A Microsoft StorSimple felügyeleti szolgáltatás újraindítása: 
   
   1. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   2. Az a **szolgáltatások** lapon válassza ki a **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>3. lépés: Telepítse újra a StorSimple Snapshot Managerben, és állítsa vissza az adatbázist
Telepítse újra a StorSimple Snapshot Managerben, kövesse a [telepítése egy új StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Ezután az alábbi eljárás segítségével a StorSimple Snapshot Manager-adatbázis visszaállítása.

#### <a name="to-restore-the-database"></a>Az adatbázis visszaállítása
1. A Microsoft StorSimple felügyeleti szolgáltatás leállításához:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   3. Az a **szolgáltatások** lapon jelölje be **Microsoft StorSimple szolgáltatás**.
   4. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **állítsa le a szolgáltatást**.
2. Keresse meg C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > ProgramData mappa rejtett.
   > 
   > 
3. Törölje a katalógus XML-fájlt, és cserélje le a korábban mentett verzióra.
4. A Microsoft StorSimple felügyeleti szolgáltatás újraindítása: 
   
   1. A Kiszolgálókezelő irányítópultján található a **eszközök** menüjében válassza **szolgáltatások**.
   2. Az a **szolgáltatások** lapon jelölje be **Microsoft StorSimple szolgáltatás**.
   3. A jobb oldali ablaktáblában alatt **Microsoft StorSimple szolgáltatás**, kattintson a **indítsa újra a szolgáltatást**.

## <a name="next-steps"></a>További lépések
* A StorSimple Snapshot Managerrel kapcsolatos további tudnivalókért keresse fel [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* További információ a StorSimple Snapshot Manager felhasználói felület, lépjen a [StorSimple Snapshot Manager felhasználói felületén](storsimple-use-snapshot-manager.md).
* A StorSimple Snapshot Managerrel kapcsolatos további információkért lépjen [használata a StorSimple Snapshot Manager felügyelete a StorSimple-megoldásokra](storsimple-snapshot-manager-admin.md).

