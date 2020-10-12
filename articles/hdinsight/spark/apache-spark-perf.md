---
title: A Spark-feladatok optimalizálása a teljesítmény érdekében – Azure HDInsight
description: Az Azure HDInsight Apache Spark-fürtök legjobb teljesítményére vonatkozó általános stratégiák megjelenítése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperfq1
ms.openlocfilehash: fbd0da43e79ee2c27f654f9bd07614e08c12fd30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756928"
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
