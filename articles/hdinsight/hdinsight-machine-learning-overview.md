---
title: A gépi tanulás áttekintése – Azure HDInsight
description: Az Azure HDInsight-ban a fürtök big data-alapú gépi tanulási lehetőségeinek áttekintése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931680"
---
# <a name="machine-learning-on-hdinsight"></a>Gépi tanulás a HDInsightban

A HDInsight lehetővé teszi a big data-adatokkal történő gépi tanulást, és lehetővé teszi, hogy értékes betekintést nyerjen a strukturált, strukturálatlan és gyorsan változó adatok nagy mennyiségű (petabájt, vagy akár exabájt) értékbetekintéséből. A HDInsight számos gépi tanulási lehetőséget kínál: SparkML és Apache Spark MLlib, R, Apache Hive és a Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML és MLlib

[A HDInsight Spark](spark/apache-spark-overview.md) az [Apache Spark](https://spark.apache.org/)Azure-ban tárolt ajánlata, amely egy egységes, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-elemzések növelése érdekében. A Spark feldolgozómotor a sebesség, a könnyű használat és a kifinomult analitika érdekében készült. A Spark memórián belüli elosztott számítási képességei jó választássá teszik a gépi tanulásban és grafikonszámításokban használt iteratív algoritmusok számára. Két méretezhető gépi tanulási kódtárak, amelyek algoritmikus modellezési képességek et az elosztott környezetben: MLlib és SparkML. Az MLlib az RDD-kre épülő eredeti API-t tartalmazza. A SparkML egy újabb csomag, amely egy magasabb szintű API-t biztosít a DataFrames-en a gépi tanulási folyamatok létrehozásához. A SparkML még nem támogatja az MLlib összes funkcióját, de az MLlib-et a Spark szabványos gépi tanulási könyvtáraként helyettesíti.

Az Apache Spark Microsoft Machine Learning-könyvtára az [MMLSpark](https://github.com/Azure/mmlspark). Ez a könyvtár úgy lett kialakítva, hogy az adatszakértők hatékonyabbak legyenek a Sparkon, növeljék a kísérletezés irását, és kihasználják az élvonalbeli gépi tanulási technikákat, beleértve a mély tanulást is, nagyon nagy adatkészleteken. Az MMLSpark egy réteget biztosít a SparkML alacsony szintű API-inak tetején, amikor méretezhető ML-modelleket hoz létre, például a karakterláncok indexelése, az adatok gépi tanulási algoritmusok által elvárt elrendezésbe kényszerítése és a szolgáltatásvektorok összeállítása. Az MMLSpark-könyvtár leegyszerűsíti ezeket és más gyakori feladatokat modellek létrehozásához a PySpark-ban.

## <a name="r"></a>R

[R](https://www.r-project.org/) jelenleg a legnépszerűbb statisztikai programozási nyelv a világon. Ez egy nyílt forráskódú adatvizualizációs eszköz, amelynek közössége több mint 2,5 millió felhasználóval rendelkezik, és egyre növekszik. Virágzó felhasználói bázisával és több mint 8000 közreműködő csomagjával az R valószínű választás sok olyan vállalat számára, akiknek gépi tanulásra van szükségük. Létrehozhat egy HDInsight-fürtöt az ML-szolgáltatásokkal, amelyek készen állnak a tömeges adatkészletekkel és modellekkel való használatra. Ez a funkció az adatszakértők és a statisztikusok számára egy ismerős R-felületet biztosít, amely igény szerint skálázható a HDInsight-on keresztül, a fürt beállításának és karbantartásának többletterhelése nélkül.

![Képzés előrejelzés R szerver](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való csatlakozáshoz és az R-parancsfájlok futtatásához.  R-parancsfájlokat is futtathat a fürt csomópontjain a ScaleR Hadoop-térképcsökkentési vagy Spark-számítási környezeteinek használatával.

A HDInsight-alapú ML-szolgáltatásokkal a Sparkkal párhuzamosíthatja a fürt csomópontjai közötti betanítást egy Spark számítási környezet használatával. Az R-parancsfájlok futtathatók közvetlenül a peremhálózati csomóponton, szükség szerint párhuzamosan az összes rendelkezésre álló mag használatával. Másik lehetőségként futtathatja a kódot a peremhálózati csomópontról, hogy indítsa el a fürt összes csomópontja között elosztott feldolgozást. Ml-szolgáltatások hdinsight sparkkal is lehetővé teszi a párhuzamosító funkciók nyílt forráskódú R-csomagok, ha szükséges.

## <a name="azure-machine-learning-and-apache-hive"></a>Az Azure Machine Learning és az Apache Hive

Az Azure Machine Learning eszközöket biztosít a prediktív elemzések modellezéséhez, és egy teljes körűen felügyelt szolgáltatást, amelysegítségével a prediktív modelleket használatra kész webszolgáltatásként telepítheti. Az Azure Machine Learning egy teljes körű prediktív elemzési megoldás a felhőben, amely segítségével hozhat létre, tesztelhet, működőképessé és kezelhet prediktív modelleket. Válasszon egy nagy algoritmuskönyvtárból, használjon webalapú stúdiót modellek létrehozásához, és egyszerűen telepítheti a modellt webszolgáltatásként.

![A Microsoft Azure gépi tanulás áttekintése](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

A HDInsight Hadoop-fürtben lévő adatokhoz használjon szolgáltatásokat [Hive-lekérdezések](../machine-learning/team-data-science-process/create-features-hive.md)használatával. *A szolgáltatástervezés* megpróbálja növelni a tanulási algoritmusok prediktív erejét olyan nyers adatokból származó funkciók létrehozásával, amelyek megkönnyítik a tanulási folyamatot. Futtathatja a HiveQL-lekérdezéseket az Azure Machine Learning Studio -ból (klasszikus), és hozzáférhet a Hive-ban feldolgozott és a blob storage-ban tárolt adatokhoz az [Adatok importálása modul](../machine-learning/studio/import-data.md)használatával.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[A mély tanulás](https://www.microsoft.com/en-us/research/group/dltc/) a gépi tanulás egyik ága, amely neurális hálózatokat használ, amelyeket az emberi agy biológiai folyamatai ihlettek. Sok kutató úgy látja, hogy a mély tanulás ígéretes megközelítés a mesterséges intelligencia fokozására. A mélytanulás ra ta-

Annak érdekében, hogy segítse a saját munkáját a mélytanulásban, a Microsoft kifejlesztette az ingyenes, könnyen használható, nyílt forráskódú [Microsoft Cognitive Toolkit-ot.](https://www.microsoft.com/en-us/cognitive-toolkit/) Ezt az eszköztárat a Microsoft-termékek széles skálája használja, a vállalatok világszerte, amelyeknek nagy léptékű mélytanulást kell telepíteniük, valamint a legújabb algoritmusok és technikák iránt érdeklődő diákok.

## <a name="see-also"></a>Lásd még

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Filmjavaslatok készítése az Apache Mahout segítségével](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive és Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Az Apache Hive és az Azure Machine Learning végpontok között](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Gépi tanulás az Apache Sparksegítségével a HDInsighton](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Mélytanulási források

* [A Microsoft Cognitive Toolkit deep learning modelljének használata az Azure HDInsight Spark-fürtjével](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Deep Learning és AI keretrendszerek az adatelemzési virtuális gép (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
