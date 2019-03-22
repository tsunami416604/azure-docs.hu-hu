---
title: Adatok áthelyezése a Salesforce-ból a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg a Salesforce-ból adatok áthelyezése az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aac1ed82a01477b081f4bc146f199eba87d97859
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312138"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Adatok áthelyezése a Salesforce-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-salesforce-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-salesforce.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Salesforce-összekötő a v2-ben](../connector-salesforce.md).

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure data factory bármely adattár, amely szerepel a fogadó oszlopában az másolhat adatokat a Salesforce-ból a [forrásként és fogadóként támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Ez a cikk épül, amely a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk, amelyen az adatok áthelyezését általános áttekintése a másolási tevékenység és a támogatott data store kombinációk.

Az Azure Data Factory jelenleg csak helyez át adatokat, a Salesforce-ból támogatja [támogatott fogadó adattárakba](data-factory-data-movement-activities.md#supported-data-stores-and-formats), de nem támogatott amely adatokat helyez át más adatokat tárolja, a Salesforce-hoz.

## <a name="supported-versions"></a>Támogatott verziók
Ez az összekötő támogatja az alábbi kiadásainak Salesforce-ban: Developer Edition szoftverhez, Professional Edition, Enterprise Edition vagy korlátlan Edition. És a Salesforce éles, védőfal és az egyéni tartomány másolása támogatja.

## <a name="prerequisites"></a>Előfeltételek
* API-t jogosultsági engedélyezve kell lennie. Lásd: [hogyan engedélyezhető az engedélyek beállítása a Salesforce-ban API-hozzáférés?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Az adatok másolása a Salesforce-ból a helyszíni adatok áruházak, rendelkeznie kell legalább Data Management Gateway 2.0 a helyszíni környezetben telepített.

## <a name="salesforce-request-limits"></a>A Salesforce kérelmekre vonatkozó korlátok
A Salesforce API-kérések és a párhuzamos API-kérések korlátokkal rendelkeznek. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, szabályozás akkor fordul elő, és látni fogja a véletlen hibákat.
- Ha a kérelmek teljes száma meghaladja a korlátot, a Salesforce-fiókban 24 órán keresztül blokkolja.

A "REQUEST_LIMIT_EXCEEDED" hiba a mindkét forgatókönyvet is kaphat. "API-t kérelmekre vonatkozó korlátok" című szakaszában talál a [Salesforce fejlesztői korlátok](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) részleteivel.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez a Salesforce-ból a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban. A Data Factory-entitások használatával adatait átmásolhatja a Salesforce-hoz használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása a Salesforce-ból az Azure-Blobba](#json-example-copy-data-from-salesforce-to-azure-blob) című szakaszát.

A következő szakaszok a Salesforce-hoz adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázat ismerteti a Salesforce-beli társított szolgáltatás adott JSON-elemek.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **A Salesforce**. |Igen |
| environmentUrl | Adja meg az URL-címet a Salesforce-példány. <br><br> – Alapértelmezett érték a "https:\//login.salesforce.com". <br> – Adatok másolása a tesztkörnyezetből, adja meg a "https://test.salesforce.com". <br> – Adatok másolása az egyéni tartományt, adja meg, például "https://[domain].my.salesforce.com". |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) alaphelyzetbe állítása/lekérése a biztonsági jogkivonat való létrehozásával kapcsolatos útmutatást. Az általános biztonsági jogkivonatokat kapcsolatos további információkért lásd: [biztonsági és API-val](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (Azure SQL, az Azure blob, az Azure table és így tovább).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz egy adatkészlet típusú **RelationalTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A Salesforce-ban a tábla neve. |Nem (Ha egy **lekérdezés** , **RelationalSource** van megadva) |

> [!IMPORTANT]
> Az API neve "__c" részét egyéni objektumokra van szükség.

![Data Factory - kapcsolat Salesforce – API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, például nevét, leírását, bemeneti és kimeneti táblák, és különböző házirend érhető el az összes típusú tevékenységet.

A tevékenység a typeProperties szakasz az elérhető tulajdonságok, másrészt tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

A másolási tevékenység, ha a forrás típusa nem **RelationalSource** (amely tartalmazza a Salesforce-hoz), a következő tulajdonságok typeProperties szakasz érhető el:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével olvassa el az adatokat. |Egy SQL-92 lekérdezés vagy [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. |Nem (Ha a **tableName** , a **adatkészlet** van megadva) |

> [!IMPORTANT]
> Az API neve "__c" részét egyéni objektumokra van szükség.

![Data Factory - kapcsolat Salesforce – API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Lekérdezési tippek
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Hol használata az adatok beolvasása az idő-oszlop záradék
Amikor adja meg a SOQL vagy SQL-lekérdezés, figyeljen oda arra a dátum és idő formátumú különbséggel. Példa:

* **SOQL minta**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-minta**:
    * **Adja meg a lekérdezést a másolás varázsló használatával:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Adja meg a lekérdezést a szerkesztő JSON használatával (escape karaktere megfelelően):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentés
Kérheti le adatokat a Salesforce-jelentésekből-lekérdezéshez, mivel megadásával `{call "<report name>"}`, például. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Rekordok beolvasása törlése a Salesforce Lomtárból
Lekérdezés a helyreállíthatóan törölt rekordok a Salesforce Lomtárból, megadhatja **"IsDeleted = 1"** a lekérdezésben. Például:

* A törölt rekordok lekérdezése, adja meg a "kiválasztása * MyTable__c a **ahol IsDeleted = 1**"
* Lekérdezés az összes többek között a meglévő és a törölt a rekordokat, adja meg a "válassza * MyTable__c a **ahol IsDeleted = 0 vagy IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-példa: Adatok másolása a Salesforce-ból az Azure Blobba
Az alábbi példa mintául szolgáló JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával tartalmaz a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása a Salesforce-ból az Azure Blob Storage mutatnak. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

Az alábbiakban a Data Factory-összetevők, amelyek kell létrehozni a forgatókönyv megvalósításához. A listán a következő szakaszok ezeket a lépéseket részleteket nyújtanak.

* Egy a típusú társított szolgáltatás [Salesforce-ban](#linked-service-properties)
* Egy a típusú társított szolgáltatás [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties)
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [Azure Blobba](data-factory-azure-blob-connector.md#dataset-properties)
* A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**A Salesforce-beli társított szolgáltatás**

Ez a példa a **Salesforce** társított szolgáltatást. Tekintse meg a [Salesforce-beli társított szolgáltatást](#linked-service-properties) a tulajdonságokat, amelyeket a társított szolgáltatás által támogatott a következő szakaszban. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) alaphelyzetbe állítása/lekérése a biztonsági jogkivonat való létrehozásával kapcsolatos útmutatást.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Azure Storage társított szolgáltatás**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**A Salesforce bemeneti adatkészlet**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Beállítás **külső** való **igaz** a Data Factory szolgáltatás tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

> [!IMPORTANT]
> Az API neve "__c" részét egyéni objektumokra van szükség.

![Data Factory - kapcsolat Salesforce – API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure blobkimeneti adatkészlet**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenységgel rendelkező folyamat**

A folyamat másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és az óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource**, és a **fogadó** típusa **BlobSink**.

Lásd: [RelationalSource tulajdonságait](#copy-activity-properties) a RelationalSource által támogatott tulajdonságok listáját.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> Az API neve "__c" részét egyéni objektumokra van szükség.

![Data Factory - kapcsolat Salesforce – API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Salesforce-leképezés típusa

| Salesforce-típus | . NET-alapú típusa |
| --- | --- |
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

> [!NOTE]
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
