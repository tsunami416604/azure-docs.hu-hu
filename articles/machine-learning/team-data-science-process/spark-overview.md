---
title: Adatelemzés a Spark használatával az Azure HDInsight – a csoportos adatelemzési folyamat
description: A Spark MLlib eszközkészlet számos lehetőséget kínál a jelentős gépi tanulási adatmodellezési képességekkel az elosztott HDInsight-környezetben.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718514"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Az Azure HDInsight Spark használatával adatelemzés áttekintése

Ez az alkalmazáscsomag témakörök használatára a HDInsight Spark például adatbetöltést, funkciófejlesztési, modellezés és a modell értékelése a közös adatok adatelemzési feladatokat mutatja be. Használt adatokat egy minta az 2013 NYC taxi utazást és diszkont adatkészlet. A modellek tartalmazzák a logisztikai és lineáris regresszió, véletlenszerű erdők és gyorsított fa átmenetes. A témakörök a modellek tárolása az Azure blob storage (WASB) és a pontszám, és a prediktív teljesítmény kiértékelése is megjelennek. Speciális témakörök ismertetik, hogyan lehet a modellek tanítása az kereszt-ellenőrzési és a hyper-paraméter kezdik. Ez a témakör áttekintést is hivatkozik a témakörök, amelyek bemutatják, hogyan állítható be a Spark-fürt a megadott forgatókönyvek a lépések elvégzéséhez szükséges.

## <a name="spark-and-mllib"></a>Spark- és MLlib
A [Spark](https://spark.apache.org/) egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a Big-adatelemző alkalmazások teljesítményének növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriabeli elosztott számítási képességekkel teszik megfelelő választás az olyan a használt a machine learning és a graph számítások iteratív algoritmusaival együtt. A [MLlib](https://spark.apache.org/mllib/) a Spark skálázható gépi tanulási könyvtára, amely az algoritmikus modellezési képességeket az elosztott környezetbe hozza.

## <a name="hdinsight-spark"></a>HDInsight Spark
A [HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) a nyílt forráskódú Spark Azure által üzemeltetett ajánlata. Emellett támogatja a Spark-fürt **Jupyter PySpark notebookjának** támogatását is, amely Spark SQL interaktív lekérdezéseket futtathat az Azure-blobokban (WASB) tárolt adatátalakításhoz, szűréshez és megjelenítéshez. PySpark a Python API Spark számára. A kódrészletek, amelyek a megoldásokat, és itt futtatása a Jupyter notebookok a Spark-fürtökön telepített adatok vizualizálásához megfelelő grafikon megjelenítése. Az alábbi témakörök a modellezési lépéseit, amely bemutatja, hogyan betanításához, kiértékelése, mentése és felhasználását a modell különböző típusú kódot tartalmaznak.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>A telepítő: Spark-fürtök és Jupyter-notebookok
Beállítási lépéseket és a kód-okat Ez az útmutató egy HDInsight Spark 1.6-os használatával. De Jupyter notebookok a HDInsight Spark 1.6-os és a Spark 2.0 fürtök biztosított. A jegyzetfüzetek leírását és azok hivatkozásait az azokat tartalmazó GitHub-adattár [readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) tartalmazza. Továbbá, a kód itt és a társított notebookok az általános és működnie kell bármelyik Spark-fürtön. Ha nem használja a HDInsight Spark, a fürt beállítása és lehet, hogy a felügyeleti lépések kissé eltérhetnek az itt látható. A Jupyter notebookokra mutató hivatkozásokat a Spark 1,6 (a Jupyter Notebook-kiszolgáló pySpark kernelében kell futtatni) és a Spark 2,0 (a Jupyter Notebook-kiszolgáló pySpark3 kernelében kell futtatni):

### <a name="spark-16-notebooks"></a>A Spark 1.6-os notebookok
Ezeket a notebookokat vannak a pySpark kernel Jupyter notebook Server futtatásához.

- [pySpark-Machine-learning-Information-Science-Spark-adatok-Exploration-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): információt nyújt az adatok feltárásának, modellezésének és pontozásának különböző algoritmusokkal történő végrehajtásáról.
- [pySpark-Machine-learning-adat-tudomány-Spark-Advanced-adat-feltárás-modellezés. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): a notebook-#1 témaköröket tartalmaz, valamint a hiperparaméter finomhangolását és a több érvényesítést használó modellek fejlesztését.
- [pySpark-Machine-learning-adattudományi-Spark-Model-fogyasztás. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): azt mutatja be, hogyan működővé tenni egy mentett modellt a Python használatával HDInsight-fürtökön.

### <a name="spark-20-notebooks"></a>A Spark 2.0 notebookok
Ezeket a notebookokat futtatásához a Jupyter notebook Server pySpark3 kernel vannak.

- [Spark 2.0-pySpark3-Machine-learning-adat-tudomány-Spark-Advanced-adat-feltárás-modellezés. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információt nyújt arról, hogyan végezheti el az adatfeltárást, modellezést és pontozást a Spark 2,0-fürtökön a New York-i, a NYC-alapú taxi Trip és a fare adathalmaz [használatával.](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data) Ez a jegyzetfüzet gyorsan megismerheti a kódot, a Spark 2.0 biztosítunk jó kiindulási pont lehet. Részletesebb jegyzetfüzet a NYC i taxik adatait elemzi, lásd a következő notebook ebben a listában. Tekintse meg a következő megjegyzéseket, amelyek összehasonlítják ezeket a jegyzetfüzeteket.
- [Spark 2.0 – pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl azt mutatja be, hogyan végezhető el az adatok huzavona (Spark SQL-és dataframe-műveletek), feltárás, modellezés és pontozás a New York-i taxi Trip és a fare adatkészlet [használatával.](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)
- [Spark 2.0 – pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl azt mutatja be, hogyan végezhető el az adatok huzavona (Spark SQL-és dataframe-műveletek), feltárás, modellezés és pontozás az 2011-es és 2012-es, jól ismert légitársaságok által használt időponthoz képest. A (például Szélsebesség, a hőmérséklet, a tengerszint feletti magasság stb.) a modellezés előtt integráltuk a légitársaság adatkészletét, így ezek az időjárási funkciók a modellben is szerepelhetnek.

<!-- -->

> [!NOTE]
> A légitársaság adatkészlet hozzá lett adva a Spark 2.0 notebookok jobban a besorolási algoritmusok használatát mutatja be. Lásd az alábbi hivatkozásokat légitársaság kapcsolatos információk időben indító adatkészletet és időjárási adatkészletet:
> 
> - A légitársaságok on-Time indulási adatvédelme: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Airport időjárási adatszolgáltatások: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> A Spark 2.0 notebookok a NYC taxi és légitársaság repülési késleltetés-adatkészleteken alapuló is igénybe vehet, 10 perc vagy több (függően a HDI-fürt mérete). A fenti listában szereplő első jegyzetfüzetben az adatfeltárás, a vizualizáció és a ML-modell képzésének számos aspektusát láthatja egy olyan jegyzetfüzetben, amely kevesebb időt vesz igénybe a levett, mint a New York-i (a taxi-és a viteldíj- [)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)fájlok előzetes csatlakoztatásával Ez a jegyzetfüzet sokkal rövidebb időt vesz igénybe (2-3 perc), és jó kiindulási pont lehet a Spark 2,0-hoz megadott kód gyors feltárásához.

<!-- -->

A Spark 2,0 modell és a pontozási modell felhasználásának operacionalizálási kapcsolatos útmutatásért tekintse meg a [spark 1,6-dokumentum felhasználásáról szóló](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) példát, amely a szükséges lépéseket ismerteti. Ha ezt a példát a Spark 2,0-on szeretné használni, cserélje le a Python-kódrészletet [erre a fájlra](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Előfeltételek

A következő eljárások a Spark 1.6-os kapcsolódnak. A Spark 2.0-s verziójának használata a notebookok leírt, és korábban csatolva.

1. Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik ilyennel, tekintse meg az [Azure ingyenes próbaverziójának beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)című témakört.

2. A forgatókönyv végrehajtásához Spark 1,6-fürtre van szüksége. A létrehozáshoz tekintse meg az első [lépések: Apache Spark létrehozása az Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakör utasításait. A fürt típusa és verziója a **fürt típusának kiválasztása** menüben van megadva.

![Fürt konfigurálása](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Egy olyan témakörben, amely bemutatja, hogyan használhatja a Scalat Python helyett a teljes adatelemzési folyamat feladatainak elvégzéséhez, tekintse [meg az adatelemzést a Scala és a Spark on Azure használatával](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>A NYC 2013-i taxik adatait
A NYC Taxi útadatok körülbelül 20 GB tömörített vesszővel elválasztott értékeket (CSV) fájl (~ 48 GB tömörítetlen), minden egyes út 173 milliónál egyes utak és a vitel magában foglaló fizetni. Minden egyes út rekord tartalmazza a begyűjtést és dropoff hely és idő, anonimizált feltörés (illesztőprogramok) licencszám és medallion (taxi az egyedi azonosító) számát. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

1. A "trip_data" CSV-fájlokat tartalmaz trip részleteit, például az utasok száma, folytattuk a munkát, és dropoff mutat, kivételre, időtartama és út hossza. Az alábbiakban néhány példa rekordokat:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV-fájlok az egyes út, például a fizetési típus, diszkont összeg, pótdíj és adók, tippek és útdíjak, fizetett diszkont és a teljes összeg fizetős tartalmaz. Az alábbiakban néhány példa rekordokat:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Ezeket a fájlokat a 0,1%-os mintát vettük, és az utazáshoz\_adatokat és az utazást\_a fare CVS-fájlokat egyetlen adatkészletbe, amelyet a bemutató bemeneti adatkészletének használ. Az utazáshoz\_az adathoz és az utazáshoz\_viteldíj a következő mezőkből áll: emlékérem, Hack\_licenc és pickup\_DateTime. Az adatkészlet minden rekordját a következő attribútumokat NYC Taxi belépőt jelölő tartalmazza:

| Mező | Rövid leírás |
| --- | --- |
| medallion |Anonimizált taxi medallion (egyedi taxi-azonosító) |
| hack_license |Anonimizált Hackney kocsivissza jogosítvány száma |
| vendor_id |Taxi szállító azonosítója |
| rate_code |Diszkont NYC taxi aránya |
| store_and_fwd_flag |Store és előre jelző |
| pickup_datetime |Dátum és idő csomópontmetrikák |
| dropoff_datetime |Dropoff dátum és idő |
| pickup_hour |Válasszon órák |
| pickup_week |Év hete csomópontmetrikák |
| Hét napja |Hét napja (tartomány: 1-7) |
| passenger_count |A taxi belépőt utasforgalmat |
| trip_time_in_secs |Utazás időre (másodpercben) |
| trip_distance |Mérföld trip távolság |
| pickup_longitude |Hosszúsági csomópontmetrikák |
| pickup_latitude |Szélesség csomópontmetrikák |
| dropoff_longitude |Dropoff hosszúság |
| dropoff_latitude |Dropoff szélesség |
| direct_distance |Közvetlen távolság a felvételi és a lemorzsolódási helyei között |
| payment_type |Fizetési típus (készpénz, hitelkártya stb.) |
| fare_amount |Diszkont összeg |
| Pótdíj |Pótdíj |
| mta_tax |MTA Metro szállítási adó |
| tip_amount |Tipp összeg |
| tolls_amount |Útdíjak összeg |
| total_amount |Teljes összeg |
| Formabontó |Formabontó (0 és 1 nem vagy Igen) |
| tip_class |Tipp osztály (0: 0, 1: 0 – 5 USD, 2: 6 – 10 USD, 3: $11 – 20., 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Hajtsa végre a Spark-fürtön lévő Jupyter notebook kód
A Jupyter Notebook az Azure portálról indíthatja el. Keresse meg a Spark-fürtöt az irányítópulton, és kattintson rá a kezelése lapon adja meg a fürt. A Spark-fürthöz társított jegyzetfüzet megnyitásához kattintson a **fürt irányítópultok** -> **Jupyter notebook**elemre.

![Fürt irányítópultjai](./media/spark-overview/spark-jupyter-on-portal.png)

A Jupyter-jegyzetfüzetek eléréséhez tallózással is megkeresheti ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** . Az URL-cím a FÜRTNÉV részét cserélje le a saját fürt nevére. A jelszót a rendszergazdai fiók a notebookok elérésére van szüksége.

![Keresse meg a Jupyter notebookok](./media/spark-overview/spark-jupyter-notebook.png)

Válassza a PySpark lehetőséget egy olyan könyvtár megtekintéséhez, amely néhány példát tartalmaz a PySpark API-t használó előre csomagolt jegyzetfüzetekre. A Spark-témakörhöz tartozó kód mintákat tartalmazó jegyzetfüzetek a [githubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) érhetők el

A jegyzetfüzeteket közvetlenül a [githubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) töltheti fel a Spark-fürtön lévő Jupyter notebook-kiszolgálóra. A Jupyter kezdőlapján kattintson a képernyő jobb oldalán található **feltöltés** gombra. Ekkor megnyílik a Fájlkezelőben. Itt beillesztheti a GitHub (nyers tartalom) URL-címét, majd kattintson a **Megnyitás**gombra.

A Jupyter-fájlok listájában megjelenik a fájl neve, és ismét **feltölthető** gomb jelenik meg. Kattintson erre a **feltöltés** gombra. Most már importált a notebookot. Ismételje meg ezeket a lépéseket az ebben a bemutatóban a notebookok feltöltése.

> [!TIP]
> Kattintson a jobb gombbal a böngészőben található hivatkozásokra, és válassza a **hivatkozás másolása** lehetőséget a GitHub nyers tartalom URL-címének lekéréséhez. A Jupyter feltöltése fájl explorer párbeszédpanelen is illessze be az URL-címet.
> 
> 

Most a következőket teheti:

* Tekintse meg a kódot a notebook kattintva.
* Hajtsa végre az egyes cellákat a **SHIFT**billentyű lenyomásával.
* A teljes jegyzetfüzet futtatásához kattintson a **cella** -> **Futtatás**elemre.
* A lekérdezések automatikus vizualizációt használja.

> [!TIP]
> A PySpark kernel automatikusan elérhetővé (HiveQL) SQL-lekérdezések kimenetének. A következő lehetőségek közül választhat: több különböző típusú vizualizáció (tábla, torta, vonal, terület vagy sáv) között a jegyzetfüzet **Type (típus** ) gombjának használatával:
>
>

![Logisztikai regressziós ROC-görbét az általános megközelítés](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>A következő lépések
Most, hogy a HDInsight Spark-fürt beállítása és a Jupyter notebookok feltöltése, készen áll a témakörök, amelyek megfelelnek a három PySpark notebookok keresztül. Ezek bemutatják, hogyan feltárhatja az adatait, majd hogyan hozhat létre és használhat fel modelleket. A speciális adatok feltárása és modellezés notebook bemutatja, hogyan közé tartozik a kereszt-ellenőrzés, abszolút, a hyper-paraméter és a modell értékelése.

**Adatelemzés és-modellezés a sparktal:** Fedezze fel az adatkészletet, és hozza létre, szerzi és értékelje ki a gépi tanulási modelleket a Spark MLlib Toolkit című témakörben található [bináris besorolási és regressziós modellek létrehozásával](spark-data-exploration-modeling.md) .

**Modell felhasználása:** A jelen témakörben létrehozott besorolási és regressziós modellek kiértékelésével kapcsolatban lásd a [Spark által készített gépi tanulási modellek pontszámát és értékelését](spark-model-consumption.md)ismertető témakört.

**Több ellenőrzés és hiperparaméter**: a [fejlett adatfeltárás és-modellezés a Spark](spark-advanced-data-exploration-modeling.md) használatával a modellek több ellenőrzéssel és a Hyper-paraméterrel történő elvetésével is betanítható

