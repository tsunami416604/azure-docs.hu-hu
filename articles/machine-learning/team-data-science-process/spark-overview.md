---
title: Adatelemzés a Spark használatával az Azure HDInsight – a csoportos adatelemzési folyamat
description: A Spark MLlib eszközkészlet számos lehetőséget kínál a jelentős gépi tanulási adatmodellezési képességekkel az elosztott HDInsight-környezetben.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b49ec91af0c2b55185d7f750420825bb174c4b6d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473731"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Az Azure HDInsight Spark használatával adatelemzés áttekintése

Ez az alkalmazáscsomag témakörök használatára a HDInsight Spark például adatbetöltést, funkciófejlesztési, modellezés és a modell értékelése a közös adatok adatelemzési feladatokat mutatja be. Használt adatokat egy minta az 2013 NYC taxi utazást és diszkont adatkészlet. A modellek tartalmazzák a logisztikai és lineáris regresszió, véletlenszerű erdők és gyorsított fa átmenetes. A témakörök a modellek tárolása az Azure blob storage (WASB) és a pontszám, és a prediktív teljesítmény kiértékelése is megjelennek. Speciális témakörök ismertetik, hogyan lehet a modellek tanítása az kereszt-ellenőrzési és a hyper-paraméter kezdik. Ez a témakör áttekintést is hivatkozik a témakörök, amelyek bemutatják, hogyan állítható be a Spark-fürt a megadott forgatókönyvek a lépések elvégzéséhez szükséges.

## <a name="spark-and-mllib"></a>Spark- és MLlib
[A Spark](http://spark.apache.org/) egy nyílt forráskódú párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozás big data elemző alkalmazások teljesítményének növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriabeli elosztott számítási képességekkel teszik megfelelő választás az olyan a használt a machine learning és a graph számítások iteratív algoritmusaival együtt. [MLlib](http://spark.apache.org/mllib/) van Spark skálázható gépi tanulási kódtár, amely a algoritmikus adatmodellezési képességekkel az elosztott környezetekben.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) nyílt forráskódú Spark on Azure üzemeltetett ajánlat. Is támogatja a **Jupyter PySpark notebookok** futtatható Spark SQL interaktív lekérdezések átalakítása, szűréssel és az Azure Blobs (WASB) tárolt adatok vizualizációja a Spark-fürtön. PySpark a Python API Spark számára. A kódrészletek, amelyek a megoldásokat, és itt futtatása a Jupyter notebookok a Spark-fürtökön telepített adatok vizualizálásához megfelelő grafikon megjelenítése. Az alábbi témakörök a modellezési lépéseit, amely bemutatja, hogyan betanításához, kiértékelése, mentése és felhasználását a modell különböző típusú kódot tartalmaznak.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>A telepítő: A Spark-fürtök és a Jupyter notebookok
Beállítási lépéseket és a kód-okat Ez az útmutató egy HDInsight Spark 1.6-os használatával. De Jupyter notebookok a HDInsight Spark 1.6-os és a Spark 2.0 fürtök biztosított. A jegyzetfüzetek és a rájuk mutató hivatkozást leírása tartalmazza a [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) az azokat tartalmazó GitHub-adattárban. Továbbá, a kód itt és a társított notebookok az általános és működnie kell bármelyik Spark-fürtön. Ha nem használja a HDInsight Spark, a fürt beállítása és lehet, hogy a felügyeleti lépések kissé eltérhetnek az itt látható. Az egyszerűség kedvéért az alábbiakban a Spark 1.6-os (a futtatható a pySpark kernel a Jupyter Notebook Server) és a Spark 2.0-s verzióját (kell futtatni a pySpark3 kernel a Jupyter Notebook Server) a Jupyter notebookok mutató hivatkozásokat:

### <a name="spark-16-notebooks"></a>A Spark 1.6-os notebookok
Ezeket a notebookokat vannak a pySpark kernel Jupyter notebook Server futtatásához.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Információt nyújt az adatfeltárás, modellezés és kiértékelés a számos különböző algoritmus végrehajtásához.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): A notebook #1 és a hiperparaméter finomhangolása és kereszt-ellenőrzés, modell fejlesztési témaköröket tartalmazza.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Bemutatja, hogyan Python használata a HDInsight-fürtökön mentett modell üzembe helyezése.

### <a name="spark-20-notebooks"></a>A Spark 2.0 notebookok
Ezeket a notebookokat futtatásához a Jupyter notebook Server pySpark3 kernel vannak.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Ez a fájl információt nyújt az adatfeltárás, modellezés, végrehajtásához és a Spark 2.0 pontozási-fürtöket a NYC Taxi utazást és a leírt diszkont adatkészlet [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Ez a jegyzetfüzet gyorsan megismerheti a kódot, a Spark 2.0 biztosítunk jó kiindulási pont lehet. Részletesebb jegyzetfüzet a NYC i taxik adatait elemzi, lásd a következő notebook ebben a listában. Lásd az alábbi listát, hasonlítsa össze ezeket a notebookokat.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Ez a fájl bemutatja, hogyan hajtsa végre az adatok konvertálását (Spark SQL- és dataframe műveletek), feltárásában, modellezés és a NYC Taxi utazást és a leírt diszkont adatkészlet pontozás [Itt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Ez a fájl bemutatja, hogyan hajtsa végre az adatok konvertálását (Spark SQL- és dataframe műveletek), feltárásában, modellezés és pontozás a jól ismert légitársaság időben indító adatkészlet 2011 és 2012 használatával. Integráltuk az repülőtér időjárási adatok (például szélsebesség, hőmérséklet, tengerszint stb.) a légitársaság adatkészlet előtt modellezés, ezért ezek időjárási szolgáltatások is tartalmazza, a modellben.

<!-- -->

> [!NOTE]
> A légitársaság adatkészlet hozzá lett adva a Spark 2.0 notebookok jobban a besorolási algoritmusok használatát mutatja be. Lásd az alábbi hivatkozásokat légitársaság kapcsolatos információk időben indító adatkészletet és időjárási adatkészletet:

>- Légitársaság időben indító adatok: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Időjárási adatok repülőtér: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)
>
>

<!-- -->

<!-- -->

> [!NOTE]
A Spark 2.0 notebookok a NYC taxi és légitársaság repülési késleltetés-adatkészleteken alapuló is igénybe vehet, 10 perc vagy több (függően a HDI-fürt mérete). A fenti listában az első jegyzetfüzet bizonyos szempontokból az adatfeltárás, a Vizualizáció és a gépi Tanulási modell betanítása egy jegyzetfüzetet, futtathat le mintavételezés NYC adatkészlet, amelyben a taxi és diszkont fájlok már előre illesztett kevesebb idő alatt látható: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Ez a jegyzetfüzet (2-3 perc) befejezéséhez sokkal rövidebb ideig tart, és előfordulhat, hogy lehet egy jó kiindulási pontjaként gyorsan megismerheti a kódot, a Spark 2.0 biztosítunk.

<!-- -->

A Spark 2.0-modell és a modell pontozása tárolófelhasználásának operacionalizálás, tekintse át a [Spark 1.6-os dokumentum-használat](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) példaként, amely tartalmazza a szükséges lépéseket. Ezzel a Spark 2.0-s, cserélje le a Python-kód fájlt a [ezt a fájlt](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Előfeltételek

A következő eljárások a Spark 1.6-os kapcsolódnak. A Spark 2.0-s verziójának használata a notebookok leírt, és korábban csatolva.

1. Rendelkeznie kell egy Azure-előfizetéssel. Ha Ön még nem rendelkezik egy, [lekérése az Azure ingyenes próbaverzió](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Szüksége lesz egy Spark 1.6-os fürtöt az útmutató végrehajtásához. Hozzon létre egyet, lásd: szereplő utasítások [első lépések: Apache Spark létrehozása az Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). A fürt típusát és verzióját van megadva a **fürt típusának kiválasztása** menü.

![Fürt konfigurálása](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ez a témakör bemutatja, hogyan használja a Python helyett a Scala egy teljes körű adatelemzési folyamat feladatok végrehajtásához, lásd: a [használata a Scala Spark on Azure Data Science](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>A NYC 2013-i taxik adatait
A NYC Taxi útadatok körülbelül 20 GB tömörített vesszővel elválasztott értékeket (CSV) fájl (~ 48 GB tömörítetlen), minden egyes út 173 milliónál egyes utak és a vitel magában foglaló fizetni. Minden egyes út rekord tartalmazza, a kiválasztják és dropoff hely és idő, anonimizált feltörés (illesztőprogramok) jogosítvány száma, és medallion (taxi az egyedi azonosító) számát. Az adatok minden lelassítja ismerteti az év 2013-hoz, és minden hónapban megtalálható a következő két adatkészletet:

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

A Microsoft ezeket a fájlokat 0,1 % mintát venni, és az utazás csatlakoztatott\_adatokat és utazás\_díjszabás közül fájlok ebben a lépésenkénti útmutatóban a bemeneti adatkészlet használandó egyetlen adatkészletekbe. Utazás csatlakozni egyedi kulcsa\_adatokat és utazás\_diszkont tevődik össze a mezők: medallion céltudatos\_engedélyt, és felvétel\_datetime. Az adatkészlet minden rekordját a következő attribútumokat NYC Taxi belépőt jelölő tartalmazza:

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
| direct_distance |Válasszon közötti távolság közvetlen fel és dropoff helyek |
| payment_type |Fizetési típus (cas, hitelkártya stb.) |
| fare_amount |Diszkont összeg |
| Pótdíj |Pótdíj |
| mta_tax |MTA adó |
| tip_amount |Tipp összeg |
| tolls_amount |Útdíjak összeg |
| total_amount |Teljes összeg |
| Formabontó |Formabontó (0 és 1 nem vagy Igen) |
| tip_class |Tipp osztály (0: 0, 1: 0 – 5 USD, 2: 6 – 10 USD, 3: $11 – 20., 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Hajtsa végre a Spark-fürtön lévő Jupyter notebook kód
A Jupyter Notebook az Azure portálról indíthatja el. Keresse meg a Spark-fürtöt az irányítópulton, és kattintson rá a kezelése lapon adja meg a fürt. A Spark-fürthöz társított a notebook megnyitásához kattintson a **fürt irányítópultjai** -> **Jupyter Notebook** .

![Fürt irányítópultjai](./media/spark-overview/spark-jupyter-on-portal.png)

Keresse a ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** a Jupyter notebookok eléréséhez. Az URL-cím a FÜRTNÉV részét cserélje le a saját fürt nevére. A jelszót a rendszergazdai fiók a notebookok elérésére van szüksége.

![Keresse meg a Jupyter notebookok](./media/spark-overview/spark-jupyter-notebook.png)

Válassza ki a PySpark néhány példa az előrecsomagolt notebookok a PySpark API-t használó tartalmazó könyvtár megtekintéséhez. A notebookok, amelyek tartalmazzák a mintakódokat, a Spark témakör Suite esetén érhető el [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Feltöltheti közvetlenül a notebookok [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) , a Spark-fürtön lévő Jupyter notebook server. A Jupyter kezdőlapján kattintson a **feltöltése** gombra a képernyő jobb oldalán jelenjen meg. Ekkor megnyílik a Fájlkezelőben. Itt másolhatja a GitHub (nyers tartalom) URL-címét a jegyzetfüzetet, és kattintson **nyílt**.

A Jupyter fájl nevére, a fájlnév megjelenik egy **feltöltése** újra gombra. Kattintson a **feltöltése** gombra. Most már importált a notebookot. Ismételje meg ezeket a lépéseket az ebben a bemutatóban a notebookok feltöltése.

> [!TIP]
> A hivatkozások a böngészőben, és válassza a jobb gombbal **hivatkozás másolása** a GitHub nyers tartalom URL-Címének lekéréséhez. A Jupyter feltöltése fájl explorer párbeszédpanelen is illessze be az URL-címet.
> 
> 

Most a következőket teheti:

* Tekintse meg a kódot a notebook kattintva.
* Hajtsa végre az egyes cellákban lenyomásával **a SHIFT + ENTER**.
* A teljes jegyzetfüzet futtatásához kattintson a **cella** -> **futtatása**.
* A lekérdezések automatikus vizualizációt használja.

> [!TIP]
> A PySpark kernel automatikusan elérhetővé (HiveQL) SQL-lekérdezések kimenetének. Használatával számos különböző típusú Vizualizációk (tábla, a kör, vonal, terület vagy sáv) közül választhatja meg vannak megadva a **típus** menü gombok a jegyzetfüzet:
>
>

![Logisztikai regressziós ROC-görbét az általános megközelítés](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>A következő lépések
Most, hogy a HDInsight Spark-fürt beállítása és a Jupyter notebookok feltöltése, készen áll a témakörök, amelyek megfelelnek a három PySpark notebookok keresztül. Ezek bemutatják, hogyan feltárhatja az adatait, majd hogyan hozhat létre és használhat fel modelleket. A speciális adatok feltárása és modellezés notebook bemutatja, hogyan közé tartozik a kereszt-ellenőrzés, abszolút, a hyper-paraméter és a modell értékelése.

**Adatáttekintés és modellezés a Spark segítségével:** Ismerje meg az adatkészlet, és hozzon létre, pontszám és a gépi tanulási modellek által feldolgozása révén kiértékelheti az [adatokat bináris besorolási és regressziós modelleket hozhat létre a Spark MLlib eszközkészlete](spark-data-exploration-modeling.md) témakör.

**Modell-felhasználás:** Az ebben a témakörben létre besorolási és regressziós modellek pontozása kezelésével kapcsolatos információkért lásd: [pontszám és kiértékelheti a Spark használatával összeállított gépi tanulási modellek](spark-model-consumption.md).

**Kereszt-ellenőrzési és a hiperparaméter kezdik**: Lásd: [speciális adatáttekintés és modellezés a Spark segítségével](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek tanítása az kezdik kereszt-ellenőrzési és a hyper-paraméter

