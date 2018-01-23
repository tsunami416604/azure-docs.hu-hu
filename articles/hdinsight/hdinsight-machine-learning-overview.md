---
title: "Machine learning áttekintése - Azure HDInsight |} Microsoft Docs"
description: "A gépi tanulási a hdinsight beállításokat ismerteti."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: ff99a7a60573cad5e6dd30d4ca48903423e9f87f
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="machine-learning-on-hdinsight"></a>Gépi tanulási a HDInsight-on

A HDInsight lehetővé teszi, hogy a gépi tanulás big Data típusú adatok, ezáltal képesek értékes információkat lekérni a nagy mennyiségű (petabájt, vagy akár exabájtjain való elemzést) strukturált, strukturálatlan, és a gyorsan változó adatokkal. A HDInsight számos machine learning lehetőség áll rendelkezésre: SparkML és MLlib, R, Hive és a Microsoft kognitív eszközkészlet.

## <a name="sparkml-and-mllib"></a>SparkML és MLlib

[HDInsight Spark](spark/apache-spark-overview.md) van egy Azure által üzemeltetett elérhető [Spark](http://spark.apache.org/), egy egységesített, nyissa meg a forrás, a párhuzamos adatok feldolgozást végző keretrendszer támogatja a memórián belüli feldolgozást a big data-elemzések növelése érdekében. A Spark program sebességét, a könnyű, valamint a kifinomult analytics lett tervezve. A Spark memóriában elosztott tárolt számítási képességei jól funkcionálnak a szerepel a machine learning és a graph számítások iteratív algoritmusaival a. Nincsenek a két algoritmikus modellezési képességekkel kerüljön az elosztott környezetben méretezhető machine learning szalagtárat: MLlib és SparkML. MLlib tartalmazza az eredeti API RDDs platformra épül. SparkML csomag egy újabb, amely egy magasabb szintű API ML folyamatok megalkotásához DataFrames platformra épül. SparkML egyelőre nem támogatják a összes MLlib szolgáltatást, de a Spark szabványos machine learning-könyvtárral, MLlib váltja fel.

Apache Spark-hez a Microsoft Machine Learning könyvtár [MMLSpark](https://github.com/Azure/mmlspark). Ezt a szalagtárat célja, hogy adja meg adatszakértőkön végig hatékonyan dolgozhatnak a Spark, kísérletezhet növelésére, és használja ki a legmodernebb gépi tanulás technikák, mély learning, a nagy adatkészleteknél, beleértve. MMLSpark biztosít a felső szintű rétege SparkML tartozó alacsony szintű API-k modellek ML méretezhető, például az indexelő karakterláncok fejlesztéskor adatok coercing gép tanulási algoritmus, és a szolgáltatás vektorok összeállításakor által várt elrendezésekben. A MMLSpark könyvtár ezek és a PySpark modelljei felépítéséhez kapcsolatos további általános feladatok egyszerűbbé teszi.

## <a name="r"></a>R

[R](https://www.r-project.org/) jelenleg a legnépszerűbb statisztikai programozási nyelvet a világon. Egy nyílt forráskódú képi megjelenítés eszköz több mint 2,5 millió felhasználó alapján, és inkább a Közösséggel. Az alap thriving felhasználóhoz, és több mint 8000 átadott csomagokat, az R esetén sok vállalatoknak gépi tanulás kell valószínűleg választani. HDInsight-fürtöt hozhat létre az R Server nagy adatkészletek és modellek használatra kész. Ezt a képességet biztosít az adatelemzők és egy ismert R felülettel is méretezhető Statisztikusok igény szerinti HDInsight keresztül nem növeli a fürt beállítása és karbantartása.

![R Server előrejelzés képzési](./media/hdinsight-machine-learning-overview/r-training.png)

A fürt élcsomópont kényelmes csatlakozzon a fürthöz, és az R parancsfájlok futtatásához.  Lehetősége is van a R parancsfájlokat futtasson a fürt csomópontjai között ScaleR a Hadoop térkép csökkentése használatával, vagy Spark számítási környezeteket.

A Spark on HDInsight R Server akkor is parallelize képzési a fürt csomópontjai között egy Spark számítási környezet használatával. R parancsfájlok futtathatók közvetlenül a peremhálózati csomóponton, igény szerint az összes rendelkezésre álló magot párhuzamos, használatával. Alternatív megoldásként futtathatja a kódot, amely a fürt összes csomópontja között van elosztva feldolgozási indítsa a élcsomópont. Az R Server on HDInsight Spark is lehetővé teszi a nyílt forráskódú R csomagok parallelizing funkciókat, ha szükséges.

## <a name="azure-machine-learning-and-hive"></a>Az Azure Machine Learning és a Hive

Az Azure Machine Learning a prediktív elemzési modell, valamint egy teljes körűen felügyelt szolgáltatás segítségével üzembe helyezheti prediktív modelljeit felhasználásához felhasználásra kész webszolgáltatásként eszközöket biztosít. Az Azure Machine Learning egy olyan teljes prediktív elemzési megoldás a felhőben, amelyek segítségével hozzon létre, tesztelheti, azok és felügyelhet. Jelöljön ki egy nagy méretű algoritmustár, létrehozási modelleket egy webalapú studio használata és egyszerűen a webszolgáltatásként a modell rendszerbe állítása.

![Hogy a speciális elemzés és a Microsoft Azure Machine Learning Hadoop érhető el](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Szolgáltatások létrehozása, adatok egy HDInsight Hadoop-fürt használatával [Hive-lekérdezések](../machine-learning/team-data-science-process/create-features-hive.md). *Jellemzőkiemelés* megkísérli a tanulási algoritmus a nyers adatok, amelyek egyszerűbbé teszik a tanulást szolgáltatások létrehozásával előrejelzési teljesítményének növelése érdekében. Az Azure ML HiveQL lekérdezések futtatása, és struktúra feldolgozása és a blob Storage tárolóban tárolt adatok elérése a [és adatokat importálhat modul](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[A részletes tanulási](https://www.microsoft.com/en-us/research/group/dltc/) gépi tanulás által használt Neurális hálózatokat, az emberi agy biológiai folyamatokat inspirálta ága. Sok kutatói mély learning egy ígéret megközelítés mesterséges eszközintelligencia továbbfejlesztésének jelenik meg. A részletes tanulási példák szóbeli nyelvi fordító, a lemezkép felismerés rendszerek és a gép indoklást.

A saját munkahelyi mély szeretnének előzetes érdekében a Microsoft arra tervezte a szabad, könnyen használható, nyílt forráskódú [Microsoft kognitív eszközkészlet](https://www.microsoft.com/en-us/cognitive-toolkit/). Ez az eszközkészlet számos Microsoft-termékek, vállalatok világszerte léptékű mély tanulási telepítéséhez van szükség, és a legújabb algoritmusok és technikák érdekelt diákoknál használja. 

## <a name="see-also"></a>Lásd még

### <a name="scenarios"></a>Forgatókönyvek

* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)
* [A Mahout movie javaslatok generálása](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive és az Azure gépi tanulás](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive és az Azure Machine Learning-végpont](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Gépi tanulási a Spark on HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>A részletes képzési erőforrást

* [A Spark mély tanulási eszközkészlet](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Kognitív eszközkészlet + a Spark Tensorflow embarrassingly párhuzamos kép besorolás](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
