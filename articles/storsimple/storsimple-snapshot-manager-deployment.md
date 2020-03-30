---
title: StorSimple Snapshot Manager telepítése | Microsoft dokumentumok
description: Ismerje meg, hogyan töltheti le és telepítheti a StorSimple Snapshot Manager t, egy MMC beépülő modult a StorSimple adatvédelmi és biztonsági mentési funkcióinak kezeléséhez.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 6d3e3d6cdf7a831bf09d9c4709c1a60d27683438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933385"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>A StorSimple Snapshot Manager MMC beépülő modul telepítése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely leegyszerűsíti az adatvédelmet és a biztonsági mentés kezelését egy Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével úgy kezelheti a helyszíni És felhőalapú Microsoft Azure StorSimple szolgáltatást, mintha teljesen integrált tárolórendszer lenne, így egyszerűsítve a biztonsági mentési és visszaállítási folyamatokat és csökkentve a költségeket. 

Ez az oktatóanyag ismerteti a konfigurációs követelményeket, valamint a StorSimple Snapshot Manager telepítésének, eltávolításának és frissítésének eljárásait.

> [!NOTE]
> * A StorSimple Snapshot Manager nem használható a Microsoft Azure StorSimple virtuális tömbök (más néven helyszíni StorSimple virtuális eszközök) kezelésére.
> * Ha a StorSimple Update 2-t a StorSimple eszközre kívánja telepíteni, a StorSimple Snapshot Manager legújabb verzióját töltse le, és telepítse azt **a StorSimple Update 2 telepítése előtt.** A StorSimple Snapshot Manager legújabb verziója visszamenőlegesen kompatibilis, és a Microsoft Azure StorSimple összes kiadott verziójával működik. Ha a StorSimple Snapshot Manager korábbi verzióját használja, frissítenie kell azt (az új verzió telepítése előtt nem kell eltávolítania az előző verziót).


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager telepítése
A StorSimple Snapshot Manager windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2 operációs rendszert futtató számítógépekre telepíthető. Windows 2008 R2 rendszert futtató kiszolgálókon telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0 szervizcsomagot is.

A Microsoft Management Console (MMC) StorSimple Snapshot Manager beépülő moduljának telepítése vagy frissítése előtt győződjön meg arról, hogy a Microsoft Azure StorSimple-eszköz és a gazdakiszolgáló megfelelően van konfigurálva.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
A következő lépések magas szintű áttekintést nyújtanak a StorSimple Snapshot Manager telepítése előtt elvégezhető konfigurációs feladatokról. A Microsoft Azure StorSimple konfigurációs és beállítási információiról, beleértve a rendszerkövetelményeket és a részletes utasításokat, olvassa [el a Helyszíni StorSimple-eszköz telepítése című témakört.](storsimple-8000-deployment-walkthrough-u2.md)

> [!IMPORTANT]
> Mielőtt elkezdené, tekintse át a [központi telepítés konfigurációs ellenőrzőlistát](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) és [a központi telepítés előfeltételeit](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) [a helyszíni StorSimple-eszköz központi telepítésében.](storsimple-8000-deployment-walkthrough-u2.md)
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager telepítése előtt
1. Csomagolja ki, csatlakoztassa és csatlakoztassa a Microsoft Azure StorSimple eszközt [a StorSimple 8100 eszköz telepítése](storsimple-8100-hardware-installation.md) vagy a [StorSimple 8600 eszköz telepítése](storsimple-8600-hardware-installation.md)című témakörben leírtak szerint.
2. Győződjön meg arról, hogy a gazdaszámítógép az alábbi operációs rendszerek valamelyikét futtatja:
   
   * Windows Server 2008 R2 (Windows 2008 R2 rendszert futtató kiszolgálókon telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0 rendszert is)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     A StorSimple virtuális eszköz, a gazdagép kell egy Microsoft Azure virtuális gép.
3. Győződjön meg arról, hogy megfelel a Microsoft Azure StorSimple összes konfigurációs követelményeinek. A részleteket az [Üzembe helyezés ifeltételeinek](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)című területen találja.
4. Csatlakoztassa az eszközt az állomáshoz, és végezze el a kezdeti konfigurációt. A részleteket a [Telepítési lépések területen találja.](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)
5. Hozzon létre köteteket az eszközön, rendelje hozzá őket a gazdagéphez, és ellenőrizze, hogy az állomás csatlakoztathatja és használhatja-e őket. A StorSimple Snapshot Manager a következő típusú köteteket támogatja:
   
   * Alapkötetek
   * Egyszerű kötetek
   * Dinamikus kötetek
   * Tükrözött dinamikus kötetek (RAID 1)
   * Fürtmegosztott kötetek
     
     A StorSimple-eszközön vagy a StorSimple virtuális eszközön lévő kötetek létrehozásáról [Deploy your on-premises StorSimple device](storsimple-8000-deployment-walkthrough-u2.md)a [6.](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)

## <a name="install-a-new-storsimple-snapshot-manager"></a>Új StorSimple Snapshot Manager telepítése
A StorSimple Snapshot Manager telepítése előtt győződjön meg arról, hogy a StorSimple eszközön vagy a StorSimple virtuális eszközön létrehozott kötetek csatlakoztatva, inicializálva és formázva vannak az [Előfeltételek konfigurálása](#configure-prerequisites)című témakörben leírtak szerint.

> [!IMPORTANT]
> * A StorSimple virtuális eszköz, a gazdagép kell egy Microsoft Azure virtuális gép. 
> * Az állomásnak Windows 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 rendszert kell futtatnia. Ha a kiszolgálón Windows Server 2008 R2 fut, telepítenie kell a Windows Server 2008 SP1 és a Windows Management Framework 3.0 szervizcsomagot is.
> * Az eszköz StorSimple Snapshot Managerhez való csatlakoztatása előtt konfigurálnia kell egy iSCSI-kapcsolatot az állomástól a StorSimple-eszközhöz.

Kövesse az alábbi lépéseket a StorSimple Snapshot Manager friss telepítésének végrehajtásához. Ha frissítést telepít, nyissa meg [a Frissítés vagy újratelepítés StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager)című témakört.

* 1. lépés: StorSimple Snapshot Manager telepítése 
* 2. lépés: A StorSimple Snapshot Manager csatlakoztatása egy eszközhöz 
* 3. lépés: Ellenőrizze a kapcsolatot a készülék 

### <a name="step-1-install-storsimple-snapshot-manager"></a>1. lépés: StorSimple Snapshot Manager telepítése
A StorSimple Snapshot Manager telepítéséhez kövesse az alábbi lépéseket.

#### <a name="to-install-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager telepítése
1. Töltse le a StorSimple Snapshot Manager szoftvert (látogasson el a Microsoft letöltőközpontjában található [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) webhelyre), és mentse azt helyileg a gazdagépen.
2. A Fájlkezelőben kattintson a jobb gombbal a tömörített mappára, majd kattintson **az Összes kibontása parancsra.**
3. A **Tömörített mappák kibontása (tömörített)** ablakban a **Cél kiválasztása és a fájlok kibontása** mezőbe írja be vagy keresse meg azt az elérési utat, amelyen a fájlt ki szeretné bontani.
   
    > [!IMPORTANT]
    > Telepítenie kell a StorSimple Snapshot Manager-t a C: meghajtóra.
    
4. Jelölje be a **Kibontott fájlok megjelenítése teljes befejezéskor jelölőnégyzetet,** majd kattintson a **Kibontás gombra.**
   
    ![Fájlok kibontása párbeszédpanel](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Amikor a kibontás befejeződött, megnyílik a célmappa. Kattintson duplán a célmappában megjelenő alkalmazásbeállítási ikonra.
6. Amikor megjelenik a **Sikeres beállítás** üzenet, kattintson a **Bezárás**gombra. Meg kell jelennie a StorSimple Snapshot Manager ikonaz asztalon.
   
    ![asztal ikon](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>2. lépés: A StorSimple Snapshot Manager csatlakoztatása egy eszközhöz
A következő lépésekkel csatlakoztassa a StorSimple Snapshot Managert egy StorSimple-eszközhöz.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>A StorSimple Snapshot Manager csatlakoztatása eszközhöz
1. Kattintson a StorSimple Snapshot Manager ikonra az asztalon. Megjelenik a StorSimple Snapshot Manager ablak. Az ablak tartalmaz egy **Hatókör** ablaktáblát, egy **Eredmények** ablaktáblát és egy **Műveletek** ablaktáblát. 
   
    ![StorSimple Snapshot Manager felhasználói felület](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * A **Hatókör** ablaktábla (a bal oldali ablaktábla) a fastruktúrába rendezett csomópontok listáját tartalmazza. Egyes csomópontok kibontásával kijelölhet egy nézetet vagy az adott csomóponthoz kapcsolódó konkrét adatokat. Kattintson a nyíl ikonra egy csomópont kibontásához vagy összecsukásához. Kattintson a jobb gombbal egy elemre a **Hatókör** ablaktáblában az adott elemhez elérhető műveletek listájának megtekintéséhez.
   * Az **Eredmények** ablaktábla (a középső ablaktábla) részletes állapotinformációkat tartalmaz a **Hatókör** ablaktáblában kijelölt csomópontról, nézetről vagy adatokról.
   * A **Műveletek** ablaktábla felsorolja azokat a műveleteket, amelyeket a **Hatókör** ablaktáblán kijelölt csomóponton, nézetben vagy adaton hajthat végre.
     
     A StorSimple Snapshot Manager felhasználói felületének teljes leírását a [StorSimple Snapshot Manager felhasználói felülete tartalmazza.](storsimple-use-snapshot-manager.md)
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal az **Eszközök** csomópontra, majd kattintson **az Eszköz konfigurálása parancsra.** Megjelenik **az Eszköz konfigurálása** párbeszédpanel.
   
    ![Eszköz konfigurálása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. Az **Eszköz** listában válassza ki a Microsoft Azure StorSimple eszköz vagy virtuális eszköz IP-címét. A **Jelszó** mezőbe írja be a StorSimple Snapshot Manager jelszót, amelyet az eszközhöz az Azure Portalon hozott létre. Kattintson az **OK** gombra.
4. A StorSimple Snapshot Manager megkeresi az azonosított eszközt. Ha az eszköz elérhető, a StorSimple Snapshot Manager hozzáad egy kapcsolatot. [Ellenőrizheti az eszközhöz való csatlakozást,](#to-verify-the-connection) és ellenőrizheti, hogy a kapcsolat sikeresen hozzá lett-e adva.
   
    Ha az eszköz bármilyen okból nem érhető el, a StorSimple Snapshot Manager hibaüzenetet ad vissza. A hibaüzenet bezárásához kattintson az **OK** gombra, majd a **Mégse gombra** az **Eszköz konfigurálása** párbeszédpanel bezárásához.
5. Amikor csatlakozik egy eszközhöz, a StorSimple Snapshot Manager importálja az adott eszközhöz konfigurált egyes kötetcsoportokat, feltéve, hogy a kötetcsoport hoz hozzá tartozó biztonsági másolatokat. A társított biztonsági másolattal nem rendelkező kötetcsoportok importálása nem történik meg. Ezenkívül a kötetcsoporthoz létrehozott biztonsági mentési házirendek importálása nem történik meg. Az importált csoportok megtekintéséhez kattintson a jobb gombbal a **Hatókör** ablaktábla legfelső **számú kötetcsoportok** csomópontjára, majd kattintson az **Importált csoportok váltása parancsra.**

### <a name="step-3-verify-the-connection-to-the-device"></a>3. lépés: Ellenőrizze a kapcsolatot a készülék
Az alábbi lépésekkel ellenőrizze, hogy a StorSimple Snapshot Manager csatlakozik-e a StorSimple-eszközhöz.

#### <a name="to-verify-the-connection"></a>A kapcsolat ellenőrzése
1. A **Hatókör** ablaktáblán kattintson az **Eszközök** csomópontra.
   
    ![StorSimple Snapshot Manager eszköz állapota](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Ellenőrizze az **Eredmények** ablaktáblát: 
   
   * Ha egy zöld jelző jelenik meg az eszköz ikonján, és az **Elérhető** az **Állapot** oszlopban, akkor az eszköz csatlakoztatva van. 
   * Ha egy piros jelző jelenik meg az eszköz ikonján, és a Nem elérhető elem jelenik meg az **Állapot** oszlopban, akkor az eszköz nincs csatlakoztatva. 
   * Ha **a frissítés** megjelenik az **Állapot** oszlopban, akkor a StorSimple Snapshot Manager kötetcsoportokat és a kapcsolódó biztonsági másolatokat kér be egy csatlakoztatott eszközhöz.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Frissítés vagy újratelepítés a StorSimple Snapshot Manager ben
A szoftver frissítése vagy újratelepítése előtt teljesen el kell távolítania a StorSimple Snapshot Managert. 

A StorSimple Snapshot Manager újratelepítése előtt biztonsági másolatot kell létrehoznia a meglévő StorSimple Snapshot Manager-adatbázisról a gazdaszámítógépen. Ezzel menti a biztonsági mentési házirendeket és a konfigurációs információkat, így könnyen visszaállíthatja ezeket az adatokat a biztonsági mentésből.

A StorSimple Snapshot Manager frissítésekor vagy újratelepítésekor kövesse az alábbi lépéseket:

* 1. lépés: StorSimple Snapshot Manager eltávolítása 
* 2. lépés: A StorSimple Snapshot Manager adatbázis ának biztonsági mentése 
* 3. lépés: Telepítse újra a StorSimple Snapshot Managert, és állítsa vissza az adatbázist 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>1. lépés: StorSimple Snapshot Manager eltávolítása
A StorSimple Snapshot Manager eltávolításához kövesse az alábbi lépéseket.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager eltávolítása
1. A gazdaszámítógépen nyissa meg a **Vezérlőpultot**, kattintson a **Programok**menügombra, majd a **Programok és szolgáltatások parancsra.**
2. A bal oldali ablaktáblában kattintson az **Eltávolítás vagy módosítás gombra.**
3. Kattintson a jobb gombbal **a StorSimple Snapshot Manager**elemre, majd kattintson az Eltávolítás **parancsra.**
4. Ezzel elindítja a StorSimple Snapshot Manager telepítőprogramját. Kattintson **a Telepítés módosítása gombra,** majd az **Eltávolítás gombra.**
   
   > [!NOTE]
   > Ha a háttérben olyan MMC-folyamatok futnak, mint például a StorSimple Snapshot Manager vagy a Lemezkezelő, az eltávolítás sikertelen lesz, és a program eltávolítása előtt az MMC összes példányának bezárására vonatkozó üzenet jelenik meg. Jelölje be **Az alkalmazások automatikus bezárása, majd a telepítés befejezése után az újraindítás megkísérlése**jelölőnégyzetet, majd kattintson az **OK**gombra.
   > 
   > 
5. Amikor az eltávolítási folyamat befejeződött, megjelenik egy **Sikeres beállítási** üzenet. Kattintson a **Bezárás** gombra.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>2. lépés: A StorSimple Snapshot Manager adatbázis ának biztonsági mentése
A StorSimple Snapshot Manager-adatbázis másolatának létrehozásához és mentéséhez kövesse az alábbi lépéseket.

#### <a name="to-back-up-the-database"></a>Az adatbázis biztonsági mentése
1. A Microsoft StorSimple felügyeleti szolgáltatás leállítása:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultjának **Eszközök** menüjében válassza a **Szolgáltatások parancslehetőséget.**
   3. A **Szolgáltatások** lapon válassza a **Microsoft StorSimple Management Service lehetőséget.**
   4. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás leállítása**elemre.
      
        ![A StorSimple Eszközkezelő szolgáltatás leállítása](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Tallózással keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog mappát. 
   
   > [!NOTE]
   > A ProgramData mappa rejtett.
  
3. Keresse meg a katalógus XML-fájlját, másolja a fájlt, és tárolja a másolatot biztonságos helyen vagy a felhőben.
   
    ![StorSimple biztonsági másolat katalógusfájlja](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Indítsa újra a Microsoft StorSimple felügyeleti szolgáltatást: 
   
   1. A Kiszolgálókezelő irányítópultjának **Eszközök** menüjében válassza a **Szolgáltatások parancslehetőséget.**
   2. A **Szolgáltatások** lapon válassza a **Microsoft StorSimple Management Service lehetőséget.**
   3. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás újraindítása**elemre. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>3. lépés: Telepítse újra a StorSimple Snapshot Managert, és állítsa vissza az adatbázist
A StorSimple Snapshot Manager újratelepítéséhez kövesse az [Új StorSimple Snapshot Manager telepítése](#install-a-new-storsimple-snapshot-manager)című témakör lépéseit. Ezután az alábbi eljárással állítsa vissza a StorSimple Snapshot Manager adatbázist.

#### <a name="to-restore-the-database"></a>Az adatbázis visszaállítása
1. A Microsoft StorSimple felügyeleti szolgáltatás leállítása:
   
   1. Indítsa el a Kiszolgálókezelőt.
   2. A Kiszolgálókezelő irányítópultjának **Eszközök** menüjében válassza a **Szolgáltatások parancslehetőséget.**
   3. A **Szolgáltatások** lapon válassza a **Microsoft StorSimple Management Service lehetőséget.**
   4. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás leállítása**elemre.
2. Tallózással keresse meg a C:\ProgramData\Microsoft\StorSimple\BACatalog mappát.
   
   > [!NOTE]
   > A ProgramData mappa rejtett.
   > 
   > 
3. Törölje a katalógus XML-fájlját, és cserélje le a korábban mentett verzióra.
4. Indítsa újra a Microsoft StorSimple felügyeleti szolgáltatást: 
   
   1. A Kiszolgálókezelő irányítópultjának **Eszközök** menüjében válassza a **Szolgáltatások parancslehetőséget.**
   2. A **Szolgáltatások** lapon válassza a **Microsoft StorSimple Management Service lehetőséget.**
   3. A jobb oldali ablaktáblában, a **Microsoft StorSimple Management Service csoportban**kattintson **a Szolgáltatás újraindítása**elemre.

## <a name="next-steps"></a>További lépések
* Ha többet szeretne megtudni a StorSimple Snapshot Managerről, olvassa el [a Mi a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Ha többet szeretne megtudni a StorSimple Snapshot Manager felhasználói felületéről, látogasson el a [StorSimple Snapshot Manager felhasználói felületére.](storsimple-use-snapshot-manager.md)
* Ha többet szeretne megtudni a StorSimple Snapshot Manager használatáról, olvassa el [a StorSimple Snapshot Manager használata a StorSimple-megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)

