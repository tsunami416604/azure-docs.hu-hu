---
title: Gépi tanulás – áttekintés – Azure HDInsight
description: Az Azure HDInsight lévő fürtök big data gépi tanulási lehetőségeinek áttekintése.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: 17615c0005a3cbc5b20cea697b4c75b5ed06ae0a
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811997"
---
# <a name="machine-learning-on-hdinsight"></a>Gépi tanulás a HDInsight-on

A HDInsight lehetővé teszi a gépi tanulást a big dataokkal, így értékes információkhoz juthat a strukturált, strukturálatlan és gyorsan mozgó adatok nagy mennyiségű (petabájt vagy akár több exabájt) adatairól. Több gépi tanulási lehetőség is létezik a HDInsight-ben:  SparkML és Apache Spark MLlib, R, Apache Hive és a Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML és MLlib

A [HDInsight Spark](spark/apache-spark-overview.md) egy [Apache Spark](https://spark.apache.org/), egy egységes, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszer, amely támogatja a memóriabeli feldolgozást a Big Data Analitika fellendítéséhez. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriabeli elosztott számítási képességekkel teszik megfelelő választás az olyan a használt a machine learning és a graph számítások iteratív algoritmusaival együtt. Két skálázható gépi tanulási függvénytár van, amely a következő elosztott környezethez biztosít algoritmikus modellezési képességeket: MLlib és SparkML. A MLlib a RDD-re épülő eredeti API-t tartalmazza. A SparkML egy újabb csomag, amely egy magasabb szintű API-t biztosít, amely a DataFrames-re épül a ML-folyamatok létrehozásához. A SparkML még nem támogatja a MLlib összes funkcióját, de a MLlib-t a Spark szabványos Machine learning-könyvtáraként váltja fel.

A Apache Spark Microsoft Machine Learning könyvtára [MMLSpark](https://github.com/Azure/mmlspark). Ez a könyvtár úgy lett kialakítva, hogy a Sparknál termelékenyebb adatszakértőket hozzon létre, növelje a kísérletezés mértékét, és kihasználja az élvonalbeli gépi tanulási technikákat, beleértve a mély tanulást, nagyon nagy adatkészleteken. A MMLSpark méretezhető ML-modellek, például indexelési karakterláncok, a gépi tanulási algoritmusok által várt elrendezésre, valamint a szolgáltatás-vektorok összeállítására szolgáló réteget biztosít a SparkML alacsony szintű API-jai felett. A MMLSpark könyvtár leegyszerűsíti ezeket és egyéb gyakori feladatokat a PySpark-modellek létrehozásához.

## <a name="r"></a>R

Az [R](https://www.r-project.org/) jelenleg a legnépszerűbb statisztikai programozási nyelv a világon. Ez egy nyílt forráskódú adatvizualizációs eszköz, amelynek közössége több mint 2 500 000 felhasználó, és egyre nő. A virágzó felhasználói bázisával és a több mint 8 000 közreműködő csomaggal az R valószínűleg számos olyan vállalat számára valószínű választás, akiknek gépi tanulásra van szükségük. Létrehozhat egy HDInsight-fürtöt ML-szolgáltatásokkal, amelyek készen állnak a nagy adatkészletekkel és modellekkel való használatra. Ez a képesség olyan jól ismert R-felületet biztosít az adatszakértők és a statisztikusok számára, amely igény szerint méretezhető a HDInsight keresztül, a fürt beállítása és karbantartása nélkül.

![Az R Serverrel való előrejelzés betanítása](./media/hdinsight-machine-learning-overview/r-training.png)

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való kapcsolódáshoz és az R-parancsfájlok futtatásához.  Lehetősége van arra is, hogy az R-szkripteket a fürt csomópontjain is futtassa, a skálázás Hadoop-térképének csökkentése vagy a Spark számítási környezetek használatával.

A Spark-nal rendelkező HDInsight ML-szolgáltatásaival a Spark számítási környezettel integrálással képzéseket biztosíthat a fürtök csomópontjain. Az R-szkripteket közvetlenül a peremhálózati csomóponton is futtathatja, ha az összes rendelkezésre álló magot szükség szerint párhuzamosan használja. Másik lehetőségként futtathatja a kódot a peremhálózati csomópontból a fürt összes csomópontján elosztott feldolgozás elindításához. A Sparktal rendelkező HDInsight ML-szolgáltatások a nyílt forráskódú R-csomagok tetszés funkcióit is lehetővé teszik, ha szükséges.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning és Apache Hive

Azure Machine Learning eszközöket biztosít a prediktív elemzések modellezéséhez, valamint egy teljes körűen felügyelt szolgáltatáshoz, amellyel a prediktív modelleket használatra kész webszolgáltatásként helyezheti üzembe. A Azure Machine Learning egy teljes körű prediktív elemzési megoldás a felhőben, amely a prediktív modellek létrehozásához, teszteléséhez, működővé tenni és kezeléséhez használható. Válasszon egy nagyméretű algoritmus-függvénytárból, használjon webalapú stúdiót modellek létrehozásához, és egyszerűen üzembe helyezheti a modellt webszolgáltatásként.

![A fejlett Analitika elérhetővé tétele a Hadoop és a Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

HDInsight Hadoop-fürtben található adatszolgáltatások létrehozása a [kaptár-lekérdezések](../machine-learning/team-data-science-process/create-features-hive.md)használatával. A szolgáltatások *mérnöki* célja, hogy a tanulási algoritmusok által létrehozott nyers adatokból származó funkciók létrehozásával növelje a tanulási algoritmusok prediktív erejét. Azure Machine Learning studióból futtathat HiveQL-lekérdezéseket, és a kaptárban feldolgozott és a blob Storage-ban tárolt adatokhoz az [adatok importálása modul](../machine-learning/studio/import-data.md)használatával férhet hozzá.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

A [Deep learning](https://www.microsoft.com/en-us/research/group/dltc/) a gépi tanulás olyan ága, amely neurális hálózatokat használ, az emberi agy biológiai folyamatai által inspirálva. Számos kutató ígéretes megközelítésként látja el a mesterséges intelligenciát. A Deep learning példái közé tartoznak a beszélő nyelvi fordítók, a képfelismerési rendszerek és a gépi indoklások.

A Microsoft az ingyenes, könnyen használható, nyílt forráskódú [Microsoft Cognitive Toolkitt](https://www.microsoft.com/en-us/cognitive-toolkit/)fejlesztette ki, hogy elősegítse a munkát a mélyreható tanulásban. Ezt az eszközkészletet számos Microsoft-termék, a vállalatok világszerte használják, és a nagy léptékű tanulás, valamint a legújabb algoritmusok és technikák érdeklik.

## <a name="see-also"></a>Lásd még

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Filmkészítési javaslatok készítése az Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive és Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive és Azure Machine Learning végpontok közötti](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Gépi tanulás a Apache Spark on HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Deep learning-erőforrások

* [Microsoft Cognitive Toolkit Deep learning-modell használata Azure HDInsight Spark-fürttel](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [A Cafe on Azure HDInsight Spark használata elosztott mély tanuláshoz](spark/apache-spark-deep-learning-caffe.md)
* [Mély tanulási és AI-keretrendszerek a Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
