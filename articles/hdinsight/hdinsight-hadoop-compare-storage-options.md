---
title: Az Azure HDInsight-fürtökkel használható tárolási lehetőségek összehasonlítása
description: Áttekintést nyújt a tárolási típusokról és az Azure HDInsight-val való munkájukról.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869845"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökkel használható tárolási lehetőségek összehasonlítása

HdInsight-fürtök létrehozásakor választhat néhány különböző Azure storage-szolgáltatás közül:

* [Azure-tárhely](./overview-azure-storage.md)
* [2. generációs Azure Data Lake Storage](./overview-data-lake-storage-gen1.md)
* [1. generációs Azure Data Lake Storage](./overview-data-lake-storage-gen2.md)

Ez a cikk áttekintést nyújt ezekről a tárolási típusokról és azok egyedi szolgáltatásairól.

## <a name="storage-types-and-features"></a>Tárolási típusok és -szolgáltatások

Az alábbi táblázat összefoglalja a HDInsight különböző verzióival támogatott Azure Storage-szolgáltatásokat:

| Tárolási szolgáltatás | Fiók típusa | Névtér típusa | Támogatott szolgáltatások | Támogatott teljesítményszintek | Támogatott hozzáférési szintek | HDInsight verzió | Fürt típusa |
|---|---|---|---|---|---|---|---|
|2. generációs Azure Data Lake Storage| Általános célú V2 | Hierarchikus (fájlrendszer) | Blob | Standard | Forró, Hűvös, Archívum | 3,6+ | A Spark 2.1 és 2.2 kivételével|
|Azure Storage| Általános célú V2 | Objektum | Blob | Standard | Forró, Hűvös, Archívum | 3,6+ | Összes |
|Azure Storage| Általános célú V1 | Objektum | Blob | Standard | N/A | Összes | Összes |
|Azure Storage| Blob-tároló** | Objektum | Blob blokkolása | Standard | Forró, Hűvös, Archívum | Összes | Összes |
|1. generációs Azure Data Lake Storage| N/A | Hierarchikus (fájlrendszer) | N/A | N/A | N/A | 3.6 Csak | Minden, kivéve a HBase |

**HdInsight-fürtök esetén csak a másodlagos tárfiókok lehetnek BlobStorage típusúak, a Lapblob pedig nem támogatott tárolási lehetőség.

Az Azure Storage-fióktípusokkal kapcsolatos további információkért tekintse meg az [Azure storage-fiók áttekintése című témakört.](../storage/common/storage-account-overview.md)

Az Azure Storage hozzáférési szintjeiről további információt az [Azure Blob storage: Premium (előzetes verzió), a gyakori elérésű, a ritka elérésű és az archív tárolási szintek című témakörben talál.](../storage/blobs/storage-blob-storage-tiers.md)

Fürtök et hozhat létre az elsődleges és választható másodlagos tároló szolgáltatások kombinációival. Az alábbi táblázat a HDInsight által jelenleg támogatott fürttárolási konfigurációkat foglalja össze:

| HDInsight verzió | Elsődleges tárolás | Másodlagos tárolás | Támogatott |
|---|---|---|---|
| 3,6 & 4,0 | Általános célú V1, Általános célú V2 | Általános célú V1, Általános célú V2, BlobStorage(Blokkblobok) | Igen |
| 3,6 & 4,0 | Általános célú V1, Általános célú V2 | 2. generációs Data Lake Storage | Nem |
| 3,6 & 4,0 | Data Lake Storage Gen2* | 2. generációs Data Lake Storage | Igen |
| 3,6 & 4,0 | Data Lake Storage Gen2* | Általános célú V1, Általános célú V2, BlobStorage(Blokkblobok) | Igen |
| 3,6 & 4,0 | 2. generációs Data Lake Storage | 1. generációs Data Lake Storage | Nem |
| 3.6 | 1. generációs Data Lake Storage | 1. generációs Data Lake Storage | Igen |
| 3.6 | 1. generációs Data Lake Storage | Általános célú V1, Általános célú V2, BlobStorage(Blokkblobok) | Igen |
| 3.6 | 1. generációs Data Lake Storage | 2. generációs Data Lake Storage | Nem |
| 4.0 | 1. generációs Data Lake Storage | Bármelyik | Nem |
| 4.0 | Általános célú V1, Általános célú V2 | 1. generációs Data Lake Storage | Nem |

*=Ez lehet egy vagy több Data Lake Storage Gen2-fiók, feltéve, hogy mindegyik úgy van beállítva, hogy ugyanazt a felügyelt identitást használja a fürthozzáféréshez.

> [!NOTE]
> A Data Lake Storage Gen2 elsődleges tárolója nem támogatott a Spark 2.1 vagy 2.2 fürtök esetében.

## <a name="next-steps"></a>További lépések

* [Azure Storage – áttekintés](./overview-azure-storage.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)
* [Az Azure Data Lake Storage Gen2 áttekintése](./overview-data-lake-storage-gen2.md)
* [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
