---
title: Adatelemzői oktatóanyag – az SQL on-demand (előzetes verzió) használata az Azure-beli nyílt adatkészletek elemzéséhez az Azure szinapszis Studióban (előzetes verzió)
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet egyszerűen elvégezni a felderítő adatelemzést, amely különböző Azure Open-adatkészleteket használ az SQL on-demand (előzetes verzió) használatával, és megjelenítheti az eredményeket az Azure szinapszis Studióban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b2fe4dea27564b96c5ef1734dc16ca4525011d17
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745647"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Az SQL on-demand (előzetes verzió) használata az Azure Open-adatkészletek elemzésére és az eredmények megjelenítésére az Azure szinapszis Studióban (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a felderítő adatok elemzését, ha különböző Azure Open-adatkészleteket egyesít az SQL igény szerinti használatával, majd megjeleníti az eredményeket az Azure szinapszis Studióban.

A [New York City (NYC) taxi-adatkészletet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) elemezzük, amely magában foglalja a pick-up és a drop-off dátum/idő, a pick-up és a drop-off helyszínek, az utazási távolságok, a részletezett viteldíjak, a díjszabási típusok, a fizetési típusok és a járművezető által jelentett utasok számát.

Az elemzés célja, hogy megkeresse a taxi-túrák számának időbeli változásával kapcsolatos trendeket. Két másik Azure Open-adatkészletet (a[munkaszüneti napokat](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) és [az időjárási adatokat](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) elemezve megtudhatja, hogy a taxik hányan vannak kiugróan.

## <a name="create-data-source"></a>Adatforrás létrehozása

Az adatforrás-objektum az Azure Storage-fiókra hivatkozik, ahol az adatelemzés szükséges. A nyilvánosan elérhető tárterülethez nincs szükség valamilyen hitelesítő adatra a tároló eléréséhez.

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a credential.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="automatic-schema-inference"></a>Automatikus séma-következtetés

Mivel a rendszer az adattárolást a parketta fájlformátumában tárolja, az automatikus séma-következtetés elérhetővé válik, így az adatlekérdezés nem szükséges a fájlok összes oszlopának adattípusának listázásához. Emellett a virtuális oszlop mechanizmusa és a filepath függvény használatával kiszűrheti a fájlok bizonyos részhalmazát.

Először Ismerkedjen meg a New York-i taxi-adattal a következő lekérdezés futtatásával:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
```

A következő ábrán a New York-i taxik adatrészlete látható:

![eredmény részlete](./media/tutorial-data-analyst/1.png)

Hasonlóképpen a következő lekérdezéssel tudjuk lekérdezni a nyilvános ünnepek adatkészletét:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'holidaydatacontainer/Processed/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Az alábbi ábrán a nyilvános ünnepek adatkészletének eredményét láthatja:

![2. eredmény](./media/tutorial-data-analyst/2.png)

Végül a következő lekérdezéssel is lekérdezheti az időjárási adatkészletet:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
```

Az alábbi ábrán az időjárási adatkészlet eredményének részlete látható:

![3. eredmény-kódrészlet](./media/tutorial-data-analyst/3.png)

További információt az egyes oszlopok jelentéséről a [New York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)-i taxi, a [Public Holidays](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)és az [időjárási](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) adathalmazok leírásában olvashat.

## <a name="time-series-seasonality-and-outlier-analysis"></a>Idősorozat, szezonális és kiugró elemzés

A következő lekérdezéssel egyszerűen összefoglalhatja a taxis szolgáltatás éves számát:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Az alábbi ábrán a taxik éves számának eredményét láthatja:

![4. eredmény](./media/tutorial-data-analyst/4.png)

Az adatok a szinapszis Studióban jeleníthetők meg a tábla diagram nézetbe való váltásával. A különböző típusú diagramok közül választhat (a körzet, a sáv, az oszlop, a vonal, a torta és a pontdiagram). Ebben az esetben az oszlopdiagram "current_year" értékre állítása a kategória oszloppal:

![eredmény vizualizáció 5](./media/tutorial-data-analyst/5.png)

Ebből a vizualizációból kiderül, hogy az évek során csökkenő számú túrákra mutató tendencia egyértelműen látható, ami valószínűleg a Ride Sharing vállalatoknak a közelmúltban megnövekedett népszerűségének köszönhető.

> [!NOTE]
> A jelen oktatóanyag írásakor a 2019-es adatmennyiség hiányos, így az adott évben számos utazásra van lehetőség.

Ezután az elemzést egyetlen évre koncentráljuk, például 2016. Az alábbi lekérdezés az év folyamán napi számú túrákat ad vissza:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Az alábbi táblázat a lekérdezés eredményének részletét jeleníti meg:

![eredmény 6. kódrészlet](./media/tutorial-data-analyst/6.png)

A "current_day" kategóriát tartalmazó oszlopdiagram és a "rides_per_day" Jelmagyarázat (sorozat) oszlop ábrázolásával egyszerűen megjelenítheti az adatdiagramot.

![eredmény vizualizációja 7](./media/tutorial-data-analyst/7.png)

A mintaterületen megfigyelhető, hogy heti minta van a szombat csúcsával. A nyári hónapokban a vakáció időtartama miatt kevesebb taxi-utazásra van lehetőség. Van azonban néhány jelentős mennyiségű, a taxival való elfordulás, amely nem egyértelmű mintázatot jelent, és hogy miért is jelentkeznek.

Következő lépésként lássuk, hogy ezek a cseppek a nyilvános munkaszüneti napokhoz csatlakoznak-e a New York-i és a Public Holidays-adatkészlettel:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'holidaydatacontainer/Processed/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![eredmény vizualizáció 8](./media/tutorial-data-analyst/8.png)

Ezúttal azt szeretnénk kiemelni, hogy a nyilvános munkaszüneti napokon milyen számú taxit kell használni. Erre a célra a kategória oszlop és a "rides_per_day" és a "Holiday" Jelmagyarázat (adatsorozat) oszlopai közül a "None" elemet választjuk.

![eredmény vizualizáció 9](./media/tutorial-data-analyst/9.png)

A mintaterületből egyértelműen látható, hogy a nyilvános ünnepek során a taxik száma alacsonyabb. Január 23-án azonban még egy megmagyarázhatatlan hatalmas csökkenés van. Az időjárási adatkészlet lekérdezésével nézzük meg a New York-i időjárási időt:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![eredmény vizualizáció 10](./media/tutorial-data-analyst/10.png)

A lekérdezés eredményei azt jelzik, hogy a több taxis túrák csökkenése az alábbiak miatt volt:

- a Blizzard ezen a napon NYC-ben, mivel nagy volt a hó (~ 30 cm)
- hideg volt (0 Celsius-fok alatti hőmérséklet)
- és szeles (~ 10M/s)

Ebből az oktatóanyagból megtudhatta, hogy az adatelemző hogyan képes gyorsan elvégezni a felderítő adatok elemzését, és könnyen egyesítheti a különböző adatkészleteket az SQL igény szerinti használatával, és megjelenítheti az eredményeket az Azure szinapszis Studio használatával.

## <a name="next-steps"></a>További lépések

Tekintse át az [igény szerinti kapcsolódás az SQL-hez Power BI Desktop & jelentés létrehozása című](tutorial-connect-power-bi-desktop.md) cikket, amelyből megtudhatja, hogyan csatlakoztathatók az SQL on-demand a Power bi Desktophoz és a jelentések létrehozásához.
 
