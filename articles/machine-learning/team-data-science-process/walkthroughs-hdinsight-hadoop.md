---
title: Analytics az Azure HDInsight Hadoop használatával Hive - Team Data Science Process
description: Példák a csapat adatelemzési folyamat, amely végigvezeti a Hive az Azure HDInsight Hadoop prediktív elemzések használatával.
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
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864162"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight-Hadoop adatelemzési forgatókönyvek a Hive használatával az Azure-ban 

Ezek a forgatókönyvek a Hive egy HDInsight Hadoop-fürttel prediktív elemzéssel. A csapatadat-elemzési folyamatban ismertetett lépéseket követik. A csapatadat-elemzési folyamat áttekintését az [Adatelemzési folyamat című](overview.md)témakörben találja. Az Azure HDInsight bemutatása az [Azure HDInsight, a Hadoop-technológiai verem és a Hadoop-fürtök bemutatása című témakörben látható.](../../hdinsight/hadoop/apache-hadoop-introduction.md)

További adatelemzési forgatókönyvek, amelyek végrehajtják a csapat adatelemzési folyamat az általuk használt **platform** szerint vannak csoportosítva. Lásd: [A csapatadat-elemzési folyamatot végrehajtó forgatókönyvek](walkthroughs.md) a példák részletezéséhez.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Taxitippek előrejelzése a Hive használatával a HDInsight Hadoop segítségével

A [HDInsight Hadoop-fürtök használata](hive-walkthrough.md) forgatókönyv a New York-i taxik adatait használja a következők előrejelzéséhez: 

- A tipp kifizetése 
- A tippösszegek elosztása

A forgatókönyv a Hive használatával valósul meg egy [Azure HDInsight Hadoop-fürttel.](https://azure.microsoft.com/services/hdinsight/) Megtudhatja, hogyan tárolhatja, tárhatja fel és funkciók mérnök adatok at nyilvánosan elérhető NYC taxi út és a viteldíj adatkészlet. Az Azure Machine Learning segítségével is hozhat létre és helyezhet üzembe modelleket.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>A hive használatával a HDInsight Hadoop segítségével a hirdetéskattintások előrejelzése

Az [Azure HDInsight Hadoop-fürtök használata egy 1 TB-os](hive-criteo-walkthrough.md) adatkészlet-forgatókönyvben egy nyilvánosan elérhető [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) kattintási adatkészletet használ annak előrejelzéséhez, hogy egy tipp kifizetésre kerül-e, és a várható összegeket. A forgatókönyv a Hive használatával valósítható meg egy [Azure HDInsight Hadoop-fürttel](https://azure.microsoft.com/services/hdinsight/) a mintaadatok tárolásához, feltárásához, szolgáltatásmérnökéhez és lefelé történő tárolásához. Az Azure Machine Learning segítségével hoz létre, betanítása és pontozása egy bináris besorolási modell előrejelzi, hogy a felhasználó rákattint egy hirdetésre. A forgatókönyv arra a következtetésre jut, amely bemutatja, hogyan teheti közzé az egyik ilyen modellt webszolgáltatásként.


## <a name="next-steps"></a>További lépések

A csapatadat-elemzési folyamatot alkotó legfontosabb összetevőkről a [Csapat adatelemzési folyamatának áttekintése című témakörben olvashat.](overview.md)

A csapatadat-elemzési folyamat életciklusának, amely az adatelemzési projektek strukturálásához használható, a [Csapat adatelemzési folyamat életciklusa](lifecycle.md)című témakörben ismer. Az életciklus ismerteti azokat a lépéseket, az elejétől a végéig, amelyeket a projektek általában a végrehajtáskor követnek. 

