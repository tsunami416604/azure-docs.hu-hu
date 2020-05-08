---
title: A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz
description: Áttekintést nyújt a tárolási típusokról, valamint arról, hogyan működnek együtt az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610700"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz

HDInsight-fürtök létrehozásakor több különböző Azure Storage-szolgáltatás közül választhat:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [1. generációs Azure Data Lake Storage](./overview-data-lake-storage-gen1.md)

Ez a cikk áttekintést nyújt ezekről a tárolási típusokról és azok egyedi funkcióiról.

## <a name="storage-types-and-features"></a>Tárolási típusok és szolgáltatások

A következő táblázat összefoglalja a HDInsight különböző verziói által támogatott Azure Storage-szolgáltatásokat:

| Tárolási szolgáltatás | Fiók típusa | Névtér típusa | Támogatott szolgáltatások | Támogatott teljesítményszint | Támogatott hozzáférési szintek | HDInsight verziója | Fürt típusa |
|---|---|---|---|---|---|---|---|
|2. generációs Azure Data Lake Storage| Általános célú v2 | Hierarchikus (fájlrendszer) | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6 + | Mind a Spark 2,1, mind a 2,2 kivételével|
|Azure Storage| Általános célú v2 | Objektum | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6 + | Összes |
|Azure Storage| Általános célú v1 | Objektum | Blob | Standard | N/A | Összes | Összes |
|Azure Storage| Blob Storage * * | Objektum | BLOB letiltása | Standard | Gyors elérésű, ritka elérésű Archívum | Összes | Összes |
|1. generációs Azure Data Lake Storage| N/A | Hierarchikus (fájlrendszer) | N/A | N/A | N/A | 3,6 csak | A HBase kivételével |

* * A HDInsight-fürtök esetében csak a másodlagos Storage-fiókok lehetnek BlobStorage, és az oldal Blobja nem támogatott tárolási lehetőség.

Az Azure Storage-fiókok típusairól további információt az [Azure Storage-fiók áttekintése](../storage/common/storage-account-overview.md) című témakörben talál.

További információ az Azure Storage hozzáférési szintjeiről [: Azure Blob Storage: Premium (előzetes verzió), gyakori, ritka elérésű és archív tárolási szintek](../storage/blobs/storage-blob-storage-tiers.md)

A fürtöket az elsődleges és választható másodlagos tárolók szolgáltatásainak kombinációi segítségével hozhatja létre. A következő táblázat összefoglalja a HDInsight által jelenleg támogatott fürtök tárolási konfigurációit:

| HDInsight verziója | Elsődleges tárterület | Másodlagos tároló | Támogatott |
|---|---|---|---|
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | 2. generációs Data Lake Storage | No |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | 2. generációs Data Lake Storage | Igen |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 & 4,0 | 2. generációs Data Lake Storage | 1. generációs Data Lake Storage | No |
| 3,6 | 1. generációs Data Lake Storage | 1. generációs Data Lake Storage | Igen |
| 3,6 | 1. generációs Data Lake Storage | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 | 1. generációs Data Lake Storage | 2. generációs Data Lake Storage | No |
| 4.0 | 1. generációs Data Lake Storage | Bármelyik | No |
| 4.0 | Általános célú v1, általános célú v2 | 1. generációs Data Lake Storage | No |

* = Ez lehet egy vagy több Data Lake Storage Gen2 fiók is, feltéve, hogy minden beállítás ugyanazt a felügyelt identitást használja a fürt eléréséhez.

> [!NOTE]
> Data Lake Storage Gen2 elsődleges tároló nem támogatott a Spark 2,1 vagy 2,2 fürtök esetében.

## <a name="next-steps"></a>További lépések

* [Az Azure Storage áttekintése](./overview-azure-storage.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)
* [Az Azure Data Lake Storage Gen2 áttekintése](./overview-data-lake-storage-gen2.md)
* [Bevezetés a Azure Data Lake Storage Gen2ba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
