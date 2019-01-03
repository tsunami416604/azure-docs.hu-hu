---
title: SQL-Exportálás az Azure Application insights |} A Microsoft Docs
description: Folyamatosan exportálhatja az Application Insights-adatok az SQL-Stream Analytics használatával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: f9b1096f2d409f46e3b9e48a2d59420930150c1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974556"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Forgatókönyv: SQL-exportálás a Stream Analytics használatával az Application Insights
Ez a cikk bemutatja, hogyan helyezheti át a küldött telemetriai adatok [Azure Application Insights] [ start] be egy Azure SQL database használatával [folyamatos exportálás] [ export] és [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

A folyamatos exportálás helyezi át, JSON-formátumban a telemetriai adatok Azure Storage-bA. A JSON-objektumok az Azure Stream Analytics elemezni, és sorok létrehozása egy adatbázistábla soraihoz azt fogja.

(Általában a folyamatos Exportálás az a módja a saját alkalmazásait posílat do Application Insights telemetria elemzése. Ön sikerült alkalmazkodik a kódminta az exportált telemetriával, például az adatok összesítése szabadon.)

Feltételezzük, hogy már rendelkezik a figyelni kívánt alkalmazás kezdjük.

Ebben a példában azt fogja használni az adatokat, de ugyanezt a mintát könnyen bővíthető egyéb adattípusok, például az egyéni események és a kivételeket. 

## <a name="add-application-insights-to-your-application"></a>Az Application Insights hozzáadása az alkalmazáshoz
Első lépések:

1. [Application Insights beállítása a weblapok](../../azure-monitor/app/javascript.md). 
   
    (Ebben a példában az adatokat az ügyfelek böngészőin feldolgozása fogunk összpontosítani, de is beállíthat az Application Insights a kiszolgáló oldalán a [Java](../../azure-monitor/app/java-get-started.md) vagy [ASP.NET](../../azure-monitor/app/asp-net.md) alkalmazás és folyamat kérelem függőség és egyéb telemetriát.)
2. Tegye közzé az alkalmazást, és tekintse meg a telemetriai adatok az Application Insights-erőforrásban jelennek meg.

## <a name="create-storage-in-azure"></a>Tároló létrehozása az Azure-ban
A folyamatos exportálás mindig adatokat egy Azure Storage-fiókot, így Önnek kell először hozza létre a tárolót.

1. Hozzon létre egy tárfiókot az előfizetésében található a [az Azure portal][portal].
   
    ![Az Azure Portalon válassza ki az új, adatok, tárolás. Válassza ki a klasszikus, válassza a létrehozás. Adja meg a tároló nevét.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Tároló létrehozása
   
    ![Az új tároló válassza ki a tárolók, a tárolókkal csempére, majd a Hozzáadás gombra](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. A tárelérési kulcs másolása
   
    Állítsa be a bemenetet a stream analytics szolgáltatással való hamarosan szüksége.
   
    ![A storage-ban nyissa meg a beállításokat, a kulcsokat, és készítsen róla egy másolatot az elsődleges hozzáférési kulcs](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Indítsa el a folyamatos exportálást, és az Azure storage
1. Az Azure Portalon keresse meg az alkalmazás számára létrehozott Application Insights-erőforrást.
   
    ![Válassza a Tallózás, Application Insights, az alkalmazás](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Hozzon létre egy folyamatos exportálást.
   
    ![Válassza a beállítások, a folyamatos exportálás hozzáadása](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Válassza ki a korábban létrehozott tárfiók:

    ![Az Exportálás cél beállítása](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Állítsa be az eseménytípusok, szeretne látni:

    ![Válassza ki az események típusai](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Néhány adat gyűlik össze legyen. Dőljön hátra, és a felhasználók használják az alkalmazását egy ideig. Telemetriai adatok származnak, és láthatja a statisztikai diagramok a [metrika explorer](../../application-insights/app-insights-metrics-explorer.md) és az egyes események [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md). 
   
    És emellett az adatok a Storage exportálandó. 
2. Vizsgálja meg az exportált adatok, vagy a portálon – kiválasztása **Tallózás**, válassza ki a tárfiókot, majd **tárolók** - vagy a Visual Studióban. A Visual Studióban válassza **megtekintése / Cloud Explorer**, és nyissa meg az Azure / Storage. (Ha ez a menüpont nem rendelkezik, az Azure SDK-t telepítenie kell: Az új projekt párbeszédpanel megnyitásához, és nyissa meg a Vizualizáció C# / Cloud / .NET-hez készült Microsoft Azure SDK beszerzése.)
   
    ![A Visual Studióban nyissa meg a Server Browser bővítményt, az Azure, a Storage](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Jegyezze fel a közös rész az elérési út neve, amely az alkalmazás neve és a kialakítási kulcs származik. 

Az események írja a blob-fájlok JSON formátumban. Minden fájl tartalmazhat egy vagy több esemény. Ezért szeretnénk az eseményadatokat olvassa el és szűrje ki a kívánt mezőket. Mindenféle dolgot javítanunk kell az adatokat, de a csomag jelenleg a Stream Analytics használatával az adatok áthelyezése egy SQL Database szolgáltatás. Amely fog megkönnyítik nagy mennyiségű érdekesnek talált lekérdezéseket futtatni.

## <a name="create-an-azure-sql-database"></a>Az Azure SQL-adatbázis létrehozása
Ismét kezdve előfizetését az [az Azure portal][portal], az adatbázis létrehozásához (és a egy új kiszolgálón, kivéve, ha már rendelkezik egy), amely fog írni az adatokat.

![Új az adatok, az SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Győződjön meg arról, hogy az adatbázis-kiszolgáló lehetővé teszi, hogy az Azure-szolgáltatásokhoz való hozzáférés:

![Tallózással keresse meg, a kiszolgálók, a kiszolgáló beállításait, a tűzfal, a hozzáférés engedélyezése az Azure-bA](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Hozzon létre egy táblát az Azure SQL DB
Az adatbázis az előző szakaszban az előnyben részesített felügyeleti eszközzel csatlakozhat. Ez az útmutató fogjuk használni [SQL Server Kezelőeszközeiben](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

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

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Ebben a példában használunk, oldal-nézetből származó adatokat. Elérhető egyéb adatok megtekintéséhez a JSON-kimeneteket a vizsgálja, és tekintse meg a [adatmodell exportálása](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Hozzon létre egy Azure Stream Analytics-példányt
Az a [az Azure portal](https://portal.azure.com/), az Azure Stream Analytics szolgáltatás és a egy új Stream Analytics-feladat létrehozása:

![Stream analytics-beállítások](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Ha az új feladat jön létre, válassza ki a **erőforrás megnyitása**.

![Stream analytics-beállítások](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Új bemenet hozzáadása

![Stream analytics-beállítások](./media/code-sample-export-sql-stream-analytics/SA004.png)

Állítsa be, hogy a folyamatos exportálás blobból bemeneti:

![Stream analytics-beállítások](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Most szüksége lesz az elsődleges hozzáférési kulcs az Ön Tárfiókjából, amely a korábban feljegyzett. Állítsa az másként a Tárfiókkulcs.

#### <a name="set-path-prefix-pattern"></a>Set elérési út előtagmintája

**Győződjön meg arról, az értékre a dátumformátum ÉÉÉÉ-HH-nn (szaggatott).**

Az elérésiút-előtag a minta adja meg, hogyan Stream Analytics a bemeneti fájlokat keres a storage-ban. Meg kell azt, hogy hogyan tárolja az adatok folyamatos exportálása. Állítsa be, ehhez hasonló:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Ebben a példában:

* `webapplication27` az Application Insights-erőforrás neve **kisbetű szerepel az összes**. 
* `1234...` a rendszerállapotkulcsot az Application Insights-erőforrás van **az eltávolított kötőjelek**. 
* `PageViews` az adatok szeretnénk elemezni. Az elérhető típusokat a szűrőt, állítsa be a folyamatos exportálás függenek. Vizsgálja meg az exportált adatok megtekintéséhez az elérhető típusokat, és tekintse meg a [adatmodell exportálása](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` szó szerint írt mintát.

A név és a Rendszerállapotkulcsot az Application Insights-erőforrás beszerzéséhez, nyissa meg a Essentialst az Áttekintés lapon, vagy nyissa meg a beállításokat.

> [!TIP]
> A mintául szolgáló funkció segítségével ellenőrizze, hogy helyesen van beállítva a bemeneti útvonalat. Ha a sikertelen: Ellenőrizze, hogy nincs-e adatokat a kiválasztott minta időtartományban a storage-ban. A bemeneti definíciójának szerkesztése, és állítsa be a storage-fiók, az elérési út előtagja, és a dátum helyesen formátumban.
> 
> 
## <a name="set-query"></a>Set-lekérdezés
Nyissa meg a lekérdezés szakaszban:

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

Figyelje meg, hogy az első néhány tulajdonságok vonatkozó adatokat. Más telemetriatípusok exportálásai lesz különböző tulajdonságai. Tekintse meg a [részletes adatmodell-referencia a tulajdonság típusát és értékét.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Kimenet az adatbázis beállítása
Válassza ki a kimeneti SQL.

![A stream Analytics szolgáltatásban válassza ki a kimenetek](./media/code-sample-export-sql-stream-analytics/SA006.png)

Adja meg az SQL-adatbázis.

![Adja meg az adatokat az adatbázis](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zárja be a varázslót, és várja meg, egy értesítés, hogy a kimenet be lett állítva.

## <a name="start-processing"></a>Indítsa el a feldolgozási
A művelet sávon, indítsa el a feladatot:

![A stream Analytics szolgáltatásban kattintson a Start gombra](./media/code-sample-export-sql-stream-analytics/SA008.png)

Kiválaszthatja, hogy e elkezdi feldolgozni az adatokat, vagy a a kezdéshez korábbi adatok kezdve. Az utóbbi akkor hasznos, ha már rendelkezik egy ideje már fut a folyamatos exportálás.

Néhány perc múlva lépjen vissza az SQL Server felügyeleti eszközeit, és figyelje meg az adatok a tárfiókba. Például használja egy lekérdezést, ehhez hasonló:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Kapcsolódó cikkek
* [Exportálás a Stream Analytics használatával Power bi-bA](../../application-insights/app-insights-export-power-bi.md)
* [Részletes adatmodell-referencia a tulajdonság típusát és értékét.](../../azure-monitor/app/export-data-model.md)
* [A folyamatos Exportálás az Application insights szolgáltatásban](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../application-insights/app-insights-metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../application-insights/app-insights-overview.md

