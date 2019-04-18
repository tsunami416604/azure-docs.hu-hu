---
title: Sharepointhoz készült StorSimple-Adapter telepítése |} A Microsoft Docs
description: Ismerteti, hogyan lehet telepíteni és konfigurálni, vagy távolítsa el a StorSimple Adapter for SharePoint a SharePoint-kiszolgálófarm.
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
ms.openlocfilehash: a2f8e75578e396085e7d80f43c1180e158967061
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885587"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Telepítse és konfigurálja a StorSimple Adapter for SharePoint
## <a name="overview"></a>Áttekintés
A StorSimple Adapter for SharePoint egy összetevő, amely lehetővé teszi a Microsoft Azure StorSimple rugalmas tárolás és adatok védelme a SharePoint-kiszolgálófarmok. Az adapter segítségével bináris nagyméretű objektum (BLOB) tartalom a Microsoft Azure StorSimple hibrid felhőalapú tárolóeszköz a tartalom az SQL Server-adatbázisok áthelyezéséhez.

A StorSimple Adapter for SharePoint működik, mint egy BLOB Storage (RBS) szolgáltató, és használja az SQL Server távoli BLOB Storage szolgáltatás strukturálatlan SharePoint-tartalom tárolására (Blobok formájában) egy fájlkiszolgálón, amely a StorSimple eszköz használatával.

> [!NOTE]
> A StorSimple Adapter for SharePoint támogatja a SharePoint Server 2010 távoli BLOB Storage (RBS). Nem támogatja a SharePoint Server 2010 külső BLOB Storage (EBS).


* A StorSimple Adapter for SharePoint letöltéséhez keresse fel [StorSimple Adapter for SharePoint] [ 1] a Microsoft Download Center.
* Korlátozások RBS és RBS tervezésével kapcsolatos további információkért lépjen [a SharePoint 2013-ban RBS használata mellett döntenek] [ 2] vagy [RBS (SharePoint Server 2010) tervezése] [ 3].

Ez az Áttekintés a többi röviden ismerteti a StorSimple Adapter szerepe a SharePoint és a SharePoint kapacitás és teljesítmény korlátokat, érdemes figyelembe vennie, mielőtt telepíti és konfigurálja az adaptert. Miután ellenőrizze ezeket az információkat, lépjen be [StorSimple Adapter for SharePoint telepítési](#storsimple-adapter-for-sharepoint-installation) az adapter beállítása a kezdéshez.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple Adapter for SharePoint előnyeit
SharePoint-webhelyre, a tartalom egy vagy több tartalom-adatbázisokban lévő strukturált Blobadatok van tárolva. Alapértelmezés szerint ezek az adatbázisok az SQL Servert futtató és a SharePoint-kiszolgálófarm található számítógépek üzemel. Blobok is villámgyorsan nő a időt foglal le a helyi tárterület mérete. Emiatt érdemes egy másik, kevésbé költséges tárolási megoldás. Az SQL Server egy távoli Blob Storage (RBS), amely lehetővé teszi, BLOB-tartalmak a fájlrendszer kívül az SQL Server-adatbázis nevű technológiát kínál. Az RBS Blobok is találhatók, a fájlrendszer azon a számítógépen, amelyen az SQL Server vagy a fájlrendszer egy másik kiszolgálón tárolhatók.

RBS van szüksége egy RBS szolgáltató, például a StorSimple Adapter for SharePoint, a SharePoint RBS engedélyezése. A StorSimple Adapter for SharePoint együttműködik RBS, helyezze át a blobokat egy kiszolgálóra, biztonsági másolatot készít a Microsoft Azure StorSimple rendszerbe, ami lehetővé teszi. A Microsoft Azure StorSimple majd tárol a BLOB-adatokat helyben vagy a felhőben, használat alapján. Blobok, amelyek nagyon aktív (általában úgy nevezik 1. réteg vagy gyakran használt adatokkal) találhatók. Kevésbé aktív és archiválási adatok a felhőben találhatók. Miután engedélyezte a RBS a tartalom-adatbázist, a StorSimple eszközön, és nem a tartalom-adatbázist a Sharepointban való létrehozásakor új BLOB tartalmak tárolja.

A Microsoft Azure StorSimple RBS végrehajtása a következő előnyöket nyújtja:

* BLOB tartalma áthelyezése egy másik kiszolgálóra csökkentheti a lekérdezés betöltése SQL-kiszolgálón, ami javíthatja az SQL Server válaszképességét. 
* Az Azure StorSimple adatméret csökkentése érdekében a deduplikáció és a tömörítést használ.
* Az Azure StorSimple a helyi űrlap és a felhőbeli pillanatképekkel védelmet biztosít. Is ha maga az adatbázis a StorSimple eszközön, akkor is készítsen biztonsági másolatot a tartalom-adatbázist és a Blobok együtt egy összeomlás-konzisztens módon. (A tartalom-adatbázis áthelyezése az eszköz csak az támogatja a StorSimple 8000 sorozatú eszköz. Ez a funkció nem támogatott a 5000 és 7000-es sorozathoz.)
* Az Azure StorSimple vész helyreállítási funkciókat, beleértve a feladatátvétel (beleértve a tesztelési helyreállítási) fájl- és helyreállítási és az adatok gyors helyreállításának tartalmazza.
* Data recovery szoftver Kroll Ontrack PowerControls, például a SharePoint-tartalom elemszintű helyreállítás végrehajtásához a blobadat StorSimple pillanatképekkel is használhatja. (A data recovery szoftver a külön vásárolható.)
* A StorSimple Adapter for SharePoint rendkívüli a SharePoint központi felügyeleti portálján, amely lehetővé teszi a teljes SharePoint-megoldás kezelését egy központi helyről.

Egyéb költség megtakarítást és előnyöket biztosíthat a BLOB tartalma áthelyezése a fájlrendszer. Például RBS használatával csökkentheti a költséges 1. szintű tárhelyre van szükség, és a tartalom-adatbázist, csökken, mert RBS csökkentheti a szükséges a SharePoint-kiszolgálófarm adatbázisok száma. Azonban más tényezők, például az adatbázis méretbeli korlátokat, és nem RBS tartalom mennyisége is befolyásolhatja tárolási követelményeit. További információ a költségeit és RBS használatának előnyei: [RBS (a SharePoint Foundation 2010) tervezése] [ 4] és [a SharePoint 2013-ban RBS használata mellett döntenek] [5].

### <a name="capacity-and-performance-limits"></a>Kapacitás és teljesítmény korlátok
Mielőtt RBS fontolóra a SharePoint-megoldásban, érdemes figyelembe vennie a tesztelt teljesítmény és a SharePoint Server 2010 és a SharePoint Server 2013 és az hogyan kapcsolódik egymáshoz ezeket a korlátokat elfogadható teljesítményt kapacitásának korlátjáig. További információkért lásd: [szoftver határok és a SharePoint 2013-hoz készült korlátok](https://technet.microsoft.com/library/cc262787.aspx).

RBS konfigurálása előtt tekintse meg az alábbiakat:

* Győződjön meg arról, hogy a tartalom (a tartalom-adatbázis mérete) és minden kapcsolódó externalized Blobok méretének teljes mérete nem haladja meg a SharePoint által támogatott maximális RBS mérete. Ez a korlát 200 GB-os. 
  
    **Mérték tartalom-adatbázist és a BLOB mérete**
  
  1. Ez a lekérdezés futtatása a központi adminisztrációs előtér-Webkiszolgálón. Indítsa el a SharePoint felügyeleti rendszerhéjat, és adja meg a tartalom-adatbázisok méretének lekérése a következő Windows PowerShell-parancsot:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ebben a lépésben a tartalom-adatbázis mérete lekérdezi a lemezen.
  2. Az egyes tartalom-adatbázist az SQL server be az SQL Management Studióban futtassa a következő SQL-lekérdezések egyikét, és adja hozzá az eredmény a számot kapott az 1. lépés.
     
     Adja meg a SharePoint 2013 tartalom-adatbázisok:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Adja meg a SharePoint 2010 tartalom-adatbázisok:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ebben a lépésben a Blobok, amely rendelkezik lett externalized méretének beolvasása.
* Javasoljuk, hogy minden BLOB és az adatbázis-tartalom helyileg tárolja a StorSimple eszközön. A StorSimple-eszközhöz egy két csomópontos fürt magas rendelkezésre állás érdekében. A tartalom-adatbázisok és a Blobok elhelyezése a StorSimple-eszközt a magas rendelkezésre állást biztosít.
  
    Hagyományos SQL-kiszolgáló áttelepítési – ajánlott eljárások használatával helyezze át a tartalom-adatbázist a StorSimple-eszköz. Helyezze át az adatbázist, csak után az adatbázis összes BLOB tartalma RBS került, a fájlmegosztás. Ha a tartalom-adatbázis áthelyezése a StorSimple-eszköz, azt javasoljuk, hogy konfigurálja-e a tartalom-adatbázist tároló mint elsődleges kötet az eszközön.
* A Microsoft Azure StorSimple, ha rétegzett köteteket használó nem garantáljuk a StorSimple-eszköz nem lesz rétegzett, a Microsoft Azure felhőalapú tárolást helyben tárolt tartalom semmilyen módon nem lehet. Ezért javasoljuk a StorSimple a helyileg rögzített kötetek SharePoint RBS együtt. Ez biztosítja, hogy az összes BLOB tartalma továbbra is helyben a StorSimple eszközön, és nem kerül a Microsoft Azure-bA.
* Ha a tartalom-adatbázisokban nem a StorSimple eszközön tárolja, használja a hagyományos SQL Server magas rendelkezésre állású ajánlott eljárásokat, amelyek támogatják a RBS. RBS SQL Server-fürtszolgáltatás támogatja, miközben az SQL Server-tükrözés nem létezik. 

> [!WARNING]
> Ha nem engedélyezte a RBS, a tartalom-adatbázis áthelyezése a StorSimple-eszköz nem javasoljuk. Ez az egy nem tesztelt konfigurációt.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple Adapter for SharePoint telepítés
A StorSimple Adapter for SharePoint telepítése előtt konfigurálni kell a StorSimple-eszközt, és győződjön meg róla, hogy a SharePoint-kiszolgálófarm és SQL-kiszolgáló a példányosítás megfelelnek-e minden előfeltétele. Ebben az oktatóanyagban a konfigurációs követelményeinek, valamint telepítése és frissítése a StorSimple Adapter for SharePoint eljárásait ismerteti.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
A StorSimple Adapter for SharePoint telepítése előtt győződjön meg róla, hogy a StorSimple eszköz-, SharePoint-kiszolgálófarm és SQL-kiszolgáló a példányosítás megfelelnek-e a következő előfeltételek vonatkoznak.

### <a name="system-requirements"></a>Rendszerkövetelmények
A StorSimple Adapter for SharePoint működik együtt a következő hardverre és szoftverekre:

* Támogatott operációs rendszer – Windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2
* Támogatott SharePoint-verziók – SharePoint Server 2010 vagy a SharePoint Server 2013
* Támogatott SQL Server-verziók – SQL Server 2008 Enterprise Edition, az SQL Server 2008 R2 Enterprise Edition vagy SQL Server 2012 Enterprise Edition
* A StorSimple-eszközök – támogatott a StorSimple 8000 sorozat, a StorSimple 7000-es sorozat és a StorSimple 5000-es sorozat.

### <a name="storsimple-device-configuration-prerequisites"></a>A StorSimple eszköz konfigurációs Előfeltételek
A StorSimple-eszköz letiltása eszköz, és ilyen fájlkiszolgálót, amelyen az adatok lehet üzemeltetni. Javasoljuk, hogy használjon helyett egy meglévő kiszolgálót külön kiszolgálóra a SharePoint-farmból. A fájlkiszolgáló az SQL Server-számítógép, amelyen a tartalom-adatbázisokban az azonos helyi hálózatra (LAN) kell lennie.

> [!TIP]
> * Ha magas rendelkezésre állású SharePoint-farm konfigurálásához, üzembe kell helyeznie a magas rendelkezésre állású fájlkiszolgálóra is.
> * Ha a tartalom-adatbázis nem a StorSimple eszközön tárolja, használja a hagyományos, magas rendelkezésre állású ajánlott eljárásokat, amelyek támogatják a RBS. RBS SQL Server-fürtszolgáltatás támogatja, miközben az SQL Server-tükrözés nem létezik. 


Győződjön meg arról, hogy a StorSimple-eszköz megfelelően van konfigurálva, és, hogy a SharePoint üzemelő példányok támogatására, és megfelelő kötetek vannak konfigurálva, és elérhető-e az SQL Server-számítógépről. Lépjen a [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md) Ha még nincs telepítve és konfigurálva a StorSimple-eszköz. Megjegyzés: a StorSimple-eszköz; IP-címe szüksége lesz rá során a StorSimple Adapter for SharePoint telepítés.

Emellett győződjön meg arról, hogy a kötet BLOB externalizációja használandó megfelel-e a következő követelményeknek:

* A kötet fájlrendszerrel egy 64 KB-os foglalási egység méretét.
* A webes kezelőfelület (WFE) és az alkalmazáskiszolgálókat kell tudni elérni a kötetet egy univerzális elnevezési konvenció (UNC) szerinti elérési útján.
* A kötet írni a SharePoint-kiszolgálófarm kell konfigurálni.

> [!NOTE]
> Után telepítse és konfigurálja az adaptert, az összes BLOB externalizációja a StorSimple-eszköz kell áthaladnia (az eszköz lesz a kötetek jelenik meg az SQL Server és a tárolási Rétegek kezelése). BLOB externalizációja semmilyen más célra nem használható.


Ha azt tervezi, a BLOBOK és az adatbázis adatait pillanatképek készítése a StorSimple Snapshot Manager használatával, mindenképpen telepítse a StorSimple Snapshot Manager adatbázis-kiszolgálón, hogy a Windows kötet árnyékmásolata szolgáltatás (VSS) végrehajtásához használhatja az SQL-írószolgáltatás.

> [!IMPORTANT]
> A StorSimple Snapshot Manager nem támogatja a SharePoint VSS-író, és nem tudja átvenni a SharePoint-adatok alkalmazáskonzisztens pillanatképeket. A SharePoint forgatókönyvekben a StorSimple Snapshot Manager csak összeomlás-konzisztens biztonsági másolatok biztosít.


## <a name="sharepoint-farm-configuration-prerequisites"></a>A SharePoint-farm konfigurációs Előfeltételek
Győződjön meg arról, hogy a SharePoint-kiszolgálófarm megfelelően van konfigurálva, a következő:

* Győződjön meg arról, hogy a SharePoint-kiszolgálófarm kifogástalan állapotban van-e, és ellenőrizze az alábbiakat:
* Minden SharePoint előtér-Webkiszolgálón és alkalmazáskiszolgálók, a farm regisztrált futnak, és a kiszolgálóról, amelyen telepíteni fogja a StorSimple Adapter for SharePoint pingelhető.
* A SharePoint időzítő szolgáltatása (SPTimerV3 vagy SPTimerV4) minden egyes ELŐTÉR-webkiszolgálón, és az alkalmazáskiszolgáló fut.
* A SharePoint időzítő szolgáltatása, mind az IIS-alkalmazáskészlet, amelyben a SharePoint központi felügyeleti webhely fut-e rendszergazdai jogosultságokkal kell rendelkeznie.
* Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági környezet (IE ESC) le van tiltva. Kövesse az alábbi lépéseket az IE ESC letiltása:
  
  1. Zárja be az Internet Explorer az összes példányát.
  2. A Kiszolgálókezelő elindítása.
  3. A bal oldali ablaktáblán kattintson a **helyi kiszolgáló**.
  4. A jobb oldali melletti **Internet Explorer fokozott biztonsági beállításai**, kattintson a **a**.
  5. A **rendszergazdák**, kattintson a **ki**.
  6. Kattintson az **OK** gombra.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Távoli BLOB Storage (RBS) előfeltételei
Győződjön meg arról, hogy használ-e az SQL Server támogatott verziója. Csak a következő verziók a következők: támogatott és RBS használhatja:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Blobok csak a, amely a StorSimple-eszköz jeleníti meg az SQL Server köteteken externalized is lehet. Nincs más célértékei BLOB externalizációja támogatottak.

Ha végrehajtotta az összes előfeltételként szükséges konfigurációs lépések, Ugrás a [telepítse a StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Telepítse a StorSimple Adapter for SharePoint
Az alábbi lépések segítségével telepítse a StorSimple Adapter for SharePoint. Ha a rendszer újratelepíteni a szoftvert, tekintse meg [frissítése, vagy telepítse újra a StorSimple Adapter for SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). A telepítéshez szükséges idő attól függ, hogy a SharePoint server farm SharePoint-adatbázisok teljes száma.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS konfigurálása
Miután telepítette a StorSimple Adapter for SharePoint, a következő eljárásban leírtak szerint konfigurálja a RBS.

> [!TIP]
> A StorSimple Adapter for SharePoint rendkívüli a SharePoint központi felügyelet lapján engedélyezve van vagy le van tiltva, a SharePoint-farm minden egyes tartalom-adatbázist a RBS lehetővé teszi. Engedélyezés vagy letiltás RBS a tartalom-adatbázist a hatására IIS alaphelyzetbe állítása, amely függően a webfarm-konfigurációt, rövid ideig megzavarhatja a SharePoint webes előtér (WFE) rendelkezésre állását. (A tényezőket, például a előtérbeli terheléselosztót, és a jelenlegi kiszolgáló munkaterhelését korlátozhatja, vagy megszüntetheti a megszakítás.) Felhasználók szembeni zavar, azt javasoljuk, hogy engedélyezi vagy letiltja a RBS csak egy tervezett karbantartási időszak alatt.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>A szemétgyűjtés konfigurálása
Ha egy SharePoint-webhelyről törölt objektumok, nem automatikusan törli a RBS tároló kötetről. Ehelyett egy aszinkron, háttér karbantartási programot törli az árva Blobok a fájl áruházból. A rendszergazdák a folyamat futtatása rendszeres időközönként ütemezheti, illetve indításához, amikor szükséges.

A karbantartási program (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) automatikusan települ a SharePoint ELŐTÉR-webkiszolgáló és az alkalmazáskiszolgálók RBS engedélyezésekor. A program telepítve van a következő helyen: *rendszerindítási meghajtó*: \Program Files\Microsoft SQL távoli Blob Storage 10.50\Maintainer\

Konfigurálásával és a karbantartási programmal kapcsolatos további információkért lásd: [RBS karbantartása, a SharePoint Server 2013][8].

> [!IMPORTANT]
> RBS karbantartó program erőforrás-igényesek. Olyan időszakra ütemezze, hogy világos tevékenységű időszakok során csak a SharePoint-farm futtatása.


### <a name="delete-orphaned-blobs-immediately"></a>Azonnal törli az árva Blobok
Ha azonnal törli az árva Blobok van szüksége, használhatja az alábbi utasításokat. Vegye figyelembe, hogy ezeket az utasításokat ennek menetét a következő összetevőket, a SharePoint 2013 környezetben egy példát:

* A tartalom-adatbázist név WSS_Content.
* Az SQL Server-név SHRPT13-SQL12\SHRPT13.
* A webalkalmazás neve a SharePoint – 80-as.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Frissítse és telepítse újra a StorSimple Adapter for SharePoint
A következő eljárással, majd telepítse újra a StorSimple Adapter for SharePoint a SharePoint server, illetve a egyszerűen frissítése, vagy telepítse újra a SharePoint-kiszolgálófarm adaptert.

> [!IMPORTANT]
> Mielőtt megkísérli a SharePoint szoftver és/vagy a frissítés frissítéséhez, vagy telepítse újra a StorSimple Adapter for SharePoint, tekintse át a következő információkat:
> 
> * Azokat a fájlokat, korábban helyezett át a külső tárhelyen RBS nem lesz elérhető, amíg az újratelepítés befejezését, és a RBS szolgáltatás újra engedélyezve van-e. Korlátozza a felhasználókra gyakorolt hatás, hajtsa végre minden frissítés vagy újratelepítésre tervezett karbantartási időszak alatt.
> * A frissítés/újratelepítése szükséges idő a SharePoint server farm SharePoint-adatbázisok száma függően változhat.
> * Miután befejeződött a frissítés/újratelepítése, akkor a tartalom-adatbázisokhoz RBS engedélyeznie kell. Lásd: [konfigurálása RBS](#configure-rbs) további információt.
> * Ha nagyon nagy számú (több mint 200),-adatbázisok SharePoint-farm RBS konfigurálása a **SharePoint központi felügyelet** lap előfordulhat, hogy időtúllépés. Ha ez bekövetkezik, frissítse az oldalt. Ez nem befolyásolja a konfigurációs folyamat.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple Adapter for SharePoint eltávolítása
A következő eljárások bemutatják, hogyan helyezze vissza a blobokat a tartalom az SQL Server-adatbázisok és majd távolítsa el a StorSimple Adapter for SharePoint. 

> [!IMPORTANT]
> Hogy visszahelyezése a blobokat a tartalom-adatbázisokban az adapter szoftverek eltávolítása előtt.


### <a name="before-you-begin"></a>Előkészületek
Gyűjtse össze a következő információkat, lépjen vissza az adatokat az SQL Server-adatbázisokhoz és az adapter eltávolító folyamat megkezdése előtt:

* A nevét, amelyhez RBS engedélyezve van minden adatbázis
* Az UNC elérési utat a megadott BLOB-tároló

### <a name="move-the-blobs-back-to-the-content-databases"></a>Lépjen vissza a blobokat a tartalom-adatbázisokhoz
A StorSimple Adapter for SharePoint szoftver eltávolítás előtt át kell telepítenie az összes szükséges blobokat is externalized vissza a tartalom az SQL Server-adatbázisok. Ha megpróbálja távolítsa el a a StorSimple Adapter for SharePoint előtt összes blobjának vissza a tartalom-adatbázisokhoz, látni fogja a következő figyelmeztető üzenet.

![Figyelmeztető üzenet](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>A Blobok a tartalom-adatbázisokhoz való visszatéréshez
1. Töltse le a externalized objektumok mindegyike.
2. Nyissa meg a **SharePoint központi felügyelet** lapon, és keresse meg a **rendszerbeállítások**.
3. A **Azure StorSimple**, kattintson a **konfigurálása a StorSimple Adapter**.
4. Az a **konfigurálása a StorSimple Adapter** lap, kattintson a **letiltása** külső BLOB storage-ból eltávolítani kívánt tartalom-adatbázisok mindegyike az alábbi gombra. 
5. Az objektumok törlése a Sharepointból, és ezután töltse fel őket újra.

Másik lehetőségként használhatja a Microsoft `RBS Migrate()` SharePoint mellékelt PowerShell-parancsmagot. További információkért lásd: [tartalmainak Migrálása, vagy onnan máshová RBS](https://technet.microsoft.com/library/ff628255.aspx).

A Blobok vissza helyezi a tartalom-adatbázist, nyissa meg a következő lépés: [Távolítsa el az adapter](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Az adapter eltávolítása
Lépjen vissza a blobokat a SQL Server tartalom-adatbázisok, az alábbi lehetőségek közül történő után távolítsa el a StorSimple Adapter for SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>A telepítőprogram használatával a adapter eltávolítása
1. Rendszergazdai jogosultságokkal rendelkező fiók használatával jelentkezzen be a webes előtér-webkiszolgálójára (WFE).
2. Kattintson duplán a StorSimple Adapter for SharePoint-telepítő. A telepítő varázsló elindul.
   
    ![A telepítő varázsló](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kattintson a **tovább**. A következő oldal jelenik meg.
   
    ![A telepítő varázsló remove lapja](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kattintson a **eltávolítása** jelölje be az eltávolítási folyamat. A következő oldal jelenik meg.
   
    ![A telepítő varázsló megerősítő oldal](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kattintson a **eltávolítása** az eltávolítás megerősítéséhez. A folyamat következő oldal jelenik meg.
   
    ![A telepítő varázsló állapotlapja](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Az eltávolítás befejeződése után a Befejezés lapon jelenik meg. Kattintson a **Befejezés** a varázsló bezárásához.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Az adapter eltávolítása a Vezérlőpult segítségével
1. Nyissa meg a Vezérlőpultot, és kattintson **programok és szolgáltatások**.
2. Válassza ki **StorSimple Adapter for SharePoint**, és kattintson a **Eltávolítás**.

## <a name="next-steps"></a>További lépések
[További információ a StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
