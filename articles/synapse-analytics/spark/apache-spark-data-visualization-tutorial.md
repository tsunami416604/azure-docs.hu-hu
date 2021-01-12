---
title: Adatvizualizáció az Apache Spark használatával
description: Gazdag adatvizualizációk létrehozása Apache Spark és Azure szinapszis Analytics-jegyzetfüzetek használatával
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 8768b8f8c7bf70b184971abc6ce27e2193823dea
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121549"
---
# <a name="analyze-data-with-apache-spark"></a>Az adatelemzés Apache Spark

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a felderítő adatok elemzését az Azure Open-adatkészletek és a Apache Spark használatával, majd megjelenítheti az eredményeket egy Azure szinapszis Studio notebookon.

Különösen elemezzük a [New York City (New York-i) taxi](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) -adatkészletet. Az adatokat az Azure Open-adatkészletek segítségével érheti el. Az adatkészlet ezen részhalmaza a Yellow taxi-utakról tartalmaz információkat, beleértve az egyes utazásokkal, a kezdő és záró időponttal, valamint a költségeket és egyéb érdekes attribútumokkal kapcsolatos információkat.
  
## <a name="before-you-begin"></a>Előkészületek
- Hozzon létre egy Apache Spark készletet az [Apache Spark készlet létrehozása oktatóanyag](../articles/../quickstart-create-apache-spark-pool-studio.md) alapján 

## <a name="download-and-prepare-the-data"></a>Az adatgyűjtés letöltése és előkészítése
1. Hozzon létre egy jegyzetfüzetet a PySpark kernel használatával. Útmutatásért lásd: [Jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
> [!Note]
> 
> A PySpark kernel miatt nem kell explicit módon létrehoznia a környezeteket. A Spark-környezet automatikusan létrejön az első kód cellájának futtatásakor.
>

2. Ebben az oktatóanyagban számos különböző kódtárat fogunk használni az adatkészlet megjelenítéséhez. Az elemzés végrehajtásához a következő könyvtárakat kell importálni: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Mivel a nyers adatmennyiség parkettás formátumú, a Spark-környezettel közvetlenül is lehívhatja a fájlt a memóriába dataframe. Hozzon létre egy Spark-dataframe az adatoknak a nyílt adatkészletek API-n keresztüli beolvasásával. Itt az olvasási tulajdonságok Spark dataframe *sémáját* fogjuk használni az adattípusok és a séma kikövetkeztetni.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Az adat olvasása után egy kezdeti szűrést szeretnénk végrehajtani az adatkészlet tisztításához. Eltávolíthatjuk a szükségtelen oszlopokat, és további oszlopokat is hozzáadhat a fontos információk kinyeréséhez. Emellett az adatkészleten belül is kiszűrjük a rendellenességeket.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Adatok elemzése
 Adatelemzőként számos eszköz érhető el, amely segít az adatokból származó elemzések kinyerésében. Az oktatóanyag ezen részében néhány hasznos eszközt ismertetünk az Azure szinapszis Analytics-Jegyzetfüzeteken belül. Ebben az elemzésben szeretnénk megismerni azokat a tényezőket, amelyek magasabb szintű taxis tippeket adnak a kiválasztott időszakra vonatkozóan.

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Először is végrehajtjuk a tájékozódási adatok elemzését Apache Spark az SQL és a Magic parancsok segítségével a szinapszis notebookot. A lekérdezésünk után az eredményeket a beépített képesség használatával fogjuk megjeleníteni ```chart options``` . 

1. Hozzon létre egy új cellát a notebookon belül, és másolja az alábbi kódot. Ennek a lekérdezésnek a használatával tisztában vagyunk azzal, hogy az átlagos tip-összegek a kiválasztott időszakban változtak. A lekérdezés segítséget nyújt a további hasznos megállapítások azonosításában is, beleértve a napi minimális/maximális tippet, valamint az átlagos Viteldíj összegét.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. A lekérdezés befejeződése után a **diagram nézetre** való váltással megjelenítheti az eredményeket. Ebben a példában egy **vonalas diagramot** hozunk létre, amely a ```day_of_month``` mező **kulcsként** és ```avgTipAmount``` **értékként** való megadásával történik. Miután elvégezte a beállításokat, kattintson az **alkalmaz** gombra a diagram frissítéséhez. 
   
## <a name="visualize-data"></a>Adatok vizualizációja
A beépített jegyzetfüzet-diagramok mellett népszerű nyílt forráskódú kódtárakat is használhat saját vizualizációk létrehozásához. Az alábbi példákban a Seaborn és a Matplotlib is használjuk, amelyeket általában Python-kódtárak használnak az adatvizualizációhoz. 

> [!Note]
> 
> Alapértelmezés szerint minden Azure-beli szinapszis-elemzési Apache Spark-készlet gyakran használt és alapértelmezett kódtárakat tartalmaz. Megtekintheti a kódtárak teljes listáját az [Azure szinapszis futtatókörnyezetben](../spark/apache-spark-version-support.md). dokumentáció. Emellett, ha az alkalmazások számára elérhetővé kívánja tenni a harmadik féltől származó vagy helyileg létrehozott kódokat, telepítheti az egyik Spark-készletbe [a könyvtárat](../spark/apache-spark-azure-portal-add-libraries.md) is.
>

1. Annak érdekében, hogy a fejlesztés könnyebben és olcsóbban történjen, a rendszer leegyszerűsíti az adathalmaz mintáját. A beépített Apache Spark mintavételezési funkciót fogjuk használni. Emellett a Seaborn és a Matplotlib is szükség van egy Panda dataframe vagy NumPy tömbre. A pandák dataframe beszerzéséhez a parancsot fogjuk használni a ```toPandas()``` dataframe konvertálásához.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Először is szeretnénk megérteni a tippek eloszlását az adatkészletben. A Matplotlib használatával létrehozunk egy hisztogramot, amely a tip-mennyiség és a darabszám eloszlását mutatja. A terjesztés alapján láthatjuk, hogy a tippek a $10-nál kisebb vagy azzal egyenlő értékekre vannak elferdítve.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Tippek hisztogramja](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. A következő lépés az adott utazáshoz tartozó tippek és a hét napja közötti kapcsolat megismerése. A Seaborn használatával hozzunk létre egy box-ábrát, amely összegzi a hét egyes napjaira vonatkozó trendeket. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Tippek napi eloszlása](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Az is előfordulhat, hogy egy másik hipotézis, hogy az utasok száma és a taxi teljes összege között pozitív kapcsolat áll fenn. A kapcsolat ellenőrzéséhez a következő kódot fogjuk futtatni egy box-ábra létrehozásához, amely bemutatja az egyes utasok számával kapcsolatos tippek eloszlását. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Box whisky-ábra](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Végül szeretnénk megismerni a viteldíj összegének összege közötti kapcsolatot. Az eredmények alapján láthatjuk, hogy több észrevétel is van, ahol az emberek nem tippet. Ugyanakkor a teljes viteldíj és a tip-mennyiség közötti pozitív kapcsolat is látható.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Pontdiagram](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>A Spark-példány leállítása

Miután befejezte az alkalmazás futtatását, állítsa le a notebookot az erőforrások felszabadításához. ehhez zárja be a fület, vagy válassza a **munkamenet befejezése** elemet a jegyzetfüzet alján található állapot panelen.

## <a name="see-also"></a>További információ

- [Áttekintés: Apache Spark az Azure szinapszis Analytics szolgáltatásban](apache-spark-overview.md)
- [Gépi tanulási modell létrehozása Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark hivatalos dokumentáció](https://spark.apache.org/docs/latest/)