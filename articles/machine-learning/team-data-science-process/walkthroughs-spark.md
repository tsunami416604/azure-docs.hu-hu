---
title: Elemzés a HDInsight Sparkon pyspark, scala – csapatadat-elemzési folyamat
description: Példák a Team Data Science Process, amely végigvezeti a PySpark és a Scala egy Azure HDInsight Spark használatával.
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
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864145"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>A HDInsight Spark adatelemzési forgatókönyvei a PySpark és a Scala használatával az Azure-ban

Ezek a forgatókönyvek a PySpark és a Scala egy Azure Spark-fürtön prediktív elemzéseket használ. A csapatadat-elemzési folyamatban ismertetett lépéseket követik. A csapatadat-elemzési folyamat áttekintését az [Adatelemzési folyamat című](overview.md)témakörben találja. A Spark HDInsight-on című [témakörben olvashat: Bevezetés a Spark a HDInsight-on című témakörbe.](../../hdinsight/spark/apache-spark-overview.md)

További adatelemzési forgatókönyvek, amelyek végrehajtják a csapat adatelemzési folyamat az általuk használt **platform** szerint vannak csoportosítva. Lásd: [A csapatadat-elemzési folyamatot végrehajtó forgatókönyvek](walkthroughs.md) a példák részletezéséhez.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Taxitippek előrejelzése a PySpark használatával az Azure Sparkon

A New York-i taxiadatok használatával a [Spark használata az Azure HDInsight-forgatókönyv](spark-overview.md) előre jelzi, hogy egy tipp kifizetésre kerül-e, és a várható összegek tartománya. Ez a példa a team data science folyamat egy forgatókönyv egy [Azure HDInsight Spark-fürt](https://azure.microsoft.com/services/hdinsight/) használatával tárolja, fedezze fel és szolgáltatás mérnök adatokat a nyilvánosan elérhető NYC taxi út és a viteldíj adatkészlet. Ez az áttekintő témakör egy HDInsight Spark-fürtöt és jupyter PySpark-jegyzetfüzeteket használ. Ezek a jegyzetfüzetek bemutatják, hogyan fedezheti fel az adatokat, majd hogyan hozhat létre és használható fel modelleket. A speciális adatfeltárási és modellezési jegyzetfüzet bemutatja, hogyan lehet keresztérvényesítést, hiperparaméter-söprést és modellértékelést.

### <a name="data-exploration-and-modeling-with-spark"></a>Adatfeltárás és modellezés a Sparkkal 
Fedezze fel az adatkészletet, és hozza létre, pontozás, és értékelje ki a gépi tanulási modellek segítségével a [bináris osztályozási és regressziós modellek létrehozása az adatok a Spark MLlib eszközkészlet témakör.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Modellfelhasználás
A témakörben létrehozott besorolási és regressziós modellek pontozásáról a [Pontszám és a Spark által készített gépi tanulási modellek kiértékelése](spark-model-consumption.md)című témakörben olvashat.

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kereszt-ellenőrzés és hiperparaméter söprés
Lásd: [Speciális adatok feltárása és modellezése a Spark segítségével,](spark-advanced-data-exploration-modeling.md) hogyan modellek betanítása kereszt-ellenőrzés és a hyper-paraméter seprés.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Taxitippek előrejelzése a Scala használatával az Azure Sparkon

A [Scala használata a Spark az Azure-ban](scala-walkthrough.md) forgatókönyv előre jelzi, hogy egy tipp fizetett, és a várhatóan fizetendő összegek tartománya. Bemutatja, hogyan használhatja a Scala felügyelt gépi tanulási feladatok a Spark machine learning-kódtár (MLlib) és sparkML-csomagok egy Azure HDInsight Spark-fürtön. Végigvezeti az [adatelemzési folyamatot](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)alkotó feladatokon: adatbetöltés és feltárás, vizualizáció, szolgáltatástervezés, modellezés és modellfelhasználás. A modellek közé tartozik a logisztikai és lineáris regresszió, véletlenszerű erdők, és a gradiens kiemelt fák.


## <a name="next-steps"></a>További lépések

A csapatadat-elemzési folyamat áttekintését a [Csapat adatelemzési folyamatának áttekintése című témakörben találja.](overview.md)

A csapatadat-elemzési folyamat életciklusának témakörét a [Csapatadat-elemzési folyamat életciklusa című témakörben közöld.](lifecycle.md) Ez az életciklus ismerteti azokat a lépéseket, az elejétől a végéig, amelyeket a projektek általában a végrehajtáskor követnek. 

