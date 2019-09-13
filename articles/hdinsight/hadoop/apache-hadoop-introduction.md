---
title: Mi a Apache Hadoop Technology stack? – Azure HDInsight
description: Bevezetés a HDInsight, valamint a Apache Hadoop Technology stackbe és összetevőkbe.
keywords: azure hadoop, hadoop azure, hadoop bevezetés, hadoop bemutatása, hadoop-technológiák, bevezetés a hadoop szolgáltatásba, a hadoop bemutatása, mi az a hadoop-fürt, mi a hadoop-fürt, mire való a hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: overview
ms.date: 08/15/2019
ms.openlocfilehash: 859e830483ac959f6f55ba27b5657779ac16d2fb
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934141"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Mi Apache Hadoop az Azure HDInsight?

Az [Apache Hadoop](https://hadoop.apache.org/) volt a big data jellegű adatkészletek fürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. A Hadoop-ökoszisztéma olyan kapcsolódó szoftvereket és segédprogramokat tartalmaz, mint például az Apache Hive, az Apache HBase, a Spark, a Kafka és sok más.

Az Azure HDInsight egy teljes körűen felügyelt, teljes spektrumú, nyílt forráskódú elemzési szolgáltatás a felhőben a vállalatok számára. Az Azure HDInsight-beli Apache Hadoop-fürt típusa lehetővé teszi a HDFS, a fonal-erőforrások kezelését, valamint egy egyszerű MapReduce programozási modellt, amellyel párhuzamosan dolgozhat és elemezheti a Batch-adatokat.

A HDInsight elérhető Hadoop Technology stack-összetevők megtekintéséhez tekintse meg a [HDInsight által elérhető összetevőket és verziókat](../hdinsight-component-versioning.md). További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a id="whatis"></a>Mi az a MapReduce?

Apache Hadoop MapReduce olyan szoftver-keretrendszer, amely nagy mennyiségű adat feldolgozására szolgáló feladatokat ír. A bemeneti adatok felosztása független adattömbökbe történik. Minden adathalmaz feldolgozása párhuzamosan történik a fürt csomópontjai között. A MapReduce-feladatok két függvényből állnak:

* **Mapper**: A bemeneti adatokat használja fel, elemzi (általában szűrési és rendezési műveletekkel), és rekordok (kulcs-érték párok) bocsát ki.

* **Szűkítő**: Felhasználja a Mapper által kibocsátott rekordok, és olyan összegző műveletet hajt végre, amely létrehoz egy kisebb, kombinált eredményt a leképezési adatokból.

A következő ábrán látható egy alapszintű Word Count MapReduce-feladat példája:   

 ![HDI.WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Ennek a feladatoknak a kimenete annak a száma, hogy az egyes szavak hányszor fordultak elő a szövegben.

* A Mapper a bemeneti szöveg minden sorát bemenetként veszi át, és megszakítja a szavakat. Egy kulcs/érték párokat bocsát ki, minden alkalommal, amikor egy szó bekövetkezik a szót, majd egy 1. A kimenet rendezése megtörténik, mielőtt elküldi azt a redukáló felé.
* A szűkítő összesíti ezeket az egyes szavakat, és egyetlen kulcs/érték párokat bocsát ki, amely a szót az előfordulások összege követve tartalmazza.

A MapReduce több nyelven is megvalósítható. A Java a leggyakoribb implementáció, és a jelen dokumentumban bemutató célokra szolgál.

## <a name="development-languages"></a>Fejlesztési nyelvek

A Java és a Java virtuális gép alapú nyelvek vagy keretrendszerek közvetlenül MapReduce-feladatokként futtathatók. A dokumentumban használt példa egy Java MapReduce alkalmazás. A nem Java nyelveken (például C#, Python vagy önálló végrehajtható fájlok) a **Hadoop streaminget**kell használniuk.

A Hadoop stream a leképező és a redukáló adatátvitelt az STDIN és az STDOUT fölé továbbítja. A leképező és a redukáló adatokat tartalmazó sort olvas az STDIN-ből, és a kimenetet az STDOUT értékre írja. A leképező és a szűkítő által beolvasott vagy kibocsátott soroknak a TAB karakterrel tagolt kulcs/érték párok formátumában kell szerepelniük:

    [key]/t[value]

További információ: [Hadoop streaming](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Az Hadoop streaming HDInsight-vel való használatát bemutató példákért tekintse meg a következő dokumentumot:

* [MapReduce C# -feladatok fejlesztése](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>További lépések

* [Apache Hadoop-fürt létrehozása a HDInsight-ben](apache-hadoop-linux-create-cluster-get-started-portal.md)
