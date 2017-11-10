---
title: "Adatok másolása Salesforce vagy Azure Data Factory használatával |} Microsoft Docs"
description: "Ismerje meg az adatok másolása támogatott fogadó adattárolókhoz Salesforce (vagy) a támogatott forráshierarchiából adatokat tárolja a Salesforce egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 017d03b76bd19a0b3a1e19c22233c61be9067d0d
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-fromto-salesforce-using-azure-data-factory"></a>Másolja az adatokat, vagy a Salesforce Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-salesforce-connector.md)
> * [2. verzió – Előzetes verzió](connector-salesforce.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és a Salesforce. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Salesforce-összekötőt a V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Másolhatja a Salesforce adatok bármely támogatott fogadó tárolót, vagy vagy a Salesforce bármely támogatott forrás adattár adatainak másolása. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Salesforce összekötő támogatja:

- A következő Salesforce-kiadásokat: **Developer Edition, Professional Edition, Enterprise Edition vagy korlátlan Edition**.
- Az adatok másolásának vagy Salesforce **éles, a védőfal és az egyéni tartomány**.

## <a name="prerequisites"></a>Előfeltételek

* A Salesforce-ban API engedély engedélyezni kell. Lásd: [hogyan engedélyezhető az engedélycsoport által a Salesforce-ban API-hozzáférés?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce kérelmekre vonatkozó korlátok

Salesforce rendelkezik mind az API-kérelmek teljes száma, és a egyidejű API-kérés. Vegye figyelembe a következő szempontokat:

- Egyidejű kérelmek száma meghaladja a korlátot, ha sávszélesség-szabályozás következik be, és véletlen hibákat látja.
- Ha a kérelmek teljes száma túllépi a korlátot, a Salesforce-fiókban 24 órán át le van tiltva.

A "REQUEST_LIMIT_EXCEEDED" hiba mindkét forgatókönyvet is akkor fordulhat elő. A "API kérelmekre vonatkozó korlátok" című a [Salesforce fejlesztői korlátok](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) cikkben alább.

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások megadhatók a Salesforce-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Salesforce kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **Salesforce**. |Igen |
| environmentUrl | Adja meg az URL-címet a Salesforce-példány. <br> -Alapértelmezett érték a `"https://login.salesforce.com"`. <br> -Adatok másolása az védőfal, adja meg a `"https://test.salesforce.com"`. <br> -Adatok másolása az egyéni tartományt, adja meg, például `"https://[domain].my.salesforce.com"`. |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót.<br/><br/>Ha szeretné, ebben a mezőben megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault, és lehetővé teszik az ADF másolja át a tevékenységek lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) útmutatást, ha alaphelyzetbe állítása/get egy biztonsági jogkivonatot. Általános biztonsági jogkivonatokat kapcsolatos további tudnivalókért lásd: [biztonsági és az API-t](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm).<br/><br/>Ha szeretné, ebben a mezőben megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy tárolja a biztonsági jogkivonatot az Azure Key Vault, és lehetővé teszik az ADF másolja át a tevékenységek lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem a forrást, a fogadó Igen |

>[!IMPORTANT]
>Adatok másolása a Salesforce, explicit módon [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) után, például a Salesforce és a hivatkozott szolgáltatásban található társítható közelében hellyel rendelkező.

**Példa: hitelesítő adatok tárolása az ADF**

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

**Példa: hitelesítő adatok tárolása az Azure Key Vault**

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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Salesforce-adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Másolja az adatokat, vagy Salesforce, állítsa be a adatkészlet type tulajdonsága **SalesforceObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SalesforceObject**  | Igen |
| objectApiName | A Salesforce objektum nevét az adatok lekéréséhez. | Nem a forrást, a fogadó Igen |

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

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
>Visszafelé kompatibilitási, amikor az adatok másolása Salesforce, az előző "RelationalTable" típusú adatkészlet fog tovább dolgozik, amíg Ön váltson át az új "SalesforceObject" típus használata javasolt.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
| tableName | A Salesforce-ban a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce-forrás és a fogadó által támogatott tulajdonságokról listáját tartalmazza.

### <a name="salesforce-as-source"></a>Salesforce forrásaként

Adatok másolása Salesforce, állítsa be a forrás típusa a másolási tevékenység **SalesforceSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **SalesforceSource** | Igen |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. Használhatja az SQL-92 lekérdezés vagy [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
>Visszafelé kompatibilitási, amikor az adatok másolása Salesforce, az előző "RelationalSource" típusú példány forrás használatával fog tovább dolgozik, amíg Ön váltson át az új "SalesforceSource" típus használata javasolt.

### <a name="salesforce-as-sink"></a>Mint fogadó Salesforce

Adatok másolása Salesforce, állítsa be a fogadó típusa a másolási tevékenység **SalesforceSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **SalesforceSink** | Igen |
| WriteBehavior | Az írási viselkedésének a művelethez.<br/>Két érték engedélyezett: **beszúrása**, és **Upsert**. | Nem (alapértelmezett érték Insert) |
| externalIdFieldName | A külső azonosító mezőben upsert művelet neve. "Külső azonosító mező"-ként kell megadni a megadott mező a Salesforce objektumban, és a megfelelő bemeneti adatok nem rendelkezhet NULL értékeket. | Igen, a "Upsert" |
| WriteBatchSize | A sorok számát az egyes kötegekben Salesforce írt adatok. | Nem (alapértelmezett érték 5000) |
| ignoreNullValues | Azt jelzi, hogy figyelmen kívül hagyja a null értékeket bemeneti adatok során írási műveletet.<br/>Két érték engedélyezett: **igaz**, és **hamis**.<br>- **Igaz**: hagyja az adatokat a cél objektum változatlan upsert/frissítési művelet során, és illessze be meghatározott alapértelmezett értéket insert művelet során.<br/>- **hamis**: frissítse az adatokat a célobjektum NULL upsert/frissítési művelet során, és illessze be NULL értéket, insert művelet során. | Nem (alapértelmezett értéke "false") |

### <a name="example-salesforce-sink-in-copy-activity"></a>Példa: A Salesforce gyűjtése a másolási tevékenység

```json
"activities":[
    {
        "name": "CopyToSalesforce",
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

### <a name="retrieving-data-from-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentés

Beolvasható adat Salesforce-jelentéseket lekérdezést megadásával `{call "<report name>"}`. Példa: `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>A Salesforce Lomtárból törölt rekordok visszanyerése

A Salesforce Lomtárból letölthető a törölt rekordok lekérdezéséhez is megadhat **"IsDeleted = 1"** a lekérdezésben. Például:

* A törölt rekordok lekérdezése, adja meg a "jelölje ki * a MyTable__c **ahol IsDeleted = 1**"
* A többek között a meglévő és a törölt rekordok lekérdezése az összes, adja meg a "jelölje ki * MyTable__c a **ahol IsDeleted = 0 vagy IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Adatgyűjtés használata a where záradék található dátum és idő oszlop

Ha SOQL vagy SQL-lekérdezés megadása, vegye figyelembe a dátum és idő formátumú különbség. Példa:

* **SOQL minta**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Adattípus-leképezést Salesforce

Amikor adatokat másol Salesforce, a következő megfeleltetéseket szolgálnak a Salesforce-adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

| Salesforce-adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| Automatikus szám |Karakterlánc |
| Jelölőnégyzet |Logikai érték |
| Currency (Pénznem) |Dupla |
| Dátum |Dátum és idő |
| Dátum és idő |Dátum és idő |
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
| URL-CÍME |Karakterlánc |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).