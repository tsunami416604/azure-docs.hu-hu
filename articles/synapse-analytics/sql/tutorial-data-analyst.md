---
title: Adatelemzői oktatóanyag – Az SQL igény szerinti (előzetes verzió) használata az Azure Open Datasets elemzéséhez az Azure Synapse Studio-ban (előzetes verzió)
description: Ebben az oktatóanyagban megtudhatja, hogyan végezhet könnyen feltáró adatelemzést a különböző Azure Open adatkészletek kombinálásával az SQL igény szerinti (előzetes verzió) használatával, és vizualizálhatja az eredményeket az Azure Synapse Studióban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423228"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Az SQL igény szerinti (előzetes verzió) használatával elemezheti az Azure Open Datasets adatkészleteket, és megjelenítheti az eredményeket az Azure Synapse Studio-ban (előzetes verzió)

Ebben az oktatóanyagban megtudhatja, hogyan végezhet feltáró adatok elemzését a különböző Azure Open adatkészletek kombinálásával az SQL igény szerinti használatával, majd az eredmények megjelenítése az Azure Synapse Studio-ban.

Különösen a New [York-i (NYC) Taxi adatkészletet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) elemezheti, amely tartalmazza a felvételi és átadási dátumokat/időpontokat, a felvételi és leadási helyeket, az utazási távolságokat, a tételes viteldíjakat, a díjtípusokat, a fizetési típusokat és a járművezető által jelentett utasszámokat.

Az elemzés középpontjában a taxitúrák számának időbeli változása idoszakai ban vannak. Két másik Azure Open Datasets[(munkaszüneti napok](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) és [időjárási adatok)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)elemzéséhez a taxifuvarok kiugró értékének megértéséhez.

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Automatikus sémakövetkeztetés

Mivel az adatok parquet fájlformátumban tárolódnak, automatikus sémakövetkeztetés áll rendelkezésre, így könnyen lekérdezhető az adatok anélkül, hogy fel kellene sorolni a fájlok összes oszlopának adattípusait. Továbbá, lehet használni a virtuális oszlop mechanizmus és filepath funkció kiszűrni egy bizonyos részhalmaza fájlokat.

Először ismerkedjen meg a NYC Taxi adataival a következő lekérdezés futtatásával:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

A következőkben a NYC Taxi adatainak eredménykódrészletét mutatja:

![eredménykódrészlet](./media/tutorial-data-analyst/1.png)

Hasonlóképpen lekérdezhetjük a munkaszüneti napok adatkészletét a következő lekérdezéssel:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Az alábbiakban a munkaszüneti napok adatkészletének eredménykódrészletét mutatja be:

![eredmény részlet 2](./media/tutorial-data-analyst/2.png)

Végül az időjárási adatkészletet a következő lekérdezéssel is lekérdezhetjük:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

A következőkben az időjárási adatkészlet eredménykódrészletét mutatja be:

![eredmény részlet 3](./media/tutorial-data-analyst/3.png)

Az egyes oszlopok jelentéséről a [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [munkaszüneti napok](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)és időjárási [adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) leírásában olvashat bővebben.

## <a name="time-series-seasonality-and-outlier-analysis"></a>Idősorok, szezonalitás és kiugró értékek elemzése

A taxitúrák éves számát a következő lekérdezéssel könnyen összegezheti:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

A következő mutatja az eredményrészlet et az éves taxiállomások számára:

![eredményrészlet 4](./media/tutorial-data-analyst/4.png)

Az adatok a Synapse Studio alkalmazásban is megjelenhetnek a Táblázat nézetről a Diagram nézetre való váltással. Különböző diagramtípusok közül választhat (Terület, Sáv, Oszlop, Vonal, Kör és Pont). Ebben az esetben ábrázoljuk az Oszlopdiagramot úgy, hogy a Kategória oszlop "current_year" lesz:

![eredmény megjelenítés 5](./media/tutorial-data-analyst/5.png)

Ebből a vizualizáció, a tendencia a csökkenő számú túrák az évek során jól látható, feltehetően a közelmúltban megnövekedett népszerűsége ride megosztása cégek.

> [!NOTE]
> A bemutató megírásakor a 2019-es adatok hiányosak, így az adott évben hatalmas csökkenés torkig van a túrák számával.

Ezután összpontosítsuk elemzésünket egyetlen évre, például 2016-ra. A következő lekérdezés az adott évben a napi fuvarok számát adja vissza:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

A következő a lekérdezés eredménykódrészletét mutatja be:

![eredmény részlet 6](./media/tutorial-data-analyst/6.png)

Ismét könnyen megjeleníthetjük az adatokat az Oszlopdiagram ábrázolásával a "current_day" kategória oszlopával és a "rides_per_day" jelmagyarázat (adatsor) oszlopával.

![eredmény megjelenítés 7](./media/tutorial-data-analyst/7.png)

A telek, megfigyelhető, hogy van egy heti minta, a szombati csúcs. A nyári hónapokban kevesebb taxiút van a nyaralási időszak miatt. Van azonban néhány jelentős csepp a taxiutak számában is, anélkül, hogy egyértelmű mintát kapnának, amikor és miért fordulnak elő.

Következő, lássuk, ha ezek a cseppek potenciálisan korrelál a munkaszüneti napokon csatlakozott NYC taxi túrák a munkaszüneti napok adatkészlet:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
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
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
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

![eredmény megjelenítés 8](./media/tutorial-data-analyst/8.png)

Ezúttal szeretnénk kiemelni a taxitúrák számát munkaszüneti napokon. Ebből a célból a "nincs" lehetőséget választjuk a Kategória oszlophoz, a "rides_per_day" és a "ünnep" jelmagyarázat (sorozat) oszlopokat.

![eredmény megjelenítés 9](./media/tutorial-data-analyst/9.png)

A telek, akkor jól látható, hogy munkaszüneti napokon számos taxi túrák alacsonyabb. Január 23-án azonban még mindig van egy megmagyarázhatatlan hatalmas visszaesés. Nézzük meg az időjárás NYC azon a napon lekérdezésével az időjárás adatkészlet:

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
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![eredmény megjelenítés 10](./media/tutorial-data-analyst/10.png)

A lekérdezés eredményei azt mutatják, hogy a számos taxizás csökkenése a következőknek köszönhető:

- hóvihar azon a napon NYC, mivel nem volt erős hó (~ 30 cm)
- hideg volt (hőmérséklet nulla Celsius fok alatt)
- és szeles (~10m/s)

Ez az oktatóanyag bemutatja, hogy az adatelemző hogyan végezhet gyorsan feltáró adatelemzést, egyszerűen kombinálhatja a különböző adatkészleteket az SQL igény szerinti használatával, és hogyan jelenítheti meg az eredményeket az Azure Synapse Studio használatával.

## <a name="next-steps"></a>További lépések

Tekintse át az [SQL igény szerinti csatlakoztatása a Power BI Desktophoz című cikket, & jelentéslétrehozása](tutorial-connect-power-bi-desktop.md) című cikkből megtudhatja, hogyan csatlakoztathatja az SQL-t igény szerint a Power BI Desktophoz, és hogyan hozhat létre jelentéseket.
 
