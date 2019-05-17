---
title: Az Azure SQL Database kiszolgáló nélküli (előzetes verzió) |} A Microsoft Docs
description: Ez a cikk ismerteti az új kiszolgáló nélküli számítási kapacitás, és összehasonlítja a meglévő üzembe helyezett számítási kapacitás
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/11/2019
ms.openlocfilehash: 7ab22a1d1b44327b28264ec5bd6ba0c44b1d65a7
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620150"
---
# <a name="sql-database-serverless-preview"></a>Az SQL Database kiszolgáló nélküli (előzetes verzió)

## <a name="what-is-the-serverless-compute-tier"></a>Mi az a kiszolgáló nélküli számítási kapacitás

Az SQL Database kiszolgáló nélküli (előzetes verzió) egy számítási kapacitás, amely a számlák összeg számítási másodpercalapú az önálló adatbázis által használt. Kiszolgáló nélküli ár – teljesítmény arányt időszakos, előre nem látható használati minták, hogy megengedhet számítási bemelegítési némi késedelem után inaktív időszakai önálló adatbázisokat optimalizálva.

Egy adatbázis a kiszolgáló nélküli számítási kapacitás a számítási tartomány képes használni, és a egy autopause késleltetés van paraméterezni.

![kiszolgáló nélküli számlázás](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Teljesítmény

- `MinVcore` és `MaxVcore` konfigurálható paraméterek, amelyek meghatározzák a számítási kapacitás az adatbázis tartományán. A memória és IO korlátok a megadott virtuális mag tartomány arányos.  
- Autopause késleltetési idő legyen a konfigurálható paraméter, amely meghatározza az az időtartam, az adatbázis kell inaktívnak lennie, mielőtt automatikusan fel van függesztve. Az adatbázis automatikusan folytatódik a következő bejelentkezéshez esetén.

### <a name="pricing"></a>Díjszabás

- Egy kiszolgáló nélküli adatbázis a teljes számlája az összegzés a számlázási számítási és tárolási számlája összegét.
Számítási erőforrások alapján számoljuk el a használt virtuális maggal és a másodpercenként felhasznált memória mennyisége.
- A számlázás a minimális számítási minimális virtuális maggal és a minimális memória alapul.
- Az adatbázis fel van függesztve, amíg csak díjszabása vonatkozik.

## <a name="scenarios"></a>Alkalmazási helyzetek

Kiszolgáló nélküli ár – teljesítmény arányt időszakos, előre nem látható használati minták, hogy megengedhet számítási bemelegítési némi késedelem után inaktív időszakai önálló adatbázisokat optimalizálva. A kiépített számítási szintje ezzel szemben az olyan önálló vagy készletezett adatbázisok esetén, amely folyamatos internetkapcsolatot igénylő számítási bemelegítési az késleltetések magasabb átlagos kihasználtsága optimalizált ár – teljesítmény arányt.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Kiválóan alkalmas a kiszolgáló nélküli számítási forgatókönyvek

- Az időszakos, előre nem látható használati minták időszakok tétlen jelentésekkel összekeveredett egyetlen adatbázist is előnyös ár megtakarítás másodpercenként felhasznált számítási mennyiségének számlázás alapján.
- Önálló adatbázisok erőforrásigény nehéz előre jelezni, és akik szívesebben számítási méretezése a szolgáltatásnak delegálni.
- A kiépített számítási kapacitás az önálló adatbázisok gyakran teljesítményszintek módosítása.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Kiválóan alkalmas a kiépített számítási forgatókönyvek

- Önálló adatbázisok az több normál és a további lényeges számítási időben.
- Adatbázisok, amelyek működését zavarják a teljesítmény feláldozását származó több gyakori vágást memória, vagy a felfüggesztett állapotba autoresuming késleltetés.
- Adatbázisok, amelyek egyetlen kiszolgáló konszolidálhatók és rugalmas készletek használata hatékonyabb ároptimalizálás időszakos, előre nem látható használati minták esetén.

## <a name="comparison-with-provisioned-compute-tier"></a>Összehasonlítás a kiépített számítási kapacitás

A következő táblázat összefoglalja a kiszolgáló nélküli számítási kapacitás és a kiépített számítási kapacitás közötti különbséget tenni:

| | **Kiszolgáló nélküli számítási** | **Kiépített számítási** |
|:---|:---|:---|
|**Tipikus használati eset**| Az inaktív időszakokat jelentésekkel összekeveredett időszakos, előre nem látható használati adatbázisok. | Adatbázisokat vagy rugalmas készletek több szokásainak.|
| **Teljesítmény energiabefektetést igénylő felügyelet** |Alacsonyabb|Jobb|
|**Számítás méretezése**|Automatikus|Manuális|
|**COMPUTE válaszideje**|Alacsonyabb inaktív időszakokat követően|Azonnali|
|**A számlázás részletessége**|Másodpercenként|/ óra|

## <a name="purchasing-model-and-service-tier"></a>A modell és a szolgáltatási csomag vásárlása

Kiszolgáló nélküli SQL-adatbázis jelenleg csak az általános célú csomagban a virtuális mag modell megvásárlása az 5. generáció hardveren támogatott.

## <a name="autoscaling"></a>Automatikus skálázás

### <a name="scaling-responsiveness"></a>Méretezési válaszideje

Általánosságban véve adatbázisok futnak egy gépen megszakítás nélkül bármekkora mennyiségű számítási által beállított korlátok a kért erőforrás igény kielégítéséhez elegendő kapacitással a `maxVcores` értéket. Néha előfordul terheléselosztási automatikusan történik, ha a gép nem tudott eleget tenni az erőforrásigény néhány percen belül. Az adatbázis online állapotban marad, során rövid ideig a művelet végén kivételével terheléselosztási, ha a kapcsolatok megszakadnak.

### <a name="memory-management"></a>Memória-kezelés

Memória a kiszolgáló nélküli adatbázisok felszabadul nagyobb gyakran kiépített adatbázisok számára. Ez a probléma fontos a kiszolgáló nélküli csökkenthetők a költségek. Memória az SQL-gyorsítótárból ellentétben a kiépített számítási felszabadul egy kiszolgáló nélküli adatbázis amikor a Processzor vagy a gyorsítótár-kihasználtság alacsony.

## <a name="autopause-and-autoresume"></a>Autopause és autoresume

### <a name="autopause"></a>Autopause

Autopause akkor aktiválódik, ha az alábbi feltételek mindegyike teljesül az autopause késleltetés időtartama:

- Munkamenetek száma = 0
- CPU = 0 (a felhasználó a felhasználói készletben futó számítási feladat)

Lehetőség van autopause letiltása, ha szükséges.

### <a name="autoresume"></a>Autoresume

Autoresume akkor aktiválódik, ha az alábbi feltételek bármelyike igaz bármikor:

|Szolgáltatás|Autoresume eseményindító|
|---|---|
|Hitelesítés és engedélyezés|Bejelentkezés|
|Veszélyforrások detektálása|Az adatbázis vagy a kiszolgáló szintjén fenyegetésészlelési beállításokat engedélyezése vagy letiltása<br>Az adatbázis vagy a kiszolgáló szintjén fenyegetésészlelési beállításokat módosítása|
|Adatfelderítés és besorolás|Hozzáadása, módosítása, törlése vagy bizalmassági címke megtekintése|
|Naplózás|Naplózási bejegyzések megtekintésére.<br>Frissítés és a naplózási házirend megtekintése|
|Adatmaszkolás|Hozzáadása, módosítása, törlése vagy adatmaszkolási szabályok megtekintése|
|Transzparens adattitkosítás|Nézetállapot vagy a transzparens adattitkosítás állapotának|
|Adattár lekérdezés (teljesítmény)|Módosítása vagy megtekintése a query store-beállítások; az automatikus hangolás|
|Automatikus hangolási|Alkalmazás- és automatikus hangolási ajánlásokat, például az automatikus indexeléshez ellenőrzése|
|Adatbázis másolása|Másolatként adatbázis létrehozása<br>Exportálás BACPAC-fájlba|
|Az SQL data Sync szolgáltatással|Eseményközpont és a tag-adatbázisok, amelyek a beállítható ütemezés szerint vagy manuálisan hajtja végre közötti szinkronizálás|
|Egyes adatbázis metaadatainak módosítása|Új adatbázis-címkék hozzáadása<br>Változó maximális virtuális maggal, min virtuális maggal, autopause késleltetés|
|SQL Server Management Studio (SSMS)|18-as verzió SSMS használatával, és nyissa meg egy új lekérdezési ablak bármilyen adatbázishoz a kiszolgáló ugyanazon a kiszolgálón található automatikus szüneteltetve adatbázisokat folytatódik. Ez a viselkedés nem jelentkezik, ha a verzió 17.9.1 SSMS használatával az IntelliSense használatával – kikapcsolva.|

### <a name="connectivity"></a>Hálózati kapcsolat

Ha egy kiszolgáló nélküli adatbázis fel van függesztve, majd az első bejelentkezés fog folytatni az adatbázis és arról, hogy az adatbázis nem érhető el, hibakód: 40613 hibát adhat vissza. Az adatbázis folytatása után a bejelentkezési kapcsolat létrehozásához meg kell ismételni. Adatbázis-kapcsolat újrapróbálkozási logikát ügyfelek nem kell módosítani.

### <a name="latency"></a>Késés

Autopause vagy egy kiszolgáló nélküli adatbázis autoresume a késés általában 1 perces sorrendjéről van.

### <a name="feature-support"></a>Funkciók támogatása

A következő szolgáltatások nem támogatják a autopausing és autoresuming. Azt jelenti, ha az alábbi funkciók bármelyike használ, majd az adatbázis online állapotban marad, függetlenül az adatbázis inaktivitás időtartama:

- Georeplikáció (aktív georeplikációs és automatikus feladatátvételi csoportok)
- Hosszú távú megőrzésének (LTR)
- A sync-adatbázis az SQL data sync használt.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Az előkészítési be a kiszolgáló nélküli számítási kapacitás

Új adatbázis létrehozása és áthelyezése egy meglévő adatbázist, egy kiszolgáló nélküli számítási kapacitás, az új adatbázis létrehozása ugyanazon mintát követi, számítási kapacitás kiosztása, és a következő két lépésből áll:

1. Adja meg a szolgáltatási célkitűzésének neve. A szolgáltatási cél írja elő a szolgáltatási rétegben, hardver generációja és maximális virtuális maggal. Az alábbi táblázat a szolgáltatási cél beállításai láthatók:

   |Szolgáltatási célkitűzésének neve|Szolgáltatásszint|Hardver generációja|Virtuális magok max. száma|
   |---|---|---|---|
   |GP_S_Gen5_1|Általános célú|Gen5|1.|
   |GP_S_Gen5_2|Általános célú|Gen5|2|
   |GP_S_Gen5_4|Általános célú|Gen5|4|

2. Szükség esetén adja meg a minimális virtuális maggal és autopause késleltetési módosíthatja az alapértelmezett értékeket. Az alábbi táblázat az elérhető értékeket a paraméterekhez.

   |Paraméter|Érték választási lehetőségek|Alapértelmezett érték|
   |---|---|---|---|
   |Virtuális magok minimális száma|Bármelyik {0.5-ös, 1, 2, 4} nem haladja meg a maximális virtuális magok|0,5 virtuális mag|
   |Autopause késleltetés|Min: 360 perc (6 óra)<br>Max: 10 080 perc (7 nap)<br>Lépésekben: 60 perc<br>Autopause letiltása: -1|360 minutes|

> [!NOTE]
> T-SQL használatával helyezze át a létező adatbázis, kiszolgáló nélküli, vagy a számítási méret módosítása jelenleg nem támogatott, de az Azure Portalon vagy a Powershellen keresztül teheti meg.

### <a name="create-new-database-using-the-azure-portal"></a>Hozzon létre új adatbázist az Azure portal használatával

Lásd: [a rövid útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Hozzon létre új adatbázist a PowerShell használatával

Az alábbi példa létrehoz egy új adatbázist a kiszolgáló nélküli számítási rétegben határozzák meg az alapértelmezett értékekkel, a min virtuális maggal és autopause késedelem nevű GP_S_Gen5_4 szolgáltatási célt.

Kiszolgáló nélküli, mint a jelenleg a katalógusban, ezért futtassa PowerShell újabb verziója szükséges `Update-Module Az.Sql` beolvasni a kiszolgáló nélküli engedélyezve van a legújabb parancsmagok.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>A kiszolgáló nélküli számítási kapacitás helyezhetik át meglévő adatbázis

Az alábbi példa áthelyez egy meglévő önálló adatbázis a kiépített számítási kapacitás az a kiszolgáló nélküli számítási kapacitás. Ebben a példában explicit módon megadja a minimális virtuális magok maximális virtuális maggal és autopause késleltetés.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Ki a kiszolgáló nélküli számítási kapacitás-adatbázis áthelyezése

Egy kiszolgáló nélküli adatbázis ugyanúgy, mint a kiépített számítási adatbázis áthelyezése egy kiszolgáló nélküli számítási kapacitás, a mozgathatók a kiépített számítási kapacitás.

## <a name="modify-serverless-configuration-parameters"></a>Kiszolgáló nélküli konfigurációs paraméterek módosítása

### <a name="maximum-vcores"></a>Virtuális magok maximális száma

A maximális virtuális magok módosítása használatával történik a [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsot a PowerShell használatával a `MaxVcore` argumentum.

### <a name="minimum-vcores"></a>Virtuális magok minimális száma

A minimális virtuális magok módosítása használatával történik a [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsot a PowerShell használatával a `MinVcore` argumentum.

### <a name="autopause-delay"></a>Autopause késleltetés

Autopause késleltetési idő módosítása használatával történik a [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsot a PowerShell használatával a `AutoPauseDelay` argumentum.

## <a name="monitor-serverless-database"></a>Kiszolgáló nélküli adatbázis figyelése

### <a name="resources-used-and-billed"></a>Erőforrások használják, és a számlázás

A kiszolgáló nélküli adatbázis erőforrások vannak ágyazva szerint a következő entitásokat:

#### <a name="app-package"></a>Alkalmazáscsomag

Az alkalmazáscsomag a külső a legtöbb erőforrást felügyeleti határ egy adatbázist, függetlenül attól, hogy az adatbázis a kiszolgáló nélküli vagy kiépített számítási szinten. Az alkalmazáscsomag tartalmazza az SQL-példányhoz, és külső szolgáltatások együttesen hatókörnek az SQL Database-adatbázis által használt összes felhasználó és a rendszer erőforrás. Külső szolgáltatások közé tartoznak az R és a teljes szöveges keresés. Az SQL-példány az összesített erőforrás-használat általában legnagyobb az alkalmazáscsomag között.

#### <a name="user-resource-pool"></a>Felhasználói erőforráskészlethez

A felhasználói erőforráskészlethez van a belső egy adatbázist, függetlenül attól, hogy az adatbázis a kiszolgáló nélküli vagy kiépített számítási szinten a legtöbb erőforrást felügyeleti határt. A felhasználói erőforráskészlethez hatókörök Processzor- és i/o DDL-lekérdezések (például CREATE, ALTER, stb.) és a DML-lekérdezések (például, SELECT, INSERT, UPDATE, DELETE, stb.) által létrehozott felhasználó számítási feladatokhoz. Ezek a lekérdezések általában kihasználtsági belül az alkalmazáscsomag legtöbb jelentős hányadát képviselik.

### <a name="metrics"></a>Mérőszámok

|Entitás|Metrika|Leírás|Mértékegységek|
|---|---|---|---|
|Alkalmazáscsomag|app_cpu_percent|Virtuális magra jogosult, használja az alkalmazás az alkalmazás számára engedélyezett maximális virtuális magok viszonyított aránya.|Százalék|
|Alkalmazáscsomag|app_cpu_billed|A compute használatáért kell az alkalmazást a jelentési időszak során mennyisége. Ebben az időszakban fizetett összeg, amely az Ez a mérőszám és a virtuális mag egységár. <br><br>Ez a metrika az értékeket határozza meg a CPU maximális használt idővel összesíti, és a másodpercenként felhasznált memória. Ha a felhasznált mennyiség kisebb, mint a beállított kiépítette a min virtuális maggal és a minimális memória minimális mérete, kiépített minimálisan történik. Annak érdekében, hogy a számlázási célból memória összehasonlítás CPU, memória van normalizált egységekbe a virtuális magok által a memória mennyiségét rescaling 3 GB-tal GB / virtuális mag.|virtuális mag másodperc|
|Alkalmazáscsomag|app_memory_percent|Az alkalmazás számára engedélyezett maximális memória képest relatív az alkalmazás által használt memória aránya.|Százalék|
|Felhasználó-készlet|cpu_percent|Felhasználói számítási feladatok számára engedélyezett maximális virtuális magok viszonyított felhasználómennyiség kiszolgálására alkalmasak által használt virtuális magok százalékos értéke.|Százalék|
|Felhasználó-készlet|data_IO_percent|Százalékos adatok felhasználómennyiség kiszolgálására alkalmasak viszonyított IOPS maximális adatok által használt IOPS engedélyezett felhasználómennyiség kiszolgálására alkalmasak.|Százalék|
|Felhasználó-készlet|log_IO_percent|Napló százaléka MB/s felhasználómennyiség kiszolgálására alkalmasak viszonyított maximális log MB/s által használt engedélyezett felhasználómennyiség kiszolgálására alkalmasak.|Százalék|
|Felhasználó-készlet|workers_percent|Felhasználómennyiség kiszolgálására alkalmasak viszonyított felhasználói számítási feladatok számára engedélyezett maximális dolgozók által használt feldolgozók százalékos értéke.|Százalék|
|Felhasználó-készlet|sessions_percent|Felhasználómennyiség kiszolgálására alkalmasak viszonyított felhasználói munkaterhelés számára engedélyezett maximális munkamenetek által használt munkamenetek százaléka.|Százalék|
____

> [!NOTE]
> Metrikák az Azure Portalon érhetők el az adatbázis panel alatt egyetlen adatbázishoz **figyelés**.

### <a name="pause-and-resume-status"></a>Szünet és folytatás állapota

Az Azure Portalon az adatbázis állapotát, amely felsorolja a benne található adatbázisok a kiszolgáló áttekintés ablaktábláján jelennek meg. Az adatbázis állapota is megjelenik az adatbázis áttekintő panelje.

A következő PowerShell paranccsal lekérdezése a felfüggesztés és a egy adatbázis állapotát folytathatja:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Erőforráskorlátok

Erőforráskorlátok, lásd: [kiszolgáló nélküli számítási kapacitás](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Számlázás

A számlázás a számítási CPU-kihasználtság és a másodpercenként felhasznált memória. Használt memória a kisebb, mint az egyes üzembe helyezett minimálisan és a Processzor mennyisége használják, majd a kiépített összeg számoljuk fel. Annak érdekében, hogy a számlázási célból memória összehasonlítás CPU, memória van normalizált egységekbe a virtuális magok által a memória mennyiségét rescaling 3 GB-tal GB / virtuális mag.

- **Számlázható erőforrás**: CPU és memória
- **Összeg ($) díjszabása**: virtuális mag egységár * maximális (min virtuális magok, a használt virtuális magok, a minimális memória GB * 1/3 memória GB felhasználva * 1/3) 
- **A Számlázás gyakorisága**: Másodpercenként

A virtuális mag egységár száma másodpercenként virtuális mag díja. Tekintse meg a [Azure SQL Database díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/sql-database/single/) az adott egység díjak egy adott régióban.

A számítási számlázzuk a következő metrika tesz elérhetővé:

- **A metrika**: app_cpu_billed (virtuális mag másodperc)
- **Definíció**: maximális (min virtuális magok, a használt virtuális magok, a minimális memória GB * 1/3 memória felhasznált GB * 1/3)
- **Jelentések gyakoriságának**: Percenként

Ez a mennyiség másodpercenként kiszámítása és összesíti több mint 1 perce.

**Példa**: Fontolja meg egy adatbázist a következő használati egy egy órás időszakban GP_S_Gen5_4 használata:

|Idő (óra: perc)|app_cpu_billed (virtuális mag másodperc)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||Összesen: 1631|

Tegyük fel, hogy a szolgáltatás $0.000073/vCore/second számítási egység díja. Ezután a számítási, a számlázás az adott egy órás időszakra vonatkozó határozza meg az alábbi képlettel: **$0.000073/vCore/second * 1631 virtuális mag másodperc $0.1191 =**

## <a name="available-regions"></a>Elérhető régiók

A kiszolgáló nélküli számítási kapacitás régiót kivéve az alábbi régiókban érhető el: Kelet-ausztráliai közép-India, Kína, Észak-Kína, Dél-Franciaország, közép-India, Németország északkelet-Németország, Nyugat-Indiát, Korea déli régiója, Dél-Afrika nyugati régiója, Egyesült Királyság északi régiója, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója és USA nyugati középső RÉGIÓJA

## <a name="next-steps"></a>További lépések

- Első lépésként lásd [a rövid útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával](sql-database-single-database-get-started.md).
- Erőforráskorlátok, lásd: [kiszolgáló nélküli számítási réteg erőforráskorlátok](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
