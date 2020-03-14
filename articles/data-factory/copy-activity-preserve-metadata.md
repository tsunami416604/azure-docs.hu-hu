---
title: Metaadatok és ACL-ek megőrzése a másolási tevékenység használatával Azure Data Factory
description: Tudnivalók a metaadatok és az ACL-ek megőrzéséről a másolási tevékenység Azure Data Factoryban való használata során.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260838"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Metaadatok és ACL-ek megőrzése a másolási tevékenység használatával Azure Data Factory

Ha Azure Data Factory másolási tevékenységet használ az adatok forrásról fogadóba való másolásához, az alábbi helyzetekben a metaadatokat és a hozzáférés-vezérlési listákat is megőrizheti.

## <a name="preserve-metadata"></a>A Lake Migration metaadatainak megőrzése

Amikor adatokat telepít át az egyik adattóból egy másikba, beleértve az [Amazon S3](connector-amazon-simple-storage-service.md)-t, az [Azure blobot](connector-azure-blob-storage.md)és a [Azure Data Lake Storage Gen2t](connector-azure-data-lake-storage.md), megtarthatja a fájl metaadatainak megőrzését az adatokkal együtt.

A másolási tevékenység a következő attribútumok megőrzését támogatja az Adatmásolás során:

- **Az ügyfél által megadott metaadatok** 
- A következő **öt adattár beépített rendszertulajdonsága**: `contentType`, `contentLanguage` (az Amazon S3 kivételével), `contentEncoding`, `contentDisposition`, `cacheControl`.

Ha fájlokat másol az Amazon S3/Azure Data Lake Storage Gen2/Azure Blobból a bináris formátumú Azure Data Lake Storage Gen2/Azure Blobba, a **megőrzés** lehetőséget a tevékenység-szerző vagy a adatok másolása eszköz **Beállítások** lapján található **Másolás tevékenység** > **Beállítások** lapon találja.

![Másolási tevékenység megőrzése metaadatok](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Az alábbi példa a másolási tevékenység JSON-konfigurációját (lásd: `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls"></a>ACL-ek megőrzése Data Lake Storage Gen1ról Gen2

Ha Azure Data Lake Storage Gen1ról a Gen2-re frissít, dönthet úgy, hogy megőrzi a POSIX hozzáférés-vezérlési listákat (ACL-eket) az adatfájlokkal együtt. A hozzáférés-vezérléssel kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) és [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../storage/blobs/data-lake-storage-access-control.md).

A másolási tevékenység támogatja a következő típusú ACL-ek megőrzését az Adatmásolás során. Kiválaszthat egy vagy több típust is:

- **ACL**: a POSIX hozzáférés-vezérlési listák másolása és megőrzése fájlokon és címtárakon. A teljes meglévő ACL-eket a forrásról a fogadóra másolja. 
- **Tulajdonos**: a fájlok és könyvtárak tulajdonos felhasználójának másolása és megőrzése. A felhasználók a fogadó Data Lake Storage Gen2hoz való hozzáférésre van szükség.
- **Csoport**: fájlok és könyvtárak tulajdonosi csoportjának másolása és megőrzése. A felhasználó hozzáférése a fogadó Data Lake Storage Gen2hoz vagy a tulajdonos felhasználóhoz (ha a tulajdonos felhasználó a célcsoport tagja is) szükséges.

Ha egy mappából történő másolást ad meg, Data Factory replikálja az adott mappához tartozó ACL-eket és a benne található fájlokat és könyvtárakat, ha a `recursive` igaz értékre van állítva. Ha egyetlen fájlból történő másolást ad meg, a rendszer átmásolja a fájl ACL-jeit.

>[!NOTE]
>Ha a Data Lake Storage Gen1 hozzáférés-vezérlési listái a Gen2-be való megőrzéséhez ADF-t használ, a rendszer felülírja a meglévő ACL-eket a Gen2's megfelelő mappájában/fájljaiban.

>[!IMPORTANT]
>Ha úgy dönt, hogy megőrzi az ACL-eket, győződjön meg arról, hogy elég magas engedélyekkel rendelkezik ahhoz, hogy a Data Factory a fogadó Data Lake Storage Gen2-fiókjával működjenek. Használhatja például a fiók kulcsának hitelesítését, vagy hozzárendelheti a Storage blob-adatok tulajdonosi szerepkörét az egyszerű szolgáltatásnév vagy a felügyelt identitás számára.

Ha bináris formátumú vagy bináris másolási lehetőséggel rendelkező Data Lake Storage Gen1ként konfigurálja a forrást, és a fogadót bináris formátummal vagy a bináris másolási lehetőséggel Data Lake Storage Gen2, a **megőrzés** lehetőséget a adatok másolása eszköz **Beállítások** lapján, vagy a **másolási tevékenység** > **Beállítások** lapján tekintheti meg a tevékenységek létrehozásához.

![Data Lake Storage Gen1 a Gen2 megőrzése ACL-hez](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Az alábbi példa a másolási tevékenység JSON-konfigurációját (lásd: `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység egyéb cikkekben talál:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítménye](copy-activity-performance.md)
