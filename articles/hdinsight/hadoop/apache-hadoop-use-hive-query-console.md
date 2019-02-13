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
ms.openlocfilehash: e568a8b7cfb21483f447b8244056de292e21093f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169401"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>A Lekérdezéskonzol használata az Apache Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ebből a cikkből megtudhatja, Apache Hive-lekérdezések futtatásához egy HDInsight Hadoop-fürtöt a böngészőben a HDInsight Lekérdezéskonzol használata.

> [!IMPORTANT]  
> A HDInsight Lekérdezéskonzol csak akkor használható a Windows-alapú HDInsight-fürtökön. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> A HDInsight 3.4 vagy újabb, lásd: [futtatása Apache Hive-lekérdezések Ambari, Hive-nézet](apache-hadoop-use-hive-ambari-view.md) információ a Hive-lekérdezések futtatása egy webböngészőből.

## <a id="prereq"></a>Előfeltételek
A jelen cikkben ismertetett lépések elvégzéséhez a következőkre lesz szüksége.

* Windows-alapú HDInsight Hadoop-fürt
* A modern webböngésző

## <a id="run"></a> A Lekérdezéskonzol használata az Apache Hive-lekérdezések futtatása
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

   * **DROP TABLE**: Törlődik a táblából, és az adatfájl, ha a tábla már létezik.
   * **KÜLSŐ TÁBLA LÉTREHOZÁSA**: A Hive egy új "külső" táblát hoz létre. Külső táblák csak a tábla definícióját tárolja a Hive; az adatok az eredeti helyén marad.

     > [!NOTE]  
     > Külső táblák használata, ha az alapul szolgáló adatokat frissíteni kell a külső forrásból (például az automatikus feltöltési folyamat), vagy egy másik MapReduce művelet várható, de azt szeretné használni a legújabb adatok Hive-lekérdezéseket.
     >
     > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.
     >
     >
   * **SOR FORMÁTUMBAN**: Arra utasítja a Hive, az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.
   * **TÁROLT TEXTFILE HELYKÉNT**: Arra utasítja a Hive, az adatok tárolására (a példaadatokat/directory), és azt szövegként tárolt
   * **VÁLASSZA KI**: Válassza ki az összes sor számát ahol oszlop **t4** értéket tartalmazza **[hiba]**. A kapott érték **3** mert három sort, amely tartalmazza ezt az értéket.
   * **INPUT__FILE__NAME PÉLDÁUL "%.log"** -arra utasítja a Hive, amely azt kell csak adatokat adja vissza a végződésű fájlokból. napló. Ez korlátozza a keresést a sample.log fájlt, amely tartalmazza az adatokat, és a más példa adatok visszaadása, amelyek nem egyeznek meg a sémában meghatározott adatfájlok tartja azt.
3. Kattintson a **Submit** (Küldés) gombra. A **feladat munkamenet** az oldal alján megjelenik a feladat részleteit.
4. Ha a **állapot** Mezőváltozások **befejezve**válassza **részleteinek megtekintése** a feladathoz. A Részletek oldalon a **Feladatkimenet** tartalmaz `[ERROR]    3`. Használhatja a **letöltése** egy fájl, amely tartalmazza a feladat kimenetének letöltése a mező alatti gombra.

## <a id="summary"></a>Summary (Összefoglalás)
Látható, a Hive-lekérdezések futtatása HDInsight-fürtben, a feladat állapotának figyelése és lekérése a kimeneti egyszerű módszert biztosít.

További információ a Hive-lekérdezés konzol használata a Hive-feladatok futtatásához, jelölje be **bevezetés** a Lekérdezéskonzol tetején, majd a mintákkal biztosított. Mindegyik minta bemutatja a folyamat, adatok, beleértve a mintában használt a hiveql vonatkozó magyarázatok elemzése a Hive használatával.

## <a id="nextsteps"></a>Következő lépések
Általános információk a HDInsight Hive-ról:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Tez Hive-val használja, ha a hibakeresési információkat a következő dokumentumban talál: [Az Apache Ambari Tez nézetben jelenítheti meg a Linux-alapú HDInsight](../hdinsight-debug-ambari-tez-view.md).

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
