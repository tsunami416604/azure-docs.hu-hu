---
title: Fedezze fel az adatokat a Hive-táblákban a Hive-lekérdezések – csoportos adatelemzési folyamat
description: A Hive-táblákban egy HDInsight Hadoop-fürtön az adatok által használt minta Hive parancsfájlok használata.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c9761ade493641b6445cf151424f9598ab25e384
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880092"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>A Hive-táblákban tárolt adatok megismerése Hive-lekérdezésekkel

Ez a cikk ismerteti a minta Hive-szkriptek, amelyekkel a Hive-táblákban egy HDInsight Hadoop-fürtön az adatok feltárására.

Ez a feladat Ez a lépés a [csoportos adatelemzési folyamat](overview.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha utasításokat van szüksége, tekintse meg [Azure Storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md)
* A HDInsight szolgáltatással egyéni Hadoop-fürt kiépítése. Ha utasításokat van szüksége, tekintse meg [testreszabása az Azure HDInsight Hadoop-fürtök Advanced Analytics](customize-hadoop-cluster.md).
* Az adatok Hive-táblák az Azure HDInsight Hadoop-fürtök lett feltöltve. Ha még nem, kövesse az utasításokat a [létrehozása és az adatok betöltése Hive-tábláihoz](move-hive-tables.md) először feltölteni az adatokat a Hive-táblákat.
* Távoli hozzáférés a fürthöz engedélyezett. Ha utasításokat van szüksége, tekintse meg [a fő csomópont, Hadoop-fürt eléréséhez](customize-hadoop-cluster.md).
* Ha a Hive-lekérdezések elküldéséhez útmutatást van szüksége, tekintse meg [hogyan Hive-lekérdezések elküldéséhez](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Példa a Hive-lekérdezés parancsprogramok adatok feltárásához
1. Lekérni az megfigyelések partíciónként  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Napi megfigyeléseket megszámlálása  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. A szintek egy kategorikus oszlop beolvasása  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Lépjen be két kategorikus oszlopok kombinációja szintek száma  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. A numerikus oszlopok terjesztési beolvasása  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Két tábla rekordjait kinyerése
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>További lekérdezési parancsfájlok taxi trip data jellegű alkalmazási
Példák a lekérdezésekre adott [NYC Taxi Útadatok](https://chriswhong.com/open-data/foil_nyc_taxi/) forgatókönyveket is rendelkezésre állnak a [GitHub-adattár](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ezeket a lekérdezéseket már rendelkezik az adatok séma van megadva, és készen áll hamarosan futtatásához.

