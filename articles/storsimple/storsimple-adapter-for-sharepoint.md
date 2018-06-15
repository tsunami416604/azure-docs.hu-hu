---
title: StorSimple-Adapter telepítése a SharePoint |} Microsoft Docs
description: Ismerteti, hogyan lehet telepíteni és konfigurálni, vagy távolítsa el a StorSimple-adaptert a SharePoint, a SharePoint-kiszolgálófarm.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: 2e1b231a5cf13d2655ff66c7e48752729c580f48
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193818"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Telepítse és konfigurálja a StorSimple-adaptert a SharePoint
## <a name="overview"></a>Áttekintés
A StorSimple-adaptert SharePoint összetevő, amely lehetővé teszi a Microsoft Azure StorSimple rugalmas tárolás és a SharePoint server farms adatok védelmére. Az adapter használatával az SQL Server-adatbázisokhoz bináris nagy objektum (BLOB) tartalom áthelyezése a Microsoft Azure StorSimple hibrid felhőalapú tárolóeszköz.

A StorSimple-adaptert SharePoint funkciókkal, mint egy BLOB Storage (RBS) szolgáltatót, és használ az SQL Server távoli BLOB Storage szolgáltatás strukturálatlan SharePoint-tartalom (BLOB formájában) egy fájlkiszolgálón, a StorSimple eszköz által támogatott.

> [!NOTE]
> A StorSimple-adaptert SharePoint SharePoint Server 2010 távoli BLOB Storage (RBS) támogatja. Nem támogatja a SharePoint Server 2010 külső BLOB Storage (EBS).


* A SharePoint a StorSimple Adapter letöltéséhez keresse fel [SharePoint StorSimple adaptert] [ 1] a Microsoft Download Center.
* A korlátozások RBS és RBS tervezésével kapcsolatos információkat lásd [RBS használatához a SharePoint 2013] [ 2] vagy [RBS (SharePoint Server 2010) tervezése][3].

Ez az Áttekintés a többi röviden a StorSimple Adapter szerepe a SharePoint és a SharePoint kapacitást és teljesítményt korlátai, meg kell ismernie a előtt telepítse és konfigurálja az adaptert. Ezek az információk áttekintése után Ugrás [SharePoint telepítési StorSimple adaptert](#storsimple-adapter-for-sharepoint-installation) az adapter beállításának megkezdéséhez.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple-adaptert SharePoint előnyei
Egy SharePoint-webhelyre, a tartalom egy vagy több tartalom-adatbázisokban strukturálatlan Blobadatok tárolja. Alapértelmezés szerint ezek az adatbázisok SQL Server fut, és a SharePoint-kiszolgálófarm található számítógépek üzemelnek. Blobok is villámgyorsan nő mérete, a helyszíni tárolási nagy mennyiségű fel. Emiatt érdemes egy másik, kevésbé költséges tárolási megoldás kereséséhez. SQL Server távoli Blob Storage (RBS), amely lehetővé teszi a BLOB tartalmát tároló a fájlrendszer, az SQL Server-adatbázison kívül nevezett technológiát biztosít. RBS Blobok a fájlrendszer SQL Servert futtató számítógépen találhatók, vagy azokat a fájlrendszer egy másik számítógépen is tárolhatók.

RBS engedélyezése a SharePoint RBS RBS-szolgáltatót, például a SharePoint, a StorSimple Adapter használatát igényli. A StorSimple-adaptert SharePoint RBS, és így Blobok áthelyezése egy kiszolgálót, amelyről biztonsági másolatot készített a Microsoft Azure StorSimple rendszer működik. Microsoft Azure StorSimple majd tárolja a TÁRFIÓKBAN tárolt adatok helyben vagy a felhőben-használata alapján. Blobok, amelyek nagyon aktív (gyakran emlegetik úgy 1. rétegbeli vagy a gyakran használt adatokkal) találhatók. Kevésbé aktív és archiválási adatok találhatók a felhőben. Után RBS engedélyezi a tartalom-adatbázis, a SharePoint létrehozott új BLOB tartalmak a StorSimple eszközön, és nem a tartalom-adatbázis tárolja.

A Microsoft Azure StorSimple RBS végrehajtása a következő előnyöket nyújtja:

* BLOB tartalma áthelyezése egy másik kiszolgálóra csökkentheti a lekérdezés terhelését SQL-kiszolgálón, amely növelheti a SQL Server válaszképességét. 
* Az Azure StorSimple adatméret csökkentése érdekében a deduplikáció és a tömörítést használ.
* Az Azure StorSimple formában a helyi és felhőbeli pillanatképeket védelmet biztosít. Is ha maga az adatbázis a StorSimple eszközön, akkor is készítsen biztonsági másolatot a tartalom-adatbázist és a Blobok együtt összeomlás-konzisztens módon. (A tartalom-adatbázis áthelyezése az eszköz csak az támogatja a StorSimple 8000 series eszköz. Ez a funkció nem támogatott a 5000 vagy 7000-es sorozathoz.)
* Az Azure StorSimple katasztrófa utáni helyreállítás funkciót, beleértve a feladatátvétel, a fájl- és helyreállítási (beleértve a teszt helyreállítási) és a adatok gyors helyreállításának tartalmazza.
* A BLOB típusú adatok elemszintű helyreállítás SharePoint-tartalom StorSimple pillanatképek adatok helyreállítási szoftverek, például Kroll Ontrack PowerControls, használható. (Adatok helyreállítási erre a szoftverre egy külön beszerzési.)
* A StorSimple-adaptert SharePoint csatlakozik, a SharePoint központi felügyeleti portált, lehetővé téve a teljes SharePoint megoldás kezelését egy központi helyről.

BLOB tartalom áthelyezését a fájlrendszer más költséghatékony és előnyt is biztosít. Például RBS segítségével csökkentheti az 1. rétegbeli költségű tárolás van szükség, és azt zsugorítja a tartalom-adatbázist, mert RBS csökkentheti a szükséges a SharePoint-kiszolgálófarm adatbázisok számát. Azonban más tényezőktől, például az adatbázis méretkorlátait, és nem RBS tartalom mennyisége is kihathat a tárolási követelmények érvényesek. További információ a költségek és RBS használatának előnyeit: [RBS (SharePoint Foundation 2010) tervezése] [ 4] és [RBS használatához a SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Kapacitást és teljesítményt korlátok
Mielőtt RBS fontolóra a SharePoint-megoldás az, a tesztelt teljesítmény és a SharePoint Server 2010 és SharePoint Server 2013 és milyen kapcsolatban áll a működés felső korlátjának elfogadható teljesítmény kapacitáskorlátait tisztában kell lennie. További információkért lásd: [szoftver határok és a SharePoint 2013 rendszerhez korlátok](https://technet.microsoft.com/library/cc262787.aspx).

RBS konfigurálása előtt tekintse meg az alábbiakat:

* Győződjön meg arról, hogy a tartalom (a tartalom-adatbázis mérete) és minden kapcsolódó externalized BLOB mérete teljes mérete nem haladja meg a SharePoint által támogatott maximális RBS méretnél. Ezt a határt 200 GB-os. 
  
    **A mérték tartalom-adatbázist és a BLOB mérete**
  
  1. Ez a lekérdezés futtatása a központi adminisztrációs előtér-Webkiszolgálón. Indítsa el a SharePoint felügyeleti rendszerhéjat, és írja be a következő Windows PowerShell-parancs használatával beszerezheti a tartalom-adatbázisok méretét:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ez a lépés lekérdezi a tartalom-adatbázis méretét a lemezen.
  2. Az SQL Management Studio futtassa a következő SQL-lekérdezések egyik minden tartalom-adatbázist az SQL server mezőbe, majd adja hozzá az eredmény a számra kapott az 1. lépés.
     
     Adja meg a SharePoint 2013 tartalom-adatbázisok:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Adja meg a SharePoint 2010 tartalom-adatbázisok:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ez a lépés lekérdezi a blobot, amely rendelkezik lett externalized méretét.
* Azt javasoljuk, az összes BLOB és az adatbázis tartalom helyileg tárolja a StorSimple eszközön. A StorSimple eszköz egy két csomópontos fürt magas rendelkezésre állásra. Magas rendelkezésre állást helyezi el a tartalom-adatbázisok és a Blobok a StorSimple eszköz biztosít.
  
    Gyakorlati tanácsok hagyományos SQL Server áttelepítés használatával helyezze át a tartalom-adatbázist a StorSimple eszköz. Az adatbázist áthelyezi az csak az adatbázis összes BLOB tartalma át lett helyezve a fájlmegosztáshoz RBS után. A tartalom-adatbázis áthelyezése a StorSimple eszköz mellett dönt, azt javasoljuk, hogy az eszköz elsődleges kötetként konfigurálása a tartalom-adatbázist tároló.
* A Microsoft Azure StorSimple, ha rétegzett kötetet használ nincs mód garantálja, hogy a StorSimple eszköz lesz nem helyezhető el a Microsoft Azure felhőben levő tárolóba való a helyileg tárolt tartalmak. Ezért azt javasoljuk, a StorSimple helyileg rögzített kötetek SharePoint RBS együtt. Ezzel biztosíthatja, hogy minden BLOB tartalma helyileg továbbra is megtalálható a StorSimple eszköz, és a Microsoft Azure nem kerül.
* Ha a tartalom-adatbázisokban nem tárolja a StorSimple eszközön, használja a hagyományos SQL Server magas rendelkezésre állású bevált gyakorlatokat RBS támogatja. SQL Server fürtözésével biztosítható RBS, SQL Server tükrözési viszont nem. 

> [!WARNING]
> RBS nincs engedélyezve, ha a tartalom-adatbázis áthelyezése a StorSimple-eszköz nem ajánlott. Ez a nem tesztelt konfigurálása során.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>A SharePoint telepítési StorSimple Adapter
A StorSimple Adapter SharePoint telepítése előtt kell konfigurálnia a StorSimple eszköz és győződjön meg arról, hogy a SharePoint-kiszolgálófarm és az SQL Server példányának létrehozásánál megfelelnek-e a szükséges előfeltételek maradéktalanul. Ez az oktatóanyag leírja a konfigurációs követelmények, valamint telepítésének és a StorSimple Adapter frissítése a SharePoint.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
A StorSimple Adapter SharePoint telepítése előtt győződjön meg arról, hogy a StorSimple eszköz, a SharePoint-kiszolgálófarm és az SQL Server példányának létrehozásánál megfelelnek-e a következő előfeltételeknek.

### <a name="system-requirements"></a>Rendszerkövetelmények
A StorSimple SharePoint-adaptert kompatibilis a következő hardverre és szoftverekre:

* Támogatott operációs rendszer – Windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2
* Támogatott SharePoint verzió – a SharePoint Server 2010 vagy a SharePoint Server 2013
* Támogatott SQL Server-verziók – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition vagy SQL Server 2012 Enterprise Edition
* StorSimple eszközökhöz – támogatja a StorSimple 8000 series, a StorSimple 7000-es sorozatot vagy a StorSimple 5000 sorozatot.

### <a name="storsimple-device-configuration-prerequisites"></a>A StorSimple eszköz konfigurációs Előfeltételek
A StorSimple eszköz blokkolása eszköz, és így igényel az egy fájlkiszolgálón, amelyen az adatok lehet üzemeltetni. Azt javasoljuk, hogy Ön egy különálló kiszolgálón helyett használjon egy meglévő kiszolgálóról a SharePoint-farmról. Ezt a fájlkiszolgálót, az SQL Server-számítógépen, amelyen a tartalom-adatbázisok ugyanazon a helyi hálózat (LAN) kell lennie.

> [!TIP]
> * Ha a SharePoint-farm, a magas rendelkezésre álláshoz konfigurál, telepítsen a magas rendelkezésre állású fájlkiszolgálóra is.
> * Ha a tartalom-adatbázis nem tárolja a StorSimple eszközön, használja a hagyományos magas rendelkezésre állású bevált gyakorlatát, RBS támogató. SQL Server fürtözésével biztosítható RBS, SQL Server tükrözési viszont nem. 


Győződjön meg arról, hogy a StorSimple eszköz megfelelően van-e konfigurálva, és, hogy a SharePoint központi telepítés támogatásához szükséges kötetek vannak-e konfigurálva, és elérhető az SQL Server számítógépen. Ugrás a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md) Ha még nincs telepítve és konfigurálva a StorSimple eszközt. Megjegyzés: a StorSimple eszköz; IP-címe szüksége lesz az StorSimple Adapter során a SharePoint-telepítéshez.

Ezenkívül győződjön meg arról, hogy a kötet BLOB externalization használandó megfelel-e az alábbi követelményeknek:

* A kötet egy 64 KB-os foglalásiegység-méret van formázva.
* A webes kezelőfelületből (WFE) és az alkalmazáskiszolgálók érhetik el a kötetet egy univerzális elnevezési konvenció (UNC) szerinti elérési útján kell lennie.
* A SharePoint-kiszolgálófarm be kell állítani a kötet írni.

> [!NOTE]
> Után telepítette, és konfigurálja az adaptert, az összes BLOB externalization be kell lépnie a StorSimple eszközön keresztül (az eszköz fogja a köteteket az SQL Server és a tárolási Rétegek kezelése). A BLOB externalization semmilyen más célra nem használható.


Ha tervezi pillanatképek készítése a BLOB és az adatbázis adatok a StorSimple Snapshot Manager segítségével, ügyeljen arra, hogy a StorSimple Snapshot Manager telepíthető az adatbázis-kiszolgáló, így a Windows kötet árnyékmásolata szolgáltatás (VSS) végrehajtásához használhatja az SQL-írószolgáltatás.

> [!IMPORTANT]
> StorSimple Snapshot Manager nem támogatja a SharePoint VSS-író, és nem tudja átvenni a SharePoint-adatok alkalmazáskonzisztens pillanatképeket. A SharePoint-forgatókönyvek a StorSimple Snapshot Manager biztosít csak összeomlásbiztos biztonsági másolatokat készíthet.


## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint-farm konfigurációs Előfeltételek
Győződjön meg arról, hogy a SharePoint-kiszolgálófarm megfelelően van beállítva, az alábbiak szerint:

* Győződjön meg arról, hogy a SharePoint-kiszolgálófarm állapota kifogástalan, és ellenőrizze a következőket:
* Minden SharePoint előtér-Webkiszolgálón és a farm regisztrált alkalmazáskiszolgálók futnak, és pingelhető-e a kiszolgálóról, amelyen telepíteni fogja a StorSimple Adapter a SharePoint.
* Minden egyes ELŐTÉR-webkiszolgálón, és az alkalmazáskiszolgáló fut a SharePoint időzítőszolgáltatása (SPTimerV3 vagy SPTimerV4).
* A SharePoint időzítőszolgáltatása, mind az IIS-alkalmazáskészlet a SharePoint központi felügyeleti helyhez futtatott rendszergazdai jogosultsággal kell rendelkeznie.
* Győződjön meg arról, hogy a Internet Explorer fokozott biztonsági környezetet (IE ESC) le van tiltva. Kövesse az alábbi lépéseket IE ESC letiltása:
  
  1. Zárja be az Internet Explorer.
  2. A Kiszolgálókezelő elindítása.
  3. Kattintson a bal oldali ablaktáblában **helyi kiszolgáló**.
  4. A jobb oldali melletti **Internet Explorer fokozott biztonsági beállítások**, kattintson a **a**.
  5. A **rendszergazdák**, kattintson a **ki**.
  6. Kattintson az **OK** gombra.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Távoli BLOB Storage (RBS) Előfeltételek
Győződjön meg arról, hogy az SQL Server egy támogatott verzióját használja. Csak az alábbi verziója nem támogatott és RBS használhatja:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Blobok is externalized, csak a, amely a StorSimple eszköz jeleníti meg az SQL Server köteteken. Nincs más célja a BLOB externalization támogatottak.

Ha végrehajtotta az összes előfeltétel-konfigurációhoz, Ugrás [a StorSimple-Adapter telepítése a SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>A SharePoint a StorSimple-Adapter telepítése
Az alábbi lépések segítségével a StorSimple-Adapter telepítése a SharePoint. Ha a szoftver újratelepítését, lásd: [frissítése vagy telepítse újra a StorSimple Adapter SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). A telepítéshez szükséges idő függ a SharePoint-adatbázisok a SharePoint-kiszolgálófarm teljes száma.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS konfigurálása
Telepítése után a StorSimple Adapter a SharePoint, konfigurálja a RBS a következő eljárásban leírtak szerint.

> [!TIP]
> A StorSimple-adaptert SharePoint csatlakozik a SharePoint központi felügyelet lapján engedélyezve van vagy le van tiltva, a SharePoint-farm minden egyes tartalom-adatbázist a RBS lehetővé. Azonban engedélyezésével vagy letiltásával RBS a tartalom-adatbázist a hatására az IIS visszaállítása, amelyek attól függően, hogy a farm konfigurációja rövid ideig megszakíthatja a SharePoint előtér-webkiszolgáló (WFE) rendelkezésre állását. (A – például a terheléselosztó előtérbeli, és az aktuális kiszolgáló munkaterhelését tényezők korlátozza, vagy a megszakítás kiküszöbölése.) Hogy megvédje a szüneteltetése felhasználók, azt javasoljuk, hogy engedélyezi vagy letiltja a RBS csak tervezett karbantartási időszak alatt történjen.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>A szemétgyűjtés konfigurálása
Ha SharePoint-webhelyről törölt objektumok, nem automatikusan törli a RBS tároló kötetről. Ehelyett egy aszinkron, háttérben karbantartási program törli az árva Blobok a szolgáltatásfájl-tároló. A rendszergazdák is ütemezheti a folyamat rendszeres időközönként futtatandó vagy indításához, amikor szükséges.

A karbantartási program (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) automatikusan települ a SharePoint ELŐTÉR-webkiszolgáló és a alkalmazáskiszolgálók RBS engedélyezi. A program telepítve van a következő helyen: *rendszerindítási meghajtó*: \Program Files\Microsoft SQL távoli Blob Storage 10.50\Maintainer\

Konfigurálásával és a karbantartási program használatával kapcsolatos információkért lásd: [RBS üzemeltetnek a SharePoint Server 2013][8].

> [!IMPORTANT]
> RBS karbantartó program erőforrás-igényesek. Olyan időszakra ütemezze úgy, hogy könnyű tevékenység idején futó a SharePoint-farm.


### <a name="delete-orphaned-blobs-immediately"></a>Azonnal árva Blobok törlése
Ha azonnal törli az árva Blobok van szüksége, használhatja az alábbi utasításokat. Ne feledje, hogy ezek az utasítások hogyan ehhez a SharePoint 2013 környezetben a következő összetevőkkel példát:

* A tartalom-adatbázis nevének megadása WSS_Content.
* Az SQL Server nevét SHRPT13-SQL12\SHRPT13.
* A webes alkalmazás neve a SharePoint – 80-as.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Frissítés, vagy telepítse újra a StorSimple Adapter SharePoint
A következő eljárás segítségével SharePoint-kiszolgáló frissítése, és telepítse a SharePoint StorSimple Adapter vagy egyszerűen frissítse, vagy telepítse újra az adapter egy meglévő SharePoint-farm.

> [!IMPORTANT]
> Frissítse a SharePoint szoftver és/vagy frissítés, vagy telepítse újra a StorSimple Adapter SharePoint megkezdése előtt, tekintse át a következőket:
> 
> * Azokat a fájlokat, korábban került a külső tárhelyen RBS nem lesz elérhető, amíg befejeződik az újratelepítés és a RBS szolgáltatás ismét engedélyezve van-e. Hatással korlátozása érdekében hajtsa végre a bármely frissítése vagy újratelepítés tervezett karbantartási időszak alatt történjen.
> * A frissítés/újratelepítése szükséges idő változhat, attól függően, hogy a SharePoint server farm SharePoint-adatbázisok teljes száma.
> * A frissítés/újratelepítés befejezése után, akkor a tartalom-adatbázisokhoz RBS engedélyezni kell. Lásd: [konfigurálása RBS](#configure-rbs) további információt.
> * Ha nagyon sok (több mint 200), adatbázisok SharePoint-farm RBS konfigurálja a **SharePoint központi felügyelet** lap esetleg túllépi az időkorlátot. Ha ez történik, frissítse az oldalt. Ez nem befolyásolja a konfigurációs folyamat.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adapter SharePoint eltávolítása
A következő eljárások azt ismertetik, hogyan helyezze vissza a Blobok az SQL Server-adatbázisokhoz, és azután távolítsa el a StorSimple Adapter a SharePoint. 

> [!IMPORTANT]
> Helyezze át a Blobok vissza a tartalom-adatbázisokban az adapter szoftverek eltávolítása előtt kell.


### <a name="before-you-begin"></a>Előkészületek
Információgyűjtés a következő előtt helyezze vissza az adatokat az SQL Server-adatbázisokhoz és az adapter eltávolító folyamat elindításához:

* Az összes olyan adatbázis, amelyhez RBS engedélyezett nevét
* Az UNC elérési útnak a konfigurált BLOB-tároló

### <a name="move-the-blobs-back-to-the-content-databases"></a>A Blobok helyezze vissza a tartalom-adatbázisok
A StorSimple-adaptert SharePoint szoftver eltávolítása előtt át kell összes volt externalized Blobok vissza a tartalom SQL Server-adatbázisok. Ha eltávolítja a StorSimple adaptert a SharePoint, mielőtt továbblép a Blobok vissza a tartalom-adatbázisok, a következő figyelmeztető üzenet jelenik meg.

![Figyelmeztető üzenet](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>A Blobok helyezhetők vissza a tartalom-adatbázisok
1. Töltse le, minden externalized objektumra.
2. Nyissa meg a **SharePoint központi felügyelet** lapon, és keresse meg a **rendszerbeállítások**.
3. A **Azure StorSimple**, kattintson a **konfigurálása a StorSimple-Adapter**.
4. Az a **konfigurálása a StorSimple-Adapter** lapján kattintson a **letiltása** távolítsa el a külső BLOB-tároló kívánt tartalom-adatbázisok mindegyike esetében gombra. 
5. Az objektumok törlése a SharePoint, majd töltse fel őket újra.

Másik lehetőségként használhatja a Microsoft` RBS Migrate()` részét képező SharePoint PowerShell-parancsmagot. További információkért lásd: [tartalmat át a virtuális gépbe vagy onnan RBS](https://technet.microsoft.com/library/ff628255.aspx).

A Blobok vissza helyezi a tartalom-adatbázist, nyissa meg a következő lépéssel: [távolítsa el az adaptert](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Az adapter eltávolítása
A Blobok helyezze vissza az SQL Server tartalom-adatbázisok használata után az alábbi lehetőségek egyikét a StorSimple-Adapter eltávolítása a SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>A telepítőprogram használatával a adapter eltávolítása
1. Rendszergazdai jogosultságokkal rendelkező fiók használatával jelentkezzen be a webes előtér-webkiszolgálójára (WFE).
2. Kattintson duplán a StorSimple SharePoint telepítő. A telepítő varázsló elindul.
   
    ![A telepítő varázsló](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kattintson a **Tovább** gombra. A következő lap jelenik meg.
   
    ![A telepítő varázsló eltávolítása lap](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kattintson a **eltávolítása** jelölje be az eltávolítási folyamat. A következő lap jelenik meg.
   
    ![A telepítő varázsló visszaigazolási lapja](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kattintson a **eltávolítása** az eltávolítás megerősítéséhez. A következő folyamat lap jelenik meg.
   
    ![A telepítő varázsló állapotlapja](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Az eltávolítás befejezésekor megjelenik a Befejezés lapra. Kattintson a **Befejezés** a telepítővarázsló bezárásához.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Távolítsa el az adaptert a Vezérlőpult segítségével
1. Nyissa meg a Vezérlőpultot, és kattintson **programok és szolgáltatások**.
2. Válassza ki **SharePoint StorSimple adaptert**, és kattintson a **Eltávolítás**.

## <a name="next-steps"></a>További lépések
[További információ a StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
