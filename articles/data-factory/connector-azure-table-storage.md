---
title: Adatok másolása az Azure Table storage szolgáltatásba vagy onnan Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Table storage támogatott forrás áruházakból származó, vagy a támogatott fogadó-áruházak, Table storage-ból a Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 0399836191050996ac3eaf0fbe59496e10e2b426
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059495"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Adatok másolása az Azure Table storage szolgáltatásba vagy onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-azure-table-connector.md)
> * [Aktuális verzió](connector-azure-table-storage.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factoryban az adatok másolása az Azure Table storage szolgáltatásba vagy onnan. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Bármely támogatott forrásadattárból adatokat másolhatja a Table storage. Is másolhatja adatok Table storage-ból bármelyik támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban Ez az Azure Table-összekötő adatok másolása a fiókkulcsra és a szolgáltatás használatával támogatja megosztott hozzáférési aláírást hitelesítések.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott a Table storage-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

### <a name="use-an-account-key"></a>Egy fiók kulcs használata

Az Azure Storage társított szolgáltatása a fiókkulcs használatával hozhat létre. A Storage globális hozzáférést biztosít az adat-előállítóban. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureTableStorage**. |Igen |
| kapcsolati Sztringje | Adja meg a connectionString tulajdonság tárolási való csatlakozáshoz szükséges információk. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "AzureTableStorage" társított szolgáltatás típusa, a jövőben használata javasolt.

**Példa**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Közös hozzáférésű jogosultságkódos hitelesítés használata

Storage társított szolgáltatás egy közös hozzáférésű jogosultságkód használatával hozhat létre. Az adat-előállító all/adott erőforrásokat a storage-ban korlátozott/időhöz kötött hozzáférést biztosít.

Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Segítségével, az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó engedélyek bizonyos készletét a és a egy megadott ideig. Nem kell megosztani a fiók hozzáférési kulcsait. A közös hozzáférésű jogosultságkód URI, amely a lekérdezési paraméterek magában foglalja a tárolási erőforrásokhoz való hitelesített hozzáférés szükséges összes információt. A közös hozzáférésű jogosultságkód-adattároló erőforrások elérésére, az ügyfél csak kell a megfelelő konstruktor vagy a metódus a közös hozzáférésű jogosultságkód adja át. Közös hozzáférésű jogosultságkódokkal kapcsolatos további információkért lásd: [közös hozzáférésű jogosultságkódok: megismerheti a közös hozzáférésű jogosultságkód modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> A Data Factory mostantól támogatja mindkét **szolgáltatás közös hozzáférésű jogosultságkódot** és **fiók közös hozzáférésű jogosultságkód**. Két, és hogyan kell őket összeállítani kapcsolatos további információkért lásd: [közös hozzáférésű jogosultságkódok típusú](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> A tárfiók szolgáltatás közös hozzáférési aláírásokat létrehozni, hajtsa végre a következő PowerShell-parancsokat. Cserélje le a zárójelben, és a szükséges engedélyeket.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Közös hozzáférésű jogosultságkódos hitelesítés használatához a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **AzureTableStorage**. |Igen |
| sasUri | Adja meg a közös hozzáférésű jogosultságkód URI-t, a tárolási erőforrások, például blob, tárolót vagy tábla. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha "AzureStorage" típusú társított szolgáltatás használja, továbbra is támogatott-van, amíg használja az új "AzureTableStorage" társított szolgáltatás típusa, a jövőben használata javasolt.

**Példa**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Amikor létrehoz egy közös hozzáférésű jogosultságkód URI Azonosítójának, vegye figyelembe a következőket:

- A társított szolgáltatás (olvasási, írási, olvasási/írási) használatának módja a következőben az adat-előállító alapján objektumok megfelelő írási/olvasási engedélyeket.
- Állítsa be **lejárati idő** megfelelően. Győződjön meg arról, hogy a tároló objektumok elérése le nem jár, a folyamat aktív időszakon belül.
- Az URI-t a jobb oldali tábla szintjén az igényei alapján kell létrehoznia.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Azure Table adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Másolja az adatokat, és az Azure Table, állítsa be a type tulajdonság, az adatkészlet **AzureTable**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **AzureTable**. |Igen |
| tableName |A Table storage-adatbázispéldányban, amelyre a társított szolgáltatás hivatkozik a tábla neve. |Igen |

**Példa**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Adat-előállítók által séma

A séma nélküli adattárakban, mint az Azure Table adat-előállító kikövetkezteti a használandó sémát az alábbi módszerek egyikével:

* Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, adat-előállító figyelembe veszi a sémát, ez a struktúra. Ebben az esetben ha egy sor nem tartalmaz egy oszlop értékét, null értékű biztosítunk azt.
* Ha nem adja meg az adatok struktúráját használatával a **struktúra** tulajdonságot az adatkészlet-definícióban, adat-előállító kikövetkezteti a használandó sémát az első sor az adatok használatával. Ebben az esetben ha az első sor a teljes séma nem tartalmaz, egyes oszlopok vannak nem talált az eredmény a másolási művelet.

Sémamentes adatforrások esetében az ajánlott eljárás az adatok struktúráját használatával adja meg a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure-tábla forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-table-as-a-source-type"></a>Azure-tábla, egy adatforrás típusa

Adatok másolása az Azure Table, állítsa be a forrás típusaként a másolási tevékenység **AzureTableSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **AzureTableSource**. |Igen |
| azureTableSourceQuery |A Table storage egyéni lekérdezés segítségével olvassa el az adatokat. A következő szakaszban található példák. |Nem |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy a kivételt a tábla nem létezik az engedélyezi-e.<br/>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett). |Nem |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery példák

Ha az Azure Table oszlop a dátum/idő típus:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Ha az Azure Table oszlop karakterlánc típusú:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

A folyamat paraméter használatakor adja le a datetime érték az előző példák alapján megfelelő formátumba.

### <a name="azure-table-as-a-sink-type"></a>Azure-tábla, a fogadó típusa

Adatok másolása az Azure Table, állítsa be a fogadó típusa a másolási tevékenység **AzureTableSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **AzureTableSink**. |Igen |
| azureTableDefaultPartitionKeyValue |Az alapértelmezett partíciókulcs-értékkel, amely a fogadó által használható. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek az értékekkel, partíciókulcsok nevét. Ha nincs megadva, a partíciós kulcs "AzureTableDefaultPartitionKeyValue" lesz. |Nem |
| azureTableRowKeyName |Adja meg az oszlop, amelynek oszlop értékeit a sor kulcsaként használt nevét. Ha nincs megadva, használjon egy GUID Azonosítót minden egyes sorára. |Nem |
| azureTableInsertType |Adatok beszúrása az Azure Table a módot. Ez a tulajdonság szabja meg, hogy rendelkeznek-e létező sorok egyeztetésével partíció-és a kimeneti tábla cserélni vagy egyesített értékekre. <br/><br/>Engedélyezett értékek a következők **egyesítési** (alapértelmezett), és **cseréje**. <br/><br> Ez a beállítás vonatkozik sorszinten a táblázatok szintjén nem. Sem a beállítás törli a kimeneti tábla sorait, amelyek a bemeneti adatok nem léteznek. A merge, és cserélje le beállításait működését kapcsolatos további információkért lásd: [entity Insert vagy merge](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [helyezze be vagy cserélje le az entitás](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nem |
| WriteBatchSize |Szúr be az Azure Table adatokat, amikor writeBatchSize vagy writeBatchTimeout találati.<br/>Megengedett értékek: egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout |Szúr be az Azure Table adatokat, amikor writeBatchSize vagy writeBatchTimeout találati.<br/>Megengedett értékek: időtartam. Például "00: 20:00" (20 percig). |Nem (az alapértelmezett érték 90 másodperc alatt, a storage-kliens alapértelmezett időkorlátja) |

**Példa**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

A céloszlop használatával a forrás oszlop leképezése a **"translator"** tulajdonság azureTablePartitionKeyName, a céloszlop használata előtt.

A következő példában a céloszlop DivisionID forrásoszlop DivisionID van leképezve:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

A partíciós kulcs "DivisionID" van megadva.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure-táblaalapú adattípus-leképezés

Az Azure Table és a másolt adatok, a következő hozzárendeléseket szolgálnak az Azure-tábla adattípusai Data Factory-közbenső adattípusok. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatos további információkért lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

Ha Ön adatok importálására és az Azure-tábla, a következő [leképezések határozzák meg az Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) használt .NET típusát, és ez fordítva is igaz az Azure-tábla OData-típusok közül.

| Az Azure tábla adattípus | Data Factory közbenső adattípus | Részletek |
|:--- |:--- |:--- |
| Edm.Binary |byte] |Bájttömb legfeljebb 64 KB-os. |
| Edm.Boolean |Logikai |Logikai érték. |
| Edm.DateTime |DateTime |Egy 64 bites érték, egyezményes világidő (UTC) szerint kifejezett. A támogatott dátum és idő tartomány kezdete éjfél. január 1, 1601. éjjel (C.E.,) (UTC). A tartomány véget ér. December 31-9999. |
| Edm.Double |double |Egy 64 bites lebegőpontos értéket. |
| Edm.Guid |GUID |A 128 bites globálisan egyedi azonosítóját. |
| Edm.Int32 |Int32 |Egy 32 bites egész számot. |
| Edm.Int64 |Int64 |Egy 64 bites egész számot. |
| Edm.String |Sztring |UTF-16 kódolású érték. Karakterlánc-értékek legfeljebb 64 KB lehet. |

## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
