---
title: Nyílt forráskódú szoftvertámogatás az Azure HDInsightban
description: A Microsoft Azure általános szintű támogatást nyújt a nyílt forráskódú technológiákhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: f93be73af4bbbd159ffc01804617892251d96347
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772170"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Nyílt forráskódú szoftvertámogatás az Azure HDInsightban

A Microsoft Azure HDInsight szolgáltatás az Apache Hadoop körül kialakított nyílt forráskódú technológiák környezetét használja. A Microsoft Azure általános szintű támogatást nyújt a nyílt forráskódú technológiákhoz. További információt az Azure-támogatás – gyakori kérdések **– Támogatási hatókör** című [szakaszában talál.](https://azure.microsoft.com/support/faq/) A HDInsight szolgáltatás további támogatási szintet biztosít a beépített összetevők számára.

## <a name="components"></a>Összetevők

A HDInsight szolgáltatásban kétféle nyílt forráskódú összetevő érhető el:

### <a name="built-in-components"></a>Beépített alkatrészek

Ezek az összetevők előtelepítettHDInsight-fürtökön vannak telepítve, és a fürt alapvető funkcióit biztosítják. A következő összetevők tartoznak ebbe a kategóriába:

* [Apache Hadoop FONAL](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Erőforrás-kezelő.
* A Hive lekérdezési nyelv [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

A fürtösszetevők teljes listája a [Mik azok az Apache Hadoop-összetevők és -verziók, amelyek a HDInsight segítségével érhetők el?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Egyéni összetevők

A fürt felhasználójaként telepítheti vagy használhatja a munkaterhelésben a közösségben elérhető vagy Ön által létrehozott bármely összetevőt.

> [!WARNING]  
> A HDInsight-fürthöz mellékelt összetevők teljes mértékben támogatottak. A Microsoft támogatási szolgálata segít az összetevőkkel kapcsolatos problémák elkülönítéséhez és megoldásához.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további elhárításához. Előfordulhat, hogy a Microsoft támogatási szolgálata meg tudja oldani a problémát. Vagy megkérhetik Önt, hogy vegyen részt a nyílt forráskódú technológiák rendelkezésre álló csatornáiban, ahol mély szakértelem rejlehet az adott technológiával kapcsolatosan. Számos közösségi oldal használható. Ilyenek például a HDInsight és a Stack Overflow [MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) [fóruma.](https://stackoverflow.com)
>
> Apache projektek is projekt oldalak az [Apache honlapján](https://apache.org). Erre példa a [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Összetevő használata

A HDInsight szolgáltatás számos lehetőséget kínál az egyéni összetevők használatára. Az összetevők fürtre való alkalmazásának vagy telepítésétől függetlenül ugyanaz a támogatási szint érvényes. Az alábbi táblázat az egyéni összetevők HDInsight-fürtökön való használatának leggyakoribb módjait ismerteti:

|Használat |Leírás |
|---|---|
|Állásbeküldése|A Hadoop vagy más típusú feladatok, amelyek egyéni összetevőket hajtanak végre vagy használnak, elküldhetők a fürtnek.|
|Fürt testreszabása|A fürt létrehozása során további beállításokat és egyéni összetevőket adhat meg, amelyek a fürtcsomópontokon vannak telepítve.|
|Példák|A népszerű egyéni összetevők esetében a Microsoft és mások mintákat nyújthatnak arról, hogyan használhatók ezek az összetevők a HDInsight-fürtökön. Ezek a minták támogatás nélkül állnak rendelkezésre.|

## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight-fürtök testreszabása parancsfájlműveletek használatával](./hdinsight-hadoop-customize-cluster-linux.md)
* [Parancsfájl-műveletparancsfájlok fejlesztése a HDInsighthoz](hdinsight-hadoop-script-actions-linux.md)
* [Python-környezet biztonságos kezelése az Azure HDInsightban szkriptműveletekkel](./spark/apache-spark-python-package-installation.md)
