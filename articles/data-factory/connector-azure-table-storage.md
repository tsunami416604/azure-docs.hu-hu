---
title: "Adatok másolása és az Azure Table storage a Data Factory használatával |} Microsoft Docs"
description: "Ismerje meg az adatok másolása Azure Table Storage támogatott forrás tárolja, vagy a Table storage támogatott fogadó áruházak, a Data Factory használatával."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: fde85936760a167f1da2289ac1d18e97df7c9c04
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Adatok másolása és az Azure Table storage Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1 - általánosan elérhető verzió](v1/data-factory-azure-table-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-table-storage.md)

Ez a cikk ismerteti a másolási tevékenység használata az Azure Data Factory és az Azure Table storage-adatok másolása. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory, amely általában a rendelkezésre álló, 1 verzióját használja [tábla tárolási összekötőt a 1-es verziójú](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok átmásolhatja bármely támogatott forrás adattár a Table storage. Is másolhat adatokat a Table storage bármely támogatott fogadó adattárolóhoz. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure-tábla összekötő támogatja az adatok másolásának fiókkulcs és a szolgáltatás segítségével megosztott hozzáférési aláírást hitelesítések.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják a Table storage a Data Factory tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

### <a name="use-an-account-key"></a>Fiók-kulcsot használ

Az Azure tárolás társított szolgáltatásának a fiók kulcs használatával hozhat létre. Storage globális hozzáférést biztosít az adat-előállítóban. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureStorage**. |Igen |
| connectionString | Adja meg a connectionString tulajdonság tárolási való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló egy magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

### <a name="use-service-shared-access-signature-authentication"></a>Szolgáltatás megosztott hozzáférési aláírást hitelesítés használata

A tárolás társított szolgáltatásának a közös hozzáférésű jogosultságkód használatával hozhat létre. Az adat-előállítóban a tárolási erőforrások minden/specifikus korlátozott/időhöz kötött hozzáférést biztosít.

A közös hozzáférésű jogosultságkód a tárfiókban lévő erőforrások delegált hozzáférést biztosít. Segítségével azt adja meg egy ügyfél korlátozott engedélyekkel a tárfiókban lévő objektumok egy meghatározott ideig, és meghatározott engedélyekkel vannak beállítva. A tárelérési kulcsok megosztásához nem rendelkezik. A közös hozzáférésű jogosultságkódot URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. A közös hozzáférésű jogosultságkódot tárolási erőforrások eléréséhez az ügyfél csak a megfelelő konstruktort vagy metódust átadása az a közös hozzáférésű jogosultságkódot van szüksége. Megosztott hozzáférési aláírásokkal kapcsolatos további információkért lásd: [Shared access signatures: megérteni a megosztott hozzáférési aláírást modell](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Adat-előállító mostantól támogatja a csak megosztott hozzáférési aláírásokkal, de nem a fiók megosztott hozzáférési aláírásokkal. További információ a kétféle típusú, és hogyan készítse őket: [közös hozzáférésű jogosultságkód típusú](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). A közös hozzáférésű az Azure portálon vagy az Azure Tártallózó előállított aláírás URL-cím egy fiók közös hozzáférésű jogosultságkódot, amely nem támogatott.

> [!TIP]
> A szolgáltatás közös hozzáférésű jogosultságkód a tárfiók létrehozásához a következő PowerShell-parancsokat hajthat végre. Cserélje le a helyőrzőket, és adja meg a szükséges engedéllyel.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Szolgáltatás megosztott hozzáférési aláírást hitelesítés használatához a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **AzureStorage**. |Igen |
| sasUri | Adja meg a közös hozzáférésű jogosultságkódot URI a tárolási erőforrások, például blob, -tároló vagy tábla. Ez a mező megjelölése SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja az Azure integrációs futásidejű vagy a Self-hosted integrációs futásidejű (ha az adattároló egy magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

Amikor létrehoz egy közös hozzáférésű jogosultságkódot URI, vegye figyelembe a következő szempontokat:

- A társított szolgáltatás (olvasási, írási, olvasás/írás) az adat-előállítóban felhasznált alapján objektumok megfelelő olvasási/írási engedélyeket.
- Állítsa be **lejárati idejének** megfelelően. Győződjön meg arról, hogy a nem az adatcsatorna aktív időszakára jár le a tárolási objektum elérésére.
- Az URI a jobb oldali tábla szintjén a igények alapján létre kell hozni.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör az Azure Table-adatkészlet által támogatott tulajdonságokról.

Másolja az adatokat, és az Azure Table, állítsa be a type tulajdonságot a DataSet **AzureTable**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani **AzureTable**. |Igen |
| tableName |A tábla a tábla tárolási adatbázispéldány hivatkozik a társított szolgáltatás neve. |Igen |

**Példa**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Adat-előállító sémája

Sémamentesadat-tárolókhoz, például az Azure tábla adat-előállító kikövetkezteti a séma a következő módszerek valamelyikével:

* Ha az adatok szerkezete használatával adja meg a **struktúra** tulajdonság az adatkészlet-definícióban, adat-előállító eleget tegyen a séma szerint ez a struktúra. Ebben az esetben ha egy sor nem tartalmaz értéket egy oszlop, null értékű biztosított azt.
* Ha nem adja meg az adatok szerkezete használatával a **struktúra** tulajdonság az adatkészlet-definícióban, adat-előállító kikövetkezteti a séma az adatok első sora használatával. Ebben az esetben ha az első sor a teljes séma nem tartalmaz, azokat az oszlopokat rendszer nem talált a másolási művelet eredménye a.

Sémamentes adatforrások, az ajánlott eljárás, hogy az adatok szerkezete használatával adja meg a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure Table-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-table-as-a-source-type"></a>Azure-tábla egy forrás típusa

Adatok másolása az Azure Table, állítsa be a forrás típusa a másolási tevékenység **AzureTableSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani **AzureTableSource**. |Igen |
| azureTableSourceQuery |Az egyéni tábla tárolási lekérdezés segítségével adatokat olvasni. A következő szakaszban található példák. |Nem |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy a tábla nem létezik a beállítását.<br/>Két érték engedélyezett **igaz** és **hamis** (alapértelmezett). |Nem |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery példák

Ha az Azure tábla oszlop a datetime típusú:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Ha az Azure tábla oszlop karakterlánc típusú:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Ha a feldolgozási sor paraméter használata esetén konvertálni a datetime érték az előző minták alapján megfelelő formátumba.

### <a name="azure-table-as-a-sink-type"></a>Azure-tábla a fogadó típusa

Adatok másolása az Azure Table, állítsa be a fogadó típusa a másolási tevékenység **AzureTableSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani **AzureTableSink**. |Igen |
| azureTableDefaultPartitionKeyValue |Az alapértelmezett partíció kulcsérték a fogadó által használható. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek értékeket fogja használni, mint partíciókulcsok nevét. Ha nincs megadva, a partíciós kulcs "AzureTableDefaultPartitionKeyValue" lesz. |Nem |
| azureTableRowKeyName |Adja meg az az oszlop, amelynek oszlop értékeit a sor kulcsaként vannak használatban. Ha nincs megadva, minden egyes sorára használjon a GUID Azonosítót. |Nem |
| azureTableInsertType |Adatok beszúrása Azure Table mód. Ez a tulajdonság szabja meg, hogy rendelkeznek-e a meglévő sorokat a táblában az egyező partíció-és sorkulcsok cseréje vagy egyesített értékükre. <br/><br/>Két érték engedélyezett **egyesítési** (alapértelmezett) és **cserélje le**. <br/><br> Ez a beállítás a táblázatok szintjén nem vonatkozik a sor szintjén. Sem a lehetőség törli a sorokat a kimeneti táblához, amely nem szerepel a bemeneti. Című témakörben olvashat a lemezegyesítési és -csere beállítások működése, [entitás beszúrása vagy egyesítési](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [beszúrása vagy entitás cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nem |
| writeBatchSize |Szúr be Azure Table adatokat, amikor writeBatchSize vagy writeBatchTimeout találati.<br/>Megengedett értékek: egész szám (sorok száma). |Nem (alapértelmezett érték 10 000) |
| writeBatchTimeout |Szúr be Azure Table adatokat, amikor writeBatchSize vagy writeBatchTimeout találati.<br/>Megengedett értékek: timespan. Például "00: 20:00" (20 perc). |Nem (alapértelmezett érték 90 másodperc, a tárolási ügyfél alapértelmezett időtúllépés) |

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

A forrásoszlop hozzárendelése a céloszlop használatával a **"fordító"** tulajdonság előtt a céloszlop azureTablePartitionKeyName is használhatja.

A következő példában forrásoszlop DivisionID a céloszlop DivisionID van leképezve:

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

## <a name="data-type-mapping-for-azure-table"></a>Adattípus-hozzárendelése az Azure tábla

A kezdő és Azure Table a másolt adatok, a következő megfeleltetéseket használhatók az Azure tábla adattípusok adat-előállító ideiglenes adattípusok. Hogyan a másolási tevékenység van leképezve a séma- és adatok típusa a fogadó kapcsolatos további tudnivalókért lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md).

Amikor áthelyezni és az Azure tábla, a következő [megfeleltetéseket határozza meg Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) használják az Azure tábla OData típusok .NET-típus, és ez fordítva is igaz.

| Az Azure Table-adattípus | Data Factory ideiglenes adattípus | Részletek |
|:--- |:--- |:--- |
| Edm.Binary |Byte] |Bájttömb legfeljebb 64 KB. |
| Edm.Boolean |logikai érték |Logikai érték. |
| Edm.DateTime |DateTime |Egy 64 bites érték kifejezett, egyezményes világidő (UTC). A támogatott dátum és idő tartomány kezdete éjfél. január 1, i. 1601. (C.E.), UTC. A tartomány véget ér. December 31 9999. |
| Edm.Double |duplaszó |Egy 64 bites lebegőpontos értéket. |
| Edm.Guid |GUID |A 128 bites globálisan egyedi azonosítóját. |
| Edm.Int32 |Int32 |Egy 32 bites egész számot. |
| Edm.Int64 |Int64 |Egy 64 bites egész számot. |
| Edm.String |Karakterlánc |Az UTF-16 kódolású érték. Karakterlánc-értékek legfeljebb 64 KB lehet. |

## <a name="next-steps"></a>További lépések
Források és mosdók adat-előállítóban másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
