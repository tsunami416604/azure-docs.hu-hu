---
title: Támogatott fájlformátumok az Azure Data Factoryban
description: Ez a témakör ismerteti a fájlalapú összekötők által támogatott fájlalapú összekötők által támogatott fájlformátumok és tömörítési kódok at.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439573"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Támogatott fájlformátumok és tömörítési kodekek az Azure Data Factoryban

*Ez a cikk a következő összekötőkre vonatkozik: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)és [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A Másolás [tevékenységgel](copy-activity-overview.md) fájlokat másolhat két fájlalapú adattár között, amely esetben az adatok másolása szerializálás vagy deszerializálás nélkül történik. 

Ezenkívül elemezheti vagy létrehozhatja egy adott formátumú fájlokat is. A következőket hajthatja végre például:

* Adatok másolása egy helyszíni SQL Server-adatbázisból, és írjon az Azure Data Lake Storage Gen2-be Parketta formátumban.
* Fájlok másolása szöveges (CSV) formátumban egy helyszíni fájlrendszerből, és írjon az Azure Blob storage Avro formátumban.
* Tömörített fájlok másolása egy helyszíni fájlrendszerből, kibonthatja őket menet közben, és kibontott fájlokat írhat az Azure Data Lake Storage Gen2-be.
* Adatok másolása Gzip tömörített szöveg (CSV) formátumban az Azure Blob storage-ból, és írja meg az Azure SQL Database-be.
* Sokkal több olyan tevékenység, amely szerializálást/deszerializációt vagy tömörítést/dekompressziót igényel.

## <a name="next-steps"></a>További lépések

Lásd a többi Másolási tevékenység cikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Tevékenységteljesítmény másolása](copy-activity-performance.md)
