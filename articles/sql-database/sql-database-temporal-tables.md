---
title: Időbeli táblázatok – első lépések
description: Megtudhatja, hogyan kezdheti el használni a temporális táblákat az Azure SQL Database-ben.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 98fd2658f3fbcb0e7e29114d29f8dc6ed39eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820727"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Időbeli táblák – első lépések az Azure SQL-adatbázisban

A historikus táblák az Azure SQL Database új programozhatósági szolgáltatása, amely lehetővé teszi az adatok változásainak teljes előzményeinek nyomon követését és elemzését anélkül, hogy egyéni kódolásra lenne szükség. Az időbeli táblázatok az időkörnyezethez szorosan kapcsolódó adatokat tárolnak, hogy a tárolt tények csak az adott időszakon belül értelmezhetők érvényesként. A Temporális táblázatok ezen tulajdonsága lehetővé teszi a hatékony időalapú elemzést és az adatok alakulásából származó elemzések megszerzését.

## <a name="temporal-scenario"></a>Időbeli forgatókönyv

Ez a cikk bemutatja a lépéseket, hogy használja a historikus táblák egy alkalmazásforgatókönyvben. Tegyük fel, hogy egy teljesen újonnan fejlesztett új webhelyen vagy egy meglévő webhelyen szeretné nyomon követni a felhasználói tevékenységet, amelyet a felhasználói tevékenység elemzésével szeretne kiterjeszteni. Ebben az egyszerűsített példában azt feltételezzük, hogy a meglátogatott weboldalak száma egy adott időszakban egy olyan mutató, amelyet rögzíteni kell, és figyelni kell a webhely adatbázisában, amely az Azure SQL Database üzemelteti. A felhasználói tevékenység történelmi elemzésének célja, hogy betáplálást kapjon a weboldal újratervezéséhez, és jobb élményt nyújtson a látogatóknak.

Az adatbázis-modell ebben a forgatókönyvben nagyon egyszerű - felhasználói tevékenység metrika képviseli egyetlen egész mező, **PageVisited**, és rögzíti az alapvető információkat a felhasználói profilt. Ezenkívül az időalapú elemzéshez minden felhasználó számára sorsort kell tartani, ahol minden sor egy adott felhasználó által egy adott időszakon belül meglátogatott oldalak számát jelöli.

![Séma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Szerencsére nem kell semmilyen erőfeszítést tennie az alkalmazásban a tevékenységi információk karbantartásához. A Temporális táblázatok, ez a folyamat automatizált - így teljes rugalmasságot során website design és több időt, hogy összpontosítson az adatok elemzése is. Az egyetlen dolog, amit meg kell tennie, hogy biztosítsa, hogy **a WebSiteInfo** tábla [időbeli rendszerverziójúként van konfigurálva.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0) Ebben a forgatókönyvben a temporális táblák pontos használata az alábbiakban ismertetjük.

## <a name="step-1-configure-tables-as-temporal"></a>1. lépés: A táblák időbeli beállítása
Attól függően, hogy új fejlesztést indít-e, vagy meglévő alkalmazást frissít, vagy időbeli attribútumok hozzáadásával időbeli táblákat hoz létre, vagy módosítja a meglévőket. Általában a forgatókönyv lehet egy mix e két lehetőség. Hajtsa végre ezeket a műveleteket az [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), az SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) vagy bármely más Transact-SQL fejlesztőeszközzel.

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Új tábla létrehozása
Az SSMS Object Explorer ben az "Új rendszerverziójú táblázat" helyi menüelem használatával nyissa meg a lekérdezésszerkesztőt egy hisspektustáblasablon-parancsfájllal, majd használja az "Értékek megadása sablonparaméterekhez" (Ctrl+Shift+M) parancsot a sablon feltöltéséhez:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Az SSDT-ben válassza a "Temporális tábla (rendszerverziószámozott)" sablont, amikor új elemeket ad hozzá az adatbázisprojekthez. Ekkor megnyílik a táblatervező, és lehetővé teszi a táblázat elrendezésének egyszerű megadását:

![SSDTÚjTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Időbeli táblát is létrehozhat a Transact-SQL utasítások közvetlen megadásával, ahogy az az alábbi példában látható. Vegye figyelembe, hogy minden időbeli tábla kötelező elemei a PERIOD definíció és a SYSTEM_VERSIONING záradék, amely egy másik felhasználói táblára hivatkozik, amely a korábbi sorverziókat tárolja:

```
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Rendszerverziójú histéka létrehozásakor a program automatikusan létrehozza az alapértelmezett konfigurációval rendelkező kísérő előzménytáblát. Az alapértelmezett előzménytábla fürtözött B-fa indexet tartalmaz az időszakoszlopokon (befejezés, indítás) és az oldaltömörítés engedélyezve van. Ez a konfiguráció optimális a legtöbb esetben, amelyben időbeli táblák at használnak, különösen [az adatok naplózása.](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0) 

Ebben a konkrét esetben arra törekszünk, hogy időalapú trendelemzést hajtsunk végre egy hosszabb adatelőzmények és nagyobb adatkészletek, így a tárolási választás az előzménytábla egy fürtözött oszlopcentrikus index. A fürtözött oszlopcentrikus nagyon jó tömörítést és teljesítményt nyújt az analitikus lekérdezésekhez. A historikus táblák rugalmasságot biztosítanak az aktuális és a historikus táblák indexei teljesen független konfigurálásához. 

> [!NOTE]
> Oszlopcentrikus indexek érhetők el a prémium szint és a standard szint, S3 és a fenti.
>

A következő parancsfájl bemutatja, hogyan módosítható az előzménytábla alapértelmezett indexe a fürtözött oszloptárra:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

A histéni táblák az Objektumkezelőben az adott ikonnal jelennek meg a könnyebb azonosítás érdekében, míg az előzménytábla gyermekcsomópontként jelenik meg.

![AlterTable tábla](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Meglévő tábla módosítása időbelire
Let's terjed ki az alternatív forgatókönyv, amelyben a WebsiteUserInfo tábla már létezik, de nem volt célja, hogy a történelem a változások. Ebben az esetben egyszerűen kiterjesztheti a meglévő táblát, hogy időbelivé váljon, ahogy az a következő példában látható:

```
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

## <a name="step-2-run-your-workload-regularly"></a>2. lépés: Rendszeresen futtassa a munkaterhelést
A temporális táblázatok fő előnye, hogy nem kell módosítania vagy módosítania a webhelyét bármilyen módon a változáskövetés végrehajtásához. Létrehozása után a historikus táblák transzparens módon megőrzik a korábbi sorverziókat minden alkalommal, amikor módosításokat hajt végre az adatokon. 

Annak érdekében, hogy kihasználja az automatikus változáskövetést ebben a konkrét forgatókönyvben, csak frissítsük a **PagesVisited oszlopot** minden alkalommal, amikor egy felhasználó befejezi a munkamenetet a webhelyen:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Fontos figyelembe venni, hogy a frissítő lekérdezés nem kell tudni a pontos időt, amikor a tényleges művelet történt, sem, hogy a korábbi adatok megőrzése a későbbi elemzés. Mindkét szempontot automatikusan kezeli az Azure SQL Database. Az alábbi ábra bemutatja, hogyan történik az előzményadatok létrehozása minden frissítéskor.

![Temporálisépítészet](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>3. lépés: Előzményadatok elemzése
Most, amikor a temporális rendszerverziózás engedélyezve van, a korábbi adatok elemzése csak egy lekérdezésre van. Ebben a cikkben néhány példát mutatunk be, amelyek a gyakori elemzési forgatókönyvekkel foglalkoznak - az összes részlet megismeréséhez, a [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) záradékkal bevezetett különböző lehetőségek feltárásához.

Ha egy órával ezelőtt a meglátogatott weblapok száma szerint rendelt 10 legjobb felhasználót szeretné megtekinteni, futtassa a következő lekérdezést:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Könnyedén módosíthatja ezt a lekérdezést, hogy elemezze a webhelylátogatásokat egy nappal ezelőtt, egy hónappal ezelőtt, vagy a múlt bármely pontján.

Az előző napi alapvető statisztikai elemzés elvégzéséhez használja a következő példát:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Egy adott felhasználó tevékenységeinek kereséséhez egy bizonyos időn belül használja a CONTAINED IN záradékot:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A grafikus megjelenítés különösen kényelmes a temporális lekérdezésekhez, mivel a trendeket és a használati mintákat intuitív módon, nagyon könnyen meg tudja mutatni:

![Időbeligráf](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Fejlődő táblaséma
Általában az alkalmazásfejlesztés közben módosítania kell a temporális tábla sémáját. Az, hogy egyszerűen futtassa a rendszeres ALTER TABLE utasítások és az Azure SQL Database megfelelően propagálja a módosításokat az előzménytáblában. A következő parancsfájl bemutatja, hogyan adhat hozzá további attribútumot a nyomon követéshez:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Hasonlóképpen módosíthatja az oszlopdefiníciót, amíg a munkaterhelés aktív:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Végül eltávolíthatja azt az oszlopot, amire már nincs szüksége.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Másik lehetőségként a legújabb [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) használatával módosíthatja a temporális táblasémát, miközben az adatbázishoz (online mód) vagy az adatbázis-projekt részeként (offline módban) csatlakozik.

## <a name="controlling-retention-of-historical-data"></a>A múltbeli adatok megőrzésének ellenőrzése
Rendszerverziójú histékkal az előzménytábla növelheti az adatbázis méretét, mint a normál táblák. Egy nagy és egyre növekvő előzménytábla válhat a probléma mind a tiszta tárolási költségek, valamint a teljesítményadó kivetése a temporális lekérdezése. Ezért az adatmegőrzési szabályzat kidolgozása az előzmények táblázatában lévő adatok kezelésére fontos szempont az egyes időbeli tábla életciklusának tervezéséhez és kezeléséhez. Az Azure SQL Database a következő módszerekkel kezelheti a historikus adatokat a histénikus táblában:

* [Táblaparticionálás](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Egyéni karbantartásparancsfájl](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>További lépések

- A temporális táblákról további információt a [Temporális táblázatok című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)
- Látogasson el a Channel 9 hallani egy [igazi ügyfél időbeli végrehajtási sikertörténet,](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) és nézni egy [élő temporális demonstráció](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

