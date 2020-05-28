---
title: A Spark-feladatok optimalizálása a teljesítmény érdekében – Azure HDInsight
description: Az Azure HDInsight Apache Spark-fürtök legjobb teljesítményére vonatkozó általános stratégiák megjelenítése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117966"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark feladatok optimalizálása a HDInsight-ben

Ez a cikk áttekintést nyújt a Apache Spark feladatok Azure HDInsight való optimalizálásához szükséges stratégiákról.

## <a name="overview"></a>Áttekintés

A Apache Spark feladatok teljesítménye több tényezőtől függ. Ezek a teljesítménnyel kapcsolatos tényezők: az adatok tárolásának módja, a fürt konfigurálásának módja, valamint az adatok feldolgozásakor használt műveletek.

Az esetlegesen felmerülő gyakori kihívások: a memória korlátozásai a nem megfelelő méretű végrehajtók, a hosszan futó műveletek és a Descartes műveletet eredményező feladatok miatt.

Számos optimalizálási lehetőség is rendelkezésre áll, amelyek segítségével elháríthatja ezeket a kihívásokat, például a gyorsítótárazást, és lehetővé teszi az adatok eldöntését.

A következő cikkekben a Spark-optimalizálás különböző szempontjaival kapcsolatos információkat talál.

* [Az adattároló optimalizálása Apache Spark](optimize-data-storage.md)
* [Az adatfeldolgozás optimalizálása Apache Spark](optimize-data-processing.md)
* [Memória használatának optimalizálása Apache Spark](optimize-memory-usage.md)
* [A HDInsight-fürt konfigurációjának optimalizálása Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>További lépések

* [Azure HDInsighton futó Apache Spark-feladatok hibakeresése](apache-spark-job-debugging.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)
