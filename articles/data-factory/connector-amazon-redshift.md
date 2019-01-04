---
title: Adatok másolása az Azure Data Factory használatával az Amazon Redshift |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Amazon redshiftből támogatott fogadó adattárakba az Azure Data Factory használatával kapcsolatban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 686b602828856e75300152c41bfe4c35cd6a8219
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970161"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával az Amazon Redshift
> [!div class="op_single_selector" title1="SelectVálassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuális verzió](connector-amazon-redshift.md)


Ez a cikk az Azure Data Factory a másolási tevékenység használatával adatokat másol egy Amazon Redshift módját ismerteti. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Amazon redshiftből adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Amazon Redshift-összekötő támogatja az adatok beolvasása a lekérdezés vagy beépített módon támogatja a Redshift eltávolítása redshiftből.

> [!TIP]
> Az a legjobb teljesítményt nyújtsák, amikor nagy mennyiségű adat átmásolása a Redshift, fontolja meg a beépített Redshift eltávolítása az Amazon S3 keresztül. Lásd: [használata UNLOAD adatokat másol az Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) című szakasz részletezi.

## <a name="prerequisites"></a>Előfeltételek

* Másolása adatok egy helyszíni adatokhoz, tárolásához használatával [helyi Integration Runtime](create-self-hosted-integration-runtime.md), hozzáférést biztosít az Integration Runtime (a gép használata IP-címe) az Amazon Redshift-fürtön. Lásd: [engedélyezik a hozzáférést a fürthöz](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) útmutatást.
* Adatok másolása az Azure-adattárba, lásd: [Azure Data Center IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653) a Compute IP-cím és az SQL tartományokat használja az Azure data centers.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Amazon Redshift-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Amazon Redshift-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **AmazonRedshift** | Igen |
| kiszolgáló |IP-cím vagy a gazdagép neve az Amazon Redshift-kiszolgáló. |Igen |
| port |Az Amazon Redshift-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem, az alapértelmezett érték 5439 |
| adatbázis |Az Amazon Redshift-adatbázis neve. |Igen |
| felhasználónév |Az adatbázishoz hozzáféréssel rendelkező felhasználó nevét. |Igen |
| jelszó |A felhasználói fiókhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az Amazon Redshift-adatkészletek által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az Amazon Redshift, állítsa be a type tulajdonság, az adatkészlet **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **RelationalTable** | Igen |
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Amazon Redshift-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift-forrás szerint

Adatok másolása az Amazon Redshift, állítsa be a forrás típusaként a másolási tevékenység **AmazonRedshiftSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **AmazonRedshiftSource** | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla. |Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| redshiftunloadsettings beállításaiban | A tulajdonságcsoport Amazon Redshift eltávolítása használatakor. | Nem |
| s3LinkedServiceName | -To-be – használja az ideiglenes tárolóként az Amazon S3 hivatkozik egy "az AmazonS3" típusú társított szolgáltatás neve megadásával. | Igen, ha a MEMÓRIÁBÓL használatával |
| bucketName | Adja meg, az S3 gyűjtőt átmeneti adatok tárolására. Ha nincs megadva, a Data Factory szolgáltatás állít elő, akkor automatikusan.  | Igen, ha a MEMÓRIÁBÓL használatával |

**. Példa: A másolási tevékenység használatával a MEMÓRIÁBÓL Amazon Redshift-forrás**

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

További UNLOAD használatával adatokat másol az Amazon Redshift hatékonyan a következő szakaszban.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Használja az adatok másolása az Amazon Redshift eltávolítása

[MEMÓRIÁBÓL](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) egy Amazon Redshift, amely egy vagy több fájlt, az Amazon Simple Storage Service (Amazon S3) lekérdezés eredményeit is el által biztosított mechanizmus. Által az Amazon redshiftből a nagyméretű másolási ajánlott módja.

**Példa: adatok másolása az Amazon Redshift az Azure SQL Data Warehouse használatával a memóriából való eltávolítása a szakaszos másolás és a polybase segítségével**

Ez a példa a használati eset, a másolási tevékenység kiürítések száma adatainak Amazon Redshift Amazon S3 konfigurált "redshiftunloadsettings beállításaiban", majd az adatok másolása az Amazon S3-ból az Azure-Blobba a "stagingSettings", végül pedig a PolyBase használatával adatok betöltése az SQL Data Az adatraktár. A közbenső formátum megfelelően kezeli a másolási tevékenység.

![Az SQL DW-példányt munkafolyamat, Redshift](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Az Amazon Redshift adattípus-leképezés

Ha az adatok másolása az Amazon Redshift, az Azure Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Amazon Redshift-adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Amazon Redshift-adattípus | Data factory közbenső adattípus |
|:--- |:--- |
| BIGINT |Int64 |
| LOGIKAI ÉRTÉK |Karakterlánc |
| CHAR |Karakterlánc |
| DATE |DateTime |
| TIZEDES TÖRT |tizedes tört |
| A KÉTSZERES PONTOSSÁG |Dupla |
| EGÉSZ SZÁM |Int32 |
| VALÓDI |Önálló |
| SMALLINT |Int16 |
| SZÖVEG |Karakterlánc |
| IDŐBÉLYEG |DateTime |
| VARCHAR |Karakterlánc |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
