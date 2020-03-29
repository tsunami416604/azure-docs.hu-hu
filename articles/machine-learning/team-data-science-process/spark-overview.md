---
title: Adatelemzés a Spark használatával az Azure HDInsighton – Csapatadat-elemzési folyamat
description: A Spark MLlib eszközkészlet jelentős gépi tanulási modellezési képességeket biztosít az elosztott HDInsight-környezetben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718514"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Az azure HDInsight Spark használatával az adatelemzés áttekintése

Ez a témakörcsomag bemutatja, hogyan használhatja a HDInsight Spark ot olyan gyakori adatelemzési feladatok elvégzéséhez, mint az adatbetöltés, a szolgáltatástervezés, a modellezés és a modellkiértékelés. A felhasznált adatok a 2013-as NYC taxiút és a viteldíj-adatkészlet mintája. A modellek közé tartozik a logisztikai és lineáris regresszió, véletlenszerű erdők, és a gradiens kiemelt fák. A témakörök azt is bemutatják, hogyan tárolhatja ezeket a modelleket az Azure blob storage (WASB) és hogyan pontozhatja és értékelheti a prediktív teljesítmény. A fejlettebb témakörök azt ismertetik, hogy a modellek hogyan képezhetők be keresztérvényesítéssel és hiperparaméter-söpréssel. Ez az áttekintő témakör is hivatkozik a témakörök, amelyek ismertetik, hogyan kell beállítani a Spark-fürt, amely a forgatókönyvek végrehajtásához szükséges.

## <a name="spark-and-mllib"></a>Spark és MLlib
A [Spark](https://spark.apache.org/) egy nyílt forráskódú párhuzamos feldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-analitikus alkalmazások teljesítményének növelése érdekében. A Spark feldolgozómotor a sebesség, a könnyű használat és a kifinomult analitika érdekében készült. A Spark memórián belüli elosztott számítási képességei jó választássá teszik a gépi tanulásban és grafikonszámításokban használt iteratív algoritmusok számára. [Az MLlib](https://spark.apache.org/mllib/) a Spark méretezhető gépi tanulási könyvtára, amely az algoritmikus modellezési képességeket hozza el ebbe az elosztott környezetbe.

## <a name="hdinsight-spark"></a>HDInsight Spark
[A HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) a nyílt forráskódú Spark Azure-üzemeltetett ajánlata. Támogatja a **Jupyter PySpark-jegyzetfüzeteket** a Spark-fürtön, amelyek a Spark SQL interaktív lekérdezéseit futtathatják az Azure Blobokban (WASB) tárolt adatok átalakításához, szűréséhez és megjelenítéséhez. A PySpark a Spark Python API-ja. A kódrészletek, amelyek a megoldásokat, és a megfelelő telkek megjelenítéséhez az adatok itt fut jupyter notebookok telepítve a Spark-fürtök. Az ezekben a témakörökben ismertetett modellezési lépések olyan kódot tartalmaznak, amely bemutatja, hogyan lehet betanítása, kiértékelése, mentése és felhasználása az egyes típusú modellek.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Beállítás: Spark-fürtök és Jupyter-jegyzetfüzetek
A beállításlépései és a kód ebben a forgatókönyvben találhatók a HDInsight Spark 1.6 használatával. A Jupyter-jegyzetfüzetek azonban hdinsight Spark 1.6-hoz és Spark 2.0-fürtökhöz is rendelkezésre állnak. A jegyzetfüzetek és a hozzájuk mutató hivatkozások leírása az azokat tartalmazó GitHub-tárház [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) található. Ezenkívül a kód itt és a csatolt jegyzetfüzetekben általános, és működnie kell minden Spark-fürtön. Ha nem használja a HDInsight Sparkot, a fürt beállítási és felügyeleti lépései némileg eltérhetnek az itt láthatótól. Az egyszerűség kedvéért, itt vannak a linkeket a Jupyter notebookok Spark 1.6 (futtatni kell a pySpark kernel a Jupyter Notebook szerver) és a Spark 2.0 (futtatni kell a pySpark3 kernel a Jupyter Notebook szerver):

### <a name="spark-16-notebooks"></a>Spark 1.6 notebookok
Ezeket a jegyzetfüzeteket a Jupyter notebook-kiszolgáló pySpark kernelében kell futtatni.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Az adatok feltárásával, modellezésével és pontozásával kapcsolatos információkat több különböző algoritmussal ismerteti.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): témaköröket tartalmaz a notebook #1, és a modell fejlesztés segítségével hyperparameter tuning és kereszt-érvényesítés.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Bemutatja, hogyan lehet működőképessé tenni egy mentett modellt a Python használatával HDInsight-fürtökön.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebookok
Ezeket a jegyzetfüzeteket a Jupyter notebook-kiszolgáló pySpark3 kernelében kell futtatni.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl tájékoztatást nyújt arról, hogyan lehet elvégezni az adatok feltárását, modellezését és pontozását a Spark 2.0 fürtökben a NYC Taxi utazás és a viteldíj-adatkészlet [használatával, amelyet itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)ismertetett . Ez a notebook jó kiindulási pont lehet a Spark 2.0-hoz megadott kód gyors feltárásához. A nyc taxi adatok részletesebb jegyzetfüzetelemzéséhez tekintse meg a következő jegyzetfüzetet ebben a listában. Tekintse meg az alábbi megjegyzéseket, amelyek összehasonlítják ezeket a jegyzetfüzeteket.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl bemutatja, hogyan kell elvégezni az adatok huzavona (Spark SQL és dataframe műveletek), feltárása, modellezése és pontozási segítségével NYC Taxi utazás és viteldíj adatkészlet [itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)leírt .
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl bemutatja, hogyan végezhetadatokat wrangling (Spark SQL és dataframe műveletek), feltárása, modellezése és pontozási a jól ismert légitársaság időben indulási adatkészlet 2011 és 2012.Spark.the pySpark3_Airline_Departure_Delay_Classification.ipynb: This file shows how to perform data wrangling (Spark SQL and dataframe operations), exploration, modeling and scoring using the well-known Airline On-time departure dataset from 2011 and 2012. A modellezés előtt integráltuk a légitársaság imázsának adatait a repülőtéri időjárási adatokkal (például szélsebesség, hőmérséklet, magasság stb.), hogy ezek az időjárási jellemzők bekerüljenek a modellbe.

<!-- -->

> [!NOTE]
> A légitársaság adatkészlete hozzá lett adva a Spark 2.0-s jegyzetfüzetekhez, hogy jobban illusztrálja a besorolási algoritmusok használatát. Az alábbi hivatkozásokat a légitársaság időben indulási és időjárási adatkészletéről talál:
> 
> - A légitársaság időben idoben megadott adatai:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Repülőtéri időjárási adatok:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> A Spark 2.0 notebookok a NYC taxi és a légitársaság járat késési adatkészletek is eltarthat 10 mins vagy több futtatásához (méretétől függően a HDI-fürt). A fenti listában szereplő első jegyzetfüzet az adatfeltárás, a vizualizáció és az ML-modell betanításának számos aspektusát mutatja be egy olyan jegyzetfüzetben, amely kevesebb időt vesz igénybe a lemintavételezett NYC-adatkészlettel, amelyben a taxi- és viteldíjfájlok előre csatlakoztak: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Ez a notebook sokkal rövidebb időt vesz igénybe (2-3 mins), és jó kiindulópont lehet a Spark 2.0-hoz megadott kód gyors felfedezéséhez.

<!-- -->

A Spark 2.0-modell üzembe as- és a pontozási modellfelhasználás üzembe asta, tekintse meg a [Spark 1.6-os használati dokumentum](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) a szükséges lépéseket ismerteti egy példa. Ha ezt a példát szeretné használni a Spark 2.0-s rendszerén, cserélje le a Python-kódfájlt erre a [fájlra.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py)

### <a name="prerequisites"></a>Előfeltételek

A következő eljárások a Spark 1.6-ra vonatkoznak. A Spark 2.0-s verziójához használja a korábban leírt és hivatkozott jegyzetfüzeteket.

1. Rendelkeznie kell egy Azure-előfizetéssel. Ha még nem rendelkezik ilyen, olvassa [el az Ingyenes Azure-próbaverzió beszereznie.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

2. A forgatókönyv végrehajtásához egy Spark 1.6-fürtre van szükség. A létrehozáshoz tekintse meg az Első lépések című témakörben található [utasításokat: Hozzon létre Apache Sparkot az Azure HDInsight-on.](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) A fürttípus és -verzió a **Fürttípus kiválasztása** menüből van megadva.

![Fürt konfigurálása](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ha egy témakört, amely bemutatja, hogyan használhatja a Scala python helyett feladatok elvégzésére egy teljes körű adatelemzési folyamat, tekintse meg az [adatelemzési a Scala használatával a Spark az Azure-ban.](scala-walkthrough.md)
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>A NYC 2013 Taxi adatok
A NYC Taxi Trip adatok körülbelül 20 GB tömörített vesszővel elválasztott értékek (CSV) fájlok (~ 48 GB tömörítetlen), amely több mint 173 millió egyéni utak és a viteldíjak fizetett minden út. Minden út rekord tartalmazza a pickup és dropoff helyét és idejét, anonimizált hack (driver's) engedély száma és medallion (taxi egyedi id) számát. Az adatok a 2013-as év összes utazására vonatkoznak, és minden hónapra vonatkozóan a következő két adatkészletben szerepelnek:

1. A "trip_data" CSV-fájlok tartalmazzák az utazás részleteit, például az utasok számát, a felvételi és leadási pontokat, az utazás időtartamát és az utazás hosszát. Íme néhány mintarekord:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. A "trip_fare" CSV fájlok tartalmazzák az egyes utazásokért fizetett viteldíj részleteit, például a fizetés típusát, a viteldíj összegét, a pótdíjat és az adókat, a tippeket és az útdíjakat, valamint a teljes kifizetett összeget. Íme néhány mintarekord:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Vettünk egy 0,1%-os mintát ezekből a\_fájlokból, és az utazási adatokat és az utazási\_viteldíj CVS-fájlokat egyetlen adatkészletbe illesztettük, amelyet a forgatókönyv bemeneti adatkészleteként használhat. Az egyedülálló kulcs,\_hogy\_csatlakozzon utazási adatok és utazási\_viteldíj áll\_a területeken: medál, hack engedély és pickup datetime. Az adatkészlet minden rekordja a következő attribútumokat tartalmazza, amelyek egy NYC Taxi-utat jelölnek:

| Mező | Rövid leírás |
| --- | --- |
| Medál |Anonimizált taxi medál (egyedi taxi azonosító) |
| hack_license |Névtelenhackney fuvarengedély száma |
| vendor_id |Taxi szállítóazonosítója |
| rate_code |NYC taxi ára viteldíj |
| store_and_fwd_flag |Tároló és továbbításjelző |
| pickup_datetime |Felvétel dátuma & időpont |
| dropoff_datetime |Dropoff dátum & idő |
| pickup_hour |Felvételi óra |
| pickup_week |Az év hetének felvétele |
| Hétköznapi |Hétköznap (tartomány 1-7) |
| passenger_count |Utasok száma egy taxi út |
| trip_time_in_secs |Utazási idő másodpercben |
| trip_distance |Megtett távolság mérföldben |
| pickup_longitude |Vegye fel a hosszúsági fokot |
| pickup_latitude |Felvétel szélességi foka |
| dropoff_longitude |Lemorzsolódási hosszúság |
| dropoff_latitude |Leadási szélesség |
| direct_distance |Közvetlen távolság a felvételi és a kidobási helyek között |
| payment_type |Fizetés típusa (készpénz, hitelkártya stb.) |
| fare_amount |Viteldíj összege |
| Felár |Felár |
| mta_tax |MTA Metro Közlekedési adó |
| tip_amount |Tipp összege |
| tolls_amount |Útdíj összege |
| total_amount |Teljes összeg |
| Megbillent |Hegyed (0/1 a nem vagy igen) |
| tip_class |Tipp osztály (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Kód végrehajtása egy Jupyter-jegyzetfüzetből a Spark-fürtön
A Jupyter-jegyzetfüzetet az Azure Portalról indíthatja el. Keresse meg a Spark-fürtöt az irányítópulton, és kattintson rá a fürt felügyeleti lapjának megadásához. A Spark-fürthöz társított jegyzetfüzet megnyitásához kattintson a **Fürtirányítópultok** -> **Jupyter-jegyzetfüzetelemre.**

![Fürtirányítópultjai](./media/spark-overview/spark-jupyter-on-portal.png)

A Jupyter notebookok eléréséhez ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** tallózással is megkeresheti. Cserélje le az URL CLUSTERNAME részét a saját fürt nevére. A jegyzetfüzetek eléréséhez a rendszergazdai fiók jelszavára van szükség.

![Jupyter notebookok böngészése](./media/spark-overview/spark-jupyter-notebook.png)

Válassza a PySpark egy könyvtárat, amely néhány példát tartalmaz a PySpark API-t használó előre csomagolt jegyzetfüzetek. A Spark-témakör ezen csomagkódmintáit tartalmazó jegyzetfüzetek a [GitHubon](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) érhetők el.

A jegyzetfüzetek feltöltheti közvetlenül a [GitHubról](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) a Jupyter notebook-kiszolgáló a Spark-fürt. A Jupyter kezdőlapján kattintson a képernyő jobb oldalán található **Feltöltés** gombra. Megnyit egy fájlkezelőt. Itt beillesztheti a jegyzetfüzet GitHub (nyers tartalom) URL-címét, és a **Megnyitás**gombra kattinthat.

A Jupyter fájllistán ismét megjelenik a fájlnév egy **Feltöltés** gombbal. Kattintson erre a **Feltöltés** gombra. Most importálta a jegyzetfüzetet. Ismételje meg ezeket a lépéseket a többi jegyzetfüzet feltöltéséhez ebből a forgatókönyvből.

> [!TIP]
> A jobb gombbal a böngészőben lévő hivatkozásokra kattintva a **Hivatkozás másolása parancsra** kattintva lejuthat a GitHub nyers tartalom URL-címére. Ezt az URL-címet beillesztheti a Jupyter Fájlkezelő feltöltése párbeszédpanelre.
> 
> 

Ezután:

* Tekintse meg a kódot a jegyzetfüzetre kattintva.
* Az egyes cellák végrehajtása a **SHIFT-ENTER**billentyű lenyomásával.
* Futtassa a teljes jegyzetfüzetet a **Cell** -> **Run**gombra kattintva.
* Használja a lekérdezések automatikus vizualizálását.

> [!TIP]
> A PySpark kernel automatikusan vizualizálja az SQL (HiveQL) lekérdezések kimenetét. A jegyzetfüzet **Típus** menügombjaival számos különböző típusú vizualizáció (Táblázat, Kör, Vonal, Terület vagy Sáv) közül választhat:
>
>

![Logisztikai regressziós ROC görbe az általános megközelítéshez](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>A következő lépések
Most, hogy be van állítva egy HDInsight Spark-fürthöz, és feltöltötte a Jupyter-jegyzetfüzeteket, készen áll a három PySpark-jegyzetfüzetnek megfelelő témakörök közötti munkára. Bemutatják, hogyan fedezheti fel az adatokat, majd hogyan hozhat létre és használható fel modelleket. A speciális adatfeltárási és modellezési jegyzetfüzet bemutatja, hogyan lehet keresztérvényesítést, hiperparaméter-söprést és modellértékelést.

**Adatfeltárás és modellezés a Sparkkal:** Fedezze fel az adatkészletet, és hozza létre, pontozás, és értékelje ki a gépi tanulási modellek segítségével a [bináris osztályozási és regressziós modellek létrehozása az adatok a Spark MLlib eszközkészlet témakör.](spark-data-exploration-modeling.md)

**Modell felhasználása:** A témakörben létrehozott besorolási és regressziós modellek pontozásáról a [Pontszám és a Spark által készített gépi tanulási modellek kiértékelése](spark-model-consumption.md)című témakörben olvashat.

**Keresztellenőrzés és hiperparaméter-söprés**: [Részletes adatfeltárás és -modellezés](spark-advanced-data-exploration-modeling.md) a Sparkkal kapcsolatban, hogyan képezhetők be a modellek keresztellenőrzéssel és hiperparaméter-söpréssel

