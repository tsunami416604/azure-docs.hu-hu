---
title: Adatok feltárása a Hive-táblákban Hive-lekérdezésekkel – Csapatadat-elemzési folyamat
description: Használjon minta Hive-parancsfájlokat, amelyek a HDInsight Hadoop-fürt Hive-tábláiban lévő adatok feltárására szolgálnak.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722169"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>A Hive-táblákban tárolt adatok megismerése Hive-lekérdezésekkel

Ez a cikk minta Hive-parancsfájlokat tartalmaz, amelyek a HIV-táblák ban lévő adatok egy HDInsight Hadoop-fürtben található adatok feltárására szolgálnak.

Ez a feladat egy lépés a [csapat adatelemzési folyamatában.](overview.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a következőket feltételezi:

* Létrehozott egy Azure-tárfiókot. Ha utasításokra van szüksége, olvassa [el az Azure Storage-fiók létrehozása című témakört.](../../storage/common/storage-account-create.md)
* Kiépített egy testreszabott Hadoop-fürtöt a HDInsight szolgáltatással. Ha utasításokra van szüksége, olvassa [el az Azure HDInsight Hadoop-fürtök testreszabása speciális elemzésekhez című](customize-hadoop-cluster.md)témakört.
* Az adatok feltöltve lettek a Hive-táblákaz Azure HDInsight Hadoop-fürtökben. Ha nem, kövesse az adatok [létrehozása és betöltése a Hive-táblák adatok](move-hive-tables.md) feltöltéséhez a Hive-táblák először utasításokat.
* Engedélyezve van a fürt távoli elérése. Ha utasításokra van szüksége, [olvassa el a Hadoop-fürt főcsomópontjának elérése című témakört.](customize-hadoop-cluster.md)
* Ha útmutatásra van szüksége a Hive-lekérdezések elküldéséhez, olvassa [el a Hive-lekérdezések küldése](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Példa Hive lekérdezési parancsfájlokra az adatok feltárásához
1. A megfigyelések száma partíciónként`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. A megfigyelések száma naponta`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. A szintek beszereznie egy kategorikus oszlopban  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. A szintek számának beszereznie két kategorikus oszlop kombinációjával`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Numerikus oszlopok eloszlásának beszereznie  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Rekordok kibontása két tábla egyesítéséből
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>További lekérdezési parancsfájlok taxiút-adatforgatókönyvekhez
Példák a new [york-i taxi-fuvaradat-forgatókönyvekre](https://chriswhong.com/open-data/foil_nyc_taxi/) jellemző lekérdezésekre a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)is találhatók. Ezek a lekérdezések már rendelkeznek megadott adatsémával, és készen állnak a futtatásra.

