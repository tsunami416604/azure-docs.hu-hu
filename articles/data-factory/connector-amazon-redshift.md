---
title: "Adatok másolása az Azure Data Factory használatával Amazon Redshift |} Microsoft Docs"
description: "További tudnivalók az adatok másolása az Amazon Redshift támogatott fogadó adattárolókhoz Azure Data Factory használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 13b317b05e56554e4f6b74a3ecfd3bc268333db0
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Amazon Redshift
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-amazon-redshift-connector.md)
> * [2. verzió – Előzetes verzió](connector-amazon-redshift.md)


Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az Amazon Redshift. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Amazon Redshift connnector a V1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Amazon Redshift adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez Amazon Redshift az összekötő támogatja, lekérdezés vagy beépített Redshift UNLOAD támogatási Redshift az adatok lekérdezése.

> [!TIP]
> A legjobb teljesítmény eléréséhez amikor nagy mennyiségű adat másolása Redshift, fontolja meg a beépített Redshift UNLOAD Amazon S3 keresztül. Lásd: [használata UNLOAD adatok másolása Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) című szakaszban talál információt.

## <a name="prerequisites"></a>Előfeltételek

* Másolása egy helyszíni adatokhoz adattároló használatával [Self-hosted integrációs futásidejű](create-self-hosted-integration-runtime.md), Amazon Redshift fürthöz hozzáférést engedélyez integrációs futásidejű (használata IP-cím a gép). Lásd: [engedélyezi a hozzáférést a fürthöz](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) utasításokat.
* Adatok másolása az Azure data tárolóhoz, lásd: [Azure Data Center IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) számítási IP-cím és az Azure-adatok által használt SQL-címtartományok szolgáltatásban.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják való Amazon Redshift összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Amazon Redshift kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AmazonRedshift** | Igen |
| kiszolgáló |Kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét az Amazon Redshift. |Igen |
| port |A TCP-portot, amelyen az Amazon Redshift kiszolgáló ügyfélkapcsolatokat száma. |Nem, az alapértelmezett érték 5439 |
| adatbázis |Az Amazon Redshift adatbázis nevét. |Igen |
| felhasználónév |Felhasználó, aki hozzáfér az adatbázis neve. |Igen |
| jelszó |A felhasználói fiók jelszavát. Ez a mező megjelölése a SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Amazon Redshift dataset által támogatott tulajdonságokról.

Adatok másolása az Amazon Redshift, állítsa be a type tulajdonságot az adathalmaz **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
| tableName | Az Amazon Redshift a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Amazon Redshift forrás által támogatott tulajdonságokról.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift forrásaként

Adatok másolása Amazon Redshift, állítsa be a forrás típusa a másolási tevékenység **AmazonRedshiftSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **AmazonRedshiftSource** | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: Válasszon * from tábla. |Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| redshiftUnloadSettings | A tulajdonságcsoport Amazon Redshift UNLOAD használatakor. | Nem |
| s3LinkedServiceName | Az Amazon S3 to-be használt ideiglenes tárolóként történő hivatkozik a társított szolgáltatás neve "AmazonS3" típusú megadásával. | Igen, ha UNLOAD használatával |
| bucketName | Adja meg a S3 gyűjtő az átmeneti adatok tárolásához. Ha nem ad meg, Data Factory szolgáltatásnak állít elő, akkor automatikusan.  | Igen, ha UNLOAD használatával |

**Példa: Amazon Redshift forrás a másolási tevékenység UNLOAD használatával**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

További adatok másolása Amazon Redshift hatékonyan a következő szakaszban UNLOAD használatával.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Adatok másolása az Amazon Redshift UNLOAD segítségével

[EL](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) egy Amazon Redshift, amely egy vagy több fájl Amazon egyszerű tárolási szolgáltatás (Amazon S3) lekérdezés eredményeit is el által biztosított mechanizmus. A nagy adatkészlet másolását Redshift Amazon által ajánlott módja.

**Példa: adatok másolása az Amazon Redshift az Azure SQL Data Warehouse használatával eltávolítani a MEMÓRIÁBÓL, előkészített másolási és a PolyBase**

Ez a minta a használati eset, a másolási tevékenység eltávolítást adatainak Amazon Redshift Amazon S3 be "redshiftUnloadSettings", majd az adatok másolása az Amazon S3 Azure Blob a "stagingSettings", végül pedig a PolyBase segítségével adatok betöltése az SQL Data Adatraktár. A közbenső formátumot megfelelően kezeli másolási tevékenység.

![Az SQL DW másolás munkafolyamat Redshift](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Az Amazon Redshift adattípus-leképezés

Az adatok másolása Amazon Redshift, amikor az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak Amazon Redshift adattípusokat. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Amazon Redshift adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| BIGINT |Int64 |
| LOGIKAI ÉRTÉK |Karakterlánc |
| KARAKTER |Karakterlánc |
| DATE |DateTime |
| DECIMÁLIS |Decimális |
| A KÉTSZERES PONTOSSÁG |Dupla |
| EGÉSZ SZÁM |Int32 |
| VALÓS |Egyedülálló |
| SMALLINT |Int16 |
| SZÖVEG |Karakterlánc |
| IDŐBÉLYEG |DateTime |
| VARCHAR |Karakterlánc |

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).
