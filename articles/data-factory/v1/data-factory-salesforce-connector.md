---
title: "Adatok áthelyezése Salesforce Data Factory használatával |} Microsoft Docs"
description: "További tudnivalók az adatok mozgatása Salesforce Azure Data Factory használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9e678e947a686b5a672af13cb0f0e60b4a272de9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Adatok áthelyezése Salesforce Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-salesforce-connector.md)
> * [2. verzió – Előzetes verzió](../connector-salesforce.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Salesforce-összekötőt, a V2](../connector-salesforce.md).


Ez a cikk ismerteti, hogyan használhatja másolási tevékenység az Azure data factoryban Salesforce bármely adattárolóhoz, amely szerepel a fogadó oszlopában az adatok másolása az [támogatott források és mosdók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. Ez a cikk épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amely adatmozgás általános áttekintést során másolási tevékenység és a támogatott adatokat tároló kombinációja.

Az Azure Data Factory jelenleg csak áthelyezése adatait a Salesforce [fogadó adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats), de nem támogatják az adatok áthelyezését más adatok a Salesforce tárolja.

## <a name="supported-versions"></a>Támogatott verziók
Ez az összekötő a következő Salesforce-kiadásokat támogatja: Developer Edition, Professional Edition, Enterprise Edition vagy korlátlan kiadását. És a Salesforce éles, védőfal és az egyéni tartomány másolását támogatja.

## <a name="prerequisites"></a>Előfeltételek
* API-engedély engedélyezve kell lennie. Lásd: [hogyan engedélyezhető az engedélycsoport által a Salesforce-ban API-hozzáférés?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Adatok másolása Salesforce a helyszíni adattárolókhoz, rendelkeznie kell legalább adatok felügyeleti átjáró 2.0-s verziójával a helyszíni környezetben.

## <a name="salesforce-request-limits"></a>Salesforce kérelmekre vonatkozó korlátok
Salesforce rendelkezik mind az API-kérelmek teljes száma, és a egyidejű API-kérés. Vegye figyelembe a következő szempontokat:

- Az egyidejű kérelmek száma meghaladja a korlátot, ha sávszélesség-szabályozás következik be, és látni fogja a véletlen hibákat.
- Ha a kérelmek teljes száma túllépi a korlátot, a Salesforce-fiókban le lesz tiltva 24 órán át.

A "REQUEST_LIMIT_EXCEEDED" hiba mindkét forgatókönyvet is akkor fordulhat elő. A "API kérelmekre vonatkozó korlátok" című a [Salesforce fejlesztői korlátok](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) cikkben alább.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok Salesforce különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása Salesforce használt adat-előállító entitások JSON-definíciók minta, lásd: [JSON-példa: adatok másolása az Salesforce az Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) című szakaszát. 

A következő szakaszok részletesen bemutatják való Salesforce adat-előállító tartozó entitások meghatározásához használt JSON tulajdonságokat: 

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat ismerteti, amelyek a Salesforce kapcsolódó szolgáltatásra vonatkozó JSON-elemek szerepelnek.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **Salesforce**. |Igen |
| environmentUrl | Adja meg az URL-címet a Salesforce-példány. <br><br> -Alapértelmezett érték a "https://login.salesforce.com". <br> -Adatok másolása az védőfal, adja meg a "https://test.salesforce.com". <br> -Adatok másolása az egyéni tartományt, adja meg, például "https://[domain].my.salesforce.com". |Nem |
| felhasználónév |Adja meg a felhasználói fiók felhasználói nevét. |Igen |
| jelszó |Adja meg a felhasználói fiókhoz tartozó jelszót. |Igen |
| securityToken |Adja meg a felhasználói fiók biztonsági jogkivonatot. Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) útmutatást, ha alaphelyzetbe állítása/get egy biztonsági jogkivonatot. Általános biztonsági jogkivonatokat kapcsolatos további tudnivalókért lásd: [biztonsági és az API-t](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Igen |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Illetve meghatározásához adatkészletek rendelkezésre álló tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla és így tovább).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz egy adatkészlet típusú **RelationalTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A Salesforce-ban a tábla neve. |Nem (Ha egy **lekérdezés** a **RelationalSource** van megadva) |

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és tevékenységek meghatározásához rendelkezésre álló tulajdonságok teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok nevét, leírását, bemeneti és kimeneti táblák, valamint különböző házirendeket is tevékenységi érhető el.

Által biztosított a typeProperties szakaszban a tevékenység tulajdonságait, másrészt tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

A másolási tevékenység, ha az adatforrás típusú **RelationalSource** (amely tartalmazza a Salesforce), a következő tulajdonságok érhetők el typeProperties szakaszában:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés segítségével adatokat olvasni. |Egy SQL-92 lekérdezés vagy [Salesforce objektum Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezés. Például: `select * from MyTable__c`. |Nem (Ha a **tableName** , a **dataset** van megadva) |

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Lekérdezés tippek
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Adatgyűjtés használata a where záradék található dátum és idő oszlop
Ha SOQL vagy SQL-lekérdezés megadása, vegye figyelembe a dátum és idő formátumú különbség. Példa:

* **SOQL minta**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-minta**:
    * **Adja meg a lekérdezés másolása varázslóval:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Használatával adhatja meg a lekérdezés szerkesztéséhez JSON (escape-karakter megfelelően):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentés
Beolvasható adat Salesforce-jelentéseket lekérdezést megadásával `{call "<report name>"}`, például. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>A Salesforce Lomtárból törölt rekordok visszanyerése
A Salesforce Lomtárból letölthető a törölt rekordok lekérdezéséhez is megadhat **"IsDeleted = 1"** a lekérdezésben. Például:

* A törölt rekordok lekérdezése, adja meg a "jelölje ki * a MyTable__c **ahol IsDeleted = 1**"
* A többek között a meglévő és a törölt rekordok lekérdezése az összes, adja meg a "jelölje ki * MyTable__c a **ahol IsDeleted = 0 vagy IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-példa: adatok másolása az Salesforce az Azure-Blobba
Az alábbi példa minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot biztosít a [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). A Salesforce adatok másolása az Azure Blob Storage mutatnak. Azonban adatok átmásolhatók a megadott mosdók bármelyikét [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.   

Az alábbiakban az adat-előállító összetevők, amelyeket kezelni kell létrehozni a forgatókönyv végrehajtásához. A lista kövesse a következő szakaszok ezeket a lépéseket részleteit tartalmazzák.

* A típusú társított szolgáltatás [Salesforce](#linked-service-properties)
* A típusú társított szolgáltatás [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [RelationalTable](#dataset-properties)
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**A Salesforce társított szolgáltatás**

Ez a példa a **Salesforce** társított szolgáltatás. Tekintse meg a [Salesforce társított szolgáltatás](#linked-service-properties) szolgáltatásnak által támogatott tulajdonságok szakaszát.  Lásd: [biztonsági jogkivonatának beszerzéséhez](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) útmutatást a biztonsági jogkivonat alaphelyzetbe állítása/beolvasása.

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
**Bemeneti Salesforce-adatkészlet**

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

Beállítás **külső** való **igaz** tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

> [!IMPORTANT]
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure blobkimeneti adatkészlet**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1).

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

**A másolási tevékenység folyamat**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **RelationalSource**, és a **fogadó** típusúra **BlobSink**.

Lásd: [RelationalSource típustulajdonságokat](#copy-activity-properties) a RelationalSource által támogatott tulajdonságok listája.

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
> Az API neve "__c" részét bármilyen egyéni objektum szükséges.

![Data Factory - kapcsolat Salesforce - API neve](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>A Salesforce leképezésének
| Salesforce-típus | . A NET-alapú típusa |
| --- | --- |
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

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Tekintse meg a [másolási tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
