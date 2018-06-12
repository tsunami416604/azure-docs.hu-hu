---
title: Exportálja a Stream Analytics az Azure Application Insights segítségével |} Microsoft Docs
description: A Stream Analytics folyamatosan átalakíthatja, szűrésére és az adatok az Application Insights exportálnia útvonalát.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: mbullwin
ms.openlocfilehash: 874a338c27262de29b1806352ec3ade068c188e0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294234"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Az Application Insights exportált adatok feldolgozása a Stream Analytics segítségével
[Az Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) adatok feldolgozására ideális eszköz [Application Insights-ból exportált](app-insights-export-telemetry.md). A Stream Analytics segítségével olvasnak be adatokat különböző forrásokból. Az átalakítási és szűrje az adatokat és mosdók számos irányítja.

Ebben a példában mi létrehozunk egy adaptert, amely beolvassa az Application Insights, átnevezése és feldolgozza az egyes mezőit és kiszolgálókészletéhez azt a Power BI-bA.

> [!WARNING]
> Nincsenek sokkal hatékonyabb és könnyebben [javasolt módját Application Insights adatainak megjelenítése Power BI-ban](app-insights-export-power-bi.md). A bemutatott elérési út csak egy példa az exportált adatok feldolgozása mutatja be.
> 
> 

![Az exportált keresztül SA PBI blokk diagramja](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tároló létrehozása az Azure-ban
A folyamatos exportálás mindig kimenete adatokat egy Azure Storage-fiók, ezért meg kell először hozza létre a tárolót.

1. Az előfizetésben a "klasszikus" storage-fiók létrehozása a [Azure-portálon](https://portal.azure.com).
   
   ![Azure-portálon válassza ki az új, az adatok, a tárolás](./media/app-insights-export-stream-analytics/030.png)
2. Tároló létrehozása
   
    ![Az új tárterületen található tárolók kiválasztása, kattintson a tárolók csempe, majd a Hozzáadás](./media/app-insights-export-stream-analytics/040.png)
3. A tárelérési kulcs másolása
   
    Kell, hogy hamarosan állítsa be a stream analytics szolgáltatás bemenete.
   
    ![A tárolóban nyissa meg a beállításokat, a kulcsokat, és az elsődleges elérési kulcsot másolatot](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Indítsa el az Azure storage a folyamatos exportálás
[A folyamatos exportálás](app-insights-export-telemetry.md) helyezi át az adatokat az Application Insights az Azure storage-be.

1. Az Azure portálon tallózással keresse meg az Application Insights-erőforrást az alkalmazáshoz létrehozott.
   
    ![A Tallózás, az Application Insights az alkalmazáshoz](./media/app-insights-export-stream-analytics/050.png)
2. A folyamatos exportálás létrehozása.
   
    ![Válassza a beállítások, folyamatos exportálási hozzáadása](./media/app-insights-export-stream-analytics/060.png)

    Válassza ki a korábban létrehozott tárfiókot:

    ![Exportálás céljának beállítása](./media/app-insights-export-stream-analytics/070.png)

    Állítsa be a megjeleníteni kívánt típusait:

    ![Válassza ki az esemény típusa](./media/app-insights-export-stream-analytics/080.png)

1. Néhány adat gyűlik össze legyen. Elhelyezkedik vissza, és a felhasználók használhassa az alkalmazást egy ideig. Telemetria határozza meg, és látni fogja, statisztikai diagramok [metrika explorer](app-insights-metrics-explorer.md) és az egyes események [diagnosztikai keresési](app-insights-diagnostic-search.md). 
   
    És is, exportálja az adatokat a tárhelyre. 
2. Vizsgálja meg az exportált adatokat. A Visual Studio felületén válassza **megtekintése / Cloud Explorer**, és nyissa meg az Azure / tárolási. (Ha még nem rendelkezik a menüpont, szeretné-e az Azure SDK telepítése: az új projekt párbeszédpanel megnyitásához, és nyissa meg a Visual C# / felhő / Microsoft Azure SDK beolvasása a .NET-hez.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Jegyezze fel az elérési út, az alkalmazás nevét és instrumentation kulcsból származtatott közös részét. 

Az események a blob-JSON formátumú fájlok kerülnek. Minden fájl tartalmazhat egy vagy több esemény. Ezért szeretnénk az esemény-adatok olvasása, és azt szeretnénk, ha a mezők szűrik. Nincsenek a különböző dolgok, azt megteheti az adatokat, de a terv ma Stream Analytics segítségével átadhatja az adatokat a Power bi-bA.

## <a name="create-an-azure-stream-analytics-instance"></a>Hozzon létre egy Azure Stream Analytics-példányt
Az a [Azure-portálon](https://portal.azure.com/), válassza ki az Azure Stream Analytics szolgáltatás, és hozzon létre egy új Stream Analytics-feladatot:

![](./media/app-insights-export-stream-analytics/SA001.png)

![](./media/app-insights-export-stream-analytics/SA002.png)

Amikor új feladatot hoz létre, jelölje ki a **forrást**.

![](./media/app-insights-export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Egy új bemenet hozzáadása

![](./media/app-insights-export-stream-analytics/SA004.png)

Állítsa be úgy, hogy a folyamatos exportálás blobból bemeneti:

![](./media/app-insights-export-stream-analytics/SA005.png)

Most szüksége lesz az elsődleges elérési kulcsot importáljon a Tárfiókba, amelyet korábban feljegyzett. Állítsa be ezt a Tárfiók kulcsára.

### <a name="set-path-prefix-pattern"></a>Set elérési út előtag mintája

**Győződjön meg arról, hogy beállítása a Date formátum éééé-hh-nn-(kötőjel).**

Az elérési út előtag mintája határozza meg, ahol a Stream Analytics talál a bemeneti fájlok a tároló. Állítsa be úgy, hogy hogyan tárolja az adatokat folyamatos exportálni kell. Állítsa be ehhez hasonló:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Ebben a példában:

* `webapplication27` az Application Insights-erőforrás neve **összes kisbetű**.
* `1234...` az Application Insights-erőforrások instrumentation kulcsa **kötőjelek kihagyásával**. 
* `PageViews` az elemezni kívánt adatok típusát. A használható típusok a folyamatos exportálás beállítása szűrő függ. Vizsgálja meg az exportált adatok megtekintéséhez a használható típusok, és tekintse meg a [exportálja az adatokat az adatmodellbe](app-insights-export-data-model.md).
* `/{date}/{time}` a minta írt szó.

> [!NOTE]
> Vizsgálja meg a tárolás ellenőrizze, hogy az elérési út jobb beolvasása.
> 

## <a name="add-new-output"></a>Új kimeneti hozzáadása
Most jelölje ki a feladatot > **kimenetek** > **Hozzáadás**.

![](./media/app-insights-export-stream-analytics/SA006.png)


![Jelölje ki az új csatornát, kattintson a kimenetek, a Hozzáadás, a Power bi-ban](./media/app-insights-export-stream-analytics/SA010.png)

Adja meg a **munkahelyi vagy iskolai fiók** engedélyezése a Stream Analytics a Power BI erőforrás elérésére. Majd találjon ki a kimenetet, és a cél a Power BI DataSet adatkészlet és a tábla nevét.

## <a name="set-the-query"></a>A lekérdezés beállítása
A lekérdezés fordítása bemeneti, kimeneti szabályozza.

A teszt funkció segítségével ellenőrizze, hogy a megfelelő kimeneti kap. A mintaadatok, amely a bemeneti adatok oldalról lejegyezte adjon neki. 

### <a name="query-to-display-counts-of-events"></a>Megjelenítendő lekérdezések események száma
Illessze be a lekérdezést:

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

* export-bemeneti érték a jelenleg megadott, a bemeneti adatfolyam alias
* o-pbi a kimeneti alias meghatározott
* Használjuk [külső alkalmazása GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) mert az esemény neve nem egy beágyazott JSON-tömb. A Select választja, majd az esemény-névvel, az adott időszakban ilyen nevű példányok számát. A [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) záradék csoportosítja az elemek perces időszakokra.

### <a name="query-to-display-metric-values"></a>Lekérdezés metrika értékek megjelenítése
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

* Ez a lekérdezés eseményrögzítési azokat a metrikák telemetriai adat az esemény időpontja és a Átjárómetrika értékeként. A metrika értékei egy tömb belül, a külső alkalmazása GetElements mintát használjuk a sor kibontásához. "myMetric" Ebben az esetben a mérőszám a neve. 

### <a name="query-to-include-values-of-dimension-properties"></a>Lekérdezés dimenzió a tulajdonságokat tartalmazza
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

* Ez a lekérdezés a dimenzió tulajdonságok nélkül attól függően, hogy egy adott dimenzió alatt a dimenzió tömb rögzített indexnél értékeket tartalmaz.

## <a name="run-the-job"></a>A feladat futtatása
A múltban elindítani a feladatot, kiválaszthatja a dátumot. 

![Jelölje ki a feladatot, és kattintson a lekérdezést. Illessze be az alábbi minta.](./media/app-insights-export-stream-analytics/SA008.png)

Várjon, amíg a feladat fut-e.

## <a name="see-results-in-power-bi"></a>A Power BI eredmények megtekintése
> [!WARNING]
> Nincsenek sokkal hatékonyabb és könnyebben [javasolt módját Application Insights adatainak megjelenítése Power BI-ban](app-insights-export-power-bi.md). A bemutatott elérési út csak egy példa az exportált adatok feldolgozása mutatja be.
> 
> 

Nyissa meg a Power BI a munkahelyi vagy iskolai fiókkal, majd válassza ki a DataSet adatkészlet és a tábla, amelyet a Stream Analytics-feladat eredményének.

![A Power bi-ban válassza ki a DataSet adatkészlet és a mezőket.](./media/app-insights-export-stream-analytics/200.png)

Most már használhat ez az adatkészlet a jelentések és irányítópultok a [Power BI](https://powerbi.microsoft.com).

![A Power bi-ban válassza ki a DataSet adatkészlet és a mezőket.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Nincs adat?
* Ellenőrizze, hogy [a dátum formátum beállítása](#set-path-prefix-pattern) megfelelően éééé-hh-nn (a kötőjeleket) számára.

## <a name="video"></a>Videó
Noam Ben Zeev bemutatja, hogyan használja a Stream Analytics exportált adatok feldolgozása.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>További lépések
* [Folyamatos exportálás](app-insights-export-telemetry.md)
* [A részletes adatok modell útmutató a tulajdonság típusát és értékét.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

