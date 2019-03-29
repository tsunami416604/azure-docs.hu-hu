---
title: Adatok másolása a Salesforce-hoz, és az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat támogatott fogadó adattárakba, a Salesforce-ból vagy a Salesforce-hoz támogatott forrás adattárakból származó egy data factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: jingwang
ms.openlocfilehash: 601ae4a896c4e52d8a1f4022c92a22988465369c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578475"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Adatok másolása a Salesforce-hoz, és az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-salesforce-connector.md)
> * [Aktuális verzió](connector-salesforce.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban másolhat adatokat, és a Salesforce-hoz. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

A Salesforce-ból adatokat másolhatja bármely támogatott fogadó adattárba. Is másolhatja adatokat bármely támogatott forrás adattárolóból a Salesforce-hoz. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Salesforce-összekötő támogatja:

- Salesforce-fejlesztő, Professional, Enterprise vagy korlátlan kiadások.
- Copying data from and to Salesforce production, sandbox, and custom domain.

A Salesforce-összekötő épül a Salesforce REST API [v39](https://developer.salesforce.com/docs/atlas.en-us.206.0.api_rest.meta/api_rest/resources_list.htm) az adatok másolása és [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) az adatok másolása.

## <a name="prerequisites"></a>Előfeltételek

API-t jogosultsági engedélyezni kell a Salesforce-ban. További információkért lásd: [engedélycsoport által a Salesforce-ban engedélyezése API-hozzáférés](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>A Salesforce kérelmekre vonatkozó korlátok

A Salesforce API-kérések és a párhuzamos API-kérések korlátokkal rendelkeznek. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, szabályozás akkor fordul elő, és véletlen hibákat lát.
- Ha a kérelmek teljes száma meghaladja a korlátot, a Salesforce-fiókban 24 órán keresztül le van tiltva.

Mindkét esetben a "REQUEST_LIMIT_EXCEEDED" hibaüzenet is kaphat. További információkért tekintse meg a "API-t kérelmekre vonatkozó korlátok" szakaszában [Salesforce fejlesztői korlátok](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások a Salesforce-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Salesforce-beli társított szolgáltatás a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot állítsa **Salesforce**. |Igen |
| environmentUrl | Adja meg a Salesforce-példány URL-CÍMÉT. <br> – Alapértelmezett érték a `"https://login.salesforce.com"`. <br> – Adatok másolása a tesztkörnyezetből, adja meg a `"https://test.salesforce.com"`. <br> – Adatok másolása az egyéni tartományt, adja meg, például `"https://[domain].my.salesforce.com"`. |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót.<br/><br/>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Alaphelyzetbe állítása és a egy biztonsági jogkivonatot beolvasni az utasításokért lásd: [egy biztonsági jogkivonatot beolvasni](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Az általános biztonsági jogkivonatokat kapcsolatos további információkért lásd: [biztonsági és API-val](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nincs forrás, Igen a fogadó Ha a forrás-beli társított szolgáltatás nem rendelkezik az integration runtime |

>[!IMPORTANT]
>Adatok másolása a Salesforce-ba, amikor az alapértelmezett Azure integrációs modul másolás végrehajtásához nem használható. Más szóval, ha a forrás-beli társított szolgáltatás nem rendelkezik a megadott integration Runtime-nak explicit módon [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) közel a Salesforce-példány egy helyre. Társítsa a Salesforce-beli társított szolgáltatás az alábbi példában látható módon.

**Példa: Store hitelesítő adatokat a Data Factoryban**

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

**Példa: Hitelesítő adatok Store a Key Vaultban**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Salesforce-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a és a Salesforce-hoz, állítsa be a type tulajdonság, az adatkészlet **SalesforceObject**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **SalesforceObject**.  | Igen |
| objectApiName | A Salesforce objektumnév be adatokat. | Nincs forrás, a fogadó Igen |

> [!IMPORTANT]
> A "__c" részét **API neve** bármely egyéni objektum szükséges.

![Data Factory Salesforce kapcsolat API neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Példa**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Előző verziókkal való kompatibilitás érdekében: Ha az adatok másolása a Salesforce-ból, ha az előző "RelationalTable" típusú adatkészletet használ, bár egy javaslatot, váltson át az új "SalesforceObject" típus tartja működik.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **RelationalTable**. | Igen |
| tableName | A Salesforce-ban a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce-forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-as-a-source-type"></a>Salesforce-forrás típusa

Adatok másolása a Salesforce-ból, állítsa a forrás típusaként a másolási tevékenység **SalesforceSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **SalesforceSource**. | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. Használhat [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) vagy SQL-92 lekérdezés. Tekintse meg a további tippek [lekérdezési tippek](#query-tips) szakaszban. Lekérdezés nem ad meg, ha a rendszer lekéri az "objectApiName" adatkészletben megadott Salesforce-objektum az adatokat. | Nem (ha az adatkészlet "objectApiName" van megadva) |
| readBehavior | Azt jelzi, hogy a meglévő rekordok lekérdezése, vagy lekérdezheti az összes rekord törölve azokat is beleértve. Ha nincs megadva, a korábbi az alapértelmezett viselkedést. <br>Megengedett értékek: **lekérdezés** (alapértelmezett), **queryAll**.  | Nem |

> [!IMPORTANT]
> A "__c" részét **API neve** bármely egyéni objektum szükséges.

![Adatkapcsolat gyári Salesforce API-nevek listája](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Előző verziókkal való kompatibilitás érdekében: Adatok másolása a Salesforce-ból, ha a korábbi "RelationalSource" típusú példány használja, ha a forrás tartja dolgozik, bár egy javaslatot, váltson át az új "SalesforceSource" típus.

### <a name="salesforce-as-a-sink-type"></a>A Salesforce, a fogadó típusa

Adatok másolása a Salesforce-hoz, állítsa a fogadó típusa a másolási tevékenység **SalesforceSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **SalesforceSink**. | Igen |
| WriteBehavior | Az írási viselkedésének a művelethez.<br/>Engedélyezett értékek a következők **beszúrása** és **Upsert**. | Nem (alapértelmezés szerint a Insert) |
| externalIdFieldName | A külsőazonosító-mező az upsert művelet neve. A megadott mezőt kell definiálni a Salesforce-objektum külsőazonosító-mezőjeként"". A megfelelő bemeneti adatokat, nem tartalmazhat NULL értékeket. | Igen, az "Upsert" |
| WriteBatchSize | A sorok száma az egyes kötegekben lévő a Salesforce-hoz írt adatok. | Nem (az alapértelmezett érték 5 000) |
| ignoreNullValues | Azt jelzi, hogy a bemeneti adatok NULL értéket figyelmen kívül a írási művelet során.<br/>Engedélyezett értékek a következők **igaz** és **hamis**.<br>- **Igaz**: Hagyja meg az adatokat a rendeltetési objektum változatlan marad, amikor ezt teszi, hogy egy upsert vagy frissítési műveletben. Helyezze be egy meghatározott alapértelmezett értéket, amikor ezt teszi, hogy egy insert művelet.<br/>- **FALSE (hamis)**: Ekkor egy upsert vagy frissítési műveletben, frissítse az adatokat a rendeltetési objektumban NULL. NULL érték szúrható, amikor ezt teszi, hogy egy insert művelet. | Nem (az alapértelmezett érték FALSE (hamis)) |

**Példa: A másolási tevékenység fogadó Salesforce-ban**

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

### <a name="retrieve-data-from-a-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentés

Kérheti le adatokat a Salesforce-jelentésekből, a lekérdezés megadásával `{call "<report name>"}`. Például: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>A Salesforce Lomtárból törölt rekordok beolvasása

Lekérdezés a helyreállíthatóan törölt rekordok a Salesforce Lomtárból, megadhatja `readBehavior` , `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL és az SQL-lekérdezési szintaxis közötti különbség

Adatok másolása a Salesforce-ból, ha SOQL lekérdezés vagy SQL-lekérdezést is használhatja. Ne feledje, hogy ezt a két különböző szintaxist és a funkciók támogatása, nem használhatók vegyesen. Ön az SOQL lekérdezéssel, amelyet natív módon támogat a Salesforce használata javasolt. Az alábbi táblázat a fő különbség:

| Szintaxis | SOQL Mode | SQL Mode |
|:--- |:--- |:--- |
| Oszlop kiválasztása | A lekérdezés, pl. másolandó mezők számbavétele kell `SELECT field1, filed2 FROM objectname` | `SELECT *` Oszlop kiválasztása mellett támogatott. |
| Idézőjelek között | Nem szerepelhetnek idézőjelek között az iktatott/objektumok nevét. | A mező/objektumok nevét is szerepelhetnek idézőjelek között, például: `SELECT "id" FROM "Account"` |
| Dátum és idő formátumban |  Tekintse meg a részleteket [Itt](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) és a felhozott példák a következő szakaszban. | Tekintse meg a részleteket [Itt](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) és a felhozott példák a következő szakaszban. |
| Logikai értékek | -Kiszolgálókként `False` és `True`, pl. `SELECT … WHERE IsDeleted=True`. | 0 vagy 1, pl. kiszolgálókként `SELECT … WHERE IsDeleted=1`. |
| Oszlop átnevezése | Nem támogatott. | Támogatott, például: `SELECT a AS b FROM …`. |
| Kapcsolat | Támogatott, például `Account_vod__r.nvs_Country__c`. | Nem támogatott. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Adatok beolvasása a where záradék található dátum és idő

A SOQL vagy SQL-lekérdezés megadása esetén a dátum és idő formátumú különbség a figyelmet. Példa:

* **SOQL minta**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformedquerytruncated"></a>Hiba történt a MALFORMED_QUERY: csonkolt

Ha eléri a hiba "MALFORMED_QUERY: "Csonkolva általában van miatt JunctionIdList típusú oszlop a adatokat, és a Salesforce rendelkezik az ilyen adatok nagy számú sort támogató korlátozást. Csökkentése érdekében, próbálja meg JunctionIdList oszlop kizárásához, vagy másolja a sorok számának korlátozásához (particionáló, több másolási tevékenység-végrehajtások).

## <a name="data-type-mapping-for-salesforce"></a>Adattípus-leképezés a Salesforce-hoz

Adatok másolása a Salesforce-ból, ha a Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Salesforce adattípusokat. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatos további információkért lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

| Salesforce-adatok típusa | Data Factory közbenső adattípus |
|:--- |:--- |
| Automatikus száma |String |
| Jelölőnégyzet |Logikai |
| Currency (Pénznem) |Decimal |
| Dátum |DateTime |
| Dátum/idő |DateTime |
| E-mail |String |
| Azonosító |String |
| Keresési kapcsolat |String |
| Többszörös kijelöléses értéklista |String |
| Szám |Decimal |
| Százalék |Decimal |
| Telefonszám |String |
| Értéklista |String |
| Szöveg |String |
| Szövegterület |String |
| Szövegterület (hosszú) |String |
| Szövegterület (részletes) |String |
| Szöveg (titkosítva) |String |
| URL-cím |String |

## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
