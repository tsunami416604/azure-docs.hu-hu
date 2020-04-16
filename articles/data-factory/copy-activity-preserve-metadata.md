---
title: Metaadatok és AC-k megőrzése másolási tevékenység használatával az Azure Data Factoryban
description: Megtudhatja, hogyan őrizheti meg a metaadatokat és az ACL-eket az Azure Data Factory másolási tevékenységhasználatával történő másolási tevékenysége során.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414159"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Metaadatok és AC-k megőrzése másolási tevékenység használatával az Azure Data Factoryban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha az Azure Data Factory másolási tevékenység segítségével másolja az adatokat a forrásból a fogadóba, a következő esetekben is megőrizheti a metaadatokat és a acl-k mentén.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>A tavak áttelepítésének metaadatainak megőrzése

Amikor áttelepíti az adatokat az egyik adattótóból a másikba, beleértve [az Amazon S3,](connector-amazon-simple-storage-service.md) [az Azure Blob](connector-azure-blob-storage.md)és az Azure Data Lake Storage [Gen2](connector-azure-data-lake-storage.md)adatokat, kiválaszthatja, hogy a fájl metaadatait az adatokkal együtt őrizze meg.

A másolási tevékenység támogatja a következő attribútumok megőrzését az adatmásolás során:

- **Az összes ügyfél által megadott metaadat** 
- És a következő **öt adattár beépített rendszer tulajdonságai**: `contentType`, `contentEncoding` `contentDisposition` `cacheControl` `contentLanguage` (kivéve az Amazon S3), , , .

Ha az Amazon S3/Azure Data Lake Storage Gen2/Azure Blob fájljait bináris formátumban másolja az Amazon S3/Azure Data Lake Storage Gen2/Azure Blob rendszerbe, a > **Tevékenységbeállításainak** **másolása**lapon a Tevékenységkészítés hez vagy a Beállítások laphoz az Adatmásolás eszközben található **Megőrzése** lehetőséget találja. **Settings**

![A tevékenységmegőrzés metaadatainak másolása](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Íme egy példa a JSON-konfiguráció `preserve`másolási tevékenységére (lásd): 

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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>A Data Lake Storage Gen1/Gen2-től a Gen2-ig tartó ACL-k megőrzése

Amikor az Azure Data Lake Storage Gen1-ről Gen2-re frissít, vagy adatokat másol az ADLS Gen2 között, megőrizheti a POSIX hozzáférés-vezérlési listákat (ACL-k) az adatfájlokkal együtt. A hozzáférés-vezérlésről további információt az [Azure Data Lake Storage Gen1 hozzáférés-vezérlése](../data-lake-store/data-lake-store-access-control.md) és az Azure Data Lake Storage [Gen2 hozzáférés-vezérléscímű témakörben talál.](../storage/blobs/data-lake-storage-access-control.md)

A másolási tevékenység támogatja a következő típusú ACL-ok megőrzését az adatmásolás során. Egy vagy több típust kiválaszthat:

- **ACL**: PoSIX hozzáférés-vezérlési listák másolása és megőrzése fájlokon és könyvtárakon. It copies the full existing ACLs from source to sink. 
- **Tulajdonos**: Másolja és őrizze meg a fájlok és könyvtárak tulajdonos felhasználóját. A Data Lake Storage Gen2-hez való rendszergazdai hozzáférés szükséges.
- **Csoport**: Fájlok és könyvtárak tulajdonában lévő csoport másolása és megőrzése. A data lake storage gen2 vagy a tulajdonában lévő felhasználó (ha a tulajdonában lévő felhasználó is tagja a célcsoportnak) rendszergazdai hozzáférésszükséges.

Ha egy mappából szeretne másolni, a Data Factory replikálja az adott mappához tartozó `recursive` ACL-ket, valamint az alatta lévő fájlokat és könyvtárakat, ha igaz értékre van állítva. Ha egyetlen fájlból adja meg a másolást, a fájlba tartozó ACL-k másolásra kerülnek.

>[!NOTE]
>Ha az ADF segítségével megőrzi az ACL-eket a Data Lake Storage Gen1/Gen2-től a Gen2-ig, a mosogató Gen2 megfelelő mappájában/fájlján lévő meglévő ACL-ok felülíródnak.

>[!IMPORTANT]
>Ha úgy dönt, hogy megőrzi az ACL-eket, győződjön meg róla, hogy elegendő engedélyt ad a Data Factory-nak ahhoz, hogy a fogadó Data Lake Storage Gen2-fiókjával szemben működjön. Használja például a fiókkulcs-hitelesítést, vagy rendelje hozzá a Storage Blob Data Owner szerepkört az egyszerű szolgáltatáshoz vagy a felügyelt identitáshoz.

Ha a forrást bináris formátumú Data Lake Storage Gen1/Gen2 néven állítja be, és bináris formátumú Data Lake Storage Gen2 vagy bináris másolási lehetőségként, akkor a **Megőrzés** lehetőség megtalálható a **Beállítások** lapon az Adatmásolás eszközben vagy a **Tevékenység** > **beállításai** másolása lapon a tevékenység készítéshez.

![Data Lake Storage Gen1/Gen2 a Gen2 Megőrzése ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Íme egy példa a JSON-konfiguráció `preserve`másolási tevékenységére (lásd): 

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

Lásd a többi Másolási tevékenység cikkeket:

- [Tevékenység másolása – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítménye](copy-activity-performance.md)
