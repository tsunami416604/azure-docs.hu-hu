---
title: Mik azok az Apache Hadoop és a MapReduce – Azure HDInsight
description: Bevezetés a HDInsight, valamint az Apache Hadoop technológia verem és összetevők.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 7e8dd69b7c58e090c30ea1aa59feddab610dd3c5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244875"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Mi az Apache Hadoop az Azure HDInsightban?

Az [Apache Hadoop](https://hadoop.apache.org/) volt a big data jellegű adatkészletek fürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. A Hadoop ökoszisztéma kapcsolódó szoftvereket és segédprogramokat tartalmaz, beleértve az Apache Hive-t, az Apache HBase-t, a Sparkot, a Kafkát és még sok mást.

Az Azure HDInsight egy teljes körűen felügyelt, teljes spektrumú, nyílt forráskódú elemzési szolgáltatás a felhőben a vállalatok számára. Az Apache Hadoop fürttípusa az Azure HDInsightban lehetővé teszi a HDFS, a YARN erőforrás-kezelés és egy egyszerű MapReduce programozási modell használatát a kötegadatok párhuzamos feldolgozásához és elemzéséhez.

A HDInsighton elérhető Hadoop-technológiák összetevőinek megtekintése: [A HDInsighttal elérhető összetevők és verziók](../hdinsight-component-versioning.md). További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a name="what-is-mapreduce"></a>Mi az a MapReduce

Az Apache Hadoop MapReduce egy olyan szoftverkeretrendszer, amely hatalmas mennyiségű adatot feldolgozó feladatok írására szolgál. A bemeneti adatok független adattömbökre vannak felosztva. Minden adattömb feldolgozása párhuzamosan a fürt csomópontjai között. A MapReduce feladat két függvényből áll:

* **Mapper**: Felhasználja a bemeneti adatokat, elemzi azokat (általában szűrési és rendezési műveletekkel), és buples -t bocsát ki (kulcs-érték párok)

* **Szűkítő**: A Leképező által kibocsátott tuple-k at fogyasztja, és olyan összegző műveletet hajt végre, amely kisebb, kombinált eredményt hoz létre a leképező adatokból

A MapReduce feladat példa alapvető szószámát az alábbi ábra szemlélteti:

 ![Hdi. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

A feladat kimenete annak a száma, hogy az egyes szavak hányszor történtek a szövegben.

* A leképező a bemeneti szöveg minden sorát bemenetként veszi fel, és szavakba foglalja. Kulcs/érték párt bocsát ki minden alkalommal, amikor a szó egy szavát követi egy 1. A kimenet rendezése előtt elküldi azt a szűkítő.
* A szűkítő összegzi ezeket az egyes számokat minden egyes szóhoz, és egyetlen kulcsot/értéket bocsát ki, amely tartalmazza a szót, majd az előfordulások összegét.

A MapReduce különböző nyelveken valósítható meg. A Java a leggyakoribb implementáció, és ebben a dokumentumban szemléltetési célokra használják.

## <a name="development-languages"></a>Fejlesztési nyelvek

Java-n és a Java virtuális gépen alapuló nyelvek vagy keretrendszerek közvetlenül MapReduce feladatként futtathatók. A dokumentumban használt példa egy Java MapReduce alkalmazás. A nem Java nyelveknek, például a C#, python vagy önálló végrehajtható fájloknak **hadoop-streamelést kell használniuk.**

A Hadoop streaming kommunikál a leképezővel és a szűkítővel az STDIN és az STDOUT felett. A leképező és a szűkítő egyszerre olvassa be az adatokat az STDIN-ből, és írja a kimenetet az STDOUT-ba. A leképező és a szűkítő által leolvasott vagy kibocsátott soroknak kulcs/érték pár formátumúaknak kell lenniük, tabulátorkarakterrel elválasztva:

    [key]/t[value]

További információ: [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

A Hadoop streamelésének HDInsight-mal való használatára vonatkozó példákat az alábbi dokumentumtartalmazza:

* [C# MapReduce feladatok fejlesztése](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>További lépések

* [Apache Hadoop-fürt létrehozása a HDInsightban](apache-hadoop-linux-create-cluster-get-started-portal.md)
