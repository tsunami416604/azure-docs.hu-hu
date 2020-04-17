---
title: Exportálás SQL-be az Azure Application Insightsból | Microsoft dokumentumok
description: Az Application Insights-adatok folyamatos exportálása SQL-be a Stream Analytics használatával.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: e67365038b9a481bc0cacf079e5d197cc3139a5f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536913"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Forgatókönyv: Exportálás SQL-be az Application Insightsból a Stream Analytics használatával
Ez a cikk bemutatja, hogyan helyezheti át a telemetriai adatokat az [Azure Application Insightsból][start] egy Azure SQL-adatbázisba [a Folyamatos exportálás][export] és az Azure [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)használatával. 

A folyamatos exportálás a telemetriai adatokat JSON formátumban helyezi át az Azure Storage-ba. A JSON-objektumokat az Azure Stream Analytics használatával fogjuk elemezni, és sorokat hozunk létre egy adatbázistáblában.

(Általánosabban, folyamatos exportálás a módja annak, hogy saját elemzést az alkalmazások által az Application Insights küldött telemetriai adatokat. Ezt a kódmintát úgy is adaptálhatja, hogy az exportált telemetriai adatokkal, például az adatok összesítésével más dolgokat is elvégezhet.)

Kezdjük azzal a feltételezéssel, hogy már rendelkezik a figyelni kívánt alkalmazással.

Ebben a példában az oldalnézet adatait fogjuk használni, de ugyanaz a minta könnyen kiterjeszthető más adattípusokra, például egyéni eseményekre és kivételekre. 

## <a name="add-application-insights-to-your-application"></a>Alkalmazáselemzési adatok hozzáadása az alkalmazáshoz
Első lépések:

1. [Állítsa be az Application Insights-ot a weblapokhoz.](../../azure-monitor/app/javascript.md) 
   
    (Ebben a példában az ügyfélböngészők lapnézeti adatainak feldolgozására összpontosítunk, de beállíthatja az Application Insights-ot a [Java](../../azure-monitor/app/java-get-started.md) vagy [ASP.NET](../../azure-monitor/app/asp-net.md) alkalmazás- és folyamatkérelem, -függőség és egyéb kiszolgálói telemetriai adatok kiszolgálói oldalán is.)
2. Tegye közzé az alkalmazást, és tekintse meg az Application Insights-erőforrásban megjelenő telemetriai adatokat.

## <a name="create-storage-in-azure"></a>Tárhely létrehozása az Azure-ban
A folyamatos exportálás mindig adatokat ad ki egy Azure Storage-fiókba, ezért először létre kell hoznia a tárolót.

1. Hozzon létre egy tárfiókot az előfizetésében az [Azure Portalon.][portal]
   
    ![Az Azure Portalon válassza az Új, Az adatok, a tárolás lehetőséget. Válassza a Klasszikus lehetőséget, és válassza a Létrehozás gombot. Adja meg a tároló nevét.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Tároló létrehozása
   
    ![Az új tárolóban válassza a Tárolók lehetőséget, kattintson a Tárolók csempére, majd a Hozzáadás](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. A tároló-hozzáférési kulcs másolása
   
    Szüksége lesz rá hamarosan a bemeneti a stream analytics szolgáltatás hoz.
   
    ![A tárolóban nyissa meg a Beállítások, kulcsok és készítsen másolatot az elsődleges hozzáférési kulcsról](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Folyamatos exportálás indítása az Azure-tárhelyre
1. Az Azure Portalon keresse meg az alkalmazáshoz létrehozott Application Insights-erőforrást.
   
    ![Válassza a Tallózás, az Application Insights, az alkalmazás lehetőséget](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Hozzon létre egy folyamatos exportálást.
   
    ![Beállítások kiválasztása, Folyamatos exportálás, Hozzáadás](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Válassza ki a korábban létrehozott tárfiókot:

    ![Az exportálási cél beállítása](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Állítsa be a megtekinteni kívánt eseménytípusokat:

    ![Eseménytípusok kiválasztása](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Hagyja, hogy néhány adat felhalmozódik. Dőljön hátra, és hagyja, hogy az emberek használják a kérelmet egy darabig. Telemetriai adatok jönnek, és látni fogja a statisztikai diagramok [metrika felfedező](../../azure-monitor/platform/metrics-charts.md) és az egyes események [diagnosztikai keresés.](../../azure-monitor/app/diagnostic-search.md) 
   
    És az adatok is exportálják a tárolóba. 
2. Vizsgálja meg az exportált adatokat, vagy a portálon – válassza a **Tallózás**lehetőséget, válassza ki a tárfiókot, majd **a Tárolók** – vagy a Visual Studio alkalmazásban. A Visual Studio alkalmazásban válassza **a View / Cloud Explorer**lehetőséget, és nyissa meg az Azure / Storage alkalmazást. (Ha nem rendelkezik ezzel a menübeállítással, telepítenie kell az Azure SDK:Nyissa meg az Új projekt párbeszédpanelt, és nyissa meg a Visual C# / Cloud / Get Microsoft Azure SDK for .NET alkalmazást.)
   
    ![A Visual Studio alkalmazásban nyissa meg a Kiszolgálóböngészőt, az Azure-t, a Storage-t](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Jegyezze fel az elérési út nevének közös részét, amely az alkalmazás nevéből és instrumentation kulcsából származik. 

Az események JSON formátumú blobfájlokba vannak írva. Minden fájl egy vagy több eseményt tartalmazhat. Ezért szeretnénk elolvasni az esemény adatait, és kiszűrni a kívánt mezőket. Az adatokkal sokféle dolgot megtehetünk, de a mai tervünk az, hogy a Stream Analytics segítségével áthelyezzük az adatokat egy SQL-adatbázisba. Ez megkönnyíti a sok érdekes lekérdezés futtatását.

## <a name="create-an-azure-sql-database"></a>Azure SQL Database-adatbázis létrehozása
Az [Azure Portalon][portal]való előfizetésből ismét létrehozhatja azt az adatbázist (és egy új kiszolgálót, ha már rendelkezik ilyen) adatbázist, amelyre az adatokat írni fogja.

![Új, Adat, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Győződjön meg arról, hogy az adatbázis-kiszolgáló engedélyezi a hozzáférést az Azure-szolgáltatásokhoz:

![Tallózás, Kiszolgálók, a kiszolgáló, a Beállítások, a tűzfal, az Azure-hoz való hozzáférés engedélyezése](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Táblázat létrehozása az Azure SQL DB-ben
Csatlakozzon az előző szakaszban létrehozott adatbázishoz az előnyben részesített felügyeleti eszközzel. Ebben a forgatókönyvben az [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS) eszközt fogjuk használni.

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Hozzon létre egy új lekérdezést, és hajtsa végre a következő T-SQL-t:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Ebben a példában oldalnézetekből származó adatokat használunk. A többi rendelkezésre álló adat megtekintéséhez vizsgálja meg a JSON-kimenetet, és tekintse meg az [exportálási adatmodellt.](../../azure-monitor/app/export-data-model.md)

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics-példány létrehozása
Az [Azure Portalon](https://portal.azure.com/)válassza ki az Azure Stream Analytics szolgáltatást, és hozzon létre egy új Stream Analytics-feladatot:

![Streamelemzési beállítások](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Az új feladat létrehozásakor válassza az **Ugrás az erőforrásra**lehetőséget.

![Streamelemzési beállítások](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Új bemenet hozzáadása

![Streamelemzési beállítások](./media/code-sample-export-sql-stream-analytics/SA004.png)

Állítsa be, hogy a folyamatos exportálásblobból származó adatokat vegyen be:

![Streamelemzési beállítások](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Most szüksége lesz az elsődleges hozzáférési kulcsa a tárfiókból, amely korábban megjegyezte. Állítsa be ezt a tárfiók kulcs.

#### <a name="set-path-prefix-pattern"></a>Görbeelőtag mintájának beállítása

**Ügyeljen arra, hogy a Dátumformátumot YYYY-MM-DD-re állítsa (kötőjellel).**

A Path prefix minta határozza meg, hogyan Stream Analytics megtalálja a bemeneti fájlokat a tárolóban. Be kell állítania, hogy megfeleljen annak, ahogyan a Folyamatos exportálás tárolja az adatokat. Állítsa be, mint ez:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Ebben a példában:

* `webapplication27`az Application Insights-erőforrás neve, **mindezt kisbetűvel.** 
* `1234...`az Application Insights erőforrás instrumentation kulcsa **eltávolított kötőjelekkel.** 
* `PageViews`az a fajta adat, amelyet elemezni szeretnénk. Az elérhető típusok a Folyamatos exportálás mezőben beállított szűrőtől függenek. Vizsgálja meg az exportált adatokat a többi elérhető típus megtekintéséhez, és tekintse meg az [exportálási adatmodellt.](../../azure-monitor/app/export-data-model.md)
* `/{date}/{time}`egy szó szerint írt minta.

Az Application Insights-erőforrás nevének és iKey-jének lekért letöltéséhez nyissa meg az Essentials alkalmazást az áttekintő lapon, vagy nyissa meg a Beállítások at.

> [!TIP]
> A Minta függvénnyel ellenőrizheti, hogy helyesen állította-e be a beviteli útvonalat. Ha nem sikerül: Ellenőrizze, hogy vannak-e adatok a tárolóban a kiválasztott mintaidőtartományhoz. Szerkesztheti a bemeneti definíciót, és ellenőrizze, hogy helyesen állította-e be a tárfiókot, az elérési út előtagot és a dátumformátumot.

 
## <a name="set-query"></a>Lekérdezés beállítása
A lekérdezésszakasz megnyitása:

Cserélje le az alapértelmezett lekérdezést a következőre:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Figyelje meg, hogy az első néhány tulajdonság az oldalnézet adataira vonatkozik. Más telemetriai típusok exportálása különböző tulajdonságokkal rendelkezik. Tekintse meg a [tulajdonságtípusok és értékek részletes adatmodell-hivatkozását.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Kimenet beállítása adatbázisba
Válassza ki az SQL-t kimenetként.

![Az adatfolyam-elemzésben válassza a Kimenetek](./media/code-sample-export-sql-stream-analytics/SA006.png)

Adja meg az SQL-adatbázist.

![Az adatbázis részleteinek kitöltése](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zárja be a varázslót, és várja meg a kimenet beállításáról szóló értesítést.

## <a name="start-processing"></a>Feldolgozás megkezdése
Indítsa el a feladatot a műveletsávból:

![Az adatfolyam-elemzésben kattintson a Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

Megadhatja, hogy mostantól kezdje-e el az adatok feldolgozását, vagy a korábbi adatokkal kezdi. Ez utóbbi akkor hasznos, ha már egy ideje futó folyamatos exportálással rendelkezik.

Néhány perc múlva lépjen vissza az SQL Server Management Tools alkalmazáshoz, és nézze meg a beáramló adatokat. Használjon például a következőhez hasonló lekérdezést:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Exportálás a Power BI-ba a Stream Analytics használatával](../../azure-monitor/app/export-power-bi.md )
* [Részletes adatmodell-hivatkozás a tulajdonságtípusokra és értékekre vonatkozóan.](../../azure-monitor/app/export-data-model.md)
* [Folyamatos exportálás az Application Insightsban](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

