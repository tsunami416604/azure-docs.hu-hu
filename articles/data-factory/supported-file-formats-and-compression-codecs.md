---
title: A másolási tevékenység által támogatott fájlformátumok Azure Data Factory
description: Ez a témakör azokat a fájlformátumokat és tömörítési kódokat ismerteti, amelyeket a Azure Data Factory másolási tevékenysége támogat.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: c044208699bf5bebb6383cfef00bf53b744369d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86522439"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Támogatott fájlformátumok és tömörítési kodekek másolási tevékenységgel Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Ez a cikk az alábbi összekötőket érinti [: Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A [másolási tevékenységgel](copy-activity-overview.md) fájlok másolását végezheti el két fájl alapú adattár között, ebben az esetben az Adatmásolást a szerializálás vagy a deszerializálás nélkül is hatékonyan másolja a rendszer. 

Emellett egy adott formátumú fájlokat is elemezheti vagy létrehozhatja. Például a következőket végezheti el:

* Adatok másolása SQL Server-adatbázisból és a Azure Data Lake Storage Gen2ba való írás parketta formátumban.
* Szövegfájlok (CSV) formátumának másolása helyszíni fájlrendszerből és az Azure Blob Storage-ba való írás Avro formátumban.
* Másolhatja a tömörített fájlokat egy helyszíni fájlrendszerből, kibonthatja azokat menet közben, és kibontott fájlokat írhat a Azure Data Lake Storage Gen2ba.
* Adatok másolása gzip tömörített szövegfájlba (CSV) az Azure Blob Storage-ból, és a Azure SQL Databasebe írás.
* Számos további tevékenység, amelyek szerializálást/deszerializálást vagy tömörítést/kibontást igényelnek.

## <a name="next-steps"></a>További lépések

Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység áttekintése](copy-activity-overview.md)
- [Másolási tevékenység teljesítménye](copy-activity-performance.md)
