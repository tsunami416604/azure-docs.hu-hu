---
title: "Hadoop Hive használata a hdinsight - Azure lekérdezés konzolon |} Microsoft Docs"
description: "Megtudhatja, hogyan futtathat Hive-lekérdezéseket a HDInsight Hadoop-fürt a böngészőből a web-alapú lekérdezés konzol segítségével."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5ae074b0-f55e-472d-94a7-005b0e79f779
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: d6032b8a1e3d338b046c958804102aeb9efcf4ab
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="run-hive-queries-using-the-query-console"></a>A lekérdezés konzollal Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ebben a cikkben, megtudhatja, hogyan futtathat Hive-lekérdezéseket a HDInsight Hadoop-fürt a böngészőből az HDInsight lekérdezés konzollal.

> [!IMPORTANT]
> A HDInsight lekérdezés konzol a Windows-alapú HDInsight-fürtök csak érhető el. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4-es vagy nagyobb, lásd: [lekérdezések futtatása Hive Ambari Hive nézetben](apache-hadoop-use-hive-ambari-view.md) információ Hive-lekérdezések egy webböngészőből.

## <a id="prereq"></a>Előfeltételek
Ebben a cikkben szereplő lépések elvégzéséhez a következőkre lesz szüksége.

* A Windows-alapú HDInsight Hadoop-fürt
* Modern webböngésző

## <a id="run"></a>A lekérdezés konzollal Hive-lekérdezések futtatása
1. Nyisson meg egy webböngészőt, és keresse meg **https://CLUSTERNAME.azurehdinsight.net**, ahol **CLUSTERNAME** a HDInsight-fürt neve. Ha a rendszer kéri, adja meg a felhasználónevet és a fürt létrehozásakor használt jelszót.
2. A lap tetején hivatkozások közül válassza ki a **Hive szerkesztő**. Ez megjeleníti, amelyek segítségével adja meg a HiveQL utasításokat, amelyek a HDInsight-fürt futtatni kívánt űrlap.

    ![a hive szerkesztő](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Cserélje le a szöveget `Select * from hivesampletable` , a következő hiveql:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * **DROP TABLE**: törli a táblázat és az adatfájlban, ha a tábla már létezik.
   * **A külső tábla létrehozása**: táblát hoz létre egy új "külső" struktúra. Külső táblák csak a tábladefiníció tárolása Hive; az adatok marad az eredeti helyen.

     > [!NOTE]
     > Külső táblák kell használni, ha az alapul szolgáló adatokat frissítenie kell a külső forrásból (például egy automatizált adatok feltöltési folyamat) vagy egy másik MapReduce művelet által várt, de azt szeretné, hogy a legfrissebb adatok használata a Hive-lekérdezéseket.
     >
     > A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában.
     >
     >
   * **SOR formátum**: Hive közli az adatok formázását. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.
   * **AS TEXTFILE helyen tárolt**: (a példa/adatkönyvtár) Hive közli az adatokat tárolja, és szövegként tárolt
   * **Válassza ki**: válassza ki az összes sorok számát ahol oszlop **t4** tartalmazza értékét **[hiba]**. Ez visszaadja-e érték **3** mert három ezt az értéket tartalmazó sorok.
   * **INPUT__FILE__NAME PÉLDÁUL "%.log"** -struktúra azt ismerteti, amely jelenleg csak a befejezési fájlok kell vissza adatokat. napló. Ez korlátozza a keresési a sample.log fájlt, amely tartalmazza az adatokat, és a nem ad vissza adatot más példa, amelyek nem felelnek meg a sémában meghatározott adatfájlok tartja azt.
3. Kattintson a **nyújt**. A **feladat munkamenet** a lap alján megjelenjen-e meg a feladat részleteit.
4. Ha a **állapot** mező módosításai **befejezve**, jelölje be **részleteinek megtekintése** a feladathoz. Részleteit megjelenítő oldalon a **Job Output** tartalmaz `[ERROR]    3`. Használhatja a **letöltése** gombra az ebbe a mezőbe, a feladat eredményének tartalmazó fájl letöltése.

## <a id="summary"></a>Summary (Összefoglalás)
Ahogy látja, a lekérdezés konzol könnyedén futtathat Hive-lekérdezéseket a HDInsight-fürtöt, figyelheti a feladat állapotát és a kimeneti beolvasása.

További információt a Hive lekérdezés konzolja segítségével futtathat Hive-feladatokat, válassza ki a **bevezetés** használja majd a biztosított mintákat a lekérdezés konzol felső részén. Minden minta bemutatja, hogyan a folyamatot, amely az adatok elemzése, beleértve a HiveQL utasításokról a mintában használt magyarázata a Hive eszközzel.

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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
