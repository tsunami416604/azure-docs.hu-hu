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
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603604"
---
# <a name="azure-sql-database-serverless-preview"></a>Az Azure SQL Database kiszolgáló nélküli (előzetes verzió)

Az Azure SQL-adatbázis, kiszolgáló nélküli (előzetes verzió) a számítási kapacitás és önálló adatbázisokat, amelyek automatikusan méretezi a számítási feladatok igény szerint és a számítási mennyiségének számlák alapján számítási használt másodpercenként. A kiszolgáló nélküli számítási kapacitás is automatikusan felfüggeszti a adatbázisok inaktív időszakokban, amikor csak tárolási történik, és automatikusan folytatja az adatbázisok, amikor a tevékenység adja vissza.

## <a name="serverless-compute-tier"></a>Kiszolgáló nélküli számítási szint

A kiszolgáló nélküli számítási kapacitás egy önálló adatbázis egy számítási az automatikus skálázás tartomány- és a egy autopause késleltetés van paraméterezni.  Ezek a paraméterek konfigurációja formázhatja az adatbázis-teljesítmény biztosítása érdekében, és számítási költségeit tartalmazza.

![kiszolgáló nélküli számlázás](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Teljesítménykonfiguráció

- A **minimális virtuális magok** és **maximális virtuális magok** konfigurálható paraméterek, amelyek meghatározzák a számítási kapacitás az adatbázis tartományán. A memória és IO korlátok a megadott virtuális mag tartomány arányos.  
- A **autopause késleltetés** egy konfigurálható paraméter, amely meghatározza az az időtartam, az adatbázis kell inaktívnak lennie, mielőtt automatikusan fel van függesztve. Az adatbázis automatikusan folytatódik a következő bejelentkezéshez vagy a többi tevékenység esetén.  Másik lehetőségként autopausing letiltható.

### <a name="cost"></a>Költségek

- Egy kiszolgáló nélküli adatbázis költsége az összegzés a számítási költségeket és a tárolási költségeket.
- Ha a számítási feladatok használatáért a minimális és maximális korlát konfigurált között, a számítási költségeket alapul vCore- és memóriahasználatát.
- Ha a számítási feladatok használatáért alatt a min korlátok konfigurálva van, a számítási költségeket alapján a minimális virtuális maggal és minimális memóriával konfigurált.
- Ha az adatbázis szüneteltetve van, a számítási költségeket nulla, és csak a tárolási költségek akkor számítunk fel.
- A tárolási költségek a kiépített számítási kapacitás alkalmazott módon határozza meg.

Költség kapcsolatos további információkért lásd: [számlázási](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Forgatókönyvek

Kiszolgáló nélküli ár – teljesítmény arányt időszakos, előre nem látható használati minták, hogy megengedhet számítási bemelegítési némi késedelem után inaktív időszakai önálló adatbázisokat optimalizálva. A kiépített számítási szintje ezzel szemben az önálló adatbázisok vagy magasabb átlagos kihasználtsága, amely folyamatos internetkapcsolatot igénylő számítási bemelegítési az késleltetések rugalmas készletek több adatbázis optimalizált ár – teljesítmény arányt.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Kiválóan alkalmas a kiszolgáló nélküli számítási forgatókönyvek

- Önálló adatbázisokat időszakos, előre nem látható használati minták tétlen és alacsonyabb átlagos számítási használat idővel időszakokat jelentésekkel összekeveredett.
- A kiépített számítási kapacitás, amelyek gyakran átméretezése és az ügyfelek, akik inkább delegálása az önálló adatbázisok számítási átméretezésekor a szolgáltatáshoz.
- Ahol számítási méretezési bonyolult vagy nem lehetséges, ha meg szeretné becsülni az SQL Database üzembe helyezés előtt-e a használati előzmények nélküli új önálló adatbázisok.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Kiválóan alkalmas a kiépített számítási forgatókönyvek

- Önálló adatbázisokat több rendszeres, kiszámítható használati minták és magasabb átlagos számítási időben.
- Adatbázisok, amelyek működését zavarják a teljesítmény feláldozását származó több gyakori vágást memória, vagy a felfüggesztett állapotba autoresuming késleltetés.
- Az időszakos, előre nem látható használati mintákat, amelyek konszolidálhatók jobb ár-teljesítmény optimalizálása a rugalmas készletek több adatbázis.

## <a name="comparison-with-provisioned-compute-tier"></a>Összehasonlítás a kiépített számítási kapacitás

A következő táblázat összefoglalja a kiszolgáló nélküli számítási kapacitás és a kiépített számítási kapacitás közötti különbséget tenni:

| | **Kiszolgáló nélküli számítási** | **Kiépített számítási** |
|:---|:---|:---|
|**Adatbázis-felhasználási minta**| Az alsó átlagos időszakos, előre nem látható használati számítási időben. |  Magasabb átlagos az több normál használati minták számítási idő és a rugalmas készleteket használó több adatbázis kihasználtságát.|
| **Teljesítmény energiabefektetést igénylő felügyelet** |Alacsonyabb|Magasabb szintű|
|**Számítás méretezése**|Automatikus|Kézi|
|**COMPUTE válaszideje**|Alacsonyabb inaktív időszakokat követően|Azonnali|
|**A számlázás részletessége**|Másodpercenként|/ óra|

## <a name="purchasing-model-and-service-tier"></a>A modell és a szolgáltatási csomag vásárlása

Kiszolgáló nélküli SQL-adatbázis jelenleg csak az általános célú csomagban a virtuális mag modell megvásárlása az 5. generáció hardveren támogatott.

## <a name="autoscaling"></a>Automatikus skálázás

### <a name="scaling-responsiveness"></a>Méretezési válaszideje

Általánosságban elmondható kiszolgáló nélküli adatbázisok futnak egy gépen elegendő kapacitással megszakítás nélkül bármilyen mennyiségű számítási korlátok a magok adatbázisonkénti maximális értéke határozza meg a kért erőforrás igény kielégítéséhez. Néha előfordul terheléselosztási automatikusan történik, ha a gép nem tudott eleget tenni az erőforrásigény néhány percen belül. Például ha az erőforrásigény 4 virtuális magot kapnak, de csak 2 virtuális mag érhető el, majd igénybe vehet néhány perc terheléselosztás, mielőtt biztosított 4 virtuális magot kapnak. Az adatbázis online állapotban marad, során rövid ideig a művelet végén kivételével terheléselosztási, ha a kapcsolatok megszakadnak.

### <a name="memory-management"></a>Memória-kezelés

Memória a kiszolgáló nélküli adatbázisok felszabadul nagyobb gyakran kiépített számítási adatbázisok számára. Ez a probléma fontos a kiszolgáló nélküli csökkenthetők a költségek, és befolyásolhatja a teljesítményt.

#### <a name="cache-reclamation"></a>Gyorsítótár visszaigénylését

Memória az SQL-gyorsítótárból ellentétben a kiépített számítási adatbázisokat, felszabadul a kiszolgáló nélküli adatbázis amikor a Processzor vagy a gyorsítótár-kihasználtság alacsony.

- Gyorsítótár-kihasználtság alacsony minősül, amikor a legtöbb amelyek összméretén legutóbb használt gyorsítótár bejegyzések csökken a küszöbérték alá egy ideig.
- Gyorsítótár visszaigénylését akkor aktiválódik, amikor a célként megadott gyorsítótár mérete fokozatosan csökken az előző méretre töredékéért, és ha használat alacsony marad címek újraigénylési funkcióját csak továbbra is.
- Gyorsítótár visszaigénylését esetén a gyorsítótár-bejegyzéseket fürtből kiválasztásakor a szabályzat esetén ugyanaz a kiválasztási szabályzat meghajtóbetűjeleket kiépített számítási adatbázisok túl magas a rendelkezésre álló memória mennyisége.
- Alább a minimális memória korlátját szerint min virtuális maggal, amely konfigurálható a gyorsítótár méretét soha nem csökken.

A kiszolgáló nélküli és a kiépített számítási adatbázisokat, gyorsítótár-bejegyzéseket az összes rendelkezésre álló memória használata esetén kiüríthetők.

#### <a name="cache-hydration"></a>Gyorsítótár hidratálási

Az SQL-gyorsítótár lemezről beolvasott adatok, ugyanúgy és ugyanazt a sebességet, mint a kiépített adatbázisok nő. Ha az adatbázis foglalt, a gyorsítótár engedélyezett növelje a memória maximális korlátig korlátozás.

## <a name="autopausing-and-autoresuming"></a>Autopausing és autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing akkor aktiválódik, ha az alábbi feltételek mindegyike teljesül az autopause késleltetés időtartama:

- Munkamenetek száma = 0
- CPU = 0 felhasználó számítási feladatot a felhasználó készlet:

Lehetőség van autopausing letiltása, ha szükséges.

A következő szolgáltatások nem támogatják a autopausing.  Azt jelenti, ha az alábbi funkciók bármelyike használ, majd az adatbázis online állapotban marad, függetlenül az adatbázis inaktivitás időtartama:

- Georeplikáció (aktív georeplikációs és automatikus feladatátvételi csoportok).
- Hosszú távú megőrzésének (LTR).
- A sync-adatbázis az SQL data sync használt.

Autopausing ideiglenesen letiltja néhány szolgáltatás frissítést igénylő, az adatbázis online állapotban lennie központi telepítése során.  Ezekben az esetekben autopausing lesz engedélyezett újra a szolgáltatást a frissítés befejeződése után.

### <a name="autoresuming"></a>Autoresuming

Autoresuming akkor aktiválódik, ha az alábbi feltételek bármelyike igaz bármikor:

|Funkció|Autoresume eseményindító|
|---|---|
|Hitelesítés és engedélyezés|Bejelentkezés|
|Fenyegetések észlelése|Az adatbázis vagy a kiszolgáló szintjén fenyegetésészlelési beállításokat engedélyezése vagy letiltása.<br>Fenyegetésészlelési beállításokat az adatbázis vagy a kiszolgáló szintjén módosítani.|
|Adatfelderítés és besorolás|Hozzáadása, módosítása, törlése vagy bizalmassági címke megtekintése|
|Naplózás|Naplózási bejegyzések megtekintésére.<br>Frissítés és a naplózási házirend megtekintése.|
|Adatmaszkolás|Hozzáadása, módosítása, törlése vagy adatmaszkolási szabályok megtekintése|
|Transzparens adattitkosítás|Nézetállapot vagy a transzparens adattitkosítás állapotának|
|Adattár lekérdezés (teljesítmény)|Módosítása vagy megtekintése a query store-beállítások; az automatikus hangolás|
|Automatikus hangolási|Alkalmazás- és automatikus hangolási ajánlásokat, például az automatikus indexeléshez ellenőrzése|
|Adatbázis másolása|Hozzon létre adatbázist másolatként.<br>Exportálás BACPAC-fájlba.|
|Az SQL data Sync szolgáltatással|Eseményközpont és a tag-adatbázisok, amelyek a beállítható ütemezés szerint vagy manuálisan hajtja végre közötti szinkronizálás|
|Egyes adatbázis metaadatainak módosítása|Új adatbázis-címkék hozzáadása.<br>Max. virtuális magok, min virtuális magok vagy autopause késleltetés módosítása.|
|SQL Server Management Studio (SSMS)|18-as verzió SSMS használatával, és nyissa meg egy új lekérdezési ablak bármilyen adatbázishoz a kiszolgáló ugyanazon a kiszolgálón található automatikus szüneteltetve adatbázisokat folytatódik. Ez a viselkedés nem jelentkezik, ha a verzió 17.9.1 SSMS használatával az IntelliSense használatával – kikapcsolva.|

Autoresuming is fel néhány szolgáltatás frissítést igénylő, az adatbázis online állapotban lennie központi telepítése során.

### <a name="connectivity"></a>Kapcsolat

Ha egy kiszolgáló nélküli adatbázis fel van függesztve, majd az első bejelentkezés fog folytatni az adatbázis és arról, hogy az adatbázis nem érhető el, hibakód: 40613 hibát adhat vissza. Az adatbázis folytatása után a bejelentkezési kapcsolat létrehozásához meg kell ismételni. Adatbázis-kapcsolat újrapróbálkozási logikát ügyfelek nem kell módosítani.

### <a name="latency"></a>Késés

A késés autoresume és a egy kiszolgáló nélküli adatbázis autopause alapvetően autopause 1 perces autoresume és 1 – 10 percet sorrendjét.

## <a name="onboarding-into-serverless-compute-tier"></a>Kiszolgáló nélküli számítási kapacitás az előkészítés

Új adatbázis létrehozása és áthelyezése egy meglévő adatbázist, egy kiszolgáló nélküli számítási kapacitás, az új adatbázis létrehozása ugyanazon mintát követi, számítási kapacitás kiosztása, és a következő két lépésből áll.

1. Adja meg a szolgáltatási célkitűzésének neve. A szolgáltatási cél írja elő a szolgáltatási rétegben, hardver generációja és maximális virtuális maggal. Az alábbi táblázat a szolgáltatási cél beállításai láthatók:

   |Szolgáltatási célkitűzésének neve|Szolgáltatásszint|Hardver generációja|Max. virtuális magok|
   |---|---|---|---|
   |GP_S_Gen5_1|Általános rendeltetés|Gen5|1|
   |GP_S_Gen5_2|Általános rendeltetés|Gen5|2|
   |GP_S_Gen5_4|Általános rendeltetés|Gen5|4|

2. Szükség esetén adja meg a minimális virtuális maggal és autopause késleltetés módosíthatja az alapértelmezett értékeket. Az alábbi táblázat az elérhető értékeket a paraméterekhez.

   |Paraméter|Érték választási lehetőségek|Alapértelmezett érték|
   |---|---|---|---|
   |Minimális virtuális magok|Bármelyik {0.5-ös, 1, 2, 4} nem haladja meg a maximális virtuális magok|0,5 virtuális mag|
   |Autopause késleltetés|Minimum: 60 perc (1 óra)<br>Maximum: 10 080 perc (7 nap)<br>Lépésekben: 60 perc<br>Autopause letiltása: -1|60 perc|

> [!NOTE]
> T-SQL használatával helyezze át a létező adatbázis, kiszolgáló nélküli, vagy a számítási méret módosítása jelenleg nem támogatott, de az Azure Portalon vagy a Powershellen keresztül teheti meg.

### <a name="create-new-database-in-serverless-compute-tier"></a>Kiszolgáló nélküli számítási kapacitás az új adatbázis létrehozása 

#### <a name="use-azure-portal"></a>Az Azure Portal használata

Lásd: [a rövid útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>A PowerShell használata

Az alábbi példa létrehoz egy új adatbázist a kiszolgáló nélküli számítási rétegben.  Ebben a példában explicit módon megadja a minimális virtuális magok maximális virtuális maggal és autopause késleltetés.

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
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Kiépített számítási kapacitás, kiszolgáló nélküli számítási kapacitás adatbázis áthelyezése

#### <a name="use-powershell"></a>A PowerShell használata

Az alábbi példa áthelyez egy adatbázist a kiépített számítási kapacitás az a kiszolgáló nélküli számítási kapacitás. Ebben a példában explicit módon megadja a minimális virtuális magok maximális virtuális maggal és autopause késleltetés.

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
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Kiszolgáló nélküli számítási kapacitás kiépített számítási kapacitás az adatbázis áthelyezése

Egy kiszolgáló nélküli adatbázis ugyanúgy, mint a kiépített számítási adatbázis áthelyezése egy kiszolgáló nélküli számítási kapacitás, a mozgathatók a kiépített számítási kapacitás.

## <a name="modifying-serverless-configuration"></a>Kiszolgáló nélküli konfigurációjának módosítása

### <a name="maximum-vcores"></a>Virtuális magok maximális száma

#### <a name="use-powershell"></a>A PowerShell használata

A maximális virtuális magok módosítása használatával történik a [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsot a PowerShell használatával a `MaxVcore` argumentum.

### <a name="minimum-vcores"></a>Virtuális magok minimális száma

#### <a name="use-powershell"></a>A PowerShell használata

A minimális virtuális magok módosítása használatával történik a [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsot a PowerShell használatával a `MinVcore` argumentum.

### <a name="autopause-delay"></a>Autopause késleltetés

#### <a name="use-powershell"></a>A PowerShell használata

Autopause késleltetési idő módosítása használatával történik a [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) parancsot a PowerShell használatával a `AutoPauseDelayInMinutes` argumentum.

## <a name="monitoring"></a>Figyelés

### <a name="resources-used-and-billed"></a>Erőforrások használják, és a számlázás

Az erőforrások egy kiszolgáló nélküli adatbázis alkalmazáscsomag, az SQL-példány és a felhasználói erőforrás-készlet entitások vannak beágyazva.

#### <a name="app-package"></a>Alkalmazáscsomag

Az alkalmazáscsomag a külső a legtöbb erőforrást felügyeleti határ egy adatbázist, függetlenül attól, hogy az adatbázis a kiszolgáló nélküli vagy kiépített számítási szinten. Az alkalmazáscsomag tartalmazza az SQL-példányhoz, és külső szolgáltatások együttesen hatókörnek az SQL Database-adatbázis által használt összes felhasználó és a rendszer erőforrás. Külső szolgáltatások közé tartoznak az R és a teljes szöveges keresés. Az SQL-példány az összesített erőforrás-használat általában legnagyobb az alkalmazáscsomag között.

#### <a name="user-resource-pool"></a>Felhasználói erőforráskészlethez

A felhasználói erőforráskészlethez van a belső egy adatbázist, függetlenül attól, hogy az adatbázis a kiszolgáló nélküli vagy kiépített számítási szinten a legtöbb erőforrást felügyeleti határt. A felhasználói erőforrás készlet hatókörök Processzor- és IO: felhasználómennyiség kiszolgálására alkalmasak például a létrehozás és ALTER és DML lekérdezések DDL-lekérdezések által generált például válassza ki, BESZÚRÁSA, frissítése és törlése. Ezek a lekérdezések általában kihasználtsági belül az alkalmazáscsomag legtöbb jelentős hányadát képviselik.

### <a name="metrics"></a>Mérőszámok

Egy kiszolgáló nélküli adatbázis app package és a felhasználói készlet erőforrás-használat monitorozásával metrikáit az alábbi táblázatban láthatók:

|Entitás|Metrika|Leírás|Egység|
|---|---|---|---|
|Alkalmazáscsomag|app_cpu_percent|Virtuális magra jogosult, használja az alkalmazás az alkalmazás számára engedélyezett maximális virtuális magok viszonyított aránya.|Százalék|
|Alkalmazáscsomag|app_cpu_billed|A compute használatáért kell az alkalmazást a jelentési időszak során mennyisége. Ebben az időszakban fizetett összeg, amely az Ez a mérőszám és a virtuális mag egységár. <br><br>Ez a metrika az értékeket határozza meg a CPU maximális használt idővel összesíti, és a másodpercenként felhasznált memória. Ha a felhasznált mennyiség kisebb, mint a beállított kiépítette a min virtuális maggal és a minimális memória minimális mérete, kiépített minimálisan történik. Annak érdekében, hogy a számlázási célból memória összehasonlítás CPU, memória van normalizált egységekbe a virtuális magok által a memória mennyiségét rescaling 3 GB-tal GB / virtuális mag.|virtuális mag másodperc|
|Alkalmazáscsomag|app_memory_percent|Az alkalmazás számára engedélyezett maximális memória képest relatív az alkalmazás által használt memória aránya.|Százalék|
|Felhasználó-készlet|cpu_percent|Felhasználói számítási feladatok számára engedélyezett maximális virtuális magok viszonyított felhasználómennyiség kiszolgálására alkalmasak által használt virtuális magok százalékos értéke.|Százalék|
|Felhasználó-készlet|data_IO_percent|Százalékos adatok felhasználómennyiség kiszolgálására alkalmasak viszonyított IOPS maximális adatok által használt IOPS engedélyezett felhasználómennyiség kiszolgálására alkalmasak.|Százalék|
|Felhasználó-készlet|log_IO_percent|Napló százaléka MB/s felhasználómennyiség kiszolgálására alkalmasak viszonyított maximális log MB/s által használt engedélyezett felhasználómennyiség kiszolgálására alkalmasak.|Százalék|
|Felhasználó-készlet|workers_percent|Felhasználómennyiség kiszolgálására alkalmasak viszonyított felhasználói számítási feladatok számára engedélyezett maximális dolgozók által használt feldolgozók százalékos értéke.|Százalék|
|Felhasználó-készlet|sessions_percent|Felhasználómennyiség kiszolgálására alkalmasak viszonyított felhasználói munkaterhelés számára engedélyezett maximális munkamenetek által használt munkamenetek százaléka.|Százalék|

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

- **Számlázható erőforrás**: Processzor és memória
- **Számlázott összeg**: virtuális mag egységár * max (min virtuális magok, a használt virtuális magok, a minimális memória GB * 1/3 memória GB felhasználva * 1/3) 
- **A Számlázás gyakorisága**: Másodpercenként

A virtuális mag egységár száma másodpercenként virtuális mag díja. Tekintse meg a [Azure SQL Database díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/sql-database/single/) az adott egység díjak egy adott régióban.

A számítási számlázzuk a következő metrika tesz elérhetővé:

- **A metrika**: app_cpu_billed (virtuális mag másodperc)
- **Definíció**: maximális (min virtuális magok, a használt virtuális magok, a minimális memória GB * 1/3 memória felhasznált GB * 1/3)
- **Jelentések gyakoriságának**: Percenként

Ez a mennyiség másodpercenként kiszámítása és összesíti több mint 1 perce.

Fontolja meg egy 1 perces vCore- és max 4 virtuális magot kapnak a konfigurált kiszolgáló nélküli adatbázis.  Ez körülbelül 3 GB-os minimális memória és a 12 GB-os maximális memória felel meg.  Tegyük fel, hogy az automatikus szüneteltetési késleltetés értéke 6 óra és az adatbázis-munkaterhelés egy 24 órás időszak alatt az első 2 órában aktív és egyéb inaktív.    

Ebben az esetben az adatbázisban történik számítási és tárolási első 8 órán belül.  Annak ellenére, hogy az adatbázis követő második órában inaktív indítása, továbbra is számlázzuk számítási az ezt követő 6 órában alapján a minimális számítási kiépítve, miközben az adatbázis online állapotban.  Csak tárolási hátralevő részében az óra 24 órás időszak van számlázzuk, míg az adatbázis fel van függesztve.

Pontosabban a számítási számla ebben a példában a következőképpen alakul:

|Időintervallum|a másodpercenként felhasznált virtuális magok|A másodpercenként felhasznált GB|A számlázás dimenzió COMPUTE|Számlázható idő alatt virtuális mag másodperc|
|---|---|---|---|---|
|0:00-1:00|4|9|felhasznált virtuális magok|4 vCores * 3600 seconds = 14400 vCore seconds|
|1:00-2:00|1|12|Használt memória|12 GB * 1/3 * 3600 seconds = 14400 vCore seconds|
|2:00-8:00|0|0|Minimális memória kiosztása|3 GB * 1/3 * 21600 seconds = 21600 vCore seconds|
|8:00-24:00|0|0|Nincs számítás számlázzuk, míg fel van függesztve|0 virtuális mag másodperc|
|A számlázás 24 órán át összes virtuális mag másodperc||||másodperc 50400 virtuális mag|

Tegyük fel, hogy a szolgáltatás $0.000073/vCore/second számítási egység díja.  A számítási díjat számítunk fel a 24 órás időszak után válik a számítási egység ár és a virtuális mag másodpercben a számlázás a termék: $0.000073/vCore/second * 50400 virtuális mag másodperc $3.68 =

## <a name="available-regions"></a>Elérhető régiók

A kiszolgáló nélküli számítási kapacitás érhető el világszerte kivéve a következő régiókban: Kelet-ausztráliai közép-India, Kína, Észak-Kína, Dél-Franciaország, közép-India, Németország északkelet-Németország, Nyugat-Indiát, Korea déli régiója, Dél-Afrika nyugati régiója, Egyesült Királyság északi régiója, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója és USA nyugati középső RÉGIÓJA.

## <a name="next-steps"></a>További lépések

- Első lépésként lásd [a rövid útmutató: Önálló adatbázis létrehozása az Azure SQL Database az Azure portal használatával](sql-database-single-database-get-started.md).
- Erőforráskorlátok, lásd: [kiszolgáló nélküli számítási réteg erőforráskorlátok](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
