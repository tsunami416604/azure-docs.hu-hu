---
title: 'Oktatóanyag: az SQL on-demand (előzetes verzió) használata az Azure-beli nyílt adatkészletek elemzéséhez az Azure szinapszis Studióban (előzetes verzió)'
description: Ebből az oktatóanyagból megtudhatja, hogyan végezhet könnyedén olyan felderítő adatelemzéseket, amelyek különböző Azure Open-adatkészleteket egyesítenek az SQL igény szerinti (előzetes verzió) használatával, és megjeleníthetik az eredményeket az Azure szinapszis Studióban.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 0471bd594dcf1b0654d6b4c496e5d39ade2739fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91539520"
---
# <a name="tutorial-use-sql-on-demand-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>Oktatóanyag: az SQL igény szerinti használata az Azure Open-adatkészletek elemzésére és az eredmények megjelenítésére az Azure szinapszis Studióban

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a felderítő adatok elemzését, ha különböző Azure Open-adatkészleteket egyesít az SQL igény szerinti használatával, majd megjeleníti az eredményeket az Azure szinapszis Studióban.

Például elemezzük a [New York City (NYC) taxi adatkészletet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) , amely a következőket tartalmazza:

- Felvételi és lejárati dátumok és időpontok.
- A pick up és a drop-off helyszínek. 
- Utazási távolságok.
- Részletezett viteldíjak.
- Díjszabási típusok.
- Fizetési típusok. 
- Illesztőprogram – jelentett utasok száma.

## <a name="automatic-schema-inference"></a>Automatikus séma-következtetés

Mivel az adattárolás a Parquet fájlformátumban történik, az automatikus séma-következtetés elérhető. Az adatokat egyszerűen lekérdezheti a fájlok összes oszlopának adattípusának listázása nélkül. A virtuális oszlop mechanizmust és a filepath függvényt is használhatja a fájlok bizonyos részhalmazának kiszűrésére.

Először Ismerkedjen meg a New York-i taxi-adatszolgáltatással a következő lekérdezés futtatásával:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

A következő kódrészlet a New York-i taxi-adatelemzés eredményét mutatja be:

![New York-i taxi – adateredmény részlete](./media/tutorial-data-analyst/1.png)

Hasonlóképpen lekérdezheti a nyilvános ünnepnapok adatkészletét a következő lekérdezés használatával:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

A következő kódrészlet a nyilvános ünnepnapok adatkészletének eredményét jeleníti meg:

![Munkaszüneti adatkészlet – eredmény kódrészlet](./media/tutorial-data-analyst/2.png)

Végül a következő lekérdezéssel is lekérdezheti az időjárási adatokat használó adatkészletet:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Az alábbi kódrészlet az időjárási adathalmaz eredményét jeleníti meg:

![Időjárási adathalmazok eredményének részlete](./media/tutorial-data-analyst/3.png)

További információt az egyes oszlopok jelentéséről a [New York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)-i taxi, a [Public Holidays](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)és az [időjárási](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) adathalmazok leírásában olvashat.

## <a name="time-series-seasonality-and-outlier-analysis"></a>Idősorozat, szezonális és kiugró elemzés

A következő lekérdezéssel egyszerűen összefoglalhatja a taxik éves számát:

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

Az alábbi kódrészlet a taxik éves számának eredményét mutatja:

![A taxi rides eredményének éves száma](./media/tutorial-data-analyst/4.png)

Az adatok megjeleníthetők a szinapszis Studióban úgy, hogy a **táblázatról** a **diagram** nézetre vált. Különböző típusú diagramok közül választhat, például a **területen**, a **sáv**, az **oszlop**, a **vonal**, a **torta**és a **Scatter**. Ebben az esetben ábrázolja az **oszlopdiagram** és a **category (kategória** ) oszlopot a **current_year**értékre:

![A rides/Year diagramot bemutató oszlopdiagram](./media/tutorial-data-analyst/5.png)

Ebből a vizualizációból kiderül, hogy az évek során csökkenő számú túrákra mutató tendencia egyértelműen látható. Ezt a csökkenést valószínűleg a Ride-Sharing vállalatok közelmúltbeli megnövekedett népszerűsége okozza.

> [!NOTE]
> Az oktatóanyag írásakor a 2019-es adatmennyiség hiányos. Ennek eredményeképpen óriási mennyiségű csökkenés van az adott évre vonatkozó túrák számában.

Ezután az elemzést egyetlen évre koncentráljuk, például 2016. A következő lekérdezés az év folyamán a túrák napi számát adja vissza:

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

A következő kódrészlet a lekérdezés eredményét jeleníti meg:

![A 2016-es találatok napi száma](./media/tutorial-data-analyst/6.png)

Újra könnyedén megjelenítheti az adatdiagramot úgy, **current_day** hogy az **oszlopdiagram** a **category (kategória** ) oszlopra van beállítva, és a **Jelmagyarázat (sorozat)** oszlopa **rides_per_day**értékre van állítva.

![Oszlopdiagram, amely a 2016-as napi számú túrákat mutatja](./media/tutorial-data-analyst/7.png)

A mintaterület diagramon láthatja, hogy heti minta van, szombatonként, a csúcs napjával. A nyári hónapokban a vakációk miatt kevesebb taxi rides van. Azt is figyelje meg, hogy a taxi-túrák száma jelentős mértékben elesik, és nem egyértelmű minta, hogy mikor és miért jelentkeznek.

Ezután lássuk, hogy a kilépések a nyilvános munkaszünetekkel együtt csatlakoznak a New York-i taxi rides adatkészlethez a nyilvános szünnapok adatkészlettel:

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

![A New York-i taxik és a munkaszüneti adatkészletek eredményeinek megjelenítése](./media/tutorial-data-analyst/8.png)

Most pedig szeretnénk kiemelni a taxik számát a nyilvános ünnepek alatt. Erre a célra a **category (kategória** ) oszlopra, a **rides_per_day** és az **ünnepnapot** a **Jelmagyarázat (adatsorozat)** oszlopai **közül választjuk** .

![A nyilvános ünnepek diagramja során beérkező taxik száma](./media/tutorial-data-analyst/9.png)

A mintaterület diagramon láthatja, hogy a nyilvános ünnepek alatt a taxis túrák száma alacsonyabb. Január 23-án még egy megmagyarázhatatlan nagy csökkenés van. Az időjárási adatokat tartalmazó adathalmaz lekérdezésével vizsgáljuk meg a New York-i időjárási időt:

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

![Időjárási adathalmaz eredményeinek vizualizációja](./media/tutorial-data-analyst/10.png)

A lekérdezés eredménye azt jelzi, hogy a taxik számának csökkenése a következő okból történt:

- A mai napon hóviharban volt a nagy hóban (~ 30 cm).
- Hideg volt (a hőmérséklet nem éri el a nulla fokos Celsius-fokot).
- Szeles (~ 10 m/s).

Ebből az oktatóanyagból megtudhatta, hogy az adatelemzők hogyan tudják gyorsan végrehajtani a felderítő adatelemzést, és a különböző adatkészleteket egyszerűen kombinálhatja az SQL igény szerinti használatával, és megjelenítheti az eredményeket az Azure szinapszis Studio használatával.

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan csatlakoztatható az SQL on-demand Power BI Desktop és hogyan hozhat létre jelentéseket, tekintse meg az [SQL igény szerinti csatolása Power bi Desktop és jelentések létrehozása](tutorial-connect-power-bi-desktop.md)című témakört.
 
