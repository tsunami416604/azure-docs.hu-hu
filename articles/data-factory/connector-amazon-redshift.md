---
title: Adatok másolása az Amazon Vöröseltolódásból a Azure Data Factory használatával | Microsoft Docs
description: További információ az Amazon vöröseltolódásról származó adatok másolásáról a Azure Data Factory használatával történő támogatott fogadó adattárakra.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: jingwang
ms.openlocfilehash: c4f04bf8e1003e33a98c44e6776f8cf887a4645b
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090559"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok másolása az Amazon Vöröseltolódásból a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuális verzió](connector-amazon-redshift.md)


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Amazon Vöröseltolódásból való másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Az Amazon vöröseltolódás-összekötő a következő tevékenységeknél támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Amazon vöröseltolódásról származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, ez az Amazon vöröseltolódás-összekötő támogatja a Vöröseltolódásból származó adatok lekérését a lekérdezés vagy a beépített vöröseltolódás támogatásával.

> [!TIP]
> Ha nagy mennyiségű adatnak a Vöröseltolódásból történő másolásakor a legjobb teljesítményt szeretné elérni, érdemes lehet a beépített vöröseltolódást használni az Amazon S3-on keresztül. További részletekért lásd: az [Eltávolítás használata az Amazon vöröseltolódása című szakaszban található adatok másolásához](#use-unload-to-copy-data-from-amazon-redshift) .

## <a name="prerequisites"></a>Előfeltételek

* Ha saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md)használatával másol egy helyszíni adattárba, adja meg Integration Runtime (a gép IP-címe) hozzáférést az Amazon vöröseltolódási fürthöz. További útmutatásért lásd: [hozzáférés engedélyezése a fürthöz](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) .
* Ha Adatmásolást végez egy Azure-adattárba, tekintse meg az Azure-adatközpontok által használt számítási IP-cím és SQL [-tartományok Azure-adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653) című témakört.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Amazon vöröseltolódás-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Amazon vöröseltolódás társított szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **AmazonRedshift** | Igen |
| server |IP address or host name of the Amazon Redshift server. |Igen |
| port |Az Amazon vöröseltolódás-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem, az alapértelmezett érték 5439 |
| database |Az Amazon vöröseltolódás-adatbázis neve. |Igen |
| username |Az adatbázishoz hozzáférő felhasználó neve. |Igen |
| password |A felhasználói fiók jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Amazon Vöröseltolódási adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Amazon Vöröseltolódásból való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AmazonRedshiftTable** | Igen |
| schema | A séma neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| table | A tábla neve. |Nem (Ha a tevékenység forrása az "query" van megadva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. A `schema` és`table` az új számítási feladatok használata. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha a beírt adatkészletet használta `RelationalTable` , a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Amazon vöröseltolódás forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-redshift-as-source"></a>Amazon vöröseltolódás forrásként

Az adatok Amazon Vöröseltolódásból való másolásához állítsa a forrás típusát a másolási tevékenység **AmazonRedshiftSource**. A következő tulajdonságok támogatottak a másolási tevékenység **source** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AmazonRedshiftSource** | Igen |
| query |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Például: select * from MyTable. |Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| redshiftUnloadSettings | Tulajdonság az Amazon vöröseltolódás eltávolításakor. | Nem |
| s3LinkedServiceName | Egy "AmazonS3" típusú társított szolgáltatás nevének megadásával egy olyan Amazon S3-ra hivatkozik, amelyet a rendszer ideiglenes tárolóként használ. | Igen, ha kitöltést használ |
| bucketName | Adja meg az S3 gyűjtőt az ideiglenes adatai tárolásához. Ha nincs megadva, Data Factory a szolgáltatás automatikusan létrehozza azt.  | Igen, ha kitöltést használ |

**Példa: Amazon vöröseltolódás forrása a másolási tevékenységben a kitöltés használatával**

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

További információ a kitöltés használatáról: az adatok Amazon Vöröseltolódásból való hatékony másolása a következő szakaszból.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Az Eltávolítás használata az adatok az Amazon Vöröseltolódásból való másolásához

Az [Eltávolítás](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) az Amazon vöröseltolódás által biztosított mechanizmus, amely képes a lekérdezés eredményeinek egy vagy több fájlba való kitöltésére az Amazon Simple Storage szolgáltatásban (Amazon S3). Az Amazon a nagyméretű adathalmazok Vöröseltolódásból való másolásának módját javasolja.

**Példa: adatok másolása az Amazon Vöröseltolódásból a Azure SQL Data Warehouse a kitöltés, a szakaszos másolás és a bázisterület használatával**

Ebben a példában a másolási tevékenység kitölti az Amazon Vöröseltolódásból az Amazon S3-be a "redshiftUnloadSettings" beállításban konfigurált, majd az adatok az Amazon S3-ból az Azure Blobba való másolását az "stagingSettings" értékben megadott módon Adatraktár. Az összes ideiglenes formátumot a másolási tevékenység megfelelően kezeli.

![Az SQL DW másolási munkafolyamatának vöröseltolódása](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Az Amazon vöröseltolódás adattípusának leképezése

Az Amazon Vöröseltolódásból történő adatmásoláskor a következő leképezések használatosak az Amazon vöröseltolódás adattípusaiból Azure Data Factory átmeneti adattípusokra. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| Amazon vöröseltolódás adattípusa | Data factory közbenső adattípus |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |Sztring |
| CHAR |Sztring |
| DATE |DateTime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXT |Sztring |
| TIMESTAMP |DateTime |
| VARCHAR |Sztring |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
