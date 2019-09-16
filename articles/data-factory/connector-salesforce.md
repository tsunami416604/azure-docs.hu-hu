---
title: Adatok másolása a és a Salesforce a Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokból a Salesforce-ből a támogatott fogadó adattárakba vagy a támogatott forrás-adattárakból a Salesforce egy másolási tevékenységgel egy adatfeldolgozó-folyamaton keresztül.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 08d1afb4175a61b70d8e04b19db187bcc87dd129
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010514"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Adatok másolása a és a Salesforce Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-salesforce-connector.md)
> * [Aktuális verzió](connector-salesforce.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Salesforce történő másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Salesforce-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)


Az adatok a Salesforce bármely támogatott fogadó adattárba másolhatók. Az adatok másolása bármely támogatott forrás-adattárból Salesforce is elvégezhető. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Salesforce-összekötő a következőket támogatja:

- Salesforce fejlesztői, Professional, Enterprise vagy Unlimited kiadásokat.
- Adatok másolása a és a rendszerből a Salesforce éles környezetbe, a homokozóba és az egyéni tartományba.

Az Salesforce-összekötő a Salesforce REST/tömeges API-ra épül, és a [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) az adatok másolására és a [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) való másolására szolgál.

## <a name="prerequisites"></a>Előfeltételek

Az API-engedélyt engedélyezni kell a Salesforce-ben. További információ: [API-hozzáférés engedélyezése a Salesforce-ben az engedélyek beállítása alapján](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce kérelmek korlátai

A Salesforce mind az API-kérelmek, mind az egyidejű API-kérelmek esetében korlátozza a korlátot. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, a szabályozás bekövetkezik, és véletlenszerű hibák jelennek meg.
- Ha a kérelmek száma meghaladja a korlátot, a Salesforce-fiók 24 óráig blokkolva lesz.

Előfordulhat, hogy mindkét esetben a "REQUEST_LIMIT_EXCEEDED" hibaüzenetet is megkapja. További információkért tekintse meg a [Salesforce fejlesztői korlátainak](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)"API-kérelmek korlátai" című szakaszát.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Salesforce-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Salesforce társított szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A Type tulajdonságot **Salesforce**értékre kell beállítani. |Igen |
| environmentUrl | Itt adhatja meg az Salesforce-példány URL-címét. <br> – Az `"https://login.salesforce.com"`alapértelmezett érték. <br> – Adatok másolása a homokozóból, a `"https://test.salesforce.com"`következőt kell megadnia:. <br> – Az adatok egyéni tartományból történő másolásához például `"https://[domain].my.salesforce.com"`a következőt kell megadnia:. |Nem |
| username |Adja meg a felhasználói fiók felhasználónevét. |Igen |
| password |A felhasználói fiókhoz tartozó jelszó megadása.<br/><br/>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| securityToken |A felhasználói fiók biztonsági jogkivonatának megadása. A biztonsági jogkivonat alaphelyzetbe állításával és lekérésével kapcsolatos útmutatásért lásd: [biztonsági jogkivonat beszerzése](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). A biztonsági jogkivonatok általános megismeréséhez lásd: [Biztonság és API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nem, forrás, igen, ha a forráshoz társított szolgáltatás nem rendelkezik integrációs futtatókörnyezettel |

>[!IMPORTANT]
>Amikor az Adatmásolást a Salesforce-be másolja, az alapértelmezett Azure Integration Runtime nem használható a másolás végrehajtásához. Más szóval, ha a forráshoz társított szolgáltatás nem rendelkezik megadott integrációs futtatókörnyezettel, explicit módon [hozzon létre egy Azure Integration Runtimet](create-azure-integration-runtime.md#create-azure-ir) a Salesforce-példány közelében. Társítsa a Salesforce társított szolgáltatást az alábbi példában látható módon.

**Példa: Hitelesítő adatok tárolása Data Factoryban**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Hitelesítő adatok tárolása Key Vaultban**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Salesforce adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a és a Salesforce, állítsa az adatkészlet Type (típus) tulajdonságát **SalesforceObject**értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **SalesforceObject**értékre kell beállítani.  | Igen |
| objectApiName | Az Salesforce-objektum neve, amelyből az adatok beolvashatók. | Nincs forrás, a fogadó Igen |

> [!IMPORTANT]
> Az **API-név** "__c" részének minden egyéni objektumhoz szüksége van.

![Data Factory Salesforce-kapcsolatok API-neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Példa:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Visszamenőleges kompatibilitáshoz: Ha a Salesforce-ből másol adatokat, ha az előző "RelationalTable" típusú adatkészletet használja, akkor továbbra is működik, miközben megjelenik egy javaslat, amely az új "SalesforceObject" típusra vált.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **RelationalTable**értékre kell állítani. | Igen |
| tableName | A tábla neve a Salesforce. | Nem (ha a tevékenység forrásában a "Query" érték van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-as-a-source-type"></a>Salesforce

Az adatok Salesforce való másolásához állítsa a forrás típusát a másolás tevékenység **SalesforceSource**értékére. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **SalesforceSource**értékre kell állítani. | Igen |
| query |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Használhatja a [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezést vagy az SQL-92 lekérdezést. További tippeket a [lekérdezési tippek](#query-tips) szakaszban talál. Ha a lekérdezés nincs megadva, a rendszer az adatkészlet "objectApiName" részében megadott Salesforce objektum összes adatát beolvassa. | Nem (ha meg van adva a "objectApiName" az adatkészletben) |
| readBehavior | Azt jelzi, hogy le kell-e kérdezni a meglévő rekordokat, vagy az összes rekordot, beleértve a törölt fájlokat is. Ha nincs megadva, az alapértelmezett viselkedés a korábbi. <br>Engedélyezett értékek: **lekérdezés** (alapértelmezett), **queryAll**.  | Nem |

> [!IMPORTANT]
> Az **API-név** "__c" részének minden egyéni objektumhoz szüksége van.

![Data Factory Salesforce-kapcsolatok API-Neveinak listája](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Visszamenőleges kompatibilitáshoz: Ha a Salesforce-ből másol adatokból, ha az előző "RelationalSource" típusú másolatot használja, a forrás továbbra is működik, miközben megjelenik egy javaslat, amely az új "SalesforceSource" típusra vált.

### <a name="salesforce-as-a-sink-type"></a>Salesforce

Az adatmásoláshoz a másolási tevékenységben állítsa be a Salesforce a **SalesforceSink**. A másolási tevékenység fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **SalesforceSink**értékre kell állítani. | Igen |
| writeBehavior | A művelet írási viselkedése.<br/>Az engedélyezett értékek: **Insert** és **Upsert**. | Nem (az alapértelmezett érték a Beszúrás) |
| externalIdFieldName | A upsert művelet külső azonosító mezőjének neve. A megadott mezőt "külső azonosító mezőként" kell definiálni a Salesforce objektumban. A megfelelő bemeneti adatokban nem szerepelhet NULL érték. | Igen a "Upsert" |
| writeBatchSize | Az egyes kötegekben Salesforce írt adatsorok száma. | Nem (az alapértelmezett érték 5 000) |
| ignoreNullValues | Azt jelzi, hogy az írási művelet során a rendszer figyelmen kívül hagyja-e a bemeneti adatokból származó NULL értékeket.<br/>Az engedélyezett értékek értéke **igaz** és **hamis**.<br>- **Igaz**: Ha upsert vagy frissítési műveletet végez, hagyja változatlanul a célként megadott objektumban lévő adatvesztést. Definiált alapértelmezett érték beszúrása egy beszúrási művelet végrehajtásakor.<br/>- **Hamis**: A upsert vagy a frissítési művelet végrehajtásakor a cél objektumban lévő Adatfrissítés NULL értékűre. Szúrjon be egy NULL értéket a beszúrási művelet végrehajtásakor. | Nem (az alapértelmezett érték hamis) |

**Példa: Salesforce-fogadó egy másolási tevékenységben**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Lekérdezési tippek

### <a name="retrieve-data-from-a-salesforce-report"></a>Adatok beolvasása egy Salesforce-jelentésből

A Salesforce-jelentésekben lévő adatok beolvasása a lekérdezés `{call "<report name>"}`megadásával végezhető el. Például: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Törölt rekordok lekérése a Salesforce Lomtárból

Ha le szeretné kérdezni a Salesforce lomtárának helyreállított törölt rekordjait, `readBehavior` `queryAll`megadhatja a következőt:. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>A SOQL és az SQL-lekérdezési szintaxis közötti különbség

Az adatok Salesforce való másolása során SOQL-lekérdezést vagy SQL-lekérdezést használhat. Vegye figyelembe, hogy ez a kettő eltérő szintaxissal és funkció-támogatással rendelkezik, ne keverje hozzá. Azt javasoljuk, hogy használja a SOQL-lekérdezést, amelyet a Salesforce natív módon támogat. A következő táblázat a fő különbségeket sorolja fel:

| Szintaxis | SOQL mód | SQL Mode |
|:--- |:--- |:--- |
| Oszlop kijelölése | Fel kell sorolni a lekérdezésbe másolandó mezőket, például:`SELECT field1, filed2 FROM objectname` | `SELECT *`az oszlop kijelölése mellett is támogatott. |
| Idézőjelek | A beiktatott/objektumnév nem lehet idézőjelben. | A mező-vagy objektumnév idézőjelek lehetnek, például:`SELECT "id" FROM "Account"` |
| Dátum és idő formátuma |  Tekintse meg [a részleteket és](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) a példákat a következő szakaszban. | Tekintse meg [a részleteket és](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) a példákat a következő szakaszban. |
| Logikai értékek | `False` Például:`SELECT … WHERE IsDeleted=True`. `True` | 0 vagy 1, például `SELECT … WHERE IsDeleted=1`:. |
| Oszlop átnevezése | Nem támogatott. | Támogatott, például: `SELECT a AS b FROM …`. |
| Kapcsolat | Támogatott, például `Account_vod__r.nvs_Country__c`:. | Nem támogatott. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Az adatlekérdezés a DateTime oszlop where záradékának használatával

Ha megadja a SOQL vagy az SQL-lekérdezést, ügyeljen a DateTime formátum különbségére. Példa:

* **SOQL minta**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>MALFORMED_QUERY hiba: csonkolt

Ha a következő hibaüzenetet találta: "MALFORMED_QUERY: Csonkolt ", általában az az oka, hogy a JunctionIdList Type oszlopa van az adathalmazban, és a Salesforce nagy számú sorral támogatja az ilyen adatmennyiséget. A megoldáshoz próbálja meg kizárni a JunctionIdList oszlopot, vagy korlátozza a másolandó sorok számát (több másolási tevékenységre is particionálhat).

## <a name="data-type-mapping-for-salesforce"></a>Adattípusok leképezése Salesforce

Az adatok Salesforce-ből való másolása során a rendszer a következő leképezéseket használja a Salesforce adattípusok között az ideiglenes adattípusok Data Factory. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md).

| Salesforce adattípusa | Data Factory közbenső adattípus |
|:--- |:--- |
| Auto Number |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |DateTime |
| Date/Time |DateTime |
| Email |String |
| Id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |String |
| Text Area |String |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |String |
| URL |Sztring |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
