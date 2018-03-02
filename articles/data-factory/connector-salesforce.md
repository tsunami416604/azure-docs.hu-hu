---
title: "Adatok másolása a kezdő és a Salesforce Azure Data Factory használatával |} Microsoft Docs"
description: "Ismerje meg az adatok másolása támogatott fogadó adattárolókhoz Salesforce vagy a támogatott forráshierarchiából adatokat tárolja a Salesforce a másolási tevékenység használatával a data factory-folyamathoz."
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
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: 3d48f1f3df7b626ec33b07b6275581821453f626
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Adatok másolása a kezdő és a Salesforce Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-salesforce-connector.md)
> * [2. verzió – Előzetes verzió](connector-salesforce.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factory a kezdő és a Salesforce-adatok másolása. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory, amely általában a rendelkezésre álló, 1 verzióját használja [Salesforce-összekötőt a 1-es verziójú](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok bármely támogatott fogadó adattárolóhoz Salesforce másolhatja. Akkor is átmásolhatja adatok bármely támogatott forrás adattár Salesforce. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Salesforce összekötő támogatja:

- Salesforce fejlesztői, Professional, Enterprise vagy korlátlan kiadások.
- Adatok másolása a kezdő és a Salesforce éles, védőfal és az egyéni tartomány.

## <a name="prerequisites"></a>Előfeltételek

A Salesforce-ban API engedély engedélyezni kell. További információkért lásd: [engedélycsoport által a Salesforce-ban engedélyezése API-hozzáférés](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce kérelmekre vonatkozó korlátok

Salesforce rendelkezik mind az API-kérelmek teljes száma, és a egyidejű API-kérés. Vegye figyelembe a következő szempontokat:

- Egyidejű kérelmek száma meghaladja a korlátot, ha sávszélesség-szabályozás következik be, és véletlen hibákat látja.
- Ha a kérelmek teljes száma túllépi a korlátot, a Salesforce-fiókban 24 órán át le van tiltva.

A "REQUEST_LIMIT_EXCEEDED" hibaüzenet mindkét forgatókönyvet is akkor fordulhat elő. További információkért lásd: a "API kérelmekre vonatkozó korlátokat" szakasz [Salesforce fejlesztői korlátok](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják, amelyek segítségével határozhatók meg az adott adat-előállító entitásokat és a Salesforce-összekötő tulajdonságait.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A Salesforce csatolt szolgáltatás a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot meg kell **Salesforce**. |Igen |
| environmentUrl | Adja meg a Salesforce-példány URL-CÍMÉT. <br> -Alapértelmezett érték a `"https://login.salesforce.com"`. <br> -Adatok másolása az védőfal, adja meg a `"https://test.salesforce.com"`. <br> -Adatok másolása az egyéni tartományt, adja meg, például `"https://[domain].my.salesforce.com"`. |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót.<br/><br/>Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Alaphelyzetbe állítása és egy biztonsági jogkivonatot beolvasni, lásd: [egy biztonsági jogkivonatot beolvasni](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Általános biztonsági jogkivonatokat kapcsolatos további tudnivalókért lásd: [biztonsági és az API-t](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nincs forrás, Igen a fogadó Ha a forrás társított szolgáltatás nem rendelkezik integrációs futásidejű |

>[!IMPORTANT]
>Amikor a Salesforce adatait átmásolja, az alapértelmezett Azure integrációs futásidejű nem hajthatók végre másolása. Ez azt jelenti, ha a forrás társított szolgáltatás nem rendelkezik a megadott integrációs futásidejű explicit módon [hozzon létre egy Azure integrációs futásidejű](create-azure-integration-runtime.md#create-azure-ir) közelében a Salesforce-példány hellyel rendelkező. Társítsa a Salesforce kapcsolódó szolgáltatás az alábbi példában látható módon.

**Példa: Adattárolóhoz használandó hitelesítő adatok adat-előállítóban**

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

**Példa: Hitelesítő adatok tárolása a Key Vault**

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a rész felsorolja a Salesforce-adatkészlet által támogatott tulajdonságokról.

Adatok másolása a kezdő és a Salesforce állítsa be a type tulajdonságot az adathalmaz **SalesforceObject**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **SalesforceObject**.  | Igen |
| objectApiName | A Salesforce objektum nevét az adatok lekéréséhez. | Nem a forrást, a fogadó Igen |

> [!IMPORTANT]
> A "__c" részét **API-név** bármilyen egyéni objektum szükséges.

![Adatkapcsolat gyári Salesforce API neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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
>A visszamenőleges kompatibilitás érdekében: másolt adatok a Salesforce, ha az előző "RelationalTable" típusú adatkészlet, akkor tartja a munkát, amíg megjelenik egy javaslat váltson át az új "SalesforceObject" típus.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani **RelationalTable**. | Igen |
| tableName | A Salesforce-ban a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce-forrás és a fogadó által támogatott tulajdonságokról listáját tartalmazza.

### <a name="salesforce-as-a-source-type"></a>A forrás típusaként Salesforce

Adatok másolása Salesforce, állítsa be a forrás típusa a másolási tevékenység **SalesforceSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani **SalesforceSource**. | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. Használhatja az SQL-92 lekérdezés vagy [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |
| readBehavior | Azt jelzi, hogy a meglévő rekordok lekérdezése, vagy nem kérdezhető le minden rekordot, beleértve a törölt néhányat a meglévők közül. Ha nincs megadva, az alapértelmezés lesz a korábbi. <br>Megengedett értékek: **lekérdezés** (alapértelmezett), **queryAll**.  | Nem |

> [!IMPORTANT]
> A "__c" részét **API-név** bármilyen egyéni objektum szükséges.

![Adatkapcsolat gyári Salesforce API listája](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>A visszamenőleges kompatibilitás érdekében: másolt adatok a Salesforce, ha az előző "RelationalSource" típus példányát használja, amíg megjelenik egy javaslat váltson át az új "SalesforceSource" típus működik-e a forrás tartja.

### <a name="salesforce-as-a-sink-type"></a>A fogadó típusa Salesforce

Adatok másolása Salesforce, állítsa be a fogadó típusa a másolási tevékenység **SalesforceSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani **SalesforceSink**. | Igen |
| WriteBehavior | Az írási viselkedésének a művelethez.<br/>Két érték engedélyezett **beszúrása** és **Upsert**. | Nem (alapértelmezett érték Insert) |
| externalIdFieldName | A külső azonosító mezőben a upsert művelet neve. A megadott mező "Külső azonosító mezője" a Salesforce-objektum definiálni kell. A megfelelő bemeneti adatok azt nem lehet NULL értékeket. | Igen, a "Upsert" |
| writeBatchSize | A sorok számát az egyes kötegekben Salesforce írt adatok. | Nem (alapértelmezett érték 5 000) |
| ignoreNullValues | Azt jelzi, hogy figyelmen kívül hagyja a bemeneti adat NULL értékek írási művelet során.<br/>Két érték engedélyezett **igaz** és **hamis**.<br>- **Igaz**: változatlanul a célobjektum lévő adatokat egy upsert vagy az update művelet végrehajtásakor. Szúrja be egy meghatározott alapértelmezett értéket, ha így tesz, az insert művelet.<br/>- **Hamis**: frissítse a célobjektum adatait NULL értékre, ha így tesz, upsert vagy az update művelet. Helyezze be NULL értékre, ha így tesz, az insert művelet. | Nem (alapértelmezett értéke "false") |

**Példa: Salesforce fogadó a a másolási tevékenység**

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

## <a name="query-tips"></a>Lekérdezés tippek

### <a name="retrieve-data-from-a-salesforce-report"></a>A Salesforce-jelentés adatainak lekérése

Beolvasható adat Salesforce-jelentéseket lekérdezést megadásával `{call "<report name>"}`. Például: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>A törölt rekordok lekérése a Salesforce Lomtár

A Salesforce Lomtárból letölthető a törölt rekordok lekérdezéséhez megadhat **"IsDeleted = 1"** a lekérdezésben. Példa:

* A törölt rekordok lekérdezése, adja meg a "jelölje ki * a MyTable__c **ahol IsDeleted = 1**."
* Adja meg az összes rekord, beleértve a meglévő és a törölt lekérdezéséhez "kiválasztása * MyTable__c a **ahol IsDeleted = 0 vagy IsDeleted = 1**."

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Adatok beolvasása a where záradékban a DateTime típusú oszlopra.

A SOQL vagy SQL-lekérdezés megadása esetén a dátum és idő formátumú különbség figyelmet fordítania. Példa:

* **SOQL minta**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Adattípus-leképezést Salesforce

Salesforce adatokat másolni, ha a következő megfeleltetéseket segítségével Salesforce adattípusok a Data Factory ideiglenes adattípusok. Hogyan a másolási tevékenység van leképezve a séma- és adatok típusa a fogadó kapcsolatos további tudnivalókért lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md).

| Salesforce-adattípus | Data Factory ideiglenes adattípus |
|:--- |:--- |
| Automatikus szám |Karakterlánc |
| Jelölőnégyzet |Logikai |
| Currency (Pénznem) |Dupla |
| Dátum |DateTime |
| Dátum/idő |DateTime |
| E-mail cím |Karakterlánc |
| Azonosító |Karakterlánc |
| Keresési kapcsolat |Karakterlánc |
| Többszörös kiválasztási lista |Karakterlánc |
| Szám |Dupla |
| Százalék |Dupla |
| Telefonszám |Karakterlánc |
| Választási lista |Karakterlánc |
| Szöveg |Karakterlánc |
| Szövegmező |Karakterlánc |
| Szövegmező (nagy) |Karakterlánc |
| Szövegmező (gazdag) |Karakterlánc |
| Szöveg (titkosítva) |Karakterlánc |
| URL-cím |Karakterlánc |

## <a name="next-steps"></a>További lépések
Források és mosdók adat-előállítóban másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).