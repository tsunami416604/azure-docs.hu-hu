---
title: Exportálás a Stream Analytics használatával az Azure Application Insightsból | Microsoft dokumentumok
description: A Stream Analytics folyamatosan átalakíthatja, szűrheti és továbbíthatja az Application Insightsból exportált adatokat.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 15d1efa3a632024429d41f27fc23c569cd85bec2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536879"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Az Application Insightsból exportált adatok feldolgozása a Stream Analytics segítségével
[Az Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ideális eszköz az [Application Insightsból exportált adatok feldolgozásához.](export-telemetry.md) A Stream Analytics különböző forrásokból is letud adatokat lekéri. Átalakíthatja és szűrheti az adatokat, majd különböző fogadókhoz irányíthatja azokat.

Ebben a példában létrehozunk egy adaptert, amely adatokat fogad az Application Insightsból, átnevez és feldolgoz néhány mezőt, és átírja azt a Power BI-ba.

> [!WARNING]
> Az [Application Insights-adatok power bi-ban való megjelenítésének](../../azure-monitor/app/export-power-bi.md )sokkal jobb és egyszerűbb módjai vannak. Az itt bemutatott elérési út csak egy példa az exportált adatok feldolgozásának szemléltetésére.
> 
> 

![Blokkdiagram az SA-n keresztül pbi-re történő exportáláshoz](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tárhely létrehozása az Azure-ban
A folyamatos exportálás mindig adatokat ad ki egy Azure Storage-fiókba, ezért először létre kell hoznia a tárolót.

1. Hozzon létre egy "klasszikus" tárfiókot az előfizetésében az [Azure Portalon.](https://portal.azure.com)
   
   ![Az Azure Portalon válassza az Új, Az adatok, a tárolás lehetőséget.](./media/export-stream-analytics/030.png)
2. Tároló létrehozása
   
    ![Az új tárolóban válassza a Tárolók lehetőséget, kattintson a Tárolók csempére, majd a Hozzáadás](./media/export-stream-analytics/040.png)
3. A tároló-hozzáférési kulcs másolása
   
    Szüksége lesz rá hamarosan a bemeneti a stream analytics szolgáltatás hoz.
   
    ![A tárolóban nyissa meg a Beállítások, kulcsok és készítsen másolatot az elsődleges hozzáférési kulcsról](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Folyamatos exportálás indítása az Azure-tárhelyre
[A folyamatos exportálás](export-telemetry.md) az Application Insights ból az Azure storage-ba helyezi át az adatokat.

1. Az Azure Portalon keresse meg az alkalmazáshoz létrehozott Application Insights-erőforrást.
   
    ![Válassza a Tallózás, az Application Insights, az alkalmazás lehetőséget](./media/export-stream-analytics/050.png)
2. Hozzon létre egy folyamatos exportálást.
   
    ![Beállítások kiválasztása, Folyamatos exportálás, Hozzáadás](./media/export-stream-analytics/060.png)

    Válassza ki a korábban létrehozott tárfiókot:

    ![Az exportálási cél beállítása](./media/export-stream-analytics/070.png)

    Állítsa be a megtekinteni kívánt eseménytípusokat:

    ![Eseménytípusok kiválasztása](./media/export-stream-analytics/080.png)

1. Hagyja, hogy néhány adat felhalmozódik. Dőljön hátra, és hagyja, hogy az emberek használják a kérelmet egy darabig. Telemetriai adatok jönnek, és látni fogja a statisztikai diagramok [metrika felfedező](../../azure-monitor/platform/metrics-charts.md) és az egyes események [diagnosztikai keresés.](../../azure-monitor/app/diagnostic-search.md) 
   
    És az adatok is exportálják a tárolóba. 
2. Vizsgálja meg az exportált adatokat. A Visual Studio alkalmazásban válassza **a View / Cloud Explorer**lehetőséget, és nyissa meg az Azure / Storage alkalmazást. (Ha nem rendelkezik ezzel a menübeállítással, telepítenie kell az Azure SDK:Nyissa meg az Új projekt párbeszédpanelt, és nyissa meg a Visual C# / Cloud / Get Microsoft Azure SDK for .NET alkalmazást.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Jegyezze fel az elérési út nevének közös részét, amely az alkalmazás nevéből és instrumentation kulcsából származik. 

Az események JSON formátumú blobfájlokba vannak írva. Minden fájl egy vagy több eseményt tartalmazhat. Ezért szeretnénk elolvasni az esemény adatait, és kiszűrni a kívánt mezőket. Az adatokkal sokféle dolgot megtehetünk, de a mai tervünk az, hogy a Stream Analytics segítségével továbbítjuk az adatokat a Power BI-ba.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics-példány létrehozása
Az [Azure Portalon](https://portal.azure.com/)válassza ki az Azure Stream Analytics szolgáltatást, és hozzon létre egy új Stream Analytics-feladatot:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Az új feladat létrehozásakor válassza az **Ugrás az erőforrásra**lehetőséget.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Új bemenet hozzáadása

![](./media/export-stream-analytics/SA004.png)

Állítsa be, hogy a folyamatos exportálásblobból származó adatokat vegyen be:

![](./media/export-stream-analytics/SA0005.png)

Most szüksége lesz az elsődleges hozzáférési kulcsa a tárfiókból, amely korábban megjegyezte. Állítsa be ezt a tárfiók kulcs.

### <a name="set-path-prefix-pattern"></a>Görbeelőtag mintájának beállítása

**Ügyeljen arra, hogy a Dátumformátumot YYYY-MM-DD-re állítsa (kötőjellel).**

A Path prefix minta határozza meg, ahol a Stream Analytics megtalálja a bemeneti fájlokat a tárolóban. Be kell állítania, hogy megfeleljen annak, ahogyan a Folyamatos exportálás tárolja az adatokat. Állítsa be, mint ez:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Ebben a példában:

* `webapplication27`az Application Insights erőforrás **neve.**
* `1234...`az Application Insights erőforrás instrumentation kulcsa, **amely kihagyja a kötőjeleket.** 
* `PageViews`az elemezni kívánt adattípus. Az elérhető típusok a Folyamatos exportálás mezőben beállított szűrőtől függenek. Vizsgálja meg az exportált adatokat a többi elérhető típus megtekintéséhez, és tekintse meg az [exportálási adatmodellt.](export-data-model.md)
* `/{date}/{time}`egy szó szerint írt minta.

> [!NOTE]
> Ellenőrizze a tárolót, hogy biztosan jól jár-e el az elérési út.
> 

## <a name="add-new-output"></a>Új kimenet hozzáadása
Most válassza ki a feladatot, > **kimenetek** > **hozzáadása**.

![](./media/export-stream-analytics/SA006.png)


![Az új csatorna kijelölése, a Kimenetek, a Hozzáadás, a Power BI parancsra](./media/export-stream-analytics/SA010.png)

Adja meg **munkahelyi vagy iskolai fiókját,** hogy engedélyezze a Stream Analytics számára a Power BI-erőforrás elérését. Ezután találjon ki egy nevet a kimenetnek, valamint a cél Power BI-adatkészletnek és táblának.

## <a name="set-the-query"></a>A lekérdezés beállítása
A lekérdezés szabályozza a fordítást a bemenetről a kimenetre.

A Teszt funkció val a megfelelő kimenet beállásának ellenőrzéséhez. Adja meg a bemeneti lapról vett mintaadatokat. 

### <a name="query-to-display-counts-of-events"></a>Lekérdezés az események számának megjelenítéséhez
A lekérdezés beillesztése:

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

* az export-input az az alias, amelyet az adatfolyam bemenetének adtunk
* pbi-output az általunk definiált kimeneti alias
* Az [OUTER APPLY GetElements függvényt használjuk,](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) mert az esemény neve beágyazott JSON-tömbben található. Ezután a Select kiválasztja az esemény nevét, az adott névvel rendelkező példányok számával együtt az időszakban. A [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) záradék az elemeket egyperces időszakokba csoportosítja.

### <a name="query-to-display-metric-values"></a>Metrikus értékek megjelenítésére irányuló lekérdezés
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

* Ez a lekérdezés a metrikák telemetriája az esemény idő és a metrika értéke. A metrikaértékek egy tömbön belül vannak, ezért az OUTER APPLY GetElements mintát használjuk a sorok kibontásához. Ebben az esetben a "myMetric" a metrika neve. 

### <a name="query-to-include-values-of-dimension-properties"></a>A dimenziótulajdonságok értékeinek felvételére irányuló lekérdezés
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

* Ez a lekérdezés a dimenziótulajdonságok értékeit tartalmazza anélkül, hogy attól függően, hogy egy adott dimenzió rögzített indexben van-e a dimenziótömbben.

## <a name="run-the-job"></a>A feladat futtatása
Kiválaszthat egy múltbeli dátumot, ahonnan a feladatot el szeretné indítani. 

![Jelölje ki a feladatot, és kattintson a Lekérdezés gombra. Illessze be az alábbi mintát.](./media/export-stream-analytics/SA008.png)

Várjon, amíg a feladat fut.

## <a name="see-results-in-power-bi"></a>Eredmények megtekintése a Power BI-ban
> [!WARNING]
> Az [Application Insights-adatok power bi-ban való megjelenítésének](../../azure-monitor/app/export-power-bi.md )sokkal jobb és egyszerűbb módjai vannak. Az itt bemutatott elérési út csak egy példa az exportált adatok feldolgozásának szemléltetésére.
> 
> 

Nyissa meg a Power BI-t munkahelyi vagy iskolai fiókjával, és válassza ki a Stream Analytics-feladat kimeneteként meghatározott adatkészletet és táblát.

![A Power BI-ban jelölje ki az adatkészletet és a mezőket.](./media/export-stream-analytics/200.png)

Most már használhatja ezt az adatkészletet jelentésekben és irányítópultokban a [Power BI-ban.](https://powerbi.microsoft.com)

![A Power BI-ban jelölje ki az adatkészletet és a mezőket.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Nincs adat?
* Ellenőrizze, hogy [helyesen állította-e be a dátumformátumot](#set-path-prefix-pattern) YYYY-MM-DD (kötőjellel).

## <a name="video"></a>Videó
Noam Ben Zeev bemutatja, hogyan lehet feldolgozni az exportált adatokat a Stream Analytics használatával.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>További lépések
* [Folyamatos exportálás](export-telemetry.md)
* [Részletes adatmodell-hivatkozás a tulajdonságtípusokra és értékekre vonatkozóan.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

