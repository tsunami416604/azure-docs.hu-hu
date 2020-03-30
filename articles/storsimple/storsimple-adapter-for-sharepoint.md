---
title: StorSimple adapter telepítése a SharePointhoz | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként telepítheti és konfigurálhatja a SharePoint-alapú StorSimple adaptert egy SharePoint-kiszolgálófarmban.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930209"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>A SharePoint-alapú StorSimple adapter telepítése és konfigurálása
## <a name="overview"></a>Áttekintés
A SharePoint-alapú StorSimple adapter olyan összetevő, amely lehetővé teszi, hogy a Microsoft Azure StorSimple rugalmas tárhelyet és adatvédelmet biztosítson a SharePoint-kiszolgálófarmok számára. Az adapter segítségével áthelyezheti a binary large object (BLOB) tartalmat az SQL Server tartalom-adatbázisaiból a Microsoft Azure StorSimple hibrid felhőalapú tárolóeszközre.

A SharePoint-alapú StorSimple adapter távoli BLOB-tároló (RBS) szolgáltatóként működik, és az SQL Server távoli BLOB-tároló szolgáltatását használja a strukturálatlan SharePoint-tartalom tárolására (BLOB-k formájában) egy StorSimple-eszköz által támogatott fájlkiszolgálón.

> [!NOTE]
> A SharePoint StorSimple adaptere támogatja a SharePoint Server 2010 távoli BLOB Storage (RBS) szolgáltatást. Nem támogatja a SharePoint Server 2010 külső BLOB Storage (EBS) szolgáltatását.


* A SharePoint-alapú StorSimple adapter letöltéséhez nyissa meg a Microsoft letöltőközpontjában található [StorSimple Adapter for SharePoint][1] webhelyet.
* Az RBS- és RBS-korlátozások tervezéséről [a Döntés az RBS használata a SharePoint 2013-ban][2] vagy az [RBS-terv (SharePoint Server 2010)][3]című részben talál.

Az áttekintés további része röviden ismerteti a SharePoint-alapú StorSimple adapter szerepét, valamint azokat a SharePoint-kapacitás- és teljesítménykorlátokat, amelyekről az adapter telepítése és konfigurálása előtt tudnia kell. Az adatok áttekintése után nyissa meg [a StorSimple Adapter for SharePoint-telepítést](#storsimple-adapter-for-sharepoint-installation) az adapter beállításának megkezdéséhez.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple adapter a SharePoint előnyeihez
Egy SharePoint-webhelyen a tartalom strukturálatlan BLOB-adatként tárolódik egy vagy több tartalom-adatbázisban. Alapértelmezés szerint ezek az adatbázisok SQL Server rendszert futtató számítógépeken találhatók, és a SharePoint-kiszolgálófarmban találhatók. A BLOB-k gyorsan növelhetik a méretet, és nagy mennyiségű helyszíni tárhelyet fogyasztanak. Emiatt érdemes lehet egy másik, olcsóbb tárolási megoldást találni. Az SQL Server egy Távoli blobtároló (RBS) nevű technológiát biztosít, amely lehetővé teszi a BLOB-tartalom fájlrendszerben, az SQL Server adatbázison kívüli tárolását. Az RBS segítségével a BLOB-k az SQL Server rendszert futtató számítógép fájlrendszerében, vagy egy másik kiszolgálószámítógépen tárolhatók a fájlrendszerben.

Az RBS használatához rbs-szolgáltatót, például a SharePoint-alapú StorSimple adaptert kell használnia az RBS sharepoint-beli engedélyezéséhez. A SharePoint-alapú StorSimple adapter együttműködik az RBS-szel, így a BLOB-k at a Microsoft Azure StorSimple rendszer által biztonsági másolatot készítő kiszolgálóra helyezheti át. A Microsoft Azure StorSimple ezután a használat alapján helyileg vagy a felhőben tárolja a BLOB-adatokat. A nagyon aktív (általában Tier 1 vagy hot data) blob-k helyileg találhatók. Kevésbé aktív és archiválási adatok a felhőben. Miután engedélyezte az RBS-t egy tartalom-adatbázisban, a SharePointban létrehozott új BLOB-tartalom a StorSimple-eszközön lesz tárolva, nem pedig a tartalom-adatbázisban.

Az RBS Microsoft Azure StorSimple implementációja a következő előnyöket nyújtja:

* Ha a BLOB-tartalmat egy külön kiszolgálóra helyezi át, csökkentheti az SQL Server lekérdezési terhelését, ami javíthatja az SQL Server válaszképességét. 
* Az Azure StorSimple deduplikációt és tömörítést használ az adatméret csökkentéséhez.
* Az Azure StorSimple helyi és felhőbeli pillanatképek formájában nyújt adatvédelmet. Továbbá, ha magát az adatbázist a StorSimple eszközre helyezi, biztonsági másolatot kaphat a tartalom-adatbázisról és a BLOB-król együtt egy összeomlás-konzisztens módon. (A tartalom-adatbázis áthelyezése az eszközre csak a StorSimple 8000 sorozatú eszköz esetén támogatott. Ez a funkció nem támogatott az 5000-es vagy 7000-es sorozatban.)
* Az Azure StorSimple tartalmazza a vész-helyreállítási funkciókat, beleértve a feladatátvételt, a fájl- és kötet-helyreállítást (beleértve a teszt-helyreállítást is), valamint az adatok gyors visszaállítását.
* A SharePoint-tartalom elemszintű helyreállításához használhatja az adat-helyreállítási szoftvert, például a Kroll Ontrack PowerControls alkalmazást a BLOB-adatok StorSimple pillanatképeivel. (Ez az adat-helyreállítási szoftver egy külön vásárlás.)
* A SharePoint-alapú StorSimple adapter a SharePoint központi felügyeleti portáljához csatlakozik, így a teljes SharePoint-megoldás központi helyről kezelhető.

A BLOB-tartalom fájlrendszerbe való áthelyezése egyéb költségmegtakarítást és előnyöket is eredményezhet. Az RBS használata például csökkentheti a költséges Tier 1-es tárterület szükségességét, és mivel a tartalom-adatbázis zsugorítja, az RBS csökkentheti a SharePoint-kiszolgálófarmban szükséges adatbázisok számát. Azonban más tényezők, például az adatbázis méretkorlátai és a nem RBS-tartalom mennyisége is befolyásolhatja a tárolási követelményeket. Az RBS használatának költségeiről és előnyeiről a [Tervezés az RBS (SharePoint Foundation 2010)][4] és [az RBS használatának eldöntése a SharePoint 2013-ban][5]című témakörben talál további információt.

### <a name="capacity-and-performance-limits"></a>Kapacitás- és teljesítménykorlátok
Mielőtt fontolóra venné az RBS használatát a SharePoint-megoldásában, tisztában kell lennie a SharePoint Server 2010 és a SharePoint Server 2013 tesztelt teljesítmény- és kapacitáskorlátaival, valamint azzal, hogy ezek a korlátok hogyan viszonyulnak az elfogadható teljesítményhez. További információt a [Szoftverhatárok és A SharePoint 2013 korlátai című](https://technet.microsoft.com/library/cc262787.aspx)témakörben talál.

Az RBS konfigurálása előtt tekintse át az alábbiakat:

* Győződjön meg arról, hogy a tartalom teljes mérete (a tartalom-adatbázis mérete és a kapcsolódó külső BLOB-k mérete) nem haladja meg a SharePoint által támogatott RBS-méretkorlátot. Ez a korlát 200 GB. 
  
    **Tartalomadatbázis és BLOB-méret mérése**
  
  1. Futtassa ezt a lekérdezést a központi adminisztrációs wfe-n. Indítsa el a SharePoint Felügyeleti rendszerhéjat, majd írja be a következő Windows PowerShell parancsot a tartalom-adatbázisok méretének lefelvételéhez:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ez a lépés leteszi a tartalom-adatbázis méretét a lemezen.
  2. Futtassa az alábbi SQL-lekérdezések egyikét az SQL Management Stusa-ban az SQL-kiszolgáló mezőben az egyes tartalom-adatbázisokban, és adja hozzá az eredményt az 1.
     
     A SharePoint 2013 tartalomadatbázisaiban írja be a következőt:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     A SharePoint 2010 tartalomadatbázisaiban írja be a következőt:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ez a lépés megkapja a kivan vételett BLOB-k méretét.
* Azt javasoljuk, hogy tárolja az összes BLOB és adatbázis-tartalom helyileg a StorSimple eszközön. A StorSimple eszköz egy kétcsomópontos fürt a magas rendelkezésre állás érdekében. A tartalom-adatbázisok és blob-k storSimple eszközre helyezése magas rendelkezésre állást biztosít.
  
    A tartalom-adatbázis storSimple-eszközre való áthelyezéséhez használja a hagyományos SQL Server áttelepítési gyakorlati tanácsokat. Csak akkor helyezze át az adatbázist, ha az adatbázisból az összes BLOB-tartalom átlett helyezve a fájlmegosztásba az RBS-en keresztül. Ha úgy dönt, hogy áthelyezi a tartalom-adatbázist a StorSimple eszközre, azt javasoljuk, hogy elsődleges kötetként konfigurálja az eszközön lévő tartalom-adatbázis-tárolót.
* A Microsoft Azure StorSimple, ha rétegzett kötetek használata, nincs mód annak garantálására, hogy a StorSimple-eszközön helyileg tárolt tartalom nem lesz rétegzett a Microsoft Azure felhőalapú tárhelyén. Ezért azt javasoljuk, hogy a StorSimple helyileg rögzített kötetek együtt SharePoint RBS. Ez biztosítja, hogy az összes BLOB-tartalom helyileg megmaradjon a StorSimple eszközön, és ne kerüljön át a Microsoft Azure-ba.
* Ha nem tárolja a tartalom-adatbázisokat a StorSimple eszközön, használja a hagyományos SQL Server magas rendelkezésre állású ajánlott eljárásokat, amelyek támogatják az RBS-t. Az SQL Server fürtözése támogatja az RBS-t, míg az SQL Server tükrözése nem. 

> [!WARNING]
> Ha nem engedélyezte az RBS-t, nem javasoljuk a tartalom-adatbázis áthelyezését a StorSimple eszközre. Ez egy nem tesztelt konfiguráció.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple adapter a SharePoint telepítéséhez
A SharePoint-alapú StorSimple adapter telepítése előtt konfigurálnia kell a StorSimple eszközt, és meg kell győződnie arról, hogy a SharePoint-kiszolgálófarm és az SQL Server példányosításminden feltételnek megfelel. Ez az oktatóanyag ismerteti a konfigurációs követelményeket, valamint a SharePoint-alapú StorSimple adapter telepítésének és frissítésének eljárásait.

## <a name="configure-prerequisites"></a>Előfeltételek konfigurálása
A SharePoint-alapú StorSimple adapter telepítése előtt győződjön meg arról, hogy a StorSimple eszköz, a SharePoint-kiszolgálófarm és az SQL Server példányosításmegfelel az alábbi előfeltételeknek.

### <a name="system-requirements"></a>Rendszerkövetelmények
A SharePoint-hoz használható StorSimple adapter a következő hardverrel és szoftverrel működik:

* Támogatott operációs rendszer – Windows Server 2008 R2 SP1, Windows Server 2012 vagy Windows Server 2012 R2
* Támogatott SharePoint-verziók – SharePoint Server 2010 vagy SharePoint Server 2013
* Támogatott SQL Server verziók – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition vagy SQL Server 2012 Enterprise Edition
* Támogatott StorSimple eszközök – StorSimple 8000 sorozat, StorSimple 7000 sorozat, vagy StorSimple 5000 sorozat.

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple eszköz konfigurációs előfeltételei
A StorSimple eszköz egy blokk eszköz, és mint ilyen, szükség van egy fájlszerver, amelyen az adatok tárolhatók. Azt javasoljuk, hogy a SharePoint-farmon lévő kiszolgáló helyett ne egy meglévő kiszolgálót használjon. Ennek a fájlkiszolgálónak ugyanazon a helyi hálózaton (LAN) kell lennie, mint a tartalom-adatbázisokat tároló SQL Server számítógépnek.

> [!TIP]
> * Ha a SharePoint-farmot magas rendelkezésre állásra állítja be, akkor a fájlkiszolgálót is telepítenie kell magas rendelkezésre állás érdekében.
> * Ha nem tárolja a tartalom-adatbázist a StorSimple-eszközön, használja a hagyományos, magas rendelkezésre állású ajánlott eljárásokat, amelyek támogatják az RBS-t. Az SQL Server fürtözése támogatja az RBS-t, míg az SQL Server tükrözése nem. 


Győződjön meg arról, hogy a StorSimple-eszköz megfelelően van konfigurálva, és hogy a SharePoint-telepítés támogatásához szükséges kötetek konfigurálva vannak és elérhetők az SQL Server-számítógépről. Nyissa [meg a helyszíni StorSimple-eszköz üzembe helyezését,](storsimple-8000-deployment-walkthrough-u2.md) ha még nem telepítette és konfigurálta a StorSimple-eszközt. Jegyezze fel a StorSimple eszköz IP-címét; szüksége lesz rá a StorSimple adapter a SharePoint telepítéséhez.

Ezenkívül győződjön meg arról, hogy a BLOB kiszervezéshez használt kötet megfelel az alábbi követelményeknek:

* A kötetet 64 KB-os foglalási egységmérettel kell formázni.
* A webes előtérnek (WFE) és az alkalmazáskiszolgálóknak unci elérési úton keresztül kell tudniuk hozzáférni a kötethez.
* A SharePoint-kiszolgálófarmot úgy kell konfigurálni, hogy a kötetre írjon.

> [!NOTE]
> Az adapter telepítése és konfigurálása után az összes BLOB-kiszervezésnek át kell mennie a StorSimple eszközön (az eszköz bemutatja a köteteket az SQL Servernek, és kezeli a tárolási szinteket). A BLOB kiszervezéshez nem használhat más célokat.


Ha azt tervezi, hogy a StorSimple Snapshot Manager segítségével pillanatképeket készít a BLOB-ról és az adatbázis-adatokról, telepítse a StorSimple Snapshot Managert az adatbázis-kiszolgálóra, hogy az SQL Writer Service használatával implementálhassa a Windows kötet árnyékmásolat-szolgáltatását (VSS).

> [!IMPORTANT]
> A StorSimple Snapshot Manager nem támogatja a SharePoint VSS Writert, és nem tud alkalmazáskonzisztens pillanatképeket levenni a SharePoint-adatokról. SharePoint-forgatókönyv esetén a StorSimple Snapshot Manager csak összeomlás-konzisztens biztonsági mentéseket biztosít.


## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint-farm konfigurációs előfeltételei
Győződjön meg arról, hogy a SharePoint-kiszolgálófarm megfelelően van konfigurálva, az alábbiak szerint:

* Ellenőrizze, hogy a SharePoint-kiszolgálófarm kifogástalan állapotban van-e, és ellenőrizze az alábbiakat:
* A farmban regisztrált összes SharePoint WFE- és alkalmazáskiszolgáló fut, és abból a kiszolgálóból pingelhető, amelyre a SharePoint-alapú StorSimple adaptert telepíti.
* A SharePoint időzítő szolgáltatás (SPTimerV3 vagy SPTimerV4) minden WFE-kiszolgálón és alkalmazáskiszolgálón fut.
* Mind a SharePoint időzítő szolgáltatás, mind az IIS alkalmazáskészlet, amely alatt a SharePoint központi felügyeleti webhely fut, rendszergazdai jogosultságokkal rendelkezik.
* Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági környezete (IE ESC) le van tiltva. Az IE ESC letiltásához kövesse az alábbi lépéseket:
  
  1. Zárja be az Internet Explorer összes példányát.
  2. Indítsa el a Kiszolgálókezelőt.
  3. A bal oldali ablaktáblában kattintson a **Helyi kiszolgáló gombra.**
  4. A jobb oldali ablaktáblán, az **IE fokozott biztonsági konfiguráció ja**mellett kattintson a Be **gombra.**
  5. A **Rendszergazdák csoportban**kattintson a **Ki**gombra.
  6. Kattintson az **OK** gombra.

## <a name="remote-blob-storage-rbs-prerequisites"></a>A távoli BLOB-tároló (RBS) előfeltételei
Győződjön meg arról, hogy az SQL Server támogatott verzióját használja. Csak a következő verziók támogatottak és használhatók az RBS-ben:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

A BLOB-k csak azon köteteken lehet kiterjeszteni, amelyeket a StorSimple eszköz az SQL Server számára bemutat. A BLOB kiszervezéshez más célok nem támogatottak.

Ha elvégezte az összes előfeltétel-konfigurációs lépést, nyissa meg [a StorSimple adapter telepítése a SharePointhoz című témakört.](#install-the-storsimple-adapter-for-sharepoint)

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>A SharePoint-alapú StorSimple adapter telepítése
A SharePoint-alapú StorSimple adapter telepítéséhez kövesse az alábbi lépéseket. Ha újratelepíti a szoftvert, olvassa el [a SharePoint-alapú StorSimple adapter frissítése vagy újratelepítése](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)című témakört. A telepítéshez szükséges idő a SharePoint-kiszolgálófarmban található SharePoint-adatbázisok teljes számától függ.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>RBS konfigurálása
A SharePoint-hoz való StorSimple adapter telepítése után konfigurálja az RBS-t az alábbi eljárásnak.

> [!TIP]
> A SharePoint-alapú StorSimple adapter a SharePoint központi felügyelet lapjához csatlakozik, így az RBS engedélyezve van vagy letiltható a SharePoint-farm minden tartalom-adatbázisában. Az RBS engedélyezése vagy letiltása azonban a tartalom-adatbázisban az IIS alaphelyzetbe állítását eredményezi, amely a farm konfigurációjától függően egy pillanatra megzavarhatja a SharePoint webes előtér (WFE) elérhetőségét. (Az olyan tényezők, mint az előtér-alapú terheléselosztó használata, az aktuális kiszolgálói munkaterhelés és így tovább, korlátozhatják vagy megszüntethetik ezt a zavart.) A felhasználók védelme érdekében a megszakítások, azt javasoljuk, hogy engedélyezze vagy tiltsa rbs csak egy tervezett karbantartási időszakban.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Szemétgyűjtés konfigurálása
Amikor objektumokat töröl egy SharePoint-webhelyről, azok nem törlődnek automatikusan az RBS-tároló kötetéről. Ehelyett egy aszinkron, háttér-karbantartási program törli az árva BLOB-kat a fájltárolóból. A rendszergazdák ütemezhetik a folyamatot, hogy rendszeresidőközönként fussanak, vagy szükség esetén elindíthatják.

Ez a karbantartási program (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) automatikusan települ az összes SharePoint WFE- és alkalmazáskiszolgálóra az RBS engedélyezésekor. A program telepítése a következő helyen történik: *boot drive*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

A karbantartási program konfigurálásáról és használatáról az [RBS karbantartása a SharePoint Server 2013-ban][8]című témakörben talál további információt.

> [!IMPORTANT]
> Az RBS karbantartó program erőforrás-igényes. Ütemezze úgy, hogy csak a SharePoint-farmon végzett könnyű tevékenységek időszakában fusson.


### <a name="delete-orphaned-blobs-immediately"></a>Árva BLOB-k azonnali törlése
Ha azonnal törölnie kell az árva BLOB-kat, kövesse az alábbi utasításokat. Vegye figyelembe, hogy ezek az utasítások egy példa arra, hogyan lehet ezt elvégezni egy SharePoint 2013 környezetben a következő összetevőkkel:

* A tartalom-adatbázis neve WSS_Content.
* Az SQL Server neve SHRPT13-SQL12\SHRPT13.
* A webalkalmazás neve SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>A SharePoint-alapú StorSimple adapter frissítése vagy újratelepítése
Az alábbi eljárással frissítheti a SharePoint-kiszolgálót, majd újratelepítheti a SharePoint-alapú StorSimple adaptert, vagy egyszerűen frissítheti vagy újratelepítheti az adaptert egy meglévő SharePoint-kiszolgálófarmban.

> [!IMPORTANT]
> Mielőtt megpróbálna frissíteni a SharePoint-szoftvert és/vagy frissíteni vagy újratelepíteni szeretné a SharePoint-alapú StorSimple adaptert, olvassa el az alábbi információkat:
> 
> * Az RBS-en keresztül külső tárolóba áthelyezett fájlok csak az újratelepítés befejeződése és az RBS-szolgáltatás újbóli engedélyezése után lesznek elérhetők. A felhasználói hatás csökkentése érdekében hajtsa végre a frissítést vagy az újratelepítést a tervezett karbantartási időszakban.
> * A frissítéshez/újratelepítéshez szükséges idő a SharePoint-kiszolgálófarmban található SharePoint-adatbázisok teljes számától függően változhat.
> * A frissítés/újratelepítés befejezése után engedélyeznie kell az RBS-t a tartalom-adatbázisokhoz. További információt az [RBS konfigurálása](#configure-rbs) című témakörben talál.
> * Ha olyan SharePoint-farmhoz konfigurál ja az RBS-t, amely nagyon sok (200-nál nagyobb) adatbázissal rendelkezik, előfordulhat, hogy a **SharePoint Központi felügyelet** lapja időtúlssze a lapot. Ebben az esetben frissítse az oldalt. Ez nincs hatással a konfigurációs folyamatra.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>StorSimple adapter a SharePoint eltávolításához
Az alábbi eljárások bemutatják, hogyan helyezheti vissza a BLOB-kat az SQL Server tartalom-adatbázisaiba, majd hogyan távolíthatja el a SharePoint-alapú StorSimple adaptert. 

> [!IMPORTANT]
> Az adapterszoftver eltávolítása előtt vissza kell helyeznie a BLOB-kat a tartalom-adatbázisokba.


### <a name="before-you-begin"></a>Előkészületek
Gyűjtse össze a következő adatokat, mielőtt visszahelyezné az adatokat az SQL Server tartalom-adatbázisaiba, és megkezdené az adapter eltávolítási folyamatát:

* Az összes olyan adatbázis neve, amelyhez az RBS engedélyezve van
* A konfigurált BLOB-tároló UNC elérési útja

### <a name="move-the-blobs-back-to-the-content-databases"></a>A BLOB-k visszaáthelyezése a tartalom-adatbázisokba
A SharePoint-szoftverhez készült StorSimple adapter eltávolítása előtt vissza kell telepítenie az SQL Server tartalom-adatbázisaiba az összes külsőleg kitelepített BLOB-t. Ha megpróbálja eltávolítani a SharePoint-hoz való StorSimple adaptert, mielőtt az összes BLOB-t visszahelyezne a tartalom-adatbázisokba, a következő figyelmeztető üzenet jelenik meg.

![Figyelmeztető üzenet](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>A BLOB-k visszaáthelyezése a tartalom-adatbázisokba
1. Töltse le az egyes kiszervezett objektumokat.
2. Nyissa meg a **SharePoint központi felügyelet** lapját, és keresse meg a **Rendszerbeállítások gombot.**
3. Az **Azure StorSimple**csoportban kattintson **a StorSimple adapter konfigurálása**elemre.
4. A **StorSimple Adapter konfigurálása** lapon kattintson a **Letiltás** gombra a külső BLOB-tárolóból eltávolítani kívánt tartalom-adatbázisok alatt. 
5. Törölje az objektumokat a SharePointból, majd töltse fel őket újra.

Azt is megteheti, `RBS Migrate()` hogy a SharePoint részét képező Microsoft PowerShell-parancsmast használja. További információt a [Tartalom áttelepítése az RBS-be vagy az RBS-ből című](https://technet.microsoft.com/library/ff628255.aspx)témakörben talál.

Miután visszahelyezi a BLOB-kat a tartalom-adatbázisba, folytassa a következő lépéssel: [Az adapter eltávolítása](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Az adapter eltávolítása
Miután visszahelyezi a BLOB-kat az SQL Server tartalom-adatbázisaiba, az alábbi lehetőségek egyikével távolíthatja el a SharePoint-alapú StorSimple adaptert.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Az adapter eltávolításához a telepítőprogram használata
1. Rendszergazdai jogosultságokkal rendelkező fiókkal jelentkezzen be a webes előtér-kiszolgálóra.
2. Kattintson duplán a SharePoint-telepítőhöz való StorSimple adapterre. Elindul a Telepítő varázsló.
   
    ![Telepítő varázsló](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kattintson a **Tovább** gombra. Megjelenik a következő oldal.
   
    ![A telepítővarázsló eltávolítása lap](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Az **eltávolítási** folyamat kijelöléséhez kattintson az Eltávolítás gombra. Megjelenik a következő oldal.
   
    ![A telepítővarázsló megerősítő lapja](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. **Az eltávolítás** megerősítéséhez kattintson az Eltávolítás gombra. Megjelenik a következő folyamatjelző lap.
   
    ![A telepítő varázsló folyamatlapja](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Amikor az eltávolítás befejeződött, megjelenik a céloldal. A Telepítő varázsló bezárásához kattintson a **Befejezés** gombra.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Az adapter eltávolításához használja a Vezérlőpultot
1. Nyissa meg a Vezérlőpultot, és kattintson **a Programok és szolgáltatások parancsra.**
2. Válassza **a StorSimple Adapter for SharePoint**lehetőséget, majd kattintson az **Eltávolítás gombra.**

## <a name="next-steps"></a>További lépések
[További információ a StorSimple szolgáltatásról.](storsimple-overview.md)

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
