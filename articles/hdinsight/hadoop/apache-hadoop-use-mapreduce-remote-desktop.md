---
title: MapReduce és a távoli asztal hadooppal a Hdinsightban - Azure |} Microsoft Docs
description: 'Útmutató: a távoli asztal használatával csatlakozhat a HDInsight Hadoop, és a MapReduce-feladatok futtatása.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb59a510085a9f08e63f17cec1de2044905f914a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398725"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>A távoli asztal hdinsight Hadoop MapReduce használata
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ebből a cikkből megtudhatja a távoli asztal használatával kapcsolódik a Hadoop on HDInsight-fürt és a Hadoop-paranccsal futtassa a MapReduce-feladatok.

> [!IMPORTANT]
> Távoli asztali Windows-alapú HDInsight-fürtök csak érhető el. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4-es vagy nagyobb, lásd: [az SSH használata MapReduce](apache-hadoop-use-mapreduce-ssh.md) információk a HDInsight-fürthöz csatlakozik, és a MapReduce-feladatok futtatása.

## <a id="prereq"></a>Előfeltételek
Ebben a cikkben szereplő lépések elvégzéséhez a következőkre lesz szüksége:

* (A HDInsight Hadoop) a Windows-alapú HDInsight-fürt
* Windows 10, Windows 8 vagy Windows 7 rendszert futtató ügyfélszámítógép

## <a id="connect"></a>Csatlakozzon a távoli asztal
A távoli asztal engedélyezése a HDInsight-fürthöz, majd csatlakozni a következő utasításokat követve [csatlakozás RDP Funkciót használnak a HDInsight-fürtök](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>A Hadoop paranccsal
Az asztalon a HDInsight-fürthöz csatlakozik, az az alábbi lépések segítségével a Hadoop-paranccsal futtassa a MapReduce feladatot:

1. A HDInsight asztalról indítsa el a **Hadoop parancssori**. Ekkor megnyílik egy új, a parancssort a **c:\apps\dist\hadoop-&lt;verziószám >** directory.

   > [!NOTE]
   > A verziószám változik Hadoop frissül. A **HADOOP_HOME** környezeti változó segítségével található az elérési út. Például `cd %HADOOP_HOME%` könyvtárak módosítja a Hadoop-címtárban, anélkül, hogy ismeri a verziószámot.
   >
   >
2. Használatához a **Hadoop** parancs egy példa MapReduce feladatot futtatni, használja a következő parancsot:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Ekkor elindul a **wordcount** osztályt, amely tartalmazza a **hadoop-mapreduce-examples.jar** fájl az aktuális könyvtárban található. Bemeneti, használja a **wasb://example/data/gutenberg/davinci.txt** dokumentum, és a kimeneti tárolódik: **wasb: / / / Példa/data/WordCountOutput**.

   > [!NOTE]
   > a MapReduce feladatot és a példaadatokat kapcsolatos további információkért lásd: <a href="hdinsight-use-mapreduce.md">MapReduce használata a HDInsight Hadoop a</a>.
   >
   >
3. A feladat részletei bocsát ki, a feldolgozása, és olyan információkat ad vissza a következőhöz hasonló a feladatot:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Ha a folyamat befejeződik, az alábbi parancs segítségével a kimeneti fájl helye: **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    Ez megjelenjen-e két fájlt **_SUCCESS** és **rész-r-00000**. A **rész-r-00000** fájl tartalmazza a kimenet ehhez a feladathoz.

   > [!NOTE]
   > Bizonyos MapReduce-feladatok az eredmények lehet, hogy e osztani több **rész-r-###** fájlokat. Ha igen, használja a ### utótag fájlok sorrendjét.
   >
   >
5. A kimenet megtekintéséhez használja a következő parancsot:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Ez megjeleníti a szavakat, amelyek szerepelnek a **wasb://example/data/gutenberg/davinci.txt** fájl hányszor minden szó történt. A következő egy példa a fájlban szereplő adatok:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Summary (Összefoglalás)
Látható, a Hadoop parancs MapReduce-feladatok futtatása a HDInsight-fürtöt, és nézze meg a feladat kimenetére egyszerű módszert biztosít.

## <a id="nextsteps"></a>Következő lépések
Általános információk a hdinsight MapReduce-feladatok:

* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
