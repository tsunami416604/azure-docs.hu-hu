---
title: Ismerje meg, Apache Spark Azure Data Lake Analytics U-SQL-fejlesztőknek.
description: Ez a cikk Apache Spark fogalmakat ismerteti, amelyek segítenek az U-SQL-fejlesztők közötti különbségek elérésében.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 3bb51c3baa4a08a1d689448b8583e3dbbe1d81d7
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966358"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Az U-SQL-fejlesztők Apache Spark megismerése

A Microsoft számos olyan elemzési szolgáltatást támogat, mint például a [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) és az [Azure HDInsight](../hdinsight/hdinsight-overview.md) , valamint a Azure Data Lake Analytics.  Halljuk a fejlesztőknek, hogy egyértelmű előnyt biztosítanak a nyílt forráskódú megoldások számára, mivel elemzési folyamatokat építenek ki.  Ha segítségre van szüksége az U-SQL-fejlesztők számára a Apache Spark megismeréséhez, és hogy miként alakíthatja át a U-SQL-parancsfájlokat Apache Sparkre, ezt az útmutatót hoztuk létre.  

Több lépésből áll, és számos alternatíva is elérhető.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Az U-SQL Apache Sparkre alakításának lépései

1. Alakítsa át a feladatok előkészítési folyamatait.

   Ha [Azure Data Factoryt](../data-factory/introduction.md) használ a Azure Data Lake Analytics szkriptek összehangolása érdekében, akkor módosítania kell őket az új Spark-programok előkészítéséhez.
2. Ismerje meg, hogy a U-SQL és a Spark hogyan kezelje az adatkezelési különbségeket

   Ha az adatait [Azure Data Lake Storage Gen1ról](../data-lake-store/data-lake-store-overview.md) [Azure Data Lake Storage Gen2ra](../storage/blobs/data-lake-storage-introduction.md)szeretné áthelyezni, akkor a fájl adatait és a katalógusban tárolt adatait is át kell másolnia. Lásd: [Spark-adatformátumok megismerése](data-lake-analytics-understand-spark-data-formats.md)
3. U-SQL-parancsfájlok átalakítása a Sparkba

   A U-SQL-parancsfájlok átalakítása előtt ki kell választania egy elemzési szolgáltatást. Néhány elérhető számítási szolgáltatás:
      - [Azure Data Factory adatfolyam](../data-factory/concepts-data-flow-overview.md) Az adatfolyamatok leképezése olyan vizuálisan tervezett adatátalakítások, amelyek lehetővé teszik az adatmérnökök számára, hogy programkód írása nélkül fejlesszenek grafikus Adatátalakítási logikát. Habár nem alkalmas az összetett felhasználói kód végrehajtására, egyszerűen képviselheti a hagyományos SQL-szerű adatfolyam átalakításokat
      - [Azure HDInsight-struktúra](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) A HDInsight Apache Hive a kinyerési, átalakítási és betöltési (ETL) műveletekhez alkalmas.  Ez azt jelenti, hogy a U-SQL-szkripteket Apache Hivera fordítja le.
      - Apache Spark motorok, például [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) vagy [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) ez azt jelenti, hogy az U-SQL-szkripteket a sparkba fogja fordítani. További információ: a [Spark-adatformátumok megismerése](data-lake-analytics-understand-spark-data-formats.md)

> [!CAUTION]
> A [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) és [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) a fürtszolgáltatások, és nem a kiszolgáló nélküli feladatok, például a Azure Data Lake Analytics. A költségek csökkentése érdekében meg kell fontolnia, hogyan kell kiépíteni a fürtöket a megfelelő költség/teljesítmény arány és az élettartamuk kezeléséhez.  Ezek a szolgáltatások eltérő teljesítménnyel rendelkeznek a .NET-ben írt felhasználói kóddal, ezért a burkolókat kell írnia, vagy egy támogatott nyelven kell újraírnia a kódot. További információ: a [Spark-adatformátumok megismerése](data-lake-analytics-understand-spark-data-formats.md), [Apache Spark kód fogalmai az U-SQL-fejlesztők](data-lake-analytics-understand-spark-code-concepts.md)számára, [.net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Következő lépések

- [A Spark-adatformátumok megismerése U-SQL-fejlesztőknek](data-lake-analytics-understand-spark-data-formats.md)
- [A Spark Code-fogalmak megismerése U-SQL-fejlesztőknek](data-lake-analytics-understand-spark-code-concepts.md)
- [Frissítse big data Analytics-megoldásait Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Az adatátalakítás a Hadoop-struktúra tevékenységgel Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Adatátalakítás a Spark-tevékenységgel Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Mi az Azure HDInsight Apache Spark](../hdinsight/spark/apache-spark-overview.md)
