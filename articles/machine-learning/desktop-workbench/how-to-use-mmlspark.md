---
title: MMLSpark Machine Learning használatával |} A Microsoft Docs
description: Útmutató az Azure Machine Learning MMLSpark könyvtár használata.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 654b2559518cd52978153310fbb1e89a91838a8a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645780"
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Microsoft Machine Learning Library for Apache Spark használata

## <a name="introduction"></a>Bevezetés

[Microsoft Machine Learning Library for Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) biztosít az eszközök, amelyekkel könnyedén létre skálázható gépi tanulási modellek nagy adatkészletek esetében. Könnyen használható folyamatok integrációja tartalmazza a [Microsoft Cognitive Toolkit ](https://github.com/Microsoft/CNTK) és [OpenCV](http://www.opencv.org/), ami lehetővé teszi: 
 * Bejövő és képadatok előzetes feldolgozása
 * Szabadkézi képek és szöveg előre betanított deep learning-modellek használatával
 * Betanítása és implicit featurization használatával besorolási és regressziós modellek pontozása.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató elvégezhető, kell tennie:
- [Az Azure Machine Learning Workbench telepítése](../service/quickstart-installation.md)
- [Az Azure HDInsight Spark-fürt beállítása](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>A kísérlet futtatásához a Docker-tárolóban

Az Azure Machine Learning Workbench használata MMLSpark futtatásakor kísérletek a Docker-tárolóban, helyi vagy távoli virtuális gép van konfigurálva. Ezzel a funkcióval egyszerűen hibakeresése és kísérletezzen a PySpark modelljeit, azokat egy fürtön a skálázási futtatása előtt. 

Első lépésként egy példát használva hozzon létre egy új projektet, és válassza a "MMLSpark a felnőtt népszámlálási" gyűjtemény példához. Válassza ki a "Docker", a számítási környezetet a projekt irányítópultján, és válassza a "Futtatás". Az Azure Machine Learning Workbench a PySpark program futtatása a Docker-tárolót hoz létre, és a program végrehajtja.

A Futtatás befejeződése után megtekintheti az eredményeket az Azure Machine Learning Workbench futtatási előzmények megtekintése.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Telepítse az MMLSpark az Azure HDInsight Spark-fürtön.

Ez és a következő lépés végrehajtásához először szüksége [hozzon létre egy Azure HDInsight Spark-fürt](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Alapértelmezés szerint az Azure Machine Learning Workbench telepíti MMLSpark a fürtön a kísérlet futtatásakor. Ezt a viselkedést vezérlő és a egy nevű fájl szerkesztésével más Spark-csomagok telepítéséhez _aml_config/spark_dependencies.yml_ a projektmappába.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

MMLSpark is telepítheti a közvetlenül a HDInsight Spark-fürtöt hozhat [parancsfájlművelet](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Compute célként az Azure HDInsight Spark-fürt beállítása

Nyissa meg a parancssori felület ablakában az Azure Machine Learning Workbench a "File" menüben, majd kattintson az "a parancssor megnyitása"

Parancssori felület ablakában futtassa a következő parancsokat:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Most már a fürt érhető el, a számítási célt a projekthez.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Futtatási kísérletet az Azure HDInsight Spark-fürtön.

Lépjen vissza a projekt irányítópultján, például "MMLSpark a felnőtt népszámlálási". Válassza ki a fürtöt, a számítási célnak, és kattintson az OK gombra.

Az Azure Machine Learning Workbench küldi el a spark-feladat a fürthöz. Nyomon követheti, és tekintse meg az eredményeket a futtatási előzmények megtekintése.

## <a name="next-steps"></a>További lépések
MMLSpark könyvtár és példák kapcsolatos információkért lásd: [MMLSpark GitHub-adattár](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark- és Spark® olyan bejegyzett védjegye vagy védjegye az Amerikai Egyesült Államokban és/vagy más országokban, az Apache Software Foundation.*
