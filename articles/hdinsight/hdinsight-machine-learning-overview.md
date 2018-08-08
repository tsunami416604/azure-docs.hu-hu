---
title: Gépi tanulás – áttekintés – Azure HDInsight
description: A gépi tanulási lehetőségek HDInsight ismerteti.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jasonh
ms.openlocfilehash: 79c50935d36070b9f6b7cd6dd0af2d09c4a30f68
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590381"
---
# <a name="machine-learning-on-hdinsight"></a>A Machine learning, a HDInsight

HDInsight lehetővé teszi, hogy a gépi tanulás big Data jellegű adatokat, és a nagy mennyiségű (több petabájtnyi vagy akár exabájtjain való elemzést) strukturált és strukturálatlan adatok egyaránt, az értékes információkat beszerzésének képessége és gyors ütemben adatokkal. HDInsight számos gépi tanulási lehetőségek: könnyen használható és MLlib, R, Hive és a Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>Könnyen használható és MLlib

[HDInsight Spark](spark/apache-spark-overview.md) , egy Azure-ban üzemeltetett ajánlat [Spark](http://spark.apache.org/), egy egységesített, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszert támogató memórián belüli feldolgozást a big data-elemzés növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriabeli elosztott számítási képességekkel teszik megfelelő választás az olyan a használt a machine learning és a graph számítások iteratív algoritmusaival együtt. Nincsenek két méretezhető machine learning-kódtárak együttese algoritmikus modellezési képességek életre az elosztott környezetekben: MLlib és könnyen használható. MLlib tartalmaz az eredeti API-ra épülő rdd-k. Könnyen használható csomag egy újabb, amelyek gépi Tanulási folyamatok megalkotásához DataFrames épülő magasabb szintű API-t biztosít. Könnyen használható még nem támogatja az a Funkciók, MLlib, de a Spark a standard szintű machine learning-könyvtárat, MLlib lecseréli.

A Microsoft Machine Learning library for Apache Spark van [MMLSpark](https://github.com/Azure/mmlspark). Ez a kódtár úgy tervezték, adja meg az adatszakértők hatékonyabbá a Spark, a Kísérletezési aránya növelése, és kihasználhatja a legmodernebb gépi tanulási technikák, beleértve a mély tanulás, nagy méretű adatkészleteken. MMLSpark biztosít egy könnyen használható az alacsony szintű API-k a felső szintű rétege létrehozását, skálázható gépi Tanulási modelleket, például az indexelő karakterláncok adatok coercing gép tanulási algoritmusok és összeállításával készíthet átjárókat funkció vektorok által várt elrendezés be. Az MMLSpark könyvtár egyszerűbbé teszi ezeket és más gyakori feladatok a PySpark modellek készítéséhez.

## <a name="r"></a>R

[R](https://www.r-project.org/) jelenleg a világ legnépszerűbb statisztikai programozási nyelvet. A több mint 2,5 millió felhasználók és az egyre növekvő közösségi egy nyílt forráskódú adatvizualizációs eszköz. Az Alap az élénk felhasználóhoz, és több mint 8000 befizetett csomagok az R a számos vállalat, akiknek szükség van a gépi tanulás nagy valószínűséggel választást. Létrehozhat egy HDInsight-fürt készen áll a használatra, a nagyméretű adatkészleteket és a modellek gépi Tanulási szolgáltatásokkal. Ez a képesség nyújt az adatszakértők és a egy jól ismert R felületen lehet skálázni Statisztikusok igény szerinti HDInsight, keresztül a fürt beállítása és fenntartását anélkül.

![Az R server-előrejelzés-képzések](./media/hdinsight-machine-learning-overview/r-training.png)

A fürt élcsomópontjához kényelmes megoldás az R-szkriptek futtatása és a fürthöz való csatlakozáshoz.  R-szkriptek futtatásához a fürt csomópontjai között ScaleR a Hadoop Mapreduce használatával lehetősége is van, vagy a Spark számítási környezetek.

HDInsight Spark ML szolgáltatásokkal a Spark számítási környezetek használatával párhuzamosítható képzési egy fürt csomópontjai között. Segítségével futtathat R-szkriptek közvetlenül, az élcsomóponton, az összes rendelkezésre álló magok párhuzamosan, igény szerint. Azt is megteheti futtathatja a kódot a fürt összes csomópontja között elosztott feldolgozási elindít az élcsomóponthoz. A HDInsight Spark Machine Learning Services emellett lehetővé teszi a párhuzamosan futtatni függvények, nyílt forráskódú R-csomagok a, ha szükséges.

## <a name="azure-machine-learning-and-hive"></a>Az Azure Machine Learning és a Hive

Az Azure Machine Learning prediktív elemzési modellt, valamint egy teljes körűen felügyelt szolgáltatás segítségével üzembe helyezheti prediktív modelljeit feldolgozható kész webszolgáltatásként eszközöket biztosít. Az Azure Machine Learning teljes körű prediktív elemzési megoldás a felhőben, amelyek segítségével létrehozása, tesztelése, üzembe helyezése és kezelése a prediktív modellek is. Jelöljön ki egy nagy méretű algoritmustár, egy webes studio használni a modellek létrehozásához, és könnyedén üzembe a modellt webszolgáltatásként.

![Így advanced analytics elérhető-e a Hadooppal a Microsoft Azure Machine Learning segítségével](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Funkciók létrehozása az adatokat egy HDInsight Hadoop-fürt használatával [Hive-lekérdezések](../machine-learning/team-data-science-process/create-features-hive.md). *Jellemzőkiemelés* megkísérli tanulási algoritmus a nyers adatokat, amelyek elősegítik a tanulási folyamat szolgáltatásokat hoz létre prediktív hatékonyságának növelése érdekében. HiveQL lekérdezéseket futtathat az Azure ML és feldolgozása a Hive és a blob storage-ban tárolt adatok eléréséhez a [adatok importálása modullal](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Deep learning](https://www.microsoft.com/en-us/research/group/dltc/) gépi Neurális hálózatokat, az emberi agy biológiai folyamatok adatközpontoknak használó ága. Számos kutatói deep learning látja, mint a mesterséges intelligencia lehetőségeinek ígéret megközelítést. Deep learning példák használja a beszélt nyelv fordítók, a lemezkép elismerési rendszer és a gép indoklása.

Annak érdekében, hogy azt mutatja be a saját munkahelyi a deep learning, a Microsoft kifejlesztett az ingyenes, könnyen használható, nyílt forráskódú [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Ez az eszközkészlet használja számos Microsoft-termékek, vállalatok globális méretekben deep learning üzembe helyezése kell, és a tanulók a legújabb algoritmusok és technikák iránt. 

## <a name="see-also"></a>Lásd még

### <a name="scenarios"></a>Forgatókönyvek

* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Filmajánlók a mahout használatával](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive-val és az Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive és az Azure Machine Learning-végpont](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Gépi tanulás a Spark on HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Deep learning-erőforrások

* [A Spark mélytanulási eszközkészlet](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [A Cognitive toolkit + Spark Tensorflow zavaróan párhuzamos képek besorolása](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
