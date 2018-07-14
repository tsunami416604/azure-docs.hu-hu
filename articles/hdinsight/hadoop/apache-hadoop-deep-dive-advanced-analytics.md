---
title: Deep dive - advanced analytics – Azure HDInsight |} A Microsoft Docs
description: Ismerje meg, hogyan fejlett analitikai algoritmusokat használ a big Data típusú adatok feldolgozásához.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: ff1f6fcae1d21d47b5ee0263b4e038287a588991
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008229"
---
# <a name="deep-dive---advanced-analytics"></a>Részletes bemutatása – speciális elemzés

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Mi a speciális elemzés HDInsight?

HDInsight lehetővé teszi a nagy mennyiségű strukturált és strukturálatlan adatok egyaránt értékes betekintést kaphat adataiba, és a gyors ütemben adatokat. Bővített analitika, a nagy mértékben skálázható architektúrák, statisztikai használatát és a machine learning-modellek és az intelligens irányítópultok sem értelmezhető elemzési tudja biztosítani. A gépi tanulást, vagy *prediktív elemzési*, algoritmusokat használ, amelyek azonosításához, és ismerje meg az előrejelzéseket és a döntések útmutató az adatok a kapcsolatokat.

## <a name="advanced-analytics-process"></a>Fejlett analitikai folyamat

![Folyamat](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Után, az üzleti probléma azonosítása és gyűjtése elindította, és az adatok feldolgozása, létrehoz egy modellt, amely a kérdést kell előre jelezni kívánt. A modell egy vagy több gépi tanulási algoritmusok használatával győződjön meg a legjobban az Ön üzleti igényeinek előrejelzése típusát.  A legtöbb az adatokat a modell betanításához teszteléséhez vagy kiértékeléséhez, a többi használandó. 

Miután létrehozta, betöltése, teszteléséhez, és a modell értékelése, a következő lépés az, hogy a kérdésekre adott válaszok Főhitelesítésszolgáltató megkezdi a modell üzembe helyezése. Az utolsó lépés, hogy a modellek teljesítményének figyelése, és szükség esetén finomhangolja. 

## <a name="common-types-of-algorithms"></a>Általános típusú algoritmusokat

Fejlett elemzési megoldásokat biztosítanak egy gépi tanulási algoritmusokat. Itt található egy összefoglaló algoritmusok és a kapcsolódó leggyakoribb üzleti alkalmazási esetek kategória.

![A Machine Learning szolgáltatás a használati esetek](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

A legjobban záró aránytól kiválasztásával, valamint kell érdemes-e meg kell adnia az adatokat a betanításhoz. Machine learning-algoritmusok kategóriái a következők:

* Csak felügyelt eszköz - algoritmus kell tartani a következőkről különböző címkézett adatokkal tud eredményekkel szolgálni előtt
* Félig felügyelt - algoritmus által keresztül egy trainer, interaktív lekérdezésre extra célok, amelyek nem álltak rendelkezésre a képzési kezdeti szakaszában kiegészíthető
* Felügyeletlen - algoritmus nem igényel a betanítási adatok 
* Megerősítő - algoritmus által használt szoftverek ügynökök annak meghatározásához, hogy ideális belül egy adott környezetben (robotika a gyakran használt)


| Algoritmus kategória| Használat | Tanulási típusa | Algoritmusok |
| --- | --- | --- | -- |
| Besorolás | Személyek vagy dolgot besorolása csoportokba | Csak felügyelt eszköz | Döntési fákat, a logisztikai regressziós, a Neurális hálózatokkal |
| Fürtözés | Példák készletét osztásával homogén csoportokba | Felügyeletlen | K-közép-fürtözés |
| A minta észlelése | Az adatok gyakran társítások azonosítása | Felügyeletlen | Társítási szabályok |
| Regresszió | Numerikus eredmények előrejelzése | Csak felügyelt eszköz | Lineáris regresszió, a Neurális hálózatokkal |
| Megerősítése | A robotok optimális viselkedés meghatározása | Megerősítése | Monte Carlo-szimulációk, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>A Machine learning, a HDInsight

HDInsight számos machine learning-beállítások egy fejlett elemzési munkafolyamat rendelkezik:

* [Machine Learning- és Spark](#machine-learning-and-spark)
* [R és a Machine Learning-szolgáltatások](#r-and-r-server)
* [Az Azure Machine Learning és a Hive](#azure-machine-learning-and-hive)
* [Spark és a Deep learning](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Machine Learning- és Spark

[HDInsight Spark](../spark/apache-spark-overview.md) , egy Azure-ban üzemeltetett ajánlat [Spark](http://spark.apache.org/), egy egységesített, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszert használó a memórián belüli feldolgozást a Big Data analytics növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési. Spark memóriabeli elosztott számítási képességekkel teszik megfelelő választás az olyan a használt a machine learning és a graph számítások iteratív algoritmusaival együtt. 

Nincsenek három méretezhető machine learning-kódtárak együttese algoritmikus modellezési képességek életre az elosztott környezetekben:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib tartalmaz az eredeti API-ra épülő Spark rdd-k.
* [**Könnyen használható** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -könnyen használható csomag egy újabb, a gépi Tanulási folyamatokat hozhat létre a Spark adatkerettípusokat jelölhet-ra épülő magasabb szintű API-t biztosít.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – a Microsoft Machine Learning library for Apache Spark (MMLSpark) úgy tervezték, hogy adja meg az adatszakértők hatékonyabbá a Spark, a Kísérletezési mértékű növelését, és a legmodernebb machine learning technikák, beleértve a mély tanulás, nagy méretű adatkészleteken. Az MMLSpark könyvtár egyszerűbbé teszi a gyakori modellezési műveletek PySpark modellek készítéséhez. 

### <a name="r-and-ml-services"></a>R és a Machine Learning-szolgáltatások

HDInsight részeként létrehozhat egy HDInsight-fürtöt [Machine Learning-szolgáltatások](../r-server/r-server-overview.md) nagyméretű adatkészleteket és modellekkel való használatra kész. Ezt az új funkciót biztosít az adatszakértők és a egy jól ismert R felületen lehet skálázni Statisztikusok igény szerinti HDInsight, keresztül a fürt beállítása és fenntartását anélkül.

### <a name="azure-machine-learning-and-hive"></a>Az Azure Machine Learning és a Hive

[Az Azure Machine Learning Studio](https://studio.azureml.net/) prediktív elemzési modellt, valamint egy teljes körűen felügyelt szolgáltatás segítségével üzembe helyezheti prediktív modelljeit feldolgozható kész webszolgáltatásként eszközöket biztosít. Az Azure Machine Learning eszközeivel Ezenfelül teljes körű prediktív elemzési megoldások való gyors létrehozásához, teszteléséhez, üzembe helyezése és prediktív modellek kezelése a felhőben. Jelöljön ki egy nagy méretű algoritmustár, egy webes studio használni a modellek létrehozásához, és könnyedén üzembe a modellt webszolgáltatásként.

### <a name="spark-and-deep-learning"></a>Spark és a Deep learning

[Deep learning](https://www.microsoft.com/research/group/dltc/) által használt gépi tanulási ága *neurálishálózat* (dnn-eket), az emberi agy biológiai folyamatok merítsen inspirációt a. Számos kutatói deep learning látja, mint egy ígéret mesterséges intelligenciát használó megközelítést. Néhány példa a deep learninget használja a beszélt nyelv fordítók, a lemezkép elismerési rendszer és a gép indoklása. Annak érdekében, hogy azt mutatja be a saját munkahelyi a deep learning, a Microsoft az ingyenes, könnyen használható, nyílt forráskódú kidolgozott [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Az eszközkészlet használja nagymértékben számos Microsoft-termékek, vállalatok globális méretekben deep learning üzembe helyezése kell, és a tanulók a legújabb algoritmusok és technikák iránt. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Forgatókönyv – pontszám lemezképek alapján azonosítja a mintákat városi fejlesztés alatt

Tekintsük át egy fejlett analitikai machine learning-folyamat használata a HDInsight egy példa.

Ebben a forgatókönyvben láthatja, hogyan dnn-eket a deep learning keretrendszer előállított a Microsoft Cognitive Toolkit (CNTK), nagy méretű kép gyűjtemények PySpark használata egy HDInsight Spark-fürtön az Azure Blob Storage-fiókban tárolt pontozó a üzembe helyezte is azt. Ez a megközelítés gyakori DNN használati esetek, légifelvételek besorolása, a alkalmazni, és segítségével azonosíthatja a legújabb mintákat városi fejlesztés alatt.  A rendszerkép előre betanított osztályozási modell fogja használni. A modell előre betanított, a rendszer a [CIFAR 10-es adatkészlet](https://www.cs.toronto.edu/~kriz/cifar.html) és 10 000 visszatartott lemezképek lett alkalmazva.

Nincsenek három kulcsfontosságú feladatokat a fejlett analitikai forgatókönyvet:

1. Hozzon létre egy Azure HDInsight Hadoop-fürtöt egy Apache Spark 2.1.0 terjesztési. 
2. Egy Azure HDInsight Spark-fürt összes csomópontjára telepítse a Microsoft Cognitive Toolkit egyéni parancsfájl futtatásához. 
3. Töltse fel egy előre elkészített Jupyter notebookot a HDInsight Spark-fürt egy betanított Microsoft Cognitive Toolkit deep learning-modell segítségével az Azure Blob Storage-fiókban a Spark Python API (PySpark) használatával a fájlok a alkalmazni. 

Ebben a példában a lefordított és elosztott Alex Krizhevsky, Vinod Nair és Geoffrey Hinton CIFAR 10-es lemezképet használja. A CIFAR-10 adatkészlet tartalmaz 60 000 32 x 32 10 egymást kölcsönösen kizáró osztályba tartozó képek színe:

![Képek](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Az adatkészlet a további részletekért lásd: Alex Krizhevsky [Learning több rétegek szolgáltatások nagyon kicsi rendszerképekből](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Az adatkészlet képzési 50 000 képkészlet és tesztelési 10 000 képkészlet lett felosztva. Első lett megadva egy Microsoft Cognitive Toolkit használatával a következő erdőtopológia-réteg – részletes konvolúciós maradék hálózati (ResNet) modell betanításához [ebben az oktatóanyagban](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) a Cognitive Toolkit GitHub-adattárból. A fennmaradó 10 000 rendszerképek tesztelése a modell pontosságát használták. Ez az elosztott számítástechnika honnan származnak szerepet: a feladat előzetesen feldolgozni, és a képek pontozási nincs magas párhuzamosítható. A mentett betanított modell aktuális használtuk:

* PySpark terjeszteni a képek és a fürt munkavégző csomópontok betanított modellt.
* Python előre a HDInsight Spark-fürt mindegyik csomópontján dolgozhassa fel.
* A cognitive Toolkit betölteni a modell és a pontszám a Előfeldolgozott lemezképeket minden egyes csomóponton.
* A PySpark-szkript futtatásához a Jupyter notebookok összesíti az eredményeket, és használja [Matplotlib](https://matplotlib.org/) megjelenítése a modell teljesítményét.

A teljes előfeldolgozása/pontozás 10 000 rendszerképet tart a kevesebb mint egy perc 4 feldolgozó csomóponttal rendelkező fürt. A modell felel meg pontosan előrejelzi a címkék ~ 9,100 (91 %) lemezképek. Egy keveredési mátrixot a leggyakrabban használt besorolási hibák mutatja be. Például a mátrix kutyák macskák, és ez fordítva is igaz mislabeling történik, hogy látható-e nagyobb gyakran más címkét párokhoz.

![Results (Eredmények)](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Próbálja ki!

Hajtsa végre a [ebben az oktatóanyagban](../spark/apache-spark-microsoft-cognitive-toolkit.md) megvalósításához a megoldás-végpontok: egy HDInsight Spark-fürt beállítása, a Cognitive Toolkit telepítése és futtatása a Jupyter Notebookot, hogy 10 000 CIFAR lemezképek pontszámmodell.

## <a name="next-steps"></a>További lépések

Hive-val és az Azure Machine Learning

* [Hive és az Azure Machine Learning-végpont](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Az 1 TB-os adatkészlet egy Azure HDInsight Hadoop-fürt használatával](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark- és MLLib

* [Gépi tanulás a Spark on HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Cognitive Toolkit és egyéb

* [Képek besorolása, a tensorflow-hoz és a Cognitive Toolkit használatával az Azure HDInsight Spark zavaróan párhuzamos](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Az adatelemzés az Azure virtuális gép](../../machine-learning/data-science-virtual-machine/overview.md)
* [Az Azure HDInsight H2O.ai bemutatása](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
