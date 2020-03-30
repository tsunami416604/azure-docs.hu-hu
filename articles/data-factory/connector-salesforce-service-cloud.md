---
title: Adatok másolása a Salesforce Service Cloud szolgáltatásból és a Salesforce Service Cloud szolgáltatásba
description: Megtudhatja, hogy miként másolhat adatokat a Salesforce Service Cloud szolgáltatásból a támogatott fogadó adattárakba vagy a támogatott forrásadat-tárolókból a Salesforce Service Cloud szolgáltatásba egy adatfeldolgozó folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 4540b27a9241a14b3d1a153d11bf43900e8ae0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153852"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Adatok másolása a Salesforce Service Cloud szolgáltatásból az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory-ban az adatok másolásához a Salesforce Service Cloud-ból és a Salesforce Service Cloud-ba. A [Másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Salesforce Service Cloud-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Salesforce Service Cloud szolgáltatásból adatokat másolhat bármely támogatott fogadó adattárba. Bármely támogatott forrásadattárból adatokat másolhat a Salesforce Service Cloud szolgáltatásba. Az adattárak listáját, amelyek által támogatott forrásként vagy fogadóként a Másolás tevékenység, lásd: A [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Ez a Salesforce Service Cloud-összekötő különösen a következőket támogatja:

- Salesforce Developer, Professional, Enterprise vagy Unlimited kiadások.
- Adatok másolása a Salesforce éles környezetéből, a sandboxból és az egyéni tartományból.

A Salesforce összekötő a Salesforce REST/Bulk API-ra épül. Alapértelmezés szerint az összekötő [v45-ös](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) t használja az adatok másolásához a Salesforce-ból, és [a v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) segítségével másolja az adatokat a Salesforce-ba. Explicit módon beállíthatja az adatok írására/írására [ `apiVersion` ](#linked-service-properties) használt API-verziót is a csatolt szolgáltatásban.

## <a name="prerequisites"></a>Előfeltételek

Az API-engedélyeket engedélyezni kell a Salesforce-ban. További információ: [API-hozzáférés engedélyezése a Salesforce-ban engedélykészlet alapján](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce-kérelem korlátai

A Salesforce korlátozza az összes API-kérelmet és az egyidejű API-kérelmeket. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, szabályozás történik, és véletlenszerű hibákat lát.
- Ha a kérelmek teljes száma meghaladja a korlátot, a Salesforce-fiók 24 órára le van tiltva.

Mindkét esetben a "REQUEST_LIMIT_EXCEEDED" hibaüzenet jelenhet meg. További információt a [Salesforce fejlesztői korlátainak](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)"API-kérelemkorlátok" című szakaszában talál.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Salesforce Service Cloud-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Salesforce csatolt szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságot **A SalesforceServiceCloud**beállításra kell állítani. |Igen |
| environmentUrl | Adja meg a Salesforce Service Cloud-példány URL-címét. <br> - Az `"https://login.salesforce.com"`alapértelmezett érték. <br> - Adatok sandboxból történő másolásához adja meg a mezőt. `"https://test.salesforce.com"` <br> - Adatok másolásához egyéni tartományból, `"https://[domain].my.salesforce.com"`adja meg például . |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen |
| jelszó |Adja meg a felhasználói fiók jelszavát.<br/><br/>Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatát. <br/><br/>A biztonsági jogkivonatokról általában a [Biztonság és az API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)című témakörben olvashat. A biztonsági jogkivonat csak akkor hagyható ki, ha hozzáadja az integrációs futásidő IP-címét a Salesforce [megbízható IP-címlistájához.](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) Az Azure IR használata esetén tekintse meg az [Azure-integrációs futásidejű IP-címeket.](azure-integration-runtime-ip-addresses.md)<br/><br/>A biztonsági jogkivonat bekérésével és alaphelyzetbe állításával kapcsolatos tudnivalókról [a Biztonsági jogkivonat beszerezése (Get a Security token) (Biztonsági jogkivonat be) és visszaállítása.](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) |Nem |
| apiVersion | Adja meg a használni kívánt Salesforce REST/Bulk `48.0`API verziót, pl. . Alapértelmezés szerint az összekötő [v45-ös](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) t használja az adatok másolásához a Salesforce-ból, és [a v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) segítségével másolja az adatokat a Salesforce-ba. | Nem |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. | Nem a forráshoz, Igen a fogadóhoz, ha a forráscsatolt szolgáltatás nem rendelkezik integrációs futásidejűsel |

>[!IMPORTANT]
>Amikor adatokat másol a Salesforce Service Cloud-ba, az alapértelmezett Azure-integrációs futásidejű nem használható a másolás végrehajtására. Más szóval, ha a forráskapcsolt szolgáltatás nem rendelkezik egy megadott integrációs futásidejű, explicit módon [hozzon létre egy Azure-integrációs futásidejű](create-azure-integration-runtime.md#create-azure-ir) egy helyet a Salesforce Service Cloud-példány közelében. Társítsa a Salesforce Service Cloud kapcsolt szolgáltatás, mint az alábbi példában.

**Példa: Hitelesítő adatok tárolása a Data Factory-ban**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
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

**Példa: Hitelesítő adatok tárolása a Key Vaultban**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz a Salesforce Service Cloud adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a Salesforce Service Cloud szolgáltatásból és a Salesforce Service Cloud szolgáltatásba a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **SalesforceServiceCloudObject (Típustulajdonság)** tulajdonságra kell állítani.  | Igen |
| objectApiName | A Salesforce objektum neve, amelyből adatokat szeretne beolvasni. | Nem a forráshoz, Igen a mosogatóhoz |

> [!IMPORTANT]
> Az **API-név** "__c" része minden egyéni objektumhoz szükséges.

![Data Factory Salesforce kapcsolat API-neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Példa:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **RelationalTable**tulajdonságra kell állítani. | Igen |
| tableName | A tábla neve a Salesforce Service Cloud szolgáltatásban. | Nem (ha a tevékenységforrásban meg van adva a "lekérdezés" ) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Salesforce Service Cloud forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud forrástípusként

Adatok másolása a Salesforce Service Cloud ból, a következő tulajdonságokat támogatja a másolási tevékenység **forrása** szakaszban.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **a SalesforceServiceCloudSource**beállításra kell állítani. | Igen |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. Használhatja [a Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezést vagy az SQL-92 lekérdezést. További tippek a [lekérdezési tippek](#query-tips) szakaszban. Ha a lekérdezés nincs megadva, az adatkészlet "objectApiName" mezőben megadott Salesforce Service Cloud objektum összes adata beolvasásra kerül. | Nem (ha az adatkészletben az "objectApiName" van megadva) |
| olvasásViselkedése | Azt jelzi, hogy le kell-e kérdezni a meglévő rekordokat, vagy le kell-e kérdezni az összes rekordot, beleértve a törölteket is. Ha nincs megadva, az alapértelmezett viselkedés az előbbi. <br>Engedélyezett értékek: **lekérdezés** (alapértelmezett), **queryAll**.  | Nem |

> [!IMPORTANT]
> Az **API-név** "__c" része minden egyéni objektumhoz szükséges.

![Data Factory Salesforce kapcsolat API-név listája](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud mint fogadótípus

Adatok másolásához a Salesforce Service Cloud, a következő tulajdonságok at a másolási tevékenység **fogadó** szakaszban támogatott.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát **A SalesforceServiceCloudSink**beállításra kell állítani. | Igen |
| writeBehavior (írási viselkedés) | A művelet írási viselkedése.<br/>Az engedélyezett értékek: **Insert** és **Upsert**. | Nem (az alapértelmezett beállítás beszúrása) |
| externalIdFieldName | A upsert művelet külső azonosítómezőjének neve. A megadott mezőt "Külső azonosító mezőként" kell definiálni a Salesforce Service Cloud objektumban. Nem lehet NULL érték a megfelelő bemeneti adatokban. | Igen a "Upsert" |
| writeBatchSize | A Salesforce Service Cloud-ba minden kötegben írt adatok sorszáma. | Nem (az alapértelmezett érték 5000) |
| ignoreNullValues | Azt jelzi, hogy az írási művelet során figyelmen kívül kell-e hagyni a bemeneti adatok NULL értékeit.<br/>Az engedélyezett értékek **igazak** és **hamisak.**<br>- **Igaz**: A célobjektumban lévő adatok változatlanok maradnak, ha upsert vagy frissítési műveletet végez. Beszúrási művelet esetén szúrjon be egy definiált alapértelmezett értéket.<br/>- **Hamis**: Frissítse a célobjektum adatait NULL értékre upsert vagy frissítési művelet esetén. Null érték beszúrása beszúrási művelet esetén. | Nem (az alapértelmezett érték hamis) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Tippek lekérdezési tippek

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Adatok beolvasása a Salesforce Service Cloud jelentésből

A Salesforce Service Cloud-jelentésekből adatokat kereshet, ha megad egy lekérdezést a rendszerként. `{call "<report name>"}` Például: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Törölt rekordok beolvasása a Salesforce Service Cloud Lomtárból

Ha a helyreállíthatóan törölt rekordokat a Salesforce `readBehavior` Service `queryAll`Cloud Lomtárból szeretné lekérdezni, megadhatja a ( a ) értéket. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Az SOQL és az SQL lekérdezés szintaxisa közötti különbség

Adatok másolásakor a Salesforce Service Cloud, használhatja soql lekérdezés vagy SQL lekérdezés. Ne feledje, hogy ez a kettő különböző szintaxisés funkciók támogatása, ne keverjük össze. Javasoljuk, hogy használja a Salesforce Service Cloud által natívan támogatott SOQL lekérdezést. Az alábbi táblázat a főbb különbségeket sorolja fel:

| Szintaxis | SOQL mód | SQL mód |
|:--- |:--- |:--- |
| Oszlopkijelölés | A lekérdezésben másolandó mezők et kell felsorolni, pl.`SELECT field1, filed2 FROM objectname` | `SELECT *`az oszlopkijelölés mellett támogatott. |
| Idézőjelek | Az iktatott/objektumnevek nem idézhetők. | A mező-/objektumnevek et meg lehet adni, pl.`SELECT "id" FROM "Account"` |
| Dátumidő formátum |  Lásd a részleteket [itt](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) és mintákat a következő részben. | Lásd a részleteket [itt](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) és mintákat a következő részben. |
| Logikai értékek | Képviseli, `False` `True`és, pl. `SELECT … WHERE IsDeleted=True`. | 0 vagy 1 színben tüntetve, pl. `SELECT … WHERE IsDeleted=1` |
| Oszlop átnevezése | Nem támogatott. | Támogatott, pl.: `SELECT a AS b FROM …`. |
| Kapcsolat | Támogatott, pl. `Account_vod__r.nvs_Country__c`. | Nem támogatott. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Adatok beolvasása a DateTime oszlop where záradékával

Az SOQL vagy SQL lekérdezés megadásakor figyeljen a DateTime formátumkülönbségre. Példa:

* **SOQL minta:**`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Hiba a MALFORMED_QUERY:Csonkolt

Ha bejön hiba "MALFORMED_QUERY: Csonka", általában ez annak köszönhető, hogy van JunctionIdList típusú oszlop az adatok és a Salesforce korlátozza az ilyen adatok támogatása nagy számú sort. A mérséklés érdekében próbálja meg kizárni a JunctionIdList oszlopot, vagy korlátozni a másolni kívánt sorok számát (több másolási tevékenységfuttatásra is particionálhat).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Adattípus-hozzárendelés a Salesforce Service Cloud-hoz

Amikor adatokat másol a Salesforce Service Cloud-ból, a következő leképezések kerülnek a Salesforce Service Cloud adattípusaiból a Data Factory köztes adattípusaiba. Ha többet szeretne tudni arról, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

| Salesforce Service Cloud adattípus | Adatgyár köztes adattípusa |
|:--- |:--- |
| Automatikus szám |Sztring |
| Jelölőnégyzet |Logikai |
| Currency (Pénznem) |Decimal |
| Dátum |DateTime |
| Dátum és idő |DateTime |
| E-mail |Sztring |
| Azonosító |Sztring |
| Keresőkapcsolat |Sztring |
| Többválasztós lista |Sztring |
| Szám |Decimal |
| Százalék |Decimal |
| Telefon |Sztring |
| Picklist |Sztring |
| Szöveg |Sztring |
| Szövegterület |Sztring |
| Szövegterület (hosszú) |Sztring |
| Szövegterület (gazdag) |Sztring |
| Szöveg (titkosított) |Sztring |
| URL-cím |Sztring |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)
