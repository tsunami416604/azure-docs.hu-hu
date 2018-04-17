---
title: Hadoop Hive és a távoli asztal használata a HDInsight - Azure |} Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a HDInsight Hadoop-fürt távoli asztal használatával, és futtassa a Hive-lekérdezéseket a Hive parancssori felület használatával.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c228e35-d58a-4f22-917a-1d20c9da89b4
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 70eab088ce87d8a62d3f258b70aaec5e2e147d0e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>A Hive használata a HDInsight a távoli asztal hadooppal
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ebben a cikkben meg fog megtudhatja, hogyan távoli asztal használatával kapcsolódni HDInsight-fürtöt, és futtassa Hive-lekérdezéseket a Hive parancssori felület (CLI) használatával.

> [!IMPORTANT]
> A távoli asztal a Windows operációs rendszert használó HDInsight-fürtök csak érhető el. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4-es vagy nagyobb, lásd: [használata a HDInsight és Beeline Hive](apache-hadoop-use-hive-beeline.md) információk Hive-lekérdezéseket közvetlenül a fürtben futó parancsot a parancssorból.

## <a id="prereq"></a>Előfeltételek
Ebben a cikkben szereplő lépések elvégzéséhez a következőkre lesz szüksége:

* (A HDInsight Hadoop) a Windows-alapú HDInsight-fürt
* Windows 10, Windows 8 vagy Windows 7 rendszert futtató ügyfélszámítógép

## <a id="connect"></a>Csatlakozzon a távoli asztal
A távoli asztal engedélyezése a HDInsight-fürthöz, majd csatlakozni a következő utasításokat követve [csatlakozás RDP Funkciót használnak a HDInsight-fürtök](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>A Hive paranccsal
Amikor az asztalon a HDInsight-fürthöz csatlakozik, tegye a következőket Hive használható:

1. A HDInsight asztalról indítsa el a **Hadoop parancssori**.
2. Adja meg a következő parancsot a Hive CLI elindításához:

        %hive_home%\bin\hive

    Ha a parancssori felület elindult, jelennek meg a parancssori felület struktúra kérdés: `hive>`.
3. A parancssori felület használatával adja meg az alábbi állításokat annak nevű új tábla létrehozása **log4jLogs** mintaadatok használatával:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * **DROP TABLE**: törli a táblázat és az adatfájlban, ha a tábla már létezik.
   * **A külső tábla létrehozása**: táblát hoz létre egy új "külső" struktúra. Külső táblák csak a táblázat megadása a Hive (az adatok marad az eredeti helyen) tárolja.

     > [!NOTE]
     > Külső táblák kell használni, ha az alapul szolgáló adatokat frissítenie kell a külső forrásból (például egy automatizált adatok feltöltési folyamat) vagy egy másik MapReduce művelet által várt, de azt szeretné, hogy a legfrissebb adatok használata a Hive-lekérdezéseket.
     >
     > A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában.
     >
     >
   * **SOR formátum**: Hive közli az adatok formázását. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.
   * **AS TEXTFILE helyen tárolt**: (a példa/adatkönyvtár) Hive közli az adatokat tárolja, és szövegként tárolt.
   * **Válassza ki**: választja ki az összes sorok számát ahol oszlop **t4** értéke **[hiba]**. Ez visszaadja-e érték **3** mert három ezt az értéket tartalmazó sorok.
   * **INPUT__FILE__NAME PÉLDÁUL "%.log"** -struktúra azt ismerteti, amely jelenleg csak a befejezési fájlok kell vissza adatokat. napló. Ez korlátozza a keresési a sample.log fájlt, amely tartalmazza az adatokat, és a nem ad vissza adatot más példa, amelyek nem felelnek meg a sémában meghatározott adatfájlok tartja azt.
4. A következő utasítások használatával hozzon létre egy új "belső" táblát nevű **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * **Hozzon létre Ha nem létezik táblázat**: egy táblát hoz létre, ha még nem létezik. Mivel a **külső** kulcsszó nem használható, ez egy belső tábla, amely a Hive-adatraktárban tárolja, és teljesen kezeli a Hive.

     > [!NOTE]
     > Eltérően **külső** táblák, egy belső tábla eldobása is törli az alapul szolgáló adatokat.
     >
     >
   * **TÁROLT AS ORC**: az adatok optimalizált sor oszlopos (ORC) formátumban tárolja. Ez az egy magas optimalizált és hatékony formátumú Hive adatainak tárolásához.
   * **ÍRJA FELÜL AZ INSERT... Válassza ki**: sorát kiválasztja a **log4jLogs** tartalmazó **[hiba]**, majd szúrja be az adatokat a **errorLogs** tábla.

     Ellenőrizheti, hogy csak olyan sort tartalmazó **[hiba]** az oszlop t4 volt tároló a **errorLogs** table, használja a következő utasítás visszaadja az összes sort **errorLogs**:

       Válassza ki * a errorLogs;

     Három sor adat vissza kell adni, az összes tartalmazó **[hiba]** az oszlop t4.

## <a id="summary"></a>Summary (Összefoglalás)
Ahogy látja, a a Hive-parancs segítségével egyszerűen interaktív futtathat Hive-lekérdezéseket a HDInsight-fürtöt, figyelheti a feladat állapotát és a kimeneti beolvasása.

## <a id="nextsteps"></a>Következő lépések
Általános információk a hdinsight Hive:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Ha Tez Hive használ, tekintse meg a hibakeresési információk a következő dokumentumokat:

* [A Tez felhasználói felület használata a Windows-alapú HDInsight-on](../hdinsight-debug-tez-ui.md)
* [Az Ambari Tez nézetben a Linux-alapú HDInsight-on](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
