---
title: Adatok másolása a és a Salesforce
description: Megtudhatja, hogyan másolhat adatokból a Salesforce-ből a támogatott fogadó adattárakba vagy a támogatott forrás-adattárakból a Salesforce egy másolási tevékenységgel egy adatfeldolgozó-folyamaton keresztül.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 89ce253806b4a39b393c74ea96ddc2523d338d84
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926253"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Adatok másolása a és a Salesforce Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-salesforce-connector.md)
> * [Aktuális verzió](connector-salesforce.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Salesforce történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

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

Előfordulhat, hogy mindkét esetben "REQUEST_LIMIT_EXCEEDED" hibaüzenetet is kap. További információkért tekintse meg a [Salesforce fejlesztői korlátainak](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)"API-kérelmek korlátai" című szakaszát.

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Salesforce-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Salesforce társított szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A Type tulajdonságot **Salesforce**értékre kell beállítani. |Igen |
| environmentUrl | Itt adhatja meg az Salesforce-példány URL-címét. <br> – Az alapértelmezett érték `"https://login.salesforce.com"`. <br> – Az adatok a homokozóból való másolásához válassza a `"https://test.salesforce.com"`lehetőséget. <br> – Az adatok egyéni tartományból történő másolásához írja be például a következőt: `"https://[domain].my.salesforce.com"`. |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen |
| jelszó |A felhasználói fiókhoz tartozó jelszó megadása.<br/><br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen |
| securityToken |A felhasználói fiók biztonsági jogkivonatának megadása. A biztonsági jogkivonat alaphelyzetbe állításával és lekérésével kapcsolatos útmutatásért lásd: [biztonsági jogkivonat beszerzése](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). A biztonsági jogkivonatok általános megismeréséhez lásd: [Biztonság és API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem, forrás, igen, ha a forráshoz társított szolgáltatás nem rendelkezik integrációs futtatókörnyezettel |

>[!IMPORTANT]
>Amikor az Adatmásolást a Salesforce-be másolja, az alapértelmezett Azure Integration Runtime nem használható a másolás végrehajtásához. Más szóval, ha a forráshoz társított szolgáltatás nem rendelkezik megadott integrációs futtatókörnyezettel, explicit módon [hozzon létre egy Azure Integration Runtimet](create-azure-integration-runtime.md#create-azure-ir) a Salesforce-példány közelében. Társítsa a Salesforce társított szolgáltatást az alábbi példában látható módon.

**Példa: hitelesítő adatok tárolása Data Factoryban**

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

**Példa: hitelesítő adatok tárolása Key Vaultban**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Salesforce adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a és a Salesforce, állítsa az adatkészlet Type (típus) tulajdonságát **SalesforceObject**értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **SalesforceObject**értékre kell beállítani.  | Igen |
| objectApiName | Az Salesforce-objektum neve, amelyből az adatok beolvashatók. | Nem, forrás, igen, fogadó |

> [!IMPORTANT]
> Az **API-név** "__c" része minden egyéni objektumhoz szükséges.

![Data Factory Salesforce-kapcsolatok API-neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Példa**

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
>A visszamenőleges kompatibilitás érdekében: amikor adatokat másol a Salesforce-ből, ha az előző "RelationalTable" típusú adatkészletet használja, akkor az továbbra is működik, miközben megjelenik egy javaslat, amely az új "SalesforceObject" típusra vált.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **RelationalTable**értékre kell állítani. | Igen |
| tableName | A tábla neve a Salesforce. | Nem (ha a tevékenység forrásában a "Query" érték van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Salesforce forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-as-a-source-type"></a>Salesforce

Az adatok Salesforce való másolásához állítsa a forrás típusát a másolás tevékenység **SalesforceSource**értékére. A másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **SalesforceSource**értékre kell állítani. | Igen |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Használhatja a [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezést vagy az SQL-92 lekérdezést. További tippeket a [lekérdezési tippek](#query-tips) szakaszban talál. Ha a lekérdezés nincs megadva, a rendszer az adatkészlet "objectApiName" részében megadott Salesforce objektum összes adatát beolvassa. | Nem (ha meg van adva a "objectApiName" az adatkészletben) |
| readBehavior | Azt jelzi, hogy le kell-e kérdezni a meglévő rekordokat, vagy az összes rekordot, beleértve a törölt fájlokat is. Ha nincs megadva, az alapértelmezett viselkedés a korábbi. <br>Engedélyezett értékek: **lekérdezés** (alapértelmezett), **queryAll**.  | Nem |

> [!IMPORTANT]
> Az **API-név** "__c" része minden egyéni objektumhoz szükséges.

![Data Factory Salesforce-kapcsolatok API-Neveinak listája](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Példa**

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
>Visszamenőleges kompatibilitás esetén: Ha a Salesforce adatok másolása után az előző "RelationalSource" típusú másolatot használja, a forrás továbbra is működik, miközben megjelenik egy javaslat, amely az új "SalesforceSource" típusra vált.

### <a name="salesforce-as-a-sink-type"></a>Salesforce

Az adatmásoláshoz a másolási tevékenységben állítsa be a Salesforce a **SalesforceSink**. A másolási tevékenység fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **SalesforceSink**értékre kell állítani. | Igen |
| writeBehavior | A művelet írási viselkedése.<br/>Az engedélyezett értékek: **Insert** és **Upsert**. | Nem (az alapértelmezett érték a Beszúrás) |
| externalIdFieldName | A upsert művelet külső azonosító mezőjének neve. A megadott mezőt "külső azonosító mezőként" kell definiálni a Salesforce objektumban. A megfelelő bemeneti adatokban nem szerepelhet NULL érték. | Igen a "Upsert" |
| writeBatchSize | Az egyes kötegekben Salesforce írt adatsorok száma. | Nem (az alapértelmezett érték 5 000) |
| ignoreNullValues | Azt jelzi, hogy az írási művelet során a rendszer figyelmen kívül hagyja-e a bemeneti adatokból származó NULL értékeket.<br/>Az engedélyezett értékek értéke **igaz** és **hamis**.<br>- **true (igaz**): az upsert vagy a frissítési művelet végrehajtásakor a célobjektum nem módosul. Definiált alapértelmezett érték beszúrása egy beszúrási művelet végrehajtásakor.<br/>- **hamis**: a upsert vagy frissítési művelet végrehajtásakor a célként megadott objektumban lévő ADATFRISSÍTÉS NULL értékűre. Szúrjon be egy NULL értéket a beszúrási művelet végrehajtásakor. | Nem (az alapértelmezett érték hamis) |

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

A Salesforce-jelentésekben lévő adatok lekéréséhez a lekérdezés `{call "<report name>"}`ként való megadásával adható meg. Például: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Törölt rekordok lekérése a Salesforce Lomtárból

Ha le szeretné kérdezni a Salesforce Lomtárában lévő nem törölt rekordokat, megadhatja `readBehavior` `queryAll`ként. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>A SOQL és az SQL-lekérdezési szintaxis közötti különbség

Az adatok Salesforce való másolása során SOQL-lekérdezést vagy SQL-lekérdezést használhat. Vegye figyelembe, hogy ez a kettő eltérő szintaxissal és funkció-támogatással rendelkezik, ne keverje hozzá. Azt javasoljuk, hogy használja a SOQL-lekérdezést, amelyet a Salesforce natív módon támogat. A következő táblázat a fő különbségeket sorolja fel:

| Szintaxis | SOQL mód | SQL-mód |
|:--- |:--- |:--- |
| Oszlop kijelölése | Fel kell sorolni a lekérdezésbe másolandó mezőket, például `SELECT field1, filed2 FROM objectname` | az oszlop kijelölése mellett `SELECT *` is támogatott. |
| Idézőjelek | A beiktatott/objektumnév nem lehet idézőjelben. | A mező-vagy objektumnév idézőjelek lehetnek, például `SELECT "id" FROM "Account"` |
| Dátum és idő formátuma |  Tekintse meg [a részleteket és](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) a példákat a következő szakaszban. | Tekintse meg [a részleteket és](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) a példákat a következő szakaszban. |
| Logikai értékek | `False` és `True`jelöli, például `SELECT … WHERE IsDeleted=True`. | 0 vagy 1, például `SELECT … WHERE IsDeleted=1`. |
| Oszlop átnevezése | Nem támogatott. | Támogatott, például: `SELECT a AS b FROM …`. |
| Relationship | Támogatott, például `Account_vod__r.nvs_Country__c`. | Nem támogatott. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Az adatlekérdezés a DateTime oszlop where záradékának használatával

Ha megadja a SOQL vagy az SQL-lekérdezést, ügyeljen a DateTime formátum különbségére. Példa:

* **SOQL minta**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>MALFORMED_QUERY hiba: csonkolt

Ha a "MALFORMED_QUERY: csonkolt" hibaüzenet jelenik meg, általában az az oka, hogy a JunctionIdList Type oszlopa van az adathalmazban, és a Salesforce korlátozást tartalmaz az ilyen típusú, nagy mennyiségű sort tartalmazó adatmennyiségre. A megoldáshoz próbálja meg kizárni a JunctionIdList oszlopot, vagy korlátozza a másolandó sorok számát (több másolási tevékenységre is particionálhat).

## <a name="data-type-mapping-for-salesforce"></a>Adattípusok leképezése Salesforce

Az adatok Salesforce-ből való másolása során a rendszer a következő leképezéseket használja a Salesforce adattípusok között az ideiglenes adattípusok Data Factory. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md).

| Salesforce adattípusa | Data Factory időközi adattípus |
|:--- |:--- |
| Automatikus szám |Sztring |
| Jelölőnégyzet |Logikai |
| Currency (Deviza) |Decimális |
| Dátum |Dátum és idő |
| Dátum/idő |Dátum és idő |
| E-mail cím |Sztring |
| Azonosító |Sztring |
| Keresési kapcsolat |Sztring |
| Többszörös kijelölési lista |Sztring |
| Szám |Decimális |
| Százalék |Decimális |
| Telefonszám |Sztring |
| Picklist |Sztring |
| Szöveg |Sztring |
| Szövegterület |Sztring |
| Szövegterület (hosszú) |Sztring |
| Szövegterület (Rich) |Sztring |
| Szöveg (titkosított) |Sztring |
| URL-cím |Sztring |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
