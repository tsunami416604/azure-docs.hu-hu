---
title: "A Spark on Azure HDInsight használatának Adattudomány áttekintése |} Microsoft Docs"
description: "A Spark MLlib eszközkészlet jelentős gépi tanulás modellezési képességekkel a HDInsight elosztott környezetben jelent."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
ms.openlocfilehash: d9964ace6b59fa65f0f5d4caff28a4291047c8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Adattudomány Spark on Azure HDInsight használatának áttekintése
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Ennek a programcsomagnak a témakörök használata a HDInsight Spark például adatfeldolgozást, a szolgáltatás mérnöki csapathoz, a modellezési és a modell kiértékelése a közös adatok tudományos feladatokat jeleníti meg. A használt adatok látható egy minta 2013 NYC taxi út és a jegy ára adatkészlet. A beépített modellek logisztikai és lineáris regressziós, véletlenszerű erdők és átmenetes súlyozott fák tartalmazza. A témakörök a ezek a modellek tárolása az Azure blob storage (WASB) és a pontszám és értékelje a prediktív teljesítményét is megjelennek. Összetettebb témákra fedik le, hogyan lehet a modellek betanítása a kereszt-ellenőrzési és a hyper-paraméter abszolút használatával. Jelen összefoglaló téma is hivatkozik a témakörök azt ismertetik, hogyan állíthatja be a Spark-fürt, amelyekre szüksége van a végrehajtásához a megadott forgatókönyvek. 

## <a name="spark-and-mllib"></a>Spark és MLlib
[Spark](http://spark.apache.org/) egy nyílt forráskódú párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozása folyamatban van a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark program sebességét, a könnyű, valamint a kifinomult analytics lett tervezve. A Spark memóriában elosztott tárolt számítási képességei jól funkcionálnak a szerepel a machine learning és a graph számítások iteratív algoritmusaival a. [MLlib](http://spark.apache.org/mllib/) van a Spark méretezhető machine learning könyvtárban, amely csökkenti a algoritmikus modellezési képességekkel az elosztott környezetben. 

## <a name="hdinsight-spark"></a>A HDInsight Spark
[HDInsight Spark](../../hdinsight/hdinsight-apache-spark-overview.md) nyílt forráskódú Spark az Azure üzemeltetett elérhető van. Is támogatja a **Jupyter PySpark notebookok** futtatható Spark SQL interaktív lekérdezések átalakítása, szűrési és megjeleníteni az Azure BLOB (WASB) tárolt adatokat a Spark-fürt. PySpark a Python API-t a Spark. A kódrészletek, amelyek a megoldásokat és megjelenítése a megfelelő előkészítésére itt futtatása a Jupyter notebookok a Spark-fürtjei telepített adatok megjelenítéséhez. A modellezési lépések, a következő témakörökben talál, amely bemutatja, hogyan betanítása, értékelje ki, mentse és felhasználását a modell különböző típusú kódot tartalmaznak. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>A telepítő: A Spark-fürtök és a Jupyter notebookok
Beállítási lépéseket és kód okat ebben a forgatókönyvben egy HDInsight Spark 1.6 használatával. De Jupyter notebookok HDInsight Spark 1.6-os és a Spark 2.0 fürtök rendelkeznek. A jegyzetfüzetek és a hozzájuk hivatkozások leírása szerepelnek a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-tárházban. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános és a Spark-fürt kell működnie. Ha nem használja a HDInsight Spark, a fürt beállítása, és lehet, hogy a felügyeleti lépések némileg eltér az itt látható. Kényelmi célokat szolgál az alábbiakban a hivatkozásokat a Jupyter notebookok Spark 1.6 (futtatásához a pySpark kernel a Jupyter Notebook kiszolgáló) és a Spark 2.0-s verzióját (a pySpark3 kernel a Jupyter Notebook kiszolgáló kell futtatni):

### <a name="spark-16-notebooks"></a>Spark 1.6-os notebookok
Ezek notebookok vannak a pySpark kernel Jupyter notebook kiszolgáló futtatásához.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): információt biztosít az adatok feltárása, modellezéséhez, és számos különböző algoritmusok pontozási végrehajtásához.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): #1 jegyzetfüzet és modell fejlesztési hyperparameter hangolása és kereszt-ellenőrzési témaköröket tartalmazza.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): bemutatja, hogyan azok egy mentett modell Python használata a HDInsight-fürtökön.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebookok
Ezek notebookok vannak a Jupyter notebook kiszolgáló pySpark3 kernel futtatásához.

- [Spark2.0-pySpark3-Machine-Learning-Data-Science-Spark-Advanced-Data-exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl hogyan hajthat végre az adatok feltárása, modellezési, és a Spark 2.0 pontozási fürtök NYC Taxi út és jegy ára adatkészlet leírt [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). A notebook gyorsan felfedezése a Spark 2.0 adtunk kódot az jó kiindulási pont lehet. További részletes notebook elemzi az NYC Taxi adatokat, lásd: a következő notebook ezen a listán. Tekintse meg a megjegyzéseket, a lista a következő, hasonlítsa össze ezeket a jegyzetfüzetek. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl bemutatja, hogyan végezhet wrangling (Spark SQL és dataframe műveletek), feltárása, modellezéséhez és a NYC Taxi út és a jegy ára adatkészlet leírt pontozási [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl bemutatja, hogyan végezhet wrangling (Spark SQL és dataframe műveletek), feltárása, modellezéséhez és pontozási a jól ismert légitársaság időben indító adatkészlet 2011 és a 2012. Azt integrálva az adatokkal repülőtéri időjárási (pl. szélsebesség, hőmérséklet, magasság stb.) a légitársaság dataset előtt modellezési, így időjárási felhasználásokhoz felvehetők a modell.

<!-- -->

> [!NOTE]
> A légitársaság dataset lett hozzáadva, a Spark 2.0 jegyzetfüzeteihez jobban a besorolási algoritmusok használatát mutatja be. Tekintse meg a következőket légitársaság kapcsolatos információk időben indító dataset és időjárási adatkészlet:

>- Légitársaság időben indító adatok: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Repülőtéri időjárási adatok: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
A Spark 2.0 jegyzetfüzeteket az NYC taxi és légitársaság repülési késleltetés-készleteket is igénybe vehet, 10 perc vagy több (attól függően, hogy a HDI-fürtnek a mérete). A fenti listában első notebook mutatja az adatok feltárása sok területén, a képi megjelenítés és ML minta egy jegyzetfüzetet le mintát NYC adatkészlet, amelyben a taxi és jegy ára fájlok voltak előre illesztett futtatásához kevesebb időt vesz igénybe, a képzési: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) jegyzetfüzet (2-3 perc) befejezéséhez sokkal rövidebb ideig tart, és előfordulhat, hogy lehet Jó kiindulópont gyorsan felfedezése a Spark 2.0 adtunk kódot. 

<!-- -->

A Spark 2.0 modell és a model felhasználás pontozó operationalization útmutatóért lásd: a [Spark 1.6-os dokumentum a felhasználás](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) egy példát, amely tartalmazza a szükséges lépéseket. Ezzel a Spark 2.0, cserélje le a Python kódját fájl [ezt a fájlt](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Előfeltételek
Az alábbi eljárások Spark 1.6 kapcsolódnak. A Spark 2.0-s verziójához használja a notebookok leírt, és korábban csatolva. 

1. rendelkeznie kell Azure-előfizetéssel. Ha még nem rendelkezik egy, lásd: [beolvasása az Azure ingyenes próbaverzió](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2 van szüksége a Spark 1.6-os-fürt forgatókönyv végrehajtásához. Szeretne létrehozni egyet, tekintse meg a utasításokat [első lépések: Apache Spark on Azure HDInsight létrehozása](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). A fürt típusa és verzió van megadva a **fürt típusának kiválasztása** menü. 

![Fürt konfigurálása](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ez a témakör bemutatja, hogyan használja a Python helyett a Scala az adatok végpontok közötti tudományos folyamat feladatok végrehajtásához, tekintse meg a [Azure Spark Scala használatával Adattudomány](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>A következőt: 2013 Taxi adatok
A következőt: Taxi út adatok körülbelül 20 GB tömörített vesszővel tagolt (CSV) fájl (tömörítetlen ~ 48 GB), több mint 173 millió egyedi való adatváltások számát és a vitel kifizette minden út. Minden út rekord tartalmazza, a felvételével kapcsolatban és Gyűjtőtár hely és idő, anonimizált rejthetők el (illesztőprogram) licenc száma, és medallion (taxi tartozó egyedi azonosító) számát. Az adatok minden való adatváltások számát ismerteti az év 2013, és minden hónap a következő két adatkészletet találhatók:

1. A "trip_data" CSV-fájlok tartalmazza út, például az utasok száma, átvételéhez és dropoff mutat, út időtartamát és út hossza. Íme néhány példa rekordok:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV fájlok tartalmazzák a jegy ára kifizette minden út, például a fizetési mód, jegy ára összeg, emelt díjas és adókat, tippeket és autópályadíjak, és a teljes összeg fizetős részleteit. Íme néhány példa rekordok:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A Microsoft ezeket a fájlokat 0,1 % mintát venni, és a út csatlakoztatott\_adatok és út\_csak egyet fájlok díjszabás be ez a forgatókönyv bemeneti adatkészlet használja egy adatkészlet. Egyedi kulcs út csatlakozni\_adatok és út\_jegy ára tevődnek össze a mezők: medallion, rejthetők el\_engedély és a felvételi\_dátum és idő. A DataSet adatkészlet egyes rekord a következő attribútumokat a következőt: Taxi út képviselő tartalmazza:

| Mező | Rövid leírás |
| --- | --- |
| medallion |Anonimizált taxi medallion (egyedi taxi azonosító) |
| hack_license |Anonimizált Hackney kocsivissza licenc száma |
| vendor_id |Taxi szállító azonosítója |
| rate_code |Jegy ára taxi gyakorisága a következőt: |
| store_and_fwd_flag |Tárolási és előre jelző |
| pickup_datetime |Dátum és idő átvételéhez |
| dropoff_datetime |Dropoff dátum és idő |
| pickup_hour |Óra átvételéhez |
| pickup_week |Az év hete átvételéhez |
| milyen napra esik |Milyen napra esik (tartomány: 1-7) |
| passenger_count |Egy taxi út az utasok száma |
| trip_time_in_secs |Visszatérési ideje másodpercben |
| trip_distance |Miles út távolság |
| pickup_longitude |A földrajzi hosszúság értéke átvételéhez |
| pickup_latitude |A földrajzi hosszúság átvételéhez |
| dropoff_longitude |Dropoff hosszúság |
| dropoff_latitude |Dropoff szélesség |
| direct_distance |Közvetlen kivételezési közötti távolság fel és dropoff helye |
| payment_type |A fizetési mód (cas, hitelkártya stb.) |
| fare_amount |A jegy ára összeg |
| Emelt díjas |Emelt díjas |
| mta_tax |MTA adó |
| tip_amount |Tipp összeg |
| tolls_amount |Autópályadíjak összeg |
| total_amount |Teljes összeg |
| Formabontó |Formabontó (0 vagy 1. nem vagy Igen) |
| tip_class |Tipp osztály (0: 0, 1: $0-5, 2: $6 – 10., 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Kód végrehajtása a Spark-fürt Jupyter notebook
Az Azure-portálon a Jupyter Notebook indíthatja el. Keresse meg a Spark-fürt az irányítópulton, és kattintson rá a felügyelet lapon adja meg a fürt. A Spark-fürt társított notebook megnyitásához kattintson **fürt irányítópultok** -> **Jupyter Notebook** .

![Fürt irányítópultok](./media/spark-overview/spark-jupyter-on-portal.png)

Is tallózással megkereshet ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** a Jupyter notebookok eléréséhez. Az URL-cím a FÜRTNÉV részét cserélje le a saját fürt nevét. Szüksége van a notebookok eléréséhez a rendszergazdai fiók jelszavát.

![Keresse meg a Jupyter notebookok](./media/spark-overview/spark-jupyter-notebook.png)

Válassza ki a PySpark néhány példa a PySpark API-t használó előcsomagolt jegyzetfüzetek tartalmazó könyvtár megjelenítéséhez. Ez a témakör a Spark Suite mintakódok tartalmazó jegyzetfüzet érhetők el [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Feltöltheti közvetlenül a notebookok [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) a Jupyter notebook kiszolgálóra a Spark-fürtön. A Jupyter kezdőlapján kattintson a **feltöltése** gomb a képernyő jobb oldalán. A Fájlkezelőben nyílik meg. Ide illessze be a Notebook, majd kattintson a GitHub (nyers tartalom) URL- **nyitott**. 

A Jupyter fájl nevére, a fájlnév megjelenik egy **feltöltése** újra gombra. Ide **feltöltése** gombra. Most már a notebook importálta. Ismételje ezeket a lépéseket a Ez a forgatókönyv más notebookok feltöltéséhez.

> [!TIP]
> Kattintson a jobb egérgombbal a a böngészőt, és válassza a hivatkozások **hivatkozás másolása** lekérni a githubon nyers tartalom URL-CÍMÉT. Az URL-cím bemásolhatja a Jupyter feltöltése explorer párbeszédpanelt.
> 
> 

Most már a következőket teheti:

* A notebook kattintva tekintse meg a kódot.
* Minden cella végrehajtása billentyűkombináció lenyomásával **a SHIFT + ENTER**.
* Futtassa a teljes jegyzetfüzet található kattintva **cella** -> **futtatása**.
* Használja az automatikus képi megjelenítés lekérdezések.

> [!TIP]
> A PySpark kernel automatikusan visualizes (HiveQL) az SQL-lekérdezések eredményének. Lehetősége van több különböző típusú megjelenítések (táblázat, torta, vonal, terület vagy sáv) között használatával kiválaszthatja azokat a **típus** a notebook menü gombjai:
> 
> 

![Logisztikai regresszió: ROC-görbe általános módszer](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>A következő lépések
Most, hogy be vannak állítva a HDInsight Spark-fürt és a Jupyter notebookok feltöltött, készen áll a témakörök, amelyek megfelelnek a három PySpark notebookok keresztül működnek. Hogyan feltérképezheti az adatokat, majd hogyan létrehozását és felhasználását a modellek mutatnak. A speciális adatok feltárása és modellezési notebook bemutatja, hogyan kereszt-ellenőrzési, abszolút, a hyper-paramétert tartalmaz, és a modell kiértékelése. 

**Az adatok feltárása és Spark modellezés:** megismerkedhet a DataSet adatkészlet és a létrehozása, a pontszám és a gépi tanulási modellek által feldolgozása révén kiértékelheti az [adatok bináris besorolási és regressziós modell létrehozása Spark MLlib eszközkészlete](spark-data-exploration-modeling.md) témakör.

**Modellhez tartozó felhasználás:** a létre ebben a témakörben besorolási és regressziós modell pontozása céljából, lásd: [pontszám és értékelje ki a Spark-beépített machine learning modellek](spark-model-consumption.md).

**Kereszt-ellenőrzési és hyperparameter abszolút**: lásd: [speciális adatok feltárása és Spark modellezés](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek betanítása a kereszt-ellenőrzési és a hyper-paraméter abszolút használatával

