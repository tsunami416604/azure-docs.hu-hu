---
title: A Hive-lekérdezéseket a Hive táblák adatokba |} Microsoft Docs
description: A Hive-lekérdezésekkel Hive táblák adatokba.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 0d46cea5-2b4c-4384-9bfa-fa20f6f75148
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: e85530e5297618b9e87f46a5a274621b060fe1fc
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>A Hive-táblákban tárolt adatok megismerése Hive-lekérdezésekkel
Ez a dokumentum minta Hive parancsfájlok, amely segítségével a Hive táblák egy HDInsight Hadoop-fürt adatokba nyújt.

A következő **menü** eszközök segítségével áttekintheti az különböző tárolási környezetekben adatokat leíró témakörökre mutató hivatkozásokat tartalmaz.

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Egy Azure storage-fiók létrehozása. Ha módosítania kell az utasításokat, lásd: [egy Azure Storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* A HDInsight szolgáltatásban egy testreszabott Hadoop-fürt üzembe helyezve. Ha módosítania kell az utasításokat, lásd: [testreszabása Azure HDInsight Hadoop-fürtök az Advanced Analytics](customize-hadoop-cluster.md).
* Az adatok az Azure HDInsight Hadoop-fürtök Hive táblák fel lett töltve. Ha még nem, kövesse az utasításokat a [létrehozása és az adatok betöltése a Hive táblák](move-hive-tables.md) feltölteni az adatokat a Hive táblák először.
* Engedélyezve van a fürt távoli eléréséhez. Ha módosítania kell az utasításokat, lásd: [a Head csomópont a Hadoop-fürt eléréséhez](customize-hadoop-cluster.md).
* Ha útmutatást nyújt a Hive-lekérdezések van szüksége, tekintse meg [hogyan küldhetnek Hive-lekérdezések](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Példa Hive lekérdezés parancsfájlok adatok feltárása
1. A szám megfigyelések partíciónként  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Napi megfigyeléseket szám  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. A szintek kategorikus oszlopban beolvasása  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. A szintek számának beolvasása a kombinációja kategorikus kétoszlopos  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. A numerikus oszlopok terjesztése beolvasása  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Rekordok kinyerése két tábla illesztése
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>A taxi út adatáttelepítések esetében a lekérdezés további parancsfájlok
Példák az adott lekérdezések [NYC Taxi út adatok](http://chriswhong.com/open-data/foil_nyc_taxi/) forgatókönyvek is szerepelnek [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ezeket a lekérdezéseket már rendelkezik az adatok séma van megadva, és készen áll elküldésre váró futtatásához.

