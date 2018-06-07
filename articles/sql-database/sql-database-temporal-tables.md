---
title: Ismerkedés az Azure SQL Database ideiglenes táblák |} Microsoft Docs
description: Ismerkedés az ideiglenes táblák használata az Azure SQL Database útmutató.
services: sql-database
author: bonova
ms.date: 03/21/2018
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.author: bonova
ms.openlocfilehash: 140d2c9f6c334cec7d2761d05d7b20eb7106b9fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649040"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Ismerkedés az Azure SQL Database ideiglenes táblák
A historikus táblák egy új programozhatóság Azure SQL-adatbázis, amely lehetővé teszi, hogy nyomon követheti és elemezheti az adatokat, nincs szükség egyéni kódolási változásai teljes előzményeit. A historikus táblák tartsa szorosan kapcsolódó idő a környezetben, hogy a tárolt tények értelmezhető adatok szerint érvényes csak a meghatározott időszakon belül. Ez a tulajdonság a Historikus táblák hatékony időalapú elemzés és beolvasásakor információkat kaphat a adatok alakulása lehetővé teszi.

## <a name="temporal-scenario"></a>A historikus forgatókönyv
Ez a cikk egy alkalmazás-forgatókönyvben a Historikus táblák magukat, hogy a lépéseket mutatja be. Tegyük fel, hogy szeretné-e nyomon követheti a felhasználói tevékenység teljesen új kifejlesztő új webhely, vagy egy meglévő webhely, amelyet a felhasználói tevékenység Analytics bővítéséhez. Egyszerűsített példában feltételezzük, hogy a számos meglátogatott weblapot során egy adott időn belül-e azt jelzi, hogy a rögzített és a webhely-adatbázisban, amely az Azure SQL Database figyelni kell. A felhasználói tevékenység korábbi elemzés célja webhely Újratervezés és jobb élményt nyújt a látogatók bemeneti adatok eléréséhez.

Ebben a forgatókönyvben az adatbázismodell nagyon egyszerű – felhasználói tevékenység metrika képviselt egyetlen egész mezőt, **PageVisited**, és a felhasználói profil alapszintű információk mellett lesz rögzítve. Emellett időpontokat elemzéshez tartja minden felhasználó esetében sorok sorozatát ahol minden sor jelenti-e egy adott felhasználó meglátogat egy adott időszakon belül lapok száma.

![Séma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Szerencsére nem kell minden erőfeszítést be az alkalmazásnak, hogy a tevékenység adatainak kezelése. A Historikus táblák Ez a folyamat automatizált - felkínálva teljesen rugalmasan webhely a tervezés során több időt az adatok elemzése maga összpontosíthat. Meg kell nyitnia egyedül ahhoz, hogy **WebSiteInfo** tábla van konfigurálva, [historikus rendszerverzióval ellátott](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). A pontos lépések az ebben a forgatókönyvben a Historikus táblák magukat, hogy az alábbiakban található.

## <a name="step-1-configure-tables-as-temporal"></a>1. lépés: Konfigurálja táblák historikus
Attól függően, hogy vannak indítása az új fejlesztési vagy meglévő alkalmazás frissítését fog létrehozni az ideiglenes táblák vagy módosíthatja a meglévőket historikus attribútumok hozzáadásával. Általános esetben adott esetben lehet ezek két lehetőségek egy kombinációja. Hajtsa végre ezeket a művelet használatával [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS) [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) vagy bármilyen más Transact-SQL fejlesztési.

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Új tábla létrehozása
Az SSMS Object Explorerben környezetben menüelem "Új rendszerverzióval ellátott tábla" segítségével nyissa meg a lekérdezés-szerkesztő historikus tábla sablon parancsfájllal és "Adjon meg értékeket a sablon paraméterek" (Ctrl + Shift + M) a sablon feltöltéséhez:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Az SSDT "(a rendszerverzióval ellátott) Historikus tábla" sablont választ az adatbázis-projekthez új elemek hozzáadásakor. Nyissa meg a tábla designer, és lehetővé teszik a egyszerűen adja meg a table elrendezést:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Is historikus tábla létrehozása a Transact-SQL-utasítások közvetlenül, megadásával az alábbi példában látható módon. Ne feledje, hogy minden historikus tábla kötelező elemeit definíciójának és a SYSTEM_VERSIONING záradékot egy másik felhasználói tábla sor korábbi verziók tárolásához hivatkozást:

````
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
````

A rendszerverzióval ellátott historikus tábla létrehozásakor a rendszer automatikusan létrehoz a hozzá tartozó előzménytábla, az alapértelmezett konfigurációval. Az alapértelmezett előzménytábla időtartamoszlopok (kezdő célból) egy fürtözött B-fa index lap tömörítést engedélyezve tartalmaz. Ez a konfiguráció nincs optimális forgatókönyvek, amelyben az ideiglenes táblák használata esetén a legtöbb, különösen a [adatok naplózását](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Ebben az esetben a Microsoft célja végrehajtásához időalapú trendelemzés keresztül hosszabb adatok előzményeit, és a nagyobb adatkészletek esetében, az előzmények tábla tárolási választott nem fürtözött oszlopcentrikus index. A fürtözött oszlopcentrikus nagyon jó tömörítési elemzési lekérdezések teljesítményt nyújt. A historikus táblák a munkakörnyezetnek indexek teljesen független a jelenlegi és a historikus táblák konfigurálása. 

> [!NOTE]
> Oszlopcentrikus indexek állnak rendelkezésre, a prémium csomagban és a normál rétegben, S3 vagy újabb.
>

Az alábbi parancsfájl bemutatja, hogyan alapértelmezett index előzménytáblán módosítani lehet a fürtözött oszlopcentrikus:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

A historikus táblák az Object Explorer az adott ikon egyszerűbb azonosításához, jelennek meg a előzménytábla gyermekcsomópontja megjelenítése közben.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>A historikus a meglévő táblázat módosítása
Most a dokumentum ismerteti az alternatív forgatókönyv, amelyben a WebsiteUserInfo tábla már létezik, de nem úgy tervezték előzményekre változások. Ebben az esetben egyszerűen kiterjesztheti a meglévő tábla historikus, legyen, a következő példában látható módon:

````
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
````

## <a name="step-2-run-your-workload-regularly"></a>2. lépés: A számítási feladatok rendszeresen futtatása
A fő ideiglenes táblák előnye, hogy nem kell módosítani, vagy állítsa be a webhely bármely olyan módon, a változáskövetés végrehajtásához. Létrehozása után a Historikus táblák transzparens módon továbbra is fennáll korábbi sor minden alkalommal, amikor módosításokat végezni az adatokon. 

Ez a forgatókönyv az automatikus változáskövetése használatához most csak frissítése oszlop **PagesVisited** minden alkalommal, amikor ér véget a felhasználó a webhelyen megszűnik munkamenet:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Fontos figyelje meg, hogy a frissítés lekérdezést kell nem ismeri a pontos idő, amikor lépett fel a tényleges műveletet sem hogyan előzményadatait megőrzi további elemzés céljából. Mindkét szempont automatikusan kezeli az Azure SQL Database. A következő ábra bemutatja, hogyan előzményadatok minden frissítési folyamatban.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>3. lépés: Az előzményadatok elemzés végrehajtása
Most historikus rendszerverzió-engedélyezésekor a rendszer előzményadatokat elemzésre másik lapra, hogy csak egy lekérdezést. Ebben a cikkben néhány példa a gyakori forgatókönyvek analysis - összes részleteit, megismerkedhet a különböző lehetőségek bevezetett címet lesz elérhető a [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) záradékban.

A felső 10 felhasználók frissítésétől egy órával ezelőtt történt megtekintett weblapok száma alapján rendezve megtekintéséhez futtassa a lekérdezést:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Könnyen módosíthatja a lekérdezés a hely látogatások alkalmával, a következő időpontban időpontban egy hónapja elemzéséhez, vagy a múltban bármikor kívánja.

Az előző napi alapvető statisztikai elemzések elvégzéséhez használja a következő példát:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

A TARTALMAZOTT IN záradék használatával adott időn belül tevékenységek egy adott felhasználó keresése:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Grafikus képi megjelenítés lehetőség időalapú lekérdezéseknél különösen akkor hasznos, mivel Ön lehet megjeleníteni a trendek és használati szokások egy egyszerűen elsajátítható módja nagyon egyszerűen:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Folyamatosan változó a következő tábla sémáját
Általában akkor módosítása a historikus tábla sémáját, amíg az alkalmazásfejlesztés során. Az adott egyszerűen futtassa a rendszeres az ALTER TABLE utasítás és az Azure SQL Database megfelelően továbbterjeszti majd a előzménytábla módosításait. Az alábbi parancsfájl bemutatja, hogyan adhat meg további attribútum figyelését:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Módosíthatja hasonlóan oszlop definíciójában, amíg aktív a számítási feladatok:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Végezetül is eltávolíthat egy oszlopot, amely már nem szükséges.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

Másik megoldásként használhatja a legújabb [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) historikus tábla sémáját módosítása kapcsolódott az adatbázishoz (online mód) vagy az adatbázis-projekt (kapcsolat nélküli módban) részeként.

## <a name="controlling-retention-of-historical-data"></a>Korábbi adatok megőrzése vezérlése
A rendszerverzióval ellátott historikus táblákon a előzménytábla megnövelheti az adatbázis mérete, amely több, mint reguláris táblák. Egy nagy- és egyre előzménytábla válhat mindkét miatt tiszta tárolási költségek, valamint a teljesítmény előíró adó historikus lekérdezése hibát. Emiatt az adatmegőrzési házirend kezeléséhez a tábla adatainak fejlesztése tervezési és kezeléséért a minden historikus tábla fontos eleme. Az Azure SQL Database a historikus tábla előzményadatokat kezeléséhez az alábbi megközelítések közül választhat:

* [Táblaparticionálást](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Egyéni karbantartási parancsprogramot](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>További lépések
A Historikus táblák részletes információkért tekintse meg [az MSDN dokumentációjában tájékozódhat](https://msdn.microsoft.com/library/dn935015.aspx).
Látogasson el a Channel 9 hallani a [valós felhasználói historikus implementációs sikeres szövegegység](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) és figyelési egy [historikus bemutató élő](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

