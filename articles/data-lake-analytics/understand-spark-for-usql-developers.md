---
title: Ismerje meg az Apache Spark ot az Azure Data Lake Analytics U-SQL fejlesztőihez.
description: Ez a cikk ismerteti az Apache Spark fogalmakat, amelyek segítenek az U-SQL-fejlesztők közötti különbségek.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648427"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Az Apache Spark ismertetése U-SQL-fejlesztőknek

A Microsoft számos Analytics-szolgáltatást támogat, például [az Azure Databricks-t](../azure-databricks/what-is-azure-databricks.md) és [az Azure HDInsight-ot,](../hdinsight/hdinsight-overview.md) valamint az Azure Data Lake Analytics-et. A fejlesztőktől azt halljuk, hogy egyértelműen előnyben részesítik a nyílt forráskódú megoldásokat, miközben elemzési folyamatokat építenek. Annak érdekében, hogy az U-SQL-fejlesztők megértsék az Apache Sparkot, és hogy hogyan alakíthatja át u-SQL-parancsfájljait Apache Sparkká, létrehoztuk ezt az útmutatót.

Ez magában foglalja a számos lépést tehet, és számos alternatívát.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Az U-SQL Apache Sparkká való átalakításának lépései

1. Alakítsa át a feladat vezénylési folyamatok.

   Ha [az Azure Data Data Data](../data-factory/introduction.md) Analytics-parancsfájlok vezényléséhez az Azure Data Lake Analytics-parancsfájlokat vezényli, azokat az új Spark-programok vezényléséhez kell módosítania.
2. Az U-SQL és a Spark adatainak kezelése közötti különbségek ismertetése

   Ha át szeretné helyezni az adatokat az [Azure Data Lake Storage Gen1-ből](../data-lake-store/data-lake-store-overview.md) az Azure Data Lake Storage [Gen2-be,](../storage/blobs/data-lake-storage-introduction.md)akkor a fájladatokat és a katalógus által karbantartott adatokat is át kell másolnia. Vegye figyelembe, hogy az Azure Data Lake Analytics csak az Azure Data Lake Storage Gen1-et támogatja. Lásd: [A Spark-adatformátumok ismertetése](understand-spark-data-formats.md)
3. Az U-SQL parancsfájlok átalakítása Sparkká

   Az U-SQL-parancsfájlok átalakítása előtt ki kell választania egy elemzési szolgáltatást. Néhány rendelkezésre álló számítási szolgáltatások a következők:
      - [Azure Data Factory DataFlow](../data-factory/concepts-data-flow-overview.md) Az adatfolyamok leképezése vizuálisan megtervezett adatátalakítások, amelyek lehetővé teszik az adatmérnökök számára, hogy kód írása nélkül fejlesszenek ki egy grafikus adatátalakítási logikát. Bár nem alkalmasak összetett felhasználói kód végrehajtására, könnyen képviselhetnek hagyományos SQL-szerű adatfolyam-átalakításokat
      - [Azure HDInsight-struktúra](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Az Apache Hive a HDInsight-on alkalmas az ETL-műveletek kinyerésére, átalakítására és betöltésére. Ez azt jelenti, hogy le fogja fordítani az U-SQL parancsfájlokat apache hive-re.
      - Az Apache Spark Engine-ek, például [az Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) vagy az Azure [Databricks](../azure-databricks/what-is-azure-databricks.md) ez azt jelenti, hogy lefordítja az U-SQL-parancsfájlokat a Sparkba. További információ: [Spark-adatformátumok ismertetése](understand-spark-data-formats.md)

> [!CAUTION]
> Az [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) és az [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) egyaránt fürtszolgáltatások, és nem kiszolgáló nélküli feladatok, például az Azure Data Lake Analytics. Meg kell vizsgálnia, hogyan kell kiépíteni a fürtök, hogy a megfelelő költség/teljesítmény arány, és hogyan kezelheti élettartamukat a költségek minimalizálása érdekében. Ezek a szolgáltatások különböző teljesítményjellemzőkkel rendelkeznek a .NET-ben írt felhasználói kóddal, ezért vagy burkolókat kell írnia, vagy át kell írnia a kódot egy támogatott nyelven. További információ: [A Spark-adatformátumok megismerése](understand-spark-data-formats.md), [Az Apache Spark-kód fogalmai nak megismerése az U-SQL-fejlesztők számára](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>További lépések

- [A Spark-adatformátumok ismertetése u-SQL-fejlesztők számára](understand-spark-data-formats.md)
- [A Spark-kód fogalmai az U-SQL-fejlesztők számára](understand-spark-code-concepts.md)
- [Frissítse big data-elemzési megoldásait az Azure Data Lake Storage Gen1-ről az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET apache sparkhoz](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Adatok átalakítása a Hadoop-struktúra használatával az Azure Data Factoryban](../data-factory/transform-data-using-hadoop-hive.md)
- [Adatok átalakítása az Azure Data Factory Spark-tevékenységével](../data-factory/transform-data-using-spark.md)
- [Mi az az Azure HDInsight-alapú Apache Spark?](../hdinsight/spark/apache-spark-overview.md)
