---
title: "Adatok másolása az Salesforce Azure Data Factory használatával |} Microsoft Docs"
description: "Útmutató: adatok másolása Salesforce támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: 7978e955bf5516a853443555ab10a69dcf22d63f
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Salesforce
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-salesforce-connector.md)
> * [2. verzió – Előzetes verzió](connector-salesforce.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni a Salesforce-adatbázis. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Salesforce-összekötőt a V1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Salesforce-adatbázis adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a Salesforce-összekötő támogatja a következő Salesforce-kiadásokat: **Developer Edition, Professional Edition, Enterprise Edition vagy korlátlan Edition**. Amely támogatja az adatok másolását a Salesforce **éles, a védőfal és az egyéni tartomány**.

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
| environmentUrl | Adja meg az URL-címet a Salesforce-példány. <br><br> -Alapértelmezett érték a `"https://login.salesforce.com"`. <br> -Adatok másolása az védőfal, adja meg a `"https://test.salesforce.com"`. <br> -Adatok másolása az egyéni tartományt, adja meg, például `"https://[domain].my.salesforce.com"`. |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) útmutatást, ha alaphelyzetbe állítása/get egy biztonsági jogkivonatot. Általános biztonsági jogkivonatokat kapcsolatos további tudnivalókért lásd: [biztonsági és az API-t](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Igen |

**Példa**

```json
{
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
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a Salesforce-adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása Salesforce, állítsa be a adatkészlet type tulajdonsága **RelationalTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **RelationalTable** | Igen |
| tableName | A Salesforce-adatbázisban a tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Példa**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Salesforce forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-as-source"></a>Salesforce forrásaként

Adatok másolása Salesforce, állítsa be a forrás típusa a másolási tevékenység **RelationalSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **RelationalSource** | Igen |
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
                "type": "RelationalSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>Lekérdezés tippek

### <a name="retrieving-data-from-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentés

Beolvasható adat Salesforce-jelentéseket lekérdezést megadásával `{call "<report name>"}. Example: `"query": "{hívás \"vizsgálati jelentés\"}" ".

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>A Salesforce Lomtárból törölt rekordok visszanyerése

A Salesforce Lomtárból letölthető a törölt rekordok lekérdezéséhez is megadhat **"IsDeleted = 1"** a lekérdezésben. Például:

* A törölt rekordok lekérdezése, adja meg a "jelölje ki * a MyTable__c **ahol IsDeleted = 1**"
* A többek között a meglévő és a törölt rekordok lekérdezése az összes, adja meg a "jelölje ki * MyTable__c a **ahol IsDeleted = 0 vagy IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Adatgyűjtés használata a where záradék található dátum és idő oszlop

Ha SOQL vagy SQL-lekérdezés megadása, vegye figyelembe a dátum és idő formátumú különbség. Példa:

* **SOQL minta**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **SQL-minta**:`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

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
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).