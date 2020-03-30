---
title: Adatok áthelyezése a Salesforce-ból a Data Factory használatával
description: Ismerje meg, hogyan helyezheti át az adatokat a Salesforce-ból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b94f6388d77cca2ef74c802aec7648091172775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281131"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Adatok áthelyezése a Salesforce-ból az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-salesforce-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-salesforce.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Salesforce-összekötő v2-ben című témakört.](../connector-salesforce.md)

Ez a cikk bemutatja, hogyan használhatja a Másolási tevékenység egy Azure-adat-előállító adatok másolása a Salesforce bármely adattár, amely szerepel a Fogadó oszlopban a [támogatott források és fogadók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Ez a cikk az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkre épül, amely általános áttekintést nyújt az adatok mozgásáról a Másolási tevékenységgel és a támogatott adattár-kombinációkkal.

Az Azure Data Factory jelenleg csak a Salesforce-ból a [támogatott fogadó adattárakba](data-factory-data-movement-activities.md#supported-data-stores-and-formats)való áthelyezését támogatja, de nem támogatja az adatok más adattárakból a Salesforce-ba való áthelyezését.

## <a name="supported-versions"></a>Támogatott verziók
Ez az összekötő a Salesforce következő kiadásait támogatja: Developer Edition, Professional Edition, Enterprise Edition vagy Unlimited Edition. És támogatja a Salesforce termelésből, a homokozóból és az egyéni tartományból történő másolást.

## <a name="prerequisites"></a>Előfeltételek
* Az API-engedélyt engedélyezni kell. Lásd: [Hogyan engedélyezhetem az API-hozzáférést a Salesforce-ban engedélykészlet szerint?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* A Salesforce-ból a helyszíni adattárakba történő másoláshoz legalább az Adatkezelési átjáró 2.0-nak telepítve kell lennie a helyszíni környezetben.

## <a name="salesforce-request-limits"></a>Salesforce-kérelem korlátai
A Salesforce korlátozza az összes API-kérelmet és az egyidejű API-kérelmeket. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, szabályozás történik, és véletlenszerű hibák jelennek meg.
- Ha a kérelmek teljes száma meghaladja a korlátot, a Salesforce-fiók 24 órán keresztül le lesz tiltva.

Mindkét esetben előfordulhat, hogy a "REQUEST_LIMIT_EXCEEDED" hibaüzenet is megjelenik. A részleteket a [Salesforce Developer Limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) cikk "API-kérelemkorlátai" című részében találja.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely különböző eszközök/API-k használatával áthelyezi az adatokat a Salesforce-ból.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. A DataForce-ból adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező minta a [JSON-példa: Adatok másolása a Salesforce-ból az Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) szakaszba ebben a cikkben.

A következő szakaszok a Salesforce-ra jellemző Data Factory entitások meghatározására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat a Salesforce csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságnak a következőre kell állítania: **Salesforce**. |Igen |
| environmentUrl | Adja meg a Salesforce-példány URL-címét. <br><br> - Az alapértelmezett\/érték a "https: /login.salesforce.com". <br> - Adatok másolásához a sandboxból adja meg a "https://test.salesforce.com" (" (A" értéket. <br> - Adatok másolásához egyéni tartományból, adja meg például a "https://[domain].my.salesforce.com".- |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználónevét. |Igen |
| jelszó |Adja meg a felhasználói fiók jelszavát. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatát. A biztonsági jogkivonat visszaállítása/bekérésa című témakörben található a [Biztonsági jogkivonat](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) visszaállítása és bekésezése című témakörben. A biztonsági jogkivonatokról általában a [Biztonság és az API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)című témakörben olvashat. |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A szakaszok, például a szerkezet, a rendelkezésre állás és a JSON-adatkészlet szabályzata hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table és így tovább).

A **typeProperties** szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **RelationalTable** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a Salesforce-ban. |Nem (ha meg van adva a **RelationalSource** **lekérdezése)** |

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![Data Factory - Salesforce kapcsolat - API-név](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák, és a különböző házirendek állnak rendelkezésre minden típusú tevékenységek.

A tevékenység típusTulajdonságai szakaszában elérhető tulajdonságok viszont az egyes tevékenységtípusoktól függően változnak. A Másolási tevékenység esetében a források és a fogadók típusától függően változnak.

A másolási tevékenységben, ha a forrás **RelationalSource** (amely magában foglalja a Salesforce-ot is) típusú, a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az adatok olvasásához használja az egyéni lekérdezést. |SQL-92 lekérdezés vagy [Salesforce Object query language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. |Nem (ha az **adatkészlet** **táblaneve** meg van adva) |

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![Data Factory - Salesforce kapcsolat - API-név](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Tippek lekérdezési tippek
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Adatok beolvasása a DateTime oszlop where záradékával
Az SOQL vagy SQL lekérdezés megadásakor figyeljen a DateTime formátumkülönbségre. Példa:

* **SOQL minta:**`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-minta**:
    * **A másolat varázsló használata a lekérdezés megadásához:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **A JSON-szerkesztés használata a lekérdezés megadásához (escape char megfelelően):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentésből
A Salesforce-jelentésekből adatokat úgy `{call "<report name>"}`kérhet be, hogy a lekérdezést (például) (például) adja meg. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Törölt rekordok beolvasása a Salesforce Lomtárból
Ha le szeretné kérdezni a helyreállíthatóan törölt rekordokat a Salesforce Lomtárból, megadhatja az **"IsDeleted = 1"** értéket a lekérdezésben. Például:

* Ha csak a törölt rekordokat szeretné lekérdezni, adja meg a "select * MyTable__c **where IsDeleted= 1**" (A MyTable__c
* Az összes rekord lekérdezéséhez, beleértve a meglévőt és a töröltet is, adja meg a "select * MyTable__c **where IsDeleted = 0 vagy IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-példa: Adatok másolása a Salesforce-ból az Azure Blobba
A következő példa minta JSON-definíciókat tartalmaz, amelyek segítségével létrehozhat egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatják, hogyan másolhat adatokat a Salesforce-ból az Azure Blob Storage-ba. Azonban az adatok átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadók bármelyikébe az Azure Data Factory másolási tevékenység használatával.

Az alábbiakban a Data Factory-összetevők, amelyeket létre kell hoznia a forgatókönyv megvalósításához. A listát követő szakaszok részletesen ismertetik ezeket a lépéseket.

* [Salesforce](#linked-service-properties) típusú csatolt szolgáltatás
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú összekapcsolt szolgáltatás
* [RelationalTable](#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
* [Relációsforrást](#copy-activity-properties) és [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties) használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md)

**Salesforce csatolt szolgáltatás**

Ez a példa a **Salesforce** csatolt szolgáltatást használja. A csatolt szolgáltatás által támogatott tulajdonságokat a [Salesforce csatolt szolgáltatás](#linked-service-properties) szakaszban tekintse meg. Lásd: [Biztonsági jogkivonat beszerezni](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) a biztonsági jogkivonat alaphelyzetbe állítására/bekérésére vonatkozó utasításokat.

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
**Azure Storage-hoz csatolt szolgáltatás**

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
**Salesforce bemeneti adatkészlet**

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

A **külső** **true** beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![Data Factory - Salesforce kapcsolat - API-név](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure blob kimeneti adatkészlet**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra.

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

**Folyamat másolási tevékenységgel**

A folyamat másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **RelationalSource**, a **fogadó** típusa pedig **BlobSink**lesz.

A RelationalSource által támogatott tulajdonságok listájának [RelationalSource típusú tulajdonságai.](#copy-activity-properties)

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
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![Data Factory - Salesforce kapcsolat - API-név](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Típusleképezés a Salesforce-hoz

| Salesforce típusa | . NET-alapú típus |
| --- | --- |
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

> [!NOTE]
> Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [Másolási tevékenység teljesítményét és hangolási útmutatót,](data-factory-copy-activity-performance.md) amely ből megtudhatja, hogy milyen kulcsfontosságú tényezők befolyásolják az adatok mozgatását (másolási tevékenység) az Azure Data Factoryban, és különböző módokon optimalizálhatja azt.
