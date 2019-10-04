---
title: Első lépések időbeli táblázatokkal a Azure SQL Databaseban | Microsoft Docs
description: Megtudhatja, hogyan kezdheti el az ideiglenes táblázatok használatát a Azure SQL Databaseban.
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
ms.openlocfilehash: 39c19661a71a8b466aa6ff25be9e895189dfbfb3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566356"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Első lépések időbeli táblázatokkal Azure SQL Database

Az időbeli táblázatok a Azure SQL Database egy új programozható funkciója, amely lehetővé teszi az adatváltozások teljes előzményeinek nyomon követését és elemzését anélkül, hogy egyéni kódolásra lenne szükség. Az ideiglenes táblák az időkörnyezettel szoros kapcsolatban tartanak adatokat, így a tárolt tények csak a megadott időszakon belül érvényesek. Az időbeli táblázatok ezen tulajdonsága lehetővé teszi a hatékony időalapú elemzést, és az adatok evolúciójának beolvasását.

## <a name="temporal-scenario"></a>Időbeli forgatókönyv

Ez a cikk bemutatja az időbeli táblázatok alkalmazási forgatókönyvekben való felhasználásának lépéseit. Tegyük fel, hogy nyomon szeretné követni a felhasználói tevékenységet egy olyan új webhelyen, amelyet a rendszer teljesen kifejleszt, vagy egy meglévő webhelyre, amelyet a felhasználói tevékenység elemzésével kíván kiterjeszteni. Ebben az egyszerűsített példában feltételezzük, hogy a meglátogatott weblapok száma egy adott időszakban egy kijelző, amelyet a Azure SQL Database üzemeltetett webhely-adatbázisában kell rögzíteni és figyelni. A felhasználói tevékenység korábbi elemzésének célja, hogy beolvassa a webhelyre való áttervezéshez szükséges adatokat, és jobb élményt nyújtson a látogatóknak.

Az ehhez a forgatókönyvhöz tartozó adatbázis-modell nagyon egyszerű – a felhasználói tevékenység mérőszáma egyetlen egész mezőből áll, a **PageVisited**, és a felhasználói profil alapinformációi mellett rögzítve. Emellett az időalapú elemzések esetében az egyes felhasználók sorait is megtarthatja, ahol minden sor az adott időszakon belül egy adott felhasználó által meglátogatott lapok számát jelöli.

![Séma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Szerencsére nem kell semmilyen erőfeszítést megtenni az alkalmazásban a tevékenység adatainak fenntartásához. Az időbeli táblázatokkal ez a folyamat automatizálható, így teljes rugalmasságot biztosít a webhelyek tervezése során, és több időt is igénybe kell vennie az adatok elemzésére. Az egyetlen teendő, hogy gondoskodjon arról, hogy a **WebSiteInfo** -tábla időszakos [](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0)rendszerverzióként legyen konfigurálva. Az időbeli táblázatok ezen forgatókönyvben való felhasználásának pontos lépései alább olvashatók.

## <a name="step-1-configure-tables-as-temporal"></a>1\. lépés: Táblák beállítása időbeliként
Attól függően, hogy új fejlesztést vagy meglévő alkalmazást frissít, ideiglenes attribútumok hozzáadásával létrehozhat ideiglenes táblákat vagy módosíthatja a meglévőket is. Általánosságban elmondható, hogy a forgatókönyv a két lehetőség kombinációja lehet. Hajtsa végre ezeket a műveleteket a [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), a [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) vagy bármely más Transact-SQL fejlesztői eszköz használatával.

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Új tábla létrehozása
A SSMS Object Explorer helyi menüje "új rendszerverzióval ellátott tábla" elemére kattintva nyissa meg a lekérdezési szerkesztőt egy időbeli táblázat sablonjának használatával, majd használja az "adja meg a sablon paramétereinek értékét" (CTRL + SHIFT + M) a sablon feltöltéséhez:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

A SSDT elemnél válassza az "ideiglenes tábla (rendszerverzióval ellátott)" sablont, amikor új elemeket ad hozzá az adatbázis-projekthez. Ekkor megnyílik a Table Designer, és lehetővé teszi a táblázat elrendezésének egyszerű megadását:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

A Transact-SQL-utasítások közvetlen megadásával is létrehozhat ideiglenes táblázatot, ahogy az alábbi példában is látható. Vegye figyelembe, hogy minden időbeli táblázat kötelező elemei az időszak-definíciók és a SYSTEM_VERSIONING záradék, amely egy másik felhasználói táblára hivatkozik, amely a korábbi sorok verzióját fogja tárolni:

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

Rendszerverzióval ellátott ideiglenes tábla létrehozásakor a rendszer automatikusan létrehozza az alapértelmezett konfigurációval rendelkező kapcsolódó előzmények táblát. Az alapértelmezett előzmények táblázat az időszak oszlopainak (Befejezés, indítás) fürtözött B-fa indexét tartalmazza, és az oldal tömörítése engedélyezve van. Ez a konfiguráció optimális az időbeli táblákat használó forgatókönyvek többsége számára, különösen az [adatok naplózása](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0)esetén. 

Ebben a konkrét esetben arra törekszünk, hogy az időalapú trendeket elemezzük hosszabb adatelőzményekkel és nagyobb adatkészletekkel, így az előzményeket tartalmazó tábla számára a fürtözött oszlopcentrikus index. A fürtözött oszlopcentrikus nagyon jó tömörítést és teljesítményt biztosít az analitikus lekérdezésekhez. Az időbeli táblázatok lehetővé teszi, hogy rugalmasan konfigurálja az indexeket az aktuális és az időszakos táblákon egymástól függetlenül. 

> [!NOTE]
> A oszlopcentrikus indexek a prémium szinten és a standard szintű, S3 és újabb verziókon érhetők el.
>

A következő parancsfájl azt mutatja be, hogy a History (előzmények) tábla alapértelmezett indexe hogyan változtatható meg a fürtözött oszlopcentrikus:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Az ideiglenes táblák a Object Explorerban jelennek meg a könnyebb azonosításhoz megadott ikonnal, míg az előzmények táblázata alárendelt csomópontként jelenik meg.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Meglévő táblázat módosítása időbeli értékre
Fedjük le azt az alternatív forgatókönyvet, amelyben a WebsiteUserInfo tábla már létezik, de nem a módosítások előzményeinek megtartására lett tervezve. Ebben az esetben egyszerűen kiterjesztheti a meglévő táblázatot, hogy ideiglenes legyen, ahogy az az alábbi példában is látható:

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

## <a name="step-2-run-your-workload-regularly"></a>2\. lépés: Számítási feladatok rendszeres futtatása
Az időbeli táblázatok legfőbb előnye, hogy a változások követéséhez semmilyen módon nem kell módosítania vagy módosítania a webhelyet. A létrehozást követően az ideiglenes táblák transzparens módon megőrzik az előző sorokat, valahányszor módosításokat végez az adatokon. 

Az adott forgatókönyv automatikus változás-követésének kihasználása érdekében csak az oszlop **PagesVisited** frissítése minden alkalommal, amikor egy felhasználó befejezi a munkamenetét a webhelyen:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Fontos megjegyezni, hogy a frissítési lekérdezésnek nem kell tudnia a tényleges művelet bekövetkezett időpontját, illetve azt, hogy a későbbi elemzések során a korábbi adatok hogyan őrződnek meg. A Azure SQL Database mindkét szempontot automatikusan kezeli. Az alábbi ábra azt szemlélteti, hogyan jönnek létre az előzmények az összes frissítésen.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>3\. lépés: Korábbi adatok elemzésének végrehajtása
Most, hogy az időbeli rendszerverziószámozás engedélyezve van, a korábbi adatok elemzése csak egy lekérdezéssel érhető el. Ebben a cikkben néhány példát ismertetünk, amelyek közös elemzési forgatókönyvekkel foglalkoznak – az összes adat megismerése érdekében Ismerkedjen meg a [for SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) záradékban bemutatott különböző lehetőségekkel.

Ha szeretné megtekinteni az első 10 felhasználót a meglátogatott weblapok száma alapján egy órával ezelőtt, futtassa ezt a lekérdezést:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

A lekérdezés egyszerűen módosítható, hogy elemezze a webhely látogatásait egy nappal ezelőtt, egy hónappal ezelőtt vagy a múltban egy adott időpontban.

Az előző nap alapvető statisztikai elemzésének végrehajtásához használja a következő példát:

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

Egy adott felhasználó tevékenységének megkereséséhez egy adott időtartamon belül használja a TARTALMAZt záradékot:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A grafikai vizualizáció különösen ideális az időbeli lekérdezésekhez, mivel a trendeket és a használati mintákat intuitív módon, nagyon egyszerűen megjelenítheti:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Változó táblázat sémája
Az alkalmazások fejlesztése során általában módosítania kell az időbeli táblázat sémáját. Ehhez egyszerűen futtassa az ALTER TABLE utasításait, és Azure SQL Database megfelelően propagálja a módosításokat az előzmények táblába. Az alábbi szkript bemutatja, hogyan adhat hozzá további attribútumokat a követéshez:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Hasonlóképpen módosíthatja az oszlop definícióját is, ha a munkaterhelés aktív:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Végül eltávolíthat egy olyan oszlopot, amelyhez már nincs szüksége.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Azt is megteheti, hogy a legújabb [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) használja az időbeli táblázat sémájának módosítására, miközben az adatbázishoz (online mód) vagy az adatbázis-projekt (offline mód) részeként csatlakozik.

## <a name="controlling-retention-of-historical-data"></a>A korábbi adatok megőrzésének szabályozása
A rendszerverzióval ellátott időszakos táblázatok esetében az előzmények táblázata a normál tábláknál nagyobb méretűre növelheti az adatbázis méretét. A nagy és egyre növekvő History-táblázat a tiszta tárolási költségekkel, valamint az időbeli lekérdezéssel kapcsolatos teljesítménybeli ÁFA kiszabásával jelentheti a problémát. Ezért az előzmények táblában lévő adatok kezelésére szolgáló adatmegőrzési szabályzat fejlesztése fontos szempont az egyes időbeli táblázatok életciklusának megtervezéséhez és kezeléséhez. A Azure SQL Database a következő módszerekkel kezelheti az időszakos táblázat korábbi információit:

* [Tábla particionálása](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Egyéni karbantartási parancsfájl](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>További lépések

- Az időbeli táblázatokkal kapcsolatos további információkért lásd: [ideiglenes táblák](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)megtekintése.
- Látogasson el a Channel 9 csatornára, és tekintse meg a valós [](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)időben zajló, [valós idejű megvalósítási](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) sikertörténetet

