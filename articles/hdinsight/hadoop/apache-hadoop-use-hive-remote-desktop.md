---
title: A Hadoop Hive és a távoli asztal használata a HDInsight – Azure
description: Ismerje meg, hogyan csatlakozhat a HDInsight Hadoop-fürtöt a távoli asztal használatával, és futtassa a Hive-lekérdezéseket a Hive-parancssori felület használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 32fb2bc9514b911d0a1ce295eee651e4b6b6388a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015077"
---
# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>A Hive használata a Hadooppal a HDInsight a távoli asztallal
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ebben a cikkben, fog megtudhatja, hogyan csatlakozhat egy HDInsight-fürtöt a távoli asztal használatával, és futtassa a Hive-lekérdezések a Hive-parancssori felület (CLI) használatával.

> [!IMPORTANT]
> A távoli asztal csak akkor használható a Windows operációs rendszert használó HDInsight-fürtökön. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> A HDInsight 3.4 vagy újabb, lásd: [Hive használata a HDInsight és a Beeline](apache-hadoop-use-hive-beeline.md) információ a Hive-lekérdezések futtatása közvetlenül a fürtön egy parancssorból.

## <a id="prereq"></a>Előfeltételek
A jelen cikkben ismertetett lépések elvégzéséhez a következőkre lesz szüksége:

* A Windows-alapú HDInsight (Hadoop on HDInsight)-fürt
* A Windows 10, Windows 8 vagy Windows 7 rendszert futtató ügyfélszámítógép

## <a id="connect"></a>Csatlakozzon a távoli asztallal
A távoli asztal engedélyezése a HDInsight-fürt, majd csatlakozhat az utasítások szerint [csatlakozhat a HDInsight-fürtök RDP-vel](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>A Hive-parancs használata
Miután csatlakoztatta a HDInsight-fürt az asztalra, a következő lépések használatával Hive használata:

1. A HDInsight asztaláról indítsa el a **Hadoop parancssor**.
2. Adja meg a Hive-parancssori felület elindítása a következő parancsot:

        %hive_home%\bin\hive

    A parancssori felület elindult, látni fogja a Hive-CLI használatával: `hive>`.
3. A parancssori felületről, adja meg az alábbi utasításokat, hozzon létre egy új táblát nevű **log4jLogs** mintaadatokat használja:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * **DROP TABLE**: törli az a táblázat és az adatfájlban, ha a tábla már létezik.
   * **CREATE EXTERNAL TABLE**: új táblát hoz létre "külső" struktúrában. Külső táblák csak a tábla definícióját tárolja a Hive (az adatok az eredeti helyen van balra).

     > [!NOTE]
     > Külső táblák használata, ha az alapul szolgáló adatokat frissíteni kell a külső forrásból (például az automatikus feltöltési folyamat), vagy egy másik MapReduce művelet várható, de azt szeretné használni a legújabb adatok Hive-lekérdezéseket.
     >
     > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.
     >
     >
   * **SOR FORMÁTUMBAN**: arra utasítja a Hive, az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.
   * **AS TEXTFILE helyen tárolt**: arra utasítja a Hive, az adatok tárolása a (a példaadatokat/directory), és hogy szövegként lesz tárolva.
   * **Válassza ki**: választja ki az összes sor számát ahol oszlop **t4** értéket tartalmazza **[hiba]**. A kapott érték **3** mert három sort, amely tartalmazza ezt az értéket.
   * **INPUT__FILE__NAME PÉLDÁUL "%.log"** -arra utasítja a Hive, amely azt kell csak adatokat adja vissza a végződésű fájlokból. napló. Ez korlátozza a keresést a sample.log fájlt, amely tartalmazza az adatokat, és a más példa adatok visszaadása, amelyek nem egyeznek meg a sémában meghatározott adatfájlok tartja azt.
4. Az alábbi utasítások segítségével hozzon létre egy új "belső" nevű táblát **hibanaplókat**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * **Hozzon létre tábla IF NOT EXISTS**: egy táblát hoz létre, ha azt nem létezik. Mivel a **külső** kulcsszó nem használható, ez egy belső tábla, amely a Hive-adatraktárban tárolja, és Hive teljes mértékben felügyelt.

     > [!NOTE]
     > Ellentétben **külső** táblákat, egy belső tábla elvetését is törli az alapul szolgáló adatokat.
     >
     >
   * **TÁROLT AS ORC**: tárolja az adatokat optimalizált sor Oszlopalapú (ORC formátumban). Ez a Hive-adatok tárolására szolgáló nagymértékben optimalizált és hatékony formátumban.
   * **ÍRJA FELÜL AZ INSERT... Válassza ki**: sor innen kiválasztja a **log4jLogs** tartalmazó tábla **[hiba]**, majd beszúrja az adatokat a **hibanaplókat** tábla.

     Győződjön meg arról, hogy csak azok a sorok, amelyek tartalmazzák a **[hiba]** oszlop t4 is tárolja a **hibanaplókat** táblában, használja a következő utasítást az összes sorát **hibanaplókat**:

       Válassza ki * a hibanaplókat;

     Három sor adat vissza kell adni, az összes tartalmazó **[hiba]** az oszlop t4.

## <a id="summary"></a>Summary (Összefoglalás)
Ahogy láthatjuk, a Hive-parancs itt egyszerűen interaktív Hive-lekérdezések futtatásához egy HDInsight-fürtön, a feladat állapotának nyomon és a kimeneti lekéréséhez.

## <a id="nextsteps"></a>Következő lépések
Általános információk a HDInsight Hive-ról:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)

Ha Tez Hive-val használ, tekintse meg a hibakeresési információkat a következő dokumentumokat:

* [A Tez felhasználói felület használata a Windows-alapú HDInsight](../hdinsight-debug-tez-ui.md)
* [A Linux-alapú HDInsight az Ambari Tez nézet használata](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

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
