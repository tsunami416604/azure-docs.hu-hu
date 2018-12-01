---
title: Exportálás a Stream Analytics az Azure Application Insights használatával |} A Microsoft Docs
description: Stream Analytics folyamatosan átalakíthatja, szűrése és az Application insights exportált adatainak átirányítása.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: mbullwin
ms.openlocfilehash: ecff4dfff42524d215df7301f6af4de8444f4029
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723380"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Application insights exportált adatok feldolgozása a Stream Analytics használatával
[Az Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) az ideális eszköz a adatfeldolgozás [Application Insights exportált](app-insights-export-telemetry.md). Stream Analytics különböző forrásokból származó adatok is lekérése. Átalakíthatja és szűrheti az adatokat, és átirányítása fogadóként különböző.

Ebben a példában létrehozunk egy adaptert, amely beolvassa az adatokat az Application Insightsból, átnevezése és feldolgozza az egyes mezőit, és átadja azt a Power BI-bA.

> [!WARNING]
> Sokkal jobb és egyszerűbb [javasolt módját Application Insights-adatok megjelenítése Power BI-ban](app-insights-export-power-bi.md). Bemutatott elérési út csak egy példa mutatja be az exportált adatok feldolgozása.
> 
> 

![Exportálás keresztül SA PBI blokk diagramja](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tároló létrehozása az Azure-ban
A folyamatos exportálás mindig adatokat egy Azure Storage-fiókot, így Önnek kell először hozza létre a tárolót.

1. Az előfizetésben a "klasszikus" storage-fiók létrehozása a [az Azure portal](https://portal.azure.com).
   
   ![Az Azure-portálon válassza az új, adatok, tárolás](./media/app-insights-export-stream-analytics/030.png)
2. Tároló létrehozása
   
    ![Az új tároló válassza ki a tárolók, a tárolókkal csempére, majd a Hozzáadás gombra](./media/app-insights-export-stream-analytics/040.png)
3. A tárelérési kulcs másolása
   
    Állítsa be a bemenetet a stream analytics szolgáltatással való hamarosan szüksége.
   
    ![A storage-ban nyissa meg a beállításokat, a kulcsokat, és készítsen róla egy másolatot az elsődleges hozzáférési kulcs](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Indítsa el a folyamatos exportálást, és az Azure storage
[A folyamatos exportálás](app-insights-export-telemetry.md) helyez át adatokat az Azure storage-bA az Application Insights.

1. Az Azure Portalon keresse meg az alkalmazás számára létrehozott Application Insights-erőforrást.
   
    ![Válassza a Tallózás, Application Insights, az alkalmazás](./media/app-insights-export-stream-analytics/050.png)
2. Hozzon létre egy folyamatos exportálást.
   
    ![Válassza a beállítások, a folyamatos exportálás hozzáadása](./media/app-insights-export-stream-analytics/060.png)

    Válassza ki a korábban létrehozott tárfiók:

    ![Az Exportálás cél beállítása](./media/app-insights-export-stream-analytics/070.png)

    Állítsa be az eseménytípusok, szeretne látni:

    ![Válassza ki az események típusai](./media/app-insights-export-stream-analytics/080.png)

1. Néhány adat gyűlik össze legyen. Dőljön hátra, és a felhasználók használják az alkalmazását egy ideig. Telemetriai adatok származnak, és láthatja a statisztikai diagramok a [metrika explorer](app-insights-metrics-explorer.md) és az egyes események [diagnosztikai keresés](app-insights-diagnostic-search.md). 
   
    És emellett az adatok a Storage exportálandó. 
2. Vizsgálja meg az exportált adatok. A Visual Studióban válassza **megtekintése / Cloud Explorer**, és nyissa meg az Azure / Storage. (Ha ez a menüpont nem rendelkezik, az Azure SDK-t telepítenie kell: Nyissa meg az új projekt párbeszédpanelen, majd Visual C# / Cloud / .NET-hez készült Microsoft Azure SDK beszerzése.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Jegyezze fel a közös rész az elérési út neve, amely az alkalmazás neve és a kialakítási kulcs származik. 

Az események írja a blob-fájlok JSON formátumban. Minden fájl tartalmazhat egy vagy több esemény. Ezért szeretnénk az eseményadatokat olvassa el és szűrje ki a kívánt mezőket. Mindenféle dolgot javítanunk kell az adatokat, de a csomag jelenleg a Stream Analytics használatával az adatok a Power bi-bA kanálu szolgáltatás.

## <a name="create-an-azure-stream-analytics-instance"></a>Hozzon létre egy Azure Stream Analytics-példányt
Az a [az Azure portal](https://portal.azure.com/), az Azure Stream Analytics szolgáltatás és a egy új Stream Analytics-feladat létrehozása:

![](./media/app-insights-export-stream-analytics/SA001.png)

![](./media/app-insights-export-stream-analytics/SA002.png)

Ha az új feladat jön létre, válassza ki a **erőforrás megnyitása**.

![](./media/app-insights-export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Új bemenet hozzáadása

![](./media/app-insights-export-stream-analytics/SA004.png)

Állítsa be, hogy a folyamatos exportálás blobból bemeneti:

![](./media/app-insights-export-stream-analytics/SA0005.png)

Most szüksége lesz az elsődleges hozzáférési kulcs az Ön Tárfiókjából, amely a korábban feljegyzett. Állítsa az másként a Tárfiókkulcs.

### <a name="set-path-prefix-pattern"></a>Set elérési út előtagmintája

**Győződjön meg arról, az értékre a dátumformátum ÉÉÉÉ-HH-nn (szaggatott).**

Az elérésiút-előtag a minta adja meg, ahol Stream Analytics a bemeneti fájlokat keres a storage-ban. Meg kell azt, hogy hogyan tárolja az adatok folyamatos exportálása. Állítsa be, ehhez hasonló:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Ebben a példában:

* `webapplication27` Application Insights-erőforrás neve **csupa kisbetű**.
* `1234...` a rendszerállapotkulcsot az Application Insights-erőforrás van **kötőjelek felsorolhatja**. 
* `PageViews` az elemzési adatok típusát. Az elérhető típusokat a szűrőt, állítsa be a folyamatos exportálás függenek. Vizsgálja meg az exportált adatok megtekintéséhez az elérhető típusokat, és tekintse meg a [adatmodell exportálása](app-insights-export-data-model.md).
* `/{date}/{time}` szó szerint írt mintát.

> [!NOTE]
> Vizsgálja meg a tárolót, hogy az elérési út jobb beolvasása.
> 

## <a name="add-new-output"></a>Új kimenet hozzáadása
Most válassza ki a feladatot > **kimenetek** > **Hozzáadás**.

![](./media/app-insights-export-stream-analytics/SA006.png)


![Válassza ki az új csatorna, kattintson a kimenetek, a Hozzáadás, a Power bi-ban](./media/app-insights-export-stream-analytics/SA010.png)

Adja meg a **munkahelyi vagy iskolai fiók** engedélyezéséhez a Stream Analytics, a Power BI-erőforrás eléréséhez. Ezután találjon ki egy nevet a kimenetet, és a Power BI-adatkészletekbe cél és a tábla.

## <a name="set-the-query"></a>Állítsa be a lekérdezést
A lekérdezés fordítása bemeneti, kimeneti szabályozza.

A teszt funkció segítségével ellenőrizze, hogy a megfelelő kimeneti kap. Adjon meg a mintaadatokat, amely a bemeneti oldaláról időt vett igénybe. 

### <a name="query-to-display-counts-of-events"></a>Lekérdezés eseményszámok megjelenítése
Illessze be ezt a lekérdezést:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* Exportálás – bemeneti adatok az aliast, hogy megadta a bemeneti Stream
* pbi-kimenet a meghatározott kimeneti alias
* Használjuk a [külső a alkalmazni GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) mert beágyazott JSON-tömb szerepel az esemény nevét. Majd válassza a választja ki az esemény nevét, és az adott időszakban az ilyen nevű-példányok számát. A [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) záradék csoportosítja az elemek egy perces időszakokra.

### <a name="query-to-display-metric-values"></a>Lekérdezés metrikaértékek megjelenítése
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Ez a lekérdezés oktatások, a metrikák telemetriát az esemény időpontját és a mérőszám értéke. A metrikaértékek találhatók egy tömb, így a Sorok kinyerése a alkalmazni GetElements külső mintát használjuk. "myMetric" Ebben az esetben a metrika neve. 

### <a name="query-to-include-values-of-dimension-properties"></a>Lekérdezés dimenzió tulajdonságának értékét tartalmazza.
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Ez a lekérdezés attól függően, egy adott dimenzió alatt egy rögzített indextől a dimenzió tömbben nélkül a dimenzió tulajdonságának értékét tartalmazza.

## <a name="run-the-job"></a>A feladat futtatása
Indítsa el a feladatot a múltbeli kiválaszthatja a dátumot. 

![Válassza ki a feladatot, majd kattintson a lekérdezést. Illessze be az alábbi mintát.](./media/app-insights-export-stream-analytics/SA008.png)

Várjon, amíg a feladat fut-e.

## <a name="see-results-in-power-bi"></a>Tekintse meg az eredményeket a Power bi-ban
> [!WARNING]
> Sokkal jobb és egyszerűbb [javasolt módját Application Insights-adatok megjelenítése Power BI-ban](app-insights-export-power-bi.md). Bemutatott elérési út csak egy példa mutatja be az exportált adatok feldolgozása.
> 
> 

Nyissa meg a Power BI a munkahelyi vagy iskolai fiókjával, és válassza ki az adatkészlet és a tábla, amelyet a Stream Analytics-feladat kimenetét.

![A Power bi-ban válassza ki az adatkészlet és a mezőket.](./media/app-insights-export-stream-analytics/200.png)

Most már használhatja az adatkészlet, jelentések és irányítópultok [Power BI](https://powerbi.microsoft.com).

![A Power bi-ban válassza ki az adatkészlet és a mezőket.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Nincs adat?
* Ellenőrizze, hogy Ön [állítsa be a dátumformátum](#set-path-prefix-pattern) megfelelően, éééé-hh-nn (a szaggatott vonal).

## <a name="video"></a>Videó
Noam Ben Zeev jeleníti meg a Stream Analytics használatával exportált adatok feldolgozása.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>További lépések
* [Folyamatos exportálás](app-insights-export-telemetry.md)
* [Részletes adatmodell-referencia a tulajdonság típusát és értékét.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

