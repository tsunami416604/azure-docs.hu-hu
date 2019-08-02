---
title: Adatok másolása az Azure Table Storage-ba és az-ból az Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhatja át a támogatott forrásokból származó adatok adatait az Azure Table Storage-ba vagy a Table Storage-ból a támogatott fogadó áruházakba Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: c4ea3c93daac1ebb88bae2b8cb01485d955be2bb
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726200"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Adatok másolása az Azure Table Storage-ba és onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-table-connector.md)
> * [Aktuális verzió](connector-azure-table-storage.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban adatok másolásához az Azure Table Storage-ba és az-ból. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Támogatott képességek

Bármilyen támogatott forrás adattárból másolhat adatokat a Table Storage-ba. Az adatok a Table Storage-ból bármely támogatott fogadó adattárba is másolhatók. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, ez az Azure Table Connector támogatja az adatok másolását a fiók kulcsa és a szolgáltatás közös hozzáférésű aláírásának hitelesítése használatával.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Table Storage-hoz kapcsolódó Data Factory definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

### <a name="use-an-account-key"></a>Fiók kulcsának használata

Létrehozhat egy Azure Storage-beli társított szolgáltatást a fiók kulcsa alapján. Az adatelőállítót globális hozzáféréssel látja el a tárolóhoz. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureTableStorage**értékre kell beállítani. |Igen |
| connectionString | Adja meg a connectionString tulajdonság tárolási való csatlakozáshoz szükséges információk. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault is elhelyezheti a fiók kulcsát, és `accountKey` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha a "AzureStorage" típusú társított szolgáltatást használta, akkor továbbra is támogatott, míg a rendszer ezt az új "AzureTableStorage" társított szolgáltatástípus használatát javasolja.

**Példa:**

```json
{
    "name": "AzureTableStorageLinkedService",
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

**Példa: fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Közös hozzáférésű aláírás-hitelesítés használata

Létrehozhat egy megosztott elérési aláírással rendelkező Storage-beli társított szolgáltatást is. Az adat-előállítót a tárolóban lévő összes/meghatározott erőforráshoz korlátozott/időhöz kötött hozzáféréssel biztosítja.

Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Ezzel a beállítással engedélyezheti az ügyfél számára a Storage-fiókban lévő objektumokra vonatkozó korlátozott engedélyeket egy adott időtartamra és a megadott engedélyekkel. Nem kell megosztani a fiók hozzáférési kulcsait. A közös hozzáférésű jogosultságkód URI, amely a lekérdezési paraméterek magában foglalja a tárolási erőforrásokhoz való hitelesített hozzáférés szükséges összes információt. A közös hozzáférésű jogosultságkód-adattároló erőforrások elérésére, az ügyfél csak kell a megfelelő konstruktor vagy a metódus a közös hozzáférésű jogosultságkód adja át. A közös hozzáférésű aláírásokkal kapcsolatos további információkért [lásd: közös hozzáférésű aláírások: A közös hozzáférésű aláírás modelljének](../storage/common/storage-dotnet-shared-access-signature-part-1.md)megismerése.

> [!NOTE]
> A Data Factory mostantól támogatja mindkét **szolgáltatás közös hozzáférésű jogosultságkódot** és **fiók közös hozzáférésű jogosultságkód**. Két, és hogyan kell őket összeállítani kapcsolatos további információkért lásd: [közös hozzáférésű jogosultságkódok típusú](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> A tárfiók szolgáltatás közös hozzáférési aláírásokat létrehozni, hajtsa végre a következő PowerShell-parancsokat. Cserélje le a zárójelben, és a szükséges engedélyeket.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

A közös hozzáférésű aláírás-hitelesítés használatához a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **AzureTableStorage**értékre kell beállítani. |Igen |
| sasUri | A megosztott hozzáférési aláírás URI azonosítójának SAS URI azonosítójának megadása a táblához. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault SAS-tokent is helyezhet el az automatikus elforgatás kihasználása és a jogkivonat-rész eltávolításához. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha a "AzureStorage" típusú társított szolgáltatást használta, akkor továbbra is támogatott, míg a rendszer ezt az új "AzureTableStorage" társított szolgáltatástípus használatát javasolja.

**Példa:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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
- Az URI-t a megfelelő tábla szintjén kell létrehozni a szükséges érték alapján.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Azure Table adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Azure-táblába való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **AzureTable**értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **AzureTable**értékre kell állítani. |Igen |
| tableName |Annak a táblának a neve, amely a társított szolgáltatás által hivatkozott Table Storage-adatbázis példányában található. |Igen |

**Példa:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Adat-előállítók által séma

A séma nélküli adattárak, például az Azure Table Data Factory a következő módszerek egyikével következtetik ki a sémát:

* Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, adat-előállító figyelembe veszi a sémát, ez a struktúra. Ebben az esetben, ha egy sor nem tartalmaz értéket egy oszlophoz, a rendszer null értéket ad meg.
* Ha nem határozza meg az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságával, Data Factory kikövetkezteti a sémát az adat első sorának használatával. Ebben az esetben, ha az első sor nem tartalmazza a teljes sémát, egyes oszlopok kimaradnak a másolási művelet eredményében.

A séma nélküli adatforrások esetében az ajánlott eljárás az adatszerkezetek meghatározása a **Structure** tulajdonság használatával.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure Table forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-table-as-a-source-type"></a>Az Azure Table forrás típusa

Az adatok Azure-táblázatból való másolásához állítsa a forrás típusát a másolás tevékenység **AzureTableSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **AzureTableSource**értékre kell állítani. |Igen |
| azureTableSourceQuery |Az egyéni tábla-tárolási lekérdezéssel olvashatja el az adatolvasást. Tekintse meg a példákat a következő szakaszban. |Nem |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy a tábla kivételének engedélyezése nem létezik-e.<br/>Engedélyezett értékek a következők **igaz** és **hamis** (alapértelmezett). |Nem |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery examples

Ha az Azure Table oszlop datetime típusú:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Ha az Azure Table oszlop karakterlánc típusú:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Ha a folyamat paramétert használja, a DateTime értéket az előző minták szerint a megfelelő formátumba öntötte.

### <a name="azure-table-as-a-sink-type"></a>Azure Table fogadó típusa

Az Azure Table-be való másoláshoz állítsa a fogadó típust a másolás tevékenység **AzureTableSink**. A másolási tevékenység fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **AzureTableSink**értékre kell állítani. |Igen |
| azureTableDefaultPartitionKeyValue |A fogadó által használható alapértelmezett partíciós kulcs értéke. |Nem |
| azureTablePartitionKeyName |Adja meg annak az oszlopnak a nevét, amelynek értékeit partíciós kulcsként használja a rendszer. Ha nincs megadva, a rendszer a "AzureTableDefaultPartitionKeyValue" paramétert használja a partíciós kulcsként. |Nem |
| azureTableRowKeyName |Adja meg annak az oszlopnak a nevét, amelynek oszlop értékeit a rendszer a sor kulcsaként használja. Ha nincs megadva, használja az egyes sorok GUID azonosítóját. |Nem |
| azureTableInsertType |Az adatgyűjtés módja az Azure Table-be. Ez a tulajdonság azt szabályozza, hogy a kimeneti táblában található, egyező partícióval és sorokkal rendelkező meglévő sorok felülírják vagy összevonták-e az értékeket. <br/><br/>Az engedélyezett értékek **egyesítése** (alapértelmezett) és **Csere**. <br/><br> Ez a beállítás a tábla szintjén nem szereplő sorra vonatkozik. Egyik lehetőség sem törli a bemeneti tábla azon sorait, amelyek nem szerepelnek a bemenetben. Az egyesítési és a lecserélési beállítások működésének megismeréséhez lásd: [entitás beszúrása vagy egyesítése](https://msdn.microsoft.com/library/azure/hh452241.aspx) és az [entitás beszúrása vagy cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nem |
| writeBatchSize |Adatbeszúrás az Azure Table-be, ha a writeBatchSize vagy a writeBatchTimeout találat.<br/>Az engedélyezett értékek egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout |Adatbeszúrás az Azure Table-be, ha a writeBatchSize vagy a writeBatchTimeout találat.<br/>Az engedélyezett értékek a TimeSpan. Ilyen például a "00:20:00" (20 perc). |Nem (az alapértelmezett érték 90 másodperc, a Storage-ügyfél alapértelmezett időtúllépése) |

**Példa:**

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

A **"Translator"** tulajdonság használatával rendelje hozzá a forrás oszlopot a cél oszlophoz, mielőtt a azureTablePartitionKeyName használja a Destination (cél) oszlopot.

A következő példában a forrás oszlop DivisionID a DivisionID a cél oszlopra van leképezve:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

A "DivisionID" paraméter a partíciós kulcsként van megadva.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Adattípusok leképezése az Azure Table szolgáltatáshoz

Amikor Adatmásolást végez az Azure Table-be, az Azure Table adattípusokból az alábbi leképezéseket használja az ideiglenes adattípusok Data Factoryához. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus](copy-activity-schema-and-type-mapping.md)-leképezéseket.

Amikor az Azure Table-be vagy az-ba helyezi át az adatátvitelt, az [Azure Table által definiált](https://msdn.microsoft.com/library/azure/dd179338.aspx) következő leképezések az Azure Table OData típusaitól a .net-be és fordítva is használhatók.

| Az Azure Table adattípusa | Data Factory közbenső adattípus | Részletek |
|:--- |:--- |:--- |
| Edm.Binary |bájt [] |Legfeljebb 64 KB méretű bájtok tömbje. |
| Edm.Boolean |bool |Logikai érték. |
| Edm.DateTime |Datetime |64 bites érték, amely egyezményes világidő (UTC) szerint van kifejezve. A támogatott DateTime tartomány kezdete éjfél, január 1-től 1601 A.D. (CE), UTC. A tartomány 9999. december 31-ig ér véget. |
| Edm.Double |double |64 bites lebegőpontos érték. |
| Edm.Guid |Guid |Egy 128 bites globálisan egyedi azonosító. |
| Edm.Int32 |Int32 |32 bites egész szám. |
| Edm.Int64 |Int64 |64 bites egész szám. |
| Edm.String |Sztring |Egy UTF-16 kódolású érték. A karakterláncok értéke legfeljebb 64 KB lehet. |

## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
