---
title: Bevezetés az Azure SQL Database időbeli Verziózású táblák használatába |} A Microsoft Docs
description: Ismerje meg, hogyan kezdheti el az Azure SQL Database időbeli Verziózású táblák használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 49491c5283ba16c5379c1115fae597bd7fd6ea19
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567128"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Bevezetés az Azure SQL Database időbeli Verziózású táblák használatába

Historikus táblák, amely lehetővé teszi, hogy nyomon követheti és elemezheti a teljes előzménylistáját, amikor az adatok saját kódolási munka nélkül módosítása az Azure SQL Database új programozhatóság szolgáltatásban érhetőek el. Historikus táblák megőrizni adatokat szorosan kapcsolódik a helyi idő, hogy a tárolt tények értelmezhetők, érvényes csak az adott időszakon belül. Ez a tulajdonság a Historikus táblák időalapú hatékony elemzési és származó adatok fejlődést szem előtt tartva teszi lehetővé.

## <a name="temporal-scenario"></a>A historikus forgatókönyv

Ez a cikk azt mutatja be, a lépések egy forgatókönyvet az időbeli Verziózású táblák használatához. Tegyük fel, hogy szeretné-e a felhasználói tevékenységek követése létre új webhely, amely még fejlesztés alatt teljesen új vagy egy meglévő webhely, amely a felhasználói tevékenység analytics bővítéséhez. Egyszerűsített példában feltételezzük, hogy megtekintett weblapok bizonyos idő alatt áll, amely a rögzített és a webhely adatbázisban, amely az Azure SQL Database felügyelt kell azt. A felhasználói tevékenység előzményadatok elemzése célja, hogy újra kell terveznie a webhelyet, és jobb felhasználói élményt nyújt a látogatók bemeneti adatok lekérése.

Ebben a forgatókönyvben az adatbázis-modell nagyon egyszerű – felhasználói tevékenység metrika egy egész szám adható mezővel jelölt **PageVisited**, és a felhasználói profil alapszintű információk mellett van rögzítve. Ezenkívül időalapú elemzéshez nyilvántartja minden felhasználónak, sorok sorozatát ahol minden egyes sora egy adott felhasználó egy adott időn belül meglátogatott oldalak számát jelöli.

![Séma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Szerencsére a nem kell minden erőfeszítés helyezni az alkalmazást, hogy a tevékenység adatainak kezelése. Az időbeli Verziózású táblák Ez a folyamat automatizált - webhely a tervezés során több időt az adatok elemzése, maga összpontosíthat teljes rugalmasságot biztosít. A következőket kell tennie dolog, hogy ellenőrizze, hogy **WebSiteInfo** tábla van konfigurálva, [historikus rendszerverzióval ellátott](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). A pontos lépések, hogy az időbeli Verziózású táblák ebben a forgatókönyvben az alábbiakban tekintheti át.

## <a name="step-1-configure-tables-as-temporal"></a>1. lépés: Historikus táblák konfigurálása
Attól függően, hogy kezdve az új fejlesztési vagy meglévő alkalmazás frissítéséhez fog historikus táblák létrehozása vagy módosíthatja a meglévőket historikus attribútumok hozzáadásával. Általános esetben az lehet, két vegyesen. Hajtsa végre ezeket a művelet használatával [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) vagy más Transact-SQL fejlesztési eszköz.

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Új tábla létrehozása
Az SSMS Object Explorerben helyi menüpont "Új rendszerverzióval ellátott tábla" használatával nyissa meg a Lekérdezésszerkesztő egy historikus tábla sablonparancsfájlt és a "Adjon meg értékeket a Sablonparaméterek" (Ctrl + Shift + M) segítségével a sablon feltöltése:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Az SSDT-ben válassza a "Historikus táblán (a rendszerverzióval ellátott)" sablon az adatbázis-projektet az új elemek hozzáadásakor. Amely Táblatervező megnyitásához, és engedélyezi, hogy egyszerűen adja meg a táblázat elrendezése:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Is létrehozhat historikus tábla közvetlenül, adja meg a Transact-SQL-utasításokat az alábbi példában látható módon. Vegye figyelembe, hogy a kötelező minden historikus tábla elemei az időtartam-definíciója, és a SYSTEM_VERSIONING záradékot egy másik felhasználói tábla, amely tárolni fogja a korábbi sor verziók hivatkozva:

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

A rendszerverzióval ellátott historikus tábla létrehozásakor a hozzájuk tartozó előzménytábla alapértelmezés szerint automatikusan jön létre. Az alapértelmezett előzménytábla lap tömörítés engedélyezése az az időszak (vége, start) oszlopban a fürtözött B-fa indexet tartalmaz. Ez a konfiguráció a legtöbb esetben az időbeli verziózású táblák használata esetén az optimális különösen [adatok naplózását](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Ez a konkrét esetben igyekszünk végre időalapú trendelemzés keresztül hosszabb adatok előzményeit és a nagyobb adatkészletek, tehát a tároló választás az előzménytábla fürtözött oszlopcentrikus index. Egy fürtözött oszlopcentrikus nagyon jó tömörítés és az elemzési lekérdezéseknél teljesítményt biztosít. Historikus táblák az indexek beállítása a jelenlegi és a historikus táblák teljesen önállóan rugalmasságot biztosítanak. 

> [!NOTE]
> Az Oszlopcentrikus indexek érhetők el a prémium szintű és a Standard szintű, S3 vagy újabb.
>

A következő parancsfájl bemutatja, hogyan módosítható előzménytáblán alapértelmezett indexet a fürtözött oszlopcentrikus:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Az előzménytábla egy alárendelt csomópont megjelenítése közben időbeli verziózású táblák az Object Explorerben, a könnyebb azonosítás, az adott ikon jelöli.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Az ALTER historikus tábla már meglévő
Nézzük terjed ki a alternatív forgatókönyvet, amelyben a WebsiteUserInfo tábla már létezik, de nem úgy tervezték, hogy a módosítások előzményei olvashatók. Ebben az esetben egyszerűen kiterjesztheti a meglévő tábla historikus, válik, az alábbi példában látható módon:

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

## <a name="step-2-run-your-workload-regularly"></a>2. lépés: Rendszeresen futtassa a számítási feladatok
A fő időbeli Verziózású táblák előnye, hogy nem kell módosítani, vagy módosítsa oly módon, amely a change tracking végrehajtani a webhely. Létrehozása után a Historikus táblák transzparens módon megőrizni sor korábbi verziók, minden alkalommal, amikor módosításokat végez az adatok. 

Ebben a konkrét esetben automatikus változáskövetés használatához, csak frissítsük oszlop **PagesVisited** minden alkalommal, amikor a felhasználó megszűnik munkamenet a webhelyen befejezése:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Fontos, és figyelje meg, hogy a frissítés lekérdezés nem kell tudnia a pontos időt a jelenlegi művelet történt, sem hogyan jövőbeli elemzések előzményadatok a rendszer megőrzi. Mindkét szempontból a az Azure SQL Database automatikusan kezeli. A következő ábra szemlélteti, hogyan előzményadatok minden frissítés a folyamatban.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>3. lépés: Hajtsa végre az előzményadatok elemzése
Most már engedélyezve van a historikus rendszerverzió, előzményadatok elemzése esetén távolabbi, csak egy lekérdezést. Ez a cikk néhány példát, amely gyakori helyzetek elemzés – minden részleteket ismerhet meg, különböző lehetőségek bevezetett biztosít a [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) záradékban.

A felső 10 felhasználóra megtekintett weblapok kezdődően egy órával ezelőtt történt száma alapján rendezve megtekintéséhez futtassa a lekérdezést:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Egyszerűen módosíthatja a lekérdezés elemzését kezdődően egy nappal ezelőtt, a hely látogatások egy hónappal ezelőtt, vagy a múltban bármikor kívánja.

Az előző napi alapvető statisztikai elemzés végrehajtásához használja a következő példát:

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

Keresse meg a tevékenységek egy adott felhasználó adott időn belül használja a TARTALMAZOTT IN záradék:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafikus Vizualizáció is historikus lekérdezések esetében különösen akkor hasznos, mivel meg lehet megjeleníteni a trendek és használati minták egy intuitív módon nagyon egyszerűen:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Folyamatosan fejlődő táblaséma
Általában fogja módosítani szeretné a historikus tábla sémáját használata közben is alkalmazások fejlesztéséhez. Ehhez egyszerűen futtassa az ALTER TABLE rendszeres utasítások és az Azure SQL Database megfelelően továbbterjeszti az előzménytábla módosításait. A következő parancsfájl bemutatja, hogyan adhat hozzá további követési attribútuma:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Ehhez hasonlóan módosíthatja oszlop definíciójában amíg aktív az alkalmazások és szolgáltatások:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Végül távolíthatja el egy oszlopot, amely már nem szükséges.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Másik megoldásként használhatja a legújabb [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) historikus tábla sémájának módosítása során kapcsolódik az adatbázishoz (online mód) vagy az adatbázis-projektje (kapcsolat nélküli módban) részeként.

## <a name="controlling-retention-of-historical-data"></a>Korábbi adatok megőrzése szabályozása
A rendszerverzióval ellátott historikus táblákon az előzménytábla növelhető az adatbázis mérete, amely több, mint tábláit. Egy nagy és egyre előzménytábla egy probléma miatt tiszta tárolási költségek, valamint a teljesítmény betartatásához is adó historikus lekérdezések válhat. Ezért fontos szempont a megtervezéséhez és kezeléséhez minden historikus tábla életciklusának fejlesztése a előzménytábla adatok kezeléséhez az adatmegőrzési házirend. Az Azure SQL Database a következő megközelítések a historikus tábla korábbi adatok kezelésére szolgáló rendelkezik:

* [Tábla particionálása](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Egyéni Tisztítószkript](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>További lépések
Historikus táblák részletes információkért tekintse meg [MSDN-dokumentáció](https://msdn.microsoft.com/library/dn935015.aspx).
Látogasson el a Channel 9 felvesszük Önnel egy [valódi ügyfelek historikus megvalósítási sikertörténete](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) , és tekintse meg a [historikus bemutató élő](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

