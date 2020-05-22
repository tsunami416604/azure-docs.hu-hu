---
title: A Spark-feladatok optimalizálása a teljesítmény érdekében – Azure HDInsight
description: Az Azure HDInsight Apache Spark-fürtök legjobb teljesítményére vonatkozó általános stratégiák megjelenítése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780115"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark feladatok optimalizálása a HDInsight-ben

Ez a cikk áttekintést nyújt a Apache Spark feladatok Azure HDInsight való optimalizálásához szükséges stratégiákról.

## <a name="overview"></a>Áttekintés

A Apache Spark feladatok teljesítménye több tényezőtől függ. Ezek a teljesítménnyel kapcsolatos tényezők: az adatok tárolásának módja, a fürt konfigurálásának módja, valamint az adatok feldolgozásakor használt műveletek.

Az esetlegesen felmerülő gyakori kihívások közé tartoznak a memóriabeli megkötések, mivel a nem megfelelő méretű végrehajtók, a hosszan futó műveletek és a Descartes-as műveleteket eredményező tevékenységek.

Vannak olyan különböző stratégiák is, amelyek segítségével elháríthatja ezeket a kihívásokat, például a gyorsítótárazást, és lehetővé teszi az adatok eldöntését.

A következő cikkekben a Spark-optimalizálás különböző szempontjaival kapcsolatos gyakori kihívásokat és megoldásokat talál.

* [Adattárolás optimalizálása](optimize-data-storage.md)
* [Az adatfeldolgozás optimalizálása](optimize-data-processing.md)
* [Memóriahasználat optimalizálása](optimize-memory-usage.md)
* [Fürt konfigurációjának optimalizálása](optimize-cluster-configuration.md)

## <a name="next-steps"></a>További lépések

* [Azure HDInsighton futó Apache Spark-feladatok hibakeresése](apache-spark-job-debugging.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Az Apache Spark beállításainak konfigurálása](apache-spark-settings.md)
