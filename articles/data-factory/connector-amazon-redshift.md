---
title: Adatok másolása az Amazon Redshift-ből
description: Ismerje meg, hogyan másolhat adatokat az Amazon Redshift-ből a támogatott fogadó adattárakba az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: ce63da745fb84ebccd57b246fc934f595dd7cda1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418252"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok másolása az Amazon Redshift szolgáltatásból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuális verzió](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása egy Amazon Redshift. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Amazon Redshift csatlakozó a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az Amazon Redshift-ből adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez az Amazon Redshift összekötő támogatja az adatok lekérését a Redshift-ből lekérdezés vagy a beépített Redshift UNLOAD támogatás használatával.

> [!TIP]
> A legjobb teljesítmény elérése érdekében, amikor nagy mennyiségű adatot másol a Redshiftből, fontolja meg a beépített Redshift UNLOAD használatát az Amazon S3-on keresztül. A részletekért olvassa [el az Adatok másolása az Amazon Redshift szakaszból az IRAK használata](#use-unload-to-copy-data-from-amazon-redshift) című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Ha önkiszolgáló [integrációs futásidejű](create-self-hosted-integration-runtime.md)használatával adatokat másol egy helyszíni adattárba, adja meg az Integrációs futásidejű (a gép IP-címét) az Amazon Redshift fürthöz való hozzáférést. Az utasításokat [a fürthöz való hozzáférés engedélyezése](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) című témakörben találja.
* Ha adatokat másol egy Azure-adattárba, tekintse meg az [Azure Data Center IP-tartományok](https://www.microsoft.com/download/details.aspx?id=41653) a számítási IP-cím és az Azure-adatközpontok által használt SQL-tartományok.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Amazon Redshift-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Amazon Redshift-hez csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **AmazonRedshift** | Igen |
| kiszolgáló |Az Amazon Redshift kiszolgáló IP-címe vagy állomásneve. |Igen |
| port |Az Amazon Redshift kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port száma. |Nem, az alapértelmezett érték 5439 |
| adatbázis |Az Amazon Redshift adatbázis neve. |Igen |
| felhasználónév |Az adatbázishoz hozzáféréssel rendelkező felhasználó neve. |Igen |
| jelszó |A felhasználói fiók jelszava. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz az Amazon Redshift adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Amazon Redshift adatainak másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **AmazonRedshiftTable** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Használja `schema` `table` és új munkaterheléshez. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

Ha gépelt adatkészletet használt, `RelationalTable` továbbra is támogatott, amíg a rendszer az újat a jövőben használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Amazon Redshift forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift mint forrás

Ha adatokat szeretne másolni az Amazon Redshift-ből, állítsa be a forrástípusát a másolási tevékenységben az **AmazonRedshiftSource elemre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **AmazonRedshiftSource** | Igen |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. Például: válassza a * lehetőséget a MyTable táblából. |Nem (ha az adatkészletben a "tableName" van megadva) |
| redshiftUnloadSettings | Ingatlancsoport az Amazon Redshift UNLOAD használatakor. | Nem |
| s3LinkedServiceName | Egy ideiglenes tárolóként használandó Amazon S3-ra utal az "AmazonS3" típusú összekapcsolt szolgáltatásnevének megadásával. | Igen, ha az UNLOAD |
| bucketName (vödörneve) | Az ideiglenes adatok tárolásához adja meg az S3 gyűjtőt. Ha nincs megadva, a Data Factory szolgáltatás automatikusan létrehozza azt.  | Igen, ha az UNLOAD |

**Példa: Amazon Redshift forrás másolási tevékenység segítségével UNLOAD**

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

Tudjon meg többet arról, hogyan használhatja a UNLOAD segítségével az Amazon Redshift adatainak hatékony másolását a következő szakaszból.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Adatok másolása az Amazon Redshift-ből az UNLOAD használatával

[Az UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) az Amazon Redshift által biztosított mechanizmus, amely az Amazon Simple Storage Service (Amazon S3) egy vagy több fájljára vonatkozó lekérdezés eredményeit ürítheti ki. Ez az amazon által ajánlott módszer a redshift nagy adatkészletének másolásához.

**Példa: adatok másolása az Amazon Redshift-ből az Azure SQL Data Warehouse-ba unload, szakaszos másolás és PolyBase használatával**

Ebben a mintahasználati esetben a másolási tevékenység eltávolítja az adatokat az Amazon Redshift-ről az Amazon S3-ra a "redshiftUnloadSettings" beállítással konfigurálva, majd másolja az adatokat az Amazon S3-ból az Azure Blobba az "átmeneti beállítások" párbeszédpanelen megadott módon, végül használja a PolyBase-t az SQL Data Warehouse-ba való adatok betöltéséhez. Az összes köztes formátumot megfelelően kezeli a másolási tevékenység.

![Redshift az SQL DW másolási munkafolyamat](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Adattípus-leképezés az Amazon Redshift hez

Az Amazon Redshift adatainak másolásakor a következő leképezések kerülnek felhasználásra az Amazon Redshift adattípusokból az Azure Data Factory köztes adattípusaiba. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| Amazon Redshift adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| BIGINT között |Int64 |
| Logikai |Sztring |
| Char |Sztring |
| DATE |DateTime |
| Decimális |Decimal |
| DUPLA PONTOSSÁG |Double |
| EGÉSZ SZÁM |Int32 |
| VALÓS SZÁM |Egyirányú |
| SMALLINT között |Int16 |
| TEXT |Sztring |
| Időbélyeg |DateTime |
| Varchar |Sztring |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
