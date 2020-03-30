---
title: Ismerje meg az Apache Spark adatformátumait az Azure Data Lake Analytics U-SQL fejlesztői számára.
description: Ez a cikk az Apache Spark-kal kapcsolatos fogalmakat ismerteti, amelyek segítségével U_SQL a fejlesztők megismerhetik az U-SQL és a Spark-adatformátumok közötti különbségeket.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648440"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Az U-SQL és a Spark-adatformátumok közötti különbségek ismertetése

Ha [azure Databricks](../azure-databricks/what-is-azure-databricks.md) vagy [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)használatával szeretné használni, javasoljuk, hogy telepítse át az adatokat az Azure Data Lake Storage [Gen1](../data-lake-store/data-lake-store-overview.md) szolgáltatásból az [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)szolgáltatásba.

A fájlok áthelyezése mellett az U-SQL táblákban tárolt adatokat is elérhetővé kell tennie a Spark számára.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Az Azure Data Lake Storage Gen1 fájlokban tárolt adatok áthelyezése

A fájlokban tárolt adatok többféleképpen mozgathatók:

- Írjon egy Azure Data Factory-folyamatot az Azure Data Lake Storage [Gen1](../data-lake-store/data-lake-store-overview.md) fiókból az [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) fiókba történő másolásához. [Azure Data Factory](../data-factory/introduction.md)
- Írjon egy Spark-feladatot, amely beolvassa az adatokat az [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) fiókból, és írja az Azure Data Lake Storage [Gen2](../storage/blobs/data-lake-storage-introduction.md) fiókba. A használati eset alapján előfordulhat, hogy más formátumban, például a Parketta formátumban szeretné írni, ha nem kell megőriznie az eredeti fájlformátumot.

Javasoljuk, hogy tekintse át a big [data-elemzési megoldások frissítése az Azure Data Lake Storage Gen1-ről az Azure Data Lake Storage Gen2-re című cikket.](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>U-SQL táblákban tárolt adatok áthelyezése

U-SQL-táblák nem értik a Spark. Ha az U-SQL táblákban tárolt adatokkal rendelkezik, egy U-SQL-feladatot fog futtatni, amely kinyeri a táblaadatokat, és olyan formátumban menti őket, amelyet a Spark megért. A legmegfelelőbb formátum a Parkettafájlok készletének létrehozása a Hive metastore mappaelrendezése után.

A kimenet az U-SQL-ben érhető el a beépített Parquet outputter segítségével, és a dinamikus kimeneti particionálás fájlkészletekkel a partíciómappák létrehozásához. [Minden eddiginél több fájlt dolgoz fel, és a Parquet használata](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) példát mutat az ilyen Spark-fogyóadatok létrehozására.

Az átalakítás után másolja az adatokat az [Azure Data Lake Storage Gen1 fájlokban tárolt adatok áthelyezése című fejezetben](#move-data-stored-in-azure-data-lake-storage-gen1-files)ismertetett módon.

## <a name="caveats"></a>Ellenmondások

- Adatszemantika Fájlok másolásakor a másolás bájtszinten történik. Így ugyanazokat az adatokat kell megjelennie az [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) fiókban. Megjegyzés: a Spark azonban egyes karaktereket eltérően értelmezhet. Előfordulhat például, hogy egy CSV-fájl ban lévő sorhatárolóhoz más alapértelmezett beállítást használ.
    Továbbá, ha gépelt adatokat másol (táblákból), akkor a Parketta és a Spark eltérő pontosságú és léptékes lehet néhány beírt érték (például egy úszó), és eltérően kezelheti a null értékeket. Például az U-SQL rendelkezik a C# szemantika null értékek, míg a Spark egy három értékű logika null értékek.

- Az adatok szervezése (particionálás) U-SQL-táblák kétszintű particionálást biztosítanak. A külső`PARTITIONED BY`szint ( ) érték szerint, és leképezi többnyire a Hive/Spark particionálási séma segítségével mappa hierarchiák. Gondoskodnia kell arról, hogy a null értékek a megfelelő mappába legyenek leképezve. Az U-SQL belső szintje (`DISTRIBUTED BY`) 4 terjesztési sémát kínál: ciklikus multiplexelés, tartomány, kivonat és közvetlen kivonat.
    Hiv/Spark-táblák csak támogatja az érték particionálásvagy kivonatparticionálás, egy másik kivonatoló függvény, mint az U-SQL. Az U-SQL-táblaadatok kimenetekor valószínűleg csak a Spark értékparticionálási értékparticionálására lesz képes leképezni, és előfordulhat, hogy a végső Spark-lekérdezésektől függően további hangolást kell végeznie az adatelrendezésen.

## <a name="next-steps"></a>További lépések

- [A Spark-kód fogalmai az U-SQL-fejlesztők számára](understand-spark-code-concepts.md)
- [Frissítse big data-elemzési megoldásait az Azure Data Lake Storage Gen1-ről az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET apache sparkhoz](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Adatok átalakítása az Azure Data Factory Spark-tevékenységével](../data-factory/transform-data-using-spark.md)
- [Adatok átalakítása a Hadoop-struktúra használatával az Azure Data Factoryban](../data-factory/transform-data-using-hadoop-hive.md)
- [Mi az az Azure HDInsight-alapú Apache Spark?](../hdinsight/spark/apache-spark-overview.md)
