---
title: Az Apache Hive használata a lekérdezés-konzolban, a HDInsight - Azure
description: Útmutató a webes Lekérdezéskonzol használata az Apache Hive-lekérdezések futtatásához egy HDInsight Hadoop-fürtöt a böngészőben.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: da270792c8987ff43c422c5b03eb8b789b8bda5e
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634590"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>A Lekérdezéskonzol használata az Apache Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ebből a cikkből megtudhatja, Apache Hive-lekérdezések futtatásához egy HDInsight Hadoop-fürtöt a böngészőben a HDInsight Lekérdezéskonzol használata.

> [!IMPORTANT]
> A HDInsight Lekérdezéskonzol csak akkor használható a Windows-alapú HDInsight-fürtökön. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> A HDInsight 3.4 vagy újabb, lásd: [futtatása Hive-lekérdezések Ambari, Hive-nézet](apache-hadoop-use-hive-ambari-view.md) információ a Hive-lekérdezések futtatása egy webböngészőből.

## <a id="prereq"></a>Előfeltételek
A jelen cikkben ismertetett lépések elvégzéséhez a következőkre lesz szüksége.

* Windows-alapú HDInsight Hadoop-fürt
* A modern webböngésző

## <a id="run"></a> A Lekérdezéskonzol használata Hive-lekérdezések futtatása
1. Nyisson meg egy webböngészőt, és navigáljon a **https://CLUSTERNAME.azurehdinsight.net**, ahol **CLUSTERNAME** a HDInsight-fürt neve. Ha a rendszer kéri, adja meg a felhasználónevet és jelszót, amelyet a fürt létrehozásakor használt.
2. Az oldal tetején lévő hivatkozások közül válassza ki a **Hive szerkesztő**. Megjelenik egy képernyő, amelyek segítségével adja meg a hiveql, amely a HDInsight-fürtön történő futásra szeretné.

    ![a hive szerkesztőben](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Cserélje le a szöveget `Select * from hivesampletable` a következő hiveql:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * **DROP TABLE**: törli az a táblázat és az adatfájlban, ha a tábla már létezik.
   * **CREATE EXTERNAL TABLE**: új táblát hoz létre "külső" struktúrában. Külső táblák csak a tábla definícióját tárolja a Hive; az adatok az eredeti helyén marad.

     > [!NOTE]
     > Külső táblák használata, ha az alapul szolgáló adatokat frissíteni kell a külső forrásból (például az automatikus feltöltési folyamat), vagy egy másik MapReduce művelet várható, de azt szeretné használni a legújabb adatok Hive-lekérdezéseket.
     >
     > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.
     >
     >
   * **SOR FORMÁTUMBAN**: arra utasítja a Hive, az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.
   * **AS TEXTFILE helyen tárolt**: arra utasítja a Hive, az adatok tárolása (a példaadatokat/directory), és azt szövegként tárolt
   * **Válassza ki**: válassza ki az összes sor számát ahol oszlop **t4** értéket tartalmazza **[hiba]**. A kapott érték **3** mert három sort, amely tartalmazza ezt az értéket.
   * **INPUT__FILE__NAME PÉLDÁUL "%.log"** -arra utasítja a Hive, amely azt kell csak adatokat adja vissza a végződésű fájlokból. napló. Ez korlátozza a keresést a sample.log fájlt, amely tartalmazza az adatokat, és a más példa adatok visszaadása, amelyek nem egyeznek meg a sémában meghatározott adatfájlok tartja azt.
3. Kattintson a **elküldése**. A **feladat munkamenet** az oldal alján megjelenik a feladat részleteit.
4. Ha a **állapot** Mezőváltozások **befejezve**válassza **részleteinek megtekintése** a feladathoz. A Részletek oldalon a **Feladatkimenet** tartalmaz `[ERROR]    3`. Használhatja a **letöltése** egy fájl, amely tartalmazza a feladat kimenetének letöltése a mező alatti gombra.

## <a id="summary"></a>Summary (Összefoglalás)
Látható, a Hive-lekérdezések futtatása HDInsight-fürtben, a feladat állapotának figyelése és lekérése a kimeneti egyszerű módszert biztosít.

További információ a Hive-lekérdezés konzol használata a Hive-feladatok futtatásához, jelölje be **bevezetés** a Lekérdezéskonzol tetején, majd a mintákkal biztosított. Mindegyik minta bemutatja a folyamat, adatok, beleértve a mintában használt a hiveql vonatkozó magyarázatok elemzése a Hive használatával.

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
