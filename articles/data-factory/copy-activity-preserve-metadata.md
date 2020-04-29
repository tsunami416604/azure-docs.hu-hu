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
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: 5ce1b85394a7bb604841f7fb941bdebf12c0bca2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414159"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Metaadatok és ACL-ek megőrzése a másolási tevékenység használatával Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha Azure Data Factory másolási tevékenységet használ az adatok forrásról fogadóba való másolásához, az alábbi helyzetekben a metaadatokat és a hozzáférés-vezérlési listákat is megőrizheti.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>A Lake Migration metaadatainak megőrzése

Amikor adatokat telepít át az egyik adattóból egy másikba, beleértve az [Amazon S3](connector-amazon-simple-storage-service.md)-t, az [Azure blobot](connector-azure-blob-storage.md)és a [Azure Data Lake Storage Gen2t](connector-azure-data-lake-storage.md), megtarthatja a fájl metaadatainak megőrzését az adatokkal együtt.

A másolási tevékenység a következő attribútumok megőrzését támogatja az Adatmásolás során:

- **Az ügyfél által megadott metaadatok** 
- Valamint a következő **öt adattár beépített rendszertulajdonsága**: `contentType` `contentLanguage` (az Amazon S3 kivételével) `contentEncoding`,, `contentDisposition`,. `cacheControl`

Ha fájlokat másol az Amazon S3/Azure Data Lake Storage Gen2/Azure blobból a bináris formátumú Azure Data Lake Storage Gen2/Azure blobba, a **megőrzés** **lehetőséget a tevékenység** > -szerzői műveletek vagy a **Beállítások** lap adatok másolása eszközének**Beállítások lapján** tekintheti meg.

![Másolási tevékenység megőrzése metaadatok](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Íme egy példa a másolási tevékenység JSON-konfigurációra `preserve`(lásd:): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>ACL-ek megőrzése Data Lake Storage Gen1/Gen2 és Gen2 között

Ha Azure Data Lake Storage Gen1ról frissít az adatok ADLS Gen2 között Gen2 vagy másol, megtarthatja a POSIX hozzáférés-vezérlési listákat (ACL-eket) az adatfájlokkal együtt. A hozzáférés-vezérléssel kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) és [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](../storage/blobs/data-lake-storage-access-control.md).

A másolási tevékenység támogatja a következő típusú ACL-ek megőrzését az Adatmásolás során. Kiválaszthat egy vagy több típust is:

- **ACL**: a POSIX hozzáférés-vezérlési listák másolása és megőrzése fájlokon és címtárakon. A teljes meglévő ACL-eket a forrásról a fogadóra másolja. 
- **Tulajdonos**: a fájlok és könyvtárak tulajdonos felhasználójának másolása és megőrzése. A felhasználók a fogadó Data Lake Storage Gen2hoz való hozzáférésre van szükség.
- **Csoport**: fájlok és könyvtárak tulajdonosi csoportjának másolása és megőrzése. A felhasználó hozzáférése a fogadó Data Lake Storage Gen2hoz vagy a tulajdonos felhasználóhoz (ha a tulajdonos felhasználó a célcsoport tagja is) szükséges.

Ha egy mappából történő másolást ad meg, Data Factory replikálja a megadott mappára és a benne lévő fájlokra és könyvtárakra vonatkozó ACL `recursive` -eket, ha az igaz értékre van állítva. Ha egyetlen fájlból történő másolást ad meg, a rendszer átmásolja a fájl ACL-jeit.

>[!NOTE]
>Ha a Data Lake Storage Gen1/Gen2 hozzáférés-vezérlési listáinak a Gen2-be való megőrzéséhez ADF-t használ, a rendszer felülírja a fogadó Gen2's tartozó meglévő ACL-eket a megfelelő mappában vagy fájlokban.

>[!IMPORTANT]
>Ha úgy dönt, hogy megőrzi az ACL-eket, győződjön meg arról, hogy elég magas engedélyekkel rendelkezik ahhoz, hogy a Data Factory a fogadó Data Lake Storage Gen2-fiókjával működjenek. Használhatja például a fiók kulcsának hitelesítését, vagy hozzárendelheti a Storage blob-adatok tulajdonosi szerepkörét az egyszerű szolgáltatásnév vagy a felügyelt identitás számára.

Ha a forrást bináris formátumú vagy bináris másolási lehetőséggel Data Lake Storage Gen1/Gen2 konfigurálja, és a fogadót bináris formátummal vagy a bináris másolási lehetőséggel Data Lake Storage Gen2, akkor a **megőrzési** beállítást a adatok másolása eszköz **Beállítások** lapján vagy a tevékenység-létrehozási **tevékenységek** > **beállításainak** másolása lapon találja.

![Data Lake Storage Gen1/Gen2 a Gen2 megőrzése ACL-hez](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Íme egy példa a másolási tevékenység JSON-konfigurációra `preserve`(lásd:): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
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

## <a name="next-steps"></a>További lépések

Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítménye](copy-activity-performance.md)
