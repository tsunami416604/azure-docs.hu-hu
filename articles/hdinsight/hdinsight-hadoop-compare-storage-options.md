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
ms.openlocfilehash: ce1c6bdfb38e37c18a18cf970d2dd08683967da3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536748"
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
|Azure Data Lake Storage Gen2| Általános célú v2 | Hierarchikus (fájlrendszer) | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6 + | Mind a Spark 2,1, mind a 2,2 kivételével|
|Azure Storage| Általános célú v2 | Objektum | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6 + | Mind |
|Azure Storage| Általános célú v1 | Objektum | Blob | Standard | n.a. | Mind | Mind |
|Azure Storage| Blob Storage * * | Objektum | BLOB letiltása | Standard | Gyors elérésű, ritka elérésű Archívum | Mind | Mind |
|1. generációs Azure Data Lake Storage| n.a. | Hierarchikus (fájlrendszer) | n.a. | n.a. | n.a. | 3,6 csak | A HBase kivételével |

* * A HDInsight-fürtök esetében csak a másodlagos Storage-fiókok lehetnek BlobStorage, és az oldal Blobja nem támogatott tárolási lehetőség.

Az Azure Storage-fiókok típusairól további információt az [Azure Storage-fiók áttekintése](../storage/common/storage-account-overview.md) című témakörben talál.

További információ az Azure Storage hozzáférési szintjeiről [: Azure Blob Storage: Premium (előzetes verzió), gyakori, ritka elérésű és archív tárolási szintek](../storage/blobs/storage-blob-storage-tiers.md)

A fürtöket az elsődleges és választható másodlagos tárolók szolgáltatásainak kombinációi segítségével hozhatja létre. A következő táblázat összefoglalja a HDInsight által jelenleg támogatott fürtök tárolási konfigurációit:

| HDInsight verziója | Elsődleges tárterület | Másodlagos tároló | Támogatott |
|---|---|---|---|
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | Data Lake Storage Gen2 | Nem |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Igen |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nem |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Igen |
| 3,6 | Data Lake Storage Gen1 | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nem |
| 4.0 | Data Lake Storage Gen1 | Bármely | Nem |
| 4.0 | Általános célú v1, általános célú v2 | Data Lake Storage Gen1 | Nem |

* = Ez lehet egy vagy több Data Lake Storage Gen2 fiók is, feltéve, hogy minden beállítás ugyanazt a felügyelt identitást használja a fürt eléréséhez.

> [!NOTE]
> Data Lake Storage Gen2 elsődleges tároló nem támogatott a Spark 2,1 vagy 2,2 fürtök esetében.

## <a name="data-replication"></a>Adatreplikáció

Az Azure HDInsight nem tárolja az ügyféladatokat. A fürt elsődleges tárolási módja a társított Storage-fiók. A fürtöt egy meglévő Storage-fiókhoz csatolhatja, vagy létrehozhat egy új Storage-fiókot a fürt létrehozási folyamata során. Új fiók létrehozásakor a rendszer helyileg redundáns tárolási (LRS-) fiókként hozza létre, és kielégíti a régión belüli adattárolási követelményeket, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net)megadott feltételeket is.

Ellenőrizheti, hogy a HDInsight megfelelően van-e konfigurálva egyetlen régióban tárolt adattároláshoz, és gondoskodjon arról, hogy a HDInsight társított Storage-fiók LRS vagy egy másik tárolási beállítás legyen megemlítve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Következő lépések

* [Az Azure Storage áttekintése](./overview-azure-storage.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)
* [Az Azure Data Lake Storage Gen2 áttekintése](./overview-data-lake-storage-gen2.md)
* [Bevezetés a Azure Data Lake Storage Gen2ba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
