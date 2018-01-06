---
title: "Az Azure Application Insights SQL exportálása |} Microsoft Docs"
description: "Application Insights adatainak folyamatos exportálása Stream Analytics használ SQL."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
editor: mrbullwinkle
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: 8d008727d964df56d128265b632dafa4ab776f98
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Forgatókönyv: Az Application Insights Stream Analytics használ SQL exportálása
Ez a cikk bemutatja, hogyan kívánja áthelyezni a telemetriai adatokat a [Azure Application Insights] [ start] használatával az Azure SQL adatbázishoz [a folyamatos exportálás] [ export] és [az Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A folyamatos exportálás a telemetriai adatok áthelyezése az Azure Storage JSON formátumban. Azt fogja elemezni a JSON-objektumok használatával az Azure Stream Analytics és sorok az adatbázistábla létrehozása.

(Általában a folyamatos exportálás ennek a módja a saját Application Insights küldeni az alkalmazások telemetriai adatok elemzését. Akkor lehetett igazítja más műveleteket az exportált telemetriai adatok, például az adatok összesítő kódmintában.)

Először foglalkozunk azt feltételezi, hogy már rendelkezik a figyelni kívánt alkalmazást.

Ebben a példában a fogjuk a lapmegtekintések adatainak használni, de ugyanilyen mintájú könnyen más adattípusok, például az egyéni események és kivételek kell terjeszteni. 

## <a name="add-application-insights-to-your-application"></a>Az Application Insights hozzáadása az alkalmazáshoz
Első lépések:

1. [Az Application Insights beállítása a weblapok](app-insights-javascript.md). 
   
    (A példában azt összpontosítson az ügyfélböngészők a lapmegtekintések adatainak feldolgozási, de a kiszolgáló oldalán is beállíthat az Application Insights a [Java](app-insights-java-get-started.md) vagy [ASP.NET](app-insights-asp-net.md) alkalmazás és a folyamat kérelem függőségi és más kiszolgáló telemetriai adat.)
2. Tegye közzé az alkalmazást, és tekintse meg a "telemetrikus" adatok jelennek meg az Application Insights-erőforrást.

## <a name="create-storage-in-azure"></a>Tároló létrehozása az Azure-ban
A folyamatos exportálás mindig kimenete adatokat egy Azure Storage-fiók, ezért meg kell először hozza létre a tárolót.

1. Az előfizetésben a storage-fiók létrehozása a [Azure-portálon][portal].
   
    ![Azure-portálon válassza az új, az adatok, a tároló. Válassza ki a klasszikus, válassza a létrehozása. Adja meg a tároló nevét.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. Tároló létrehozása
   
    ![Az új tárterületen található tárolók kiválasztása, kattintson a tárolók csempe, majd a Hozzáadás](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. A tárelérési kulcs másolása
   
    Kell, hogy hamarosan állítsa be a stream analytics szolgáltatás bemenete.
   
    ![A tárolóban nyissa meg a beállításokat, a kulcsokat, és az elsődleges elérési kulcsot másolatot](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Indítsa el az Azure storage a folyamatos exportálás
1. Az Azure portálon tallózással keresse meg az Application Insights-erőforrást az alkalmazáshoz létrehozott.
   
    ![A Tallózás, az Application Insights az alkalmazáshoz](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. A folyamatos exportálás létrehozása.
   
    ![Válassza a beállítások, folyamatos exportálási hozzáadása](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    Válassza ki a korábban létrehozott tárfiókot:

    ![Exportálás céljának beállítása](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    Állítsa be a megjeleníteni kívánt típusait:

    ![Válassza ki az esemény típusa](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. Néhány adat gyűlik össze legyen. Elhelyezkedik vissza, és a felhasználók használhassa az alkalmazást egy ideig. Telemetria határozza meg, és látni fogja, statisztikai diagramok [metrika explorer](app-insights-metrics-explorer.md) és az egyes események [diagnosztikai keresési](app-insights-diagnostic-search.md). 
   
    És is, exportálja az adatokat a tárhelyre. 
2. Vizsgálja meg az exportált adatok, vagy a portál - kiválasztása **Tallózás**, válassza ki a tárfiókot, majd **tárolók** - vagy a Visual Studióban. A Visual Studio felületén válassza **megtekintése / Cloud Explorer**, és nyissa meg az Azure / tárolási. (Ha még nem rendelkezik a menüpont, szeretné-e az Azure SDK telepítése: az új projekt párbeszédpanel megnyitásához, és nyissa meg a Visual C# / felhő / Microsoft Azure SDK beolvasása a .NET-hez.)
   
    ![Nyissa meg a Visual Studio Server Browser bővítményt, az Azure, a tároló](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Jegyezze fel az elérési út, az alkalmazás nevét és instrumentation kulcsból származtatott közös részét. 

Az események a blob-JSON formátumú fájlok kerülnek. Minden fájl tartalmazhat egy vagy több esemény. Ezért szeretnénk az esemény-adatok olvasása, és azt szeretnénk, ha a mezők szűrik. Nincsenek a különböző dolgok, azt megteheti az adatokat, de a terv ma Stream Analytics segítségével az adatok áthelyezése az SQL-adatbázishoz. Amely könnyen nagy mennyiségű érdekes lekérdezések futtatásához.

## <a name="create-an-azure-sql-database"></a>Egy Azure SQL-adatbázis létrehozása
Még egyszer az előfizetés-től kezdődő [Azure-portálon][portal], hozza létre az adatbázist (és az új kiszolgáló kivéve, ha már van egy) amely meg fog írni.

![Új adatok, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

Győződjön meg arról, hogy az adatbázis-kiszolgáló Azure-szolgáltatásokhoz való hozzáférést:

![Tallózással keresse meg, kiszolgálók, a kiszolgáló beállításait, a tűzfal, a hozzáférés engedélyezése az Azure-bA](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Létrehoz egy táblát az Azure SQL-adatbázis
Csatlakozás az előnyben részesített felügyeleti eszközzel az előző szakaszban létrehozott adatbázishoz. Ebben a bemutatóban fogjuk használni [az SQL Server felügyeleti eszközei](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Hozzon létre egy új lekérdezést, és hajtsa végre a következő T-SQL:

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

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

Ez a példa Lapmegtekintések adatainak használunk. A rendelkezésre álló adatok megtekintéséhez vizsgálhatja meg a JSON-kimenetét, és tekintse meg a [exportálja az adatokat az adatmodellbe](app-insights-export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Hozzon létre egy Azure Stream Analytics-példányt
Az a [Azure-portálon](https://portal.azure.com/), válassza ki az Azure Stream Analytics szolgáltatás, és hozzon létre egy új Stream Analytics-feladatot:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA001.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA002.png)

Amikor új feladatot hoz létre, jelölje ki a **forrást**.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Egy új bemenet hozzáadása

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA004.png)

Állítsa be úgy, hogy a folyamatos exportálás blobból bemeneti:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA005.png)

Most szüksége lesz az elsődleges elérési kulcsot importáljon a Tárfiókba, amelyet korábban feljegyzett. Állítsa be ezt a Tárfiók kulcsára.

#### <a name="set-path-prefix-pattern"></a>Set elérési út előtag mintája

**Győződjön meg arról, hogy beállítása a Date formátum éééé-hh-nn-(kötőjel).**

Az elérési út előtag mintája határozza meg, hogyan Stream Analytics keresse meg a bemeneti fájlokat a tárolóban. Állítsa be úgy, hogy hogyan tárolja az adatokat folyamatos exportálni kell. Állítsa be ehhez hasonló:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Ebben a példában:

* `webapplication27`Application Insights-erőforrások, neve **minden a nagybetűs**. 
* `1234...`az Application Insights-erőforrások instrumentation kulcsa **az eltávolított kötőjelek**. 
* `PageViews`az adatok elemzéséhez szeretnénk típusa van. A használható típusok a folyamatos exportálás beállítása szűrő függ. Vizsgálja meg az exportált adatok megtekintéséhez a használható típusok, és tekintse meg a [exportálja az adatokat az adatmodellbe](app-insights-export-data-model.md).
* `/{date}/{time}`a minta írt szó.

Ahhoz, hogy a név és az Application Insights-erőforrás iKey, nyissa meg az Essentials az Áttekintés lap, vagy nyissa meg a beállításokat.

> [!TIP]
> A Sample függvény segítségével ellenőrizze, hogy a bemeneti elérési utat helyesen van beállítva. Ha a hiba: Ellenőrizze, hogy van-e adatokat a minta időtartományát úgy döntött, hogy a tároló. A bemeneti definition szerkessze, és ellenőrizze, állítsa be a tárfiók, elérési út előtag és a dátum helyesen formátumban.
> 
> 
## <a name="set-query"></a>Set-lekérdezés
Nyissa meg a lekérdezés szakaszt:

Cserélje le az alapértelmezett lekérdezés:

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

Figyelje meg, hogy az első néhány tulajdonságok Lapmegtekintések adatainak vonatkoznak. Más telemetriai típusú kivitel más tulajdonságokkal fog rendelkezni. Tekintse meg a [részletes adatok modell hivatkozást a tulajdonság típusát és értékét.](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>Kimeneti adatbázis beállítása
Válassza ki az SQL kimeneteként.

![A stream analytics válassza ki a kimenetek](./media/app-insights-code-sample-export-sql-stream-analytics/SA006.png)

Adja meg az SQL-adatbázis.

![Adja meg az adatbázis részletei](./media/app-insights-code-sample-export-sql-stream-analytics/SA007.png)

Zárja be a varázslót, és várja meg, egy értesítés, hogy a kimeneti be van állítva.

## <a name="start-processing"></a>Indítsa el a feldolgozási
Indítsa el a feladatot az műveletsávon:

![A stream analytics kattintson a Start](./media/app-insights-code-sample-export-sql-stream-analytics/SA008.png)

Dönthet úgy, hogy-e el most, vagy kezdje korábbi adatok kiindulva adatainak feldolgozása. Az utóbbi akkor hasznos, ha korábban a folyamatos exportálás már fut egy ideig.

Néhány perc elteltével lépjen vissza az SQL Server felügyeleti eszközei, és tekintse meg a áramló adatokat. Például lekérdezéssel ehhez hasonló:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Kapcsolódó cikkek
* [A Power bi szolgáltatásba, használja a Stream Analytics exportálása](app-insights-export-power-bi.md)
* [A részletes adatok modell útmutató a tulajdonság típusát és értékét.](app-insights-export-data-model.md)
* [A folyamatos Exportálás az Application Insightsban](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

