---
title: "Adatok másolása az Azure Table Storage használata a Data Factory |} Microsoft Docs"
description: "Útmutató: adatok másolása az Azure Table Storage támogatott forrás áruházakból (vagy) a Table Storage támogatott fogadó áruházak Data Factory használatával."
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
ms.date: 11/07/2017
ms.author: jingwang
ms.openlocfilehash: ca5f8e43b6667aa1c2e3ac38e7ea00b5bd86b72f
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Data Factory használatához Azure táblából
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-table-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-table-storage.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, és az Azure tábla. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure Table Storage-összekötőt a V1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása az egyetlen támogatott forrás adattár az Azure Table vagy adatok másolása az Azure tábla bármely támogatott fogadó adattárolóhoz. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, az Azure Table-összekötő támogatja az adatok másolását a szolgáltatást is használja **fiókkulcs** és **szolgáltatás SAS** (közös hozzáférésű Jogosultságkód) hitelesítés.

## <a name="get-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való Azure Table Storage a Data Factory tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

### <a name="using-account-key"></a>Fiók kulcsával.

Az Azure tárolás társított szolgáltatásának a fiókkulcs, amely az adat-előállítóban globális hozzáférést biztosít az Azure Storage használatával hozhat létre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| connectionString | Adja meg a connectionString tulajdonság az Azure storage való kapcsolódáshoz szükséges adatokat. Ez a mező megjelölése SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

### <a name="using-service-sas-authentication"></a>Szolgáltatás SAS-hitelesítéssel

Az Azure tárolás társított szolgáltatásának is létrehozhat egy közös hozzáférésű Jogosultságkód (SAS), amely a tárolási erőforrások minden/specifikus korlátozott/időhöz kötött hozzáférést biztosít a data factory használatával.

Egy közös hozzáférésű Jogosultságkód (SAS) delegált hozzáférést biztosít azokhoz a tárfiókban lévő erőforrások. Lehetővé teszi az ügyfél csak korlátozott engedélyekkel a tárfiókban lévő objektumok egy adott időszakban, és engedélyeket, megadott számú anélkül, hogy a fiók hozzáférési kulcsait megosztásához megadását. A SAS URI, amely a lekérdezési paraméterek magában foglalja a hitelesített hozzáférést a tároló egyik erőforrásához szükséges összes adatot. Az SA-kat a tárolási erőforrások eléréséhez az ügyfélnek csak kell átadni a SAS a megfelelő konstruktort vagy metódust. Részletes információ a SAS: [megosztott hozzáférési aláírásokkal: az SAS-modell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Az Azure Data Factory most csak támogatja **szolgáltatás SAS** , de nem fiók SAS. Lásd: [típusok a megosztott hozzáférési aláírásokkal](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) kétféle típusú és létrehozására vonatkozó további információért. Az Azure-portálon generable SAS URL-cím vagy Tártallózó egy fiók SAS, ami nem támogatott.
>

Szolgáltatás SAS-hitelesítés használatához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureStorage** |Igen |
| sasUri | Adja meg a megosztott hozzáférési aláírást URI az Azure Storage-erőforrások, például a blob, -tároló vagy tábla. Ez a mező megjelölése SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

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

Amikor hoz létre egy **SAS URI**, figyelembe véve a következő szempontokat:

- Állítsa be a megfelelő olvasási/írási **engedélyek** alapján a társított szolgáltatás (olvasási, írási, olvasás/írás) az adat-előállítóban felhasznált objektumokon.
- Állítsa be **lejárati idejének** megfelelően. Győződjön meg arról, hogy az Azure Storage-objektumokhoz való hozzáférést nem jár le az adatcsatorna aktív időszakára.
- A jobb oldali tábla szintjén a igények alapján URI kell létrehozni.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure tábla a dataset által támogatott tulajdonságokról.

Adatok másolása az Azure tábla, állítsa be a type tulajdonságot az adathalmaz **AzureTable**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **AzureTable** |Igen |
| tableName |Az az Azure tábla adatbázispéldány táblájának, amelyre a társított szolgáltatás neve hivatkozik. |Igen |

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

Például az Azure tábla tárolóinak sémamentes adatokra a Data Factory szolgáltatásnak kikövetkezteti a séma a következő módszerek valamelyikével:

1. Ha az adatok szerkezete használatával adja meg a **struktúra** tulajdonsághoz a DataSet adatkészlet-definícióban a Data Factory szolgáltatásnak eleget tegyen a séma szerint ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy olyan oszlop értékét, null értékű biztosított azt.
2. Ha nem adja meg az adatok szerkezete használatával a **struktúra** tulajdonság az adatkészlet-definícióban, adat-előállító kikövetkezteti a séma az adatok első sora használatával. Ebben az esetben ha az első sort tartalmazza a teljes séma, azokat az oszlopokat vannak nem talált a másolási művelet eredménye.

Ezért sémamentes adatforrások, az ajánlott eljárás, hogy adja meg az adatok szerkezete a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure Table-forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-table-as-source"></a>Azure-tábla forrásaként

Adatok másolása az Azure Table, állítsa be a forrás típusa a másolási tevékenység **AzureTableSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **AzureTableSource** |Igen |
| azureTableSourceQuery |Az Azure tábla egyéni lekérdezés segítségével adatokat olvasni. Példák a következő szakaszban. |Nem |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy a tábla kivétel swallow nem létezik.<br/>Két érték engedélyezett: **igaz**, és **hamis** (alapértelmezett). |Nem |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery példák

Ha Azure táblaoszlop karakterlánc típusú:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Ha Azure táblaoszlop dátum/idő típusú:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>A fogadó Azure táblázatban

Adatok másolása az Azure Table, állítsa be a fogadó típusa a másolási tevékenység **AzureTableSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **AzureTableSink** |Igen |
| azureTableDefaultPartitionKeyValue |Alapértelmezett partíció kulcs értékét, amely a fogadó által használható. |Nem |
| azureTablePartitionKeyName |Adja meg az oszlop, amelynek értékeket fogja használni, mint partíciókulcsok nevét. Ha nincs megadva, a partíciós kulcs "AzureTableDefaultPartitionKeyValue" lesz. |Nem |
| azureTableRowKeyName |Adja meg az oszlop, amelynek oszlop értékeit sor kulcsaként vannak használatban. Ha nincs megadva, minden egyes sorára használjon a GUID Azonosítót. |Nem |
| azureTableInsertType |A mód lehet adatokat beszúrni az Azure-tábla. Ez a tulajdonság szabja meg, hogy rendelkeznek-e a meglévő sorokat a táblában az egyező partíció-és sorkulcsok cseréje vagy egyesített értékükre. <br/><br/>Két érték engedélyezett: **egyesítési** (alapértelmezett), és **cserélje le**. <br/><br> Ez a beállítás a sor szintjén, a táblázatok szintjén nem vonatkozik, és sem a lehetőség törli a kimeneti táblához, amely nem szerepel a bemeneti sorokat. Ezeket a beállításokat (lemezegyesítési és -csere) működése, lásd: [Insert vagy az egyesítéses entitás](https://msdn.microsoft.com/library/azure/hh452241.aspx) és [Insert vagy az entitás cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx) témaköröket. |Nem |
| WriteBatchSize |Amikor writeBatchSize vagy writeBatchTimeout találati adatok beillesztése az Azure-tábla.<br/>Két érték engedélyezett: egész szám (sorok száma) |Nem (alapértelmezett beállítás 10000) |
| writeBatchTimeout |Amikor writeBatchSize vagy writeBatchTimeout találati adatok beillesztése az Azure-tábla.<br/>Két érték engedélyezett: timespan. Példa: "00: 20:00" (20 perc) |Nem (alapértelmezett érték 90 másodperc - tároló ügyfél alapértelmezett időtúllépés) |

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

A forrásoszlop hozzárendelése a céloszlop "fordító" tulajdonság használatával, mint a azureTablePartitionKeyName a céloszlop használatba vétele előtt.

A következő példában a forrásoszlop DivisionID a céloszlop DivisionID lehet hozzárendelni.

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

/, Az Azure tábla az adatok másolásakor a következő leképezéseit segítségével az Azure Table-adattípusok Azure Data Factory ideiglenes adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

Ha megköveteli az adatok & Azure táblából, a következő [Azure Table szolgáltatás által meghatározott hozzárendelések](https://msdn.microsoft.com/library/azure/dd179338.aspx) használják az Azure tábla OData típusok .NET-típus, és ez fordítva is igaz.

| Az Azure Table-adattípus | Data factory ideiglenes adattípus | Részletek |
|:--- |:--- |:--- |
| Edm.Binary |Byte] |Bájttömb legfeljebb 64 KB. |
| Edm.Boolean |logikai érték |Logikai érték. |
| Edm.DateTime |Dátum és idő |Egy 64 bites érték kifejezett, egyezményes világidő (UTC). A támogatott dátum és idő tartomány kezdődik 12:00 éjféltől. január 1, i 1601. (SZ) (UTC). A tartomány vége December 31 9999. |
| Edm.Double |Dupla |Egy 64 bites lebegőpontos értéket. |
| Edm.Guid |GUID |A 128 bites globálisan egyedi azonosítóját. |
| Edm.Int32 |Int32 |Egy 32 bites egész számot. |
| Edm.Int64 |Int64 |Egy 64 bites egész számot. |
| Edm.String |Karakterlánc |Az UTF-16 kódolású érték. Karakterlánc-értékek legfeljebb 64 KB lehet. |

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).