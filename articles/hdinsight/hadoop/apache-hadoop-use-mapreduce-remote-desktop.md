---
title: A MapReduce és a távoli asztal a HDInsight - Azure Hadoop-keretrendszerrel
description: Ismerje meg, hogy a távoli asztal használatával csatlakozhat a HDInsight a Hadoop és a MapReduce-feladatok futtatása.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ee87bd5743307ea5b9ee3ca1258e8cd985a095c5
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012928"
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>A MapReduce használata a Hadooppal a HDInsight a távoli asztallal
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ebből a cikkből megismerheti, hogyan és a egy Hadoop HDInsight-fürtön a távoli asztal használatával csatlakozhat, és a Hadoop-paranccsal futtassa a MapReduce-feladatok lesz.

> [!IMPORTANT]
> A távoli asztal csak akkor használható a Windows-alapú HDInsight-fürtökön. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> A HDInsight 3.4 vagy újabb, lásd: [MapReduce használata az ssh-val](apache-hadoop-use-mapreduce-ssh.md) információ a HDInsight-fürthöz csatlakozik, és a MapReduce-feladatok futtatása.

## <a id="prereq"></a>Előfeltételek
A jelen cikkben ismertetett lépések elvégzéséhez a következőkre lesz szüksége:

* A Windows-alapú HDInsight (Hadoop on HDInsight)-fürt
* A Windows 10, Windows 8 vagy Windows 7 rendszert futtató ügyfélszámítógép

## <a id="connect"></a>Csatlakozzon a távoli asztallal
A távoli asztal engedélyezése a HDInsight-fürt, majd csatlakozhat az utasítások szerint [csatlakozhat a HDInsight-fürtök RDP-vel](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>A Hadoop-parancs használata
Amikor az asztalon, a HDInsight-fürthöz csatlakozik, használja az alábbi lépéseket egy MapReduce-feladatot futtathatja a Hadoop-parancs használatával:

1. A HDInsight asztaláról indítsa el a **Hadoop parancssor**. Ekkor megnyílik egy új parancssort a **c:\apps\dist\hadoop-&lt;verziószám >** könyvtár.

   > [!NOTE]
   > A verziószám módosítja a Hadoop frissítésekor. A **HADOOP_HOME** környezeti változó segítségével keresse meg az elérési utat. Ha például `cd %HADOOP_HOME%` a Hadoop-címtárban, könyvtárak, tudni, hogy a verziószám nem változik.
   >
   >
2. Használatához a **Hadoop** parancs futtatása egy példa MapReduce-feladatot, használja a következő parancsot:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Ekkor elindul a **wordcount** osztály, amely tartalmazza a **hadoop-mapreduce-examples.jar** fájlt az aktuális könyvtárban található. Bemenetként, használja a **wasb://example/data/gutenberg/davinci.txt** dokumentumhoz, és a kimeneti tárolt: **wasb: / / / például/data/WordCountOutput**.

   > [!NOTE]
   > a MapReduce-feladatot, és a példaadatokat kapcsolatos további információkért lásd: <a href="hdinsight-use-mapreduce.md">MapReduce használata a HDInsight Hadoop</a>.
   >
   >
3. A feladat részletei a feldolgozása, és azt információkat ad vissza a következőhöz hasonló a feladat befejezésekor bocsát ki:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Ha a feladat befejeződött, a következő paranccsal listázhatja a kimeneti fájlok tárolt **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    Ez a két fájlt kell megjelenítenie **_SUCCESS** és **. rész – az r-00000**. A **. rész – az r-00000** fájl tartalmazza a feladat kimenetét.

   > [!NOTE]
   > Bizonyos MapReduce-feladatok az eredmények lehet, hogy elosztja a több **. rész – az r-###** fájlokat. Ha igen, használja a ### utótagot a fájlok sorrendjét.
   >
   >
5. A kimenet megtekintéséhez használja a következő parancsot:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Ez az abban található szavak listáját jeleníti meg a **wasb://example/data/gutenberg/davinci.txt** fájlt, valamint, hogy hányszor történt az egyes szavak. Az alábbiakban látható egy példa a fájlban szereplő adatok:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Summary (Összefoglalás)
Látható, a Hadoop parancs MapReduce-feladatok futtatásához egy HDInsight-fürtön, és nézze meg a feladat kimenetének egyszerű módszert biztosít.

## <a id="nextsteps"></a>Következő lépések
HDInsight MapReduce-feladatok általános tájékoztatást:

* [HDInsight hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)
