---
title: Adatok másolása az Azure Table tárolóba és onnan
description: Megtudhatja, hogyan másolhatja az adatokat a támogatott forrástárolókból az Azure Table storage-ba, vagy a Table storage-ból a támogatott fogadótárolókba a Data Factory használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: 6edd32f8f3579238d1f08f55ce9fb1528fa5d211
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417481"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Adatok másolása az Azure Table storage-ba és onnan az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-table-connector.md)
> * [Aktuális verzió](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása az Azure Table storage-ba és onnan. A [Tevékenység másolása áttekintéscímű](copy-activity-overview.md) cikkre épül, amely a Másolási tevékenység általános áttekintését mutatja be.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Table storage-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Bármely támogatott forrásadattárból adatokat másolhat table storage-ba. A Table storage-ból is másolhat adatokat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában láthatja.

Pontosabban ez az Azure Table-összekötő támogatja az adatok másolását a fiókkulcs és a szolgáltatás megosztott hozzáférés-aláírás-hitelesítések használatával.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Table storage-ra jellemző Data Factory-entitások definiálására használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

### <a name="use-an-account-key"></a>Fiókkulcs használata

Azure Storage-alapú szolgáltatás a fiókkulcs használatával hozhat létre. Globális hozzáférést biztosít az adatgyár számára a Storage szolgáltatáshoz. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **az AzureTableStorage**tulajdonságra kell állítani. |Igen |
| connectionString (kapcsolati karakterlánc) | Adja meg a connectionString tulajdonság storage-hoz való csatlakozásához szükséges adatokat. <br/>Fiókkulcsot is helyezhet el az Azure `accountKey` Key Vaultban, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. |Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>Ha az "AzureStorage" típusú kapcsolt szolgáltatás használata, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja ezt az új "AzureTableStorage" csatolt szolgáltatástípus megy előre.

**Példa:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiókkulcs tárolása az Azure Key Vaultban**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

### <a name="use-shared-access-signature-authentication"></a>Megosztott hozzáférésű aláírás-hitelesítés használata

A storage-hoz csatolt szolgáltatást megosztott hozzáférésű aláírással is létrehozhatja. Az adat-előállító számára korlátozott/időhöz kötött hozzáférést biztosít a tárolóösszes/specifikus erőforrásához.

A megosztott hozzáférésű aláírás delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz. Segítségével korlátozott engedélyeket adhat az ügyfélnek a tárfiókban lévő objektumokhoz egy adott ideig és egy megadott engedélykészlettel. Nem kell megosztania a fiókhozzáférési kulcsokat. A megosztott hozzáférésű aláírás olyan URI, amely a lekérdezési paraméterekben a tárolási erőforrás hitelesített eléréséhez szükséges összes információt magában foglalja. A megosztott hozzáférésű aláírással rendelkező tárolóerőforrások eléréséhez az ügyfélnek csak a megosztott hozzáférésű aláírást kell átadnia a megfelelő konstruktornak vagy metódusnak. A megosztott hozzáférésű aláírásokról a [Megosztott hozzáférésű aláírások: A közös hozzáférésű aláírásmodell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md)című témakörben talál további információt.

> [!NOTE]
> A Data Factory mostantól támogatja a **szolgáltatás megosztott hozzáférési aláírásait** és **a fiók megosztott hozzáférési aláírásait is.** A megosztott hozzáférésű aláírásokról további információt az [Azure Storage-erőforrásokhoz való korlátozott hozzáférés megadása megosztott hozzáférésű aláírások (SAS) használatával](../storage/common/storage-sas-overview.md)című témakörben talál. 

> [!TIP]
> Ha egy szolgáltatás megosztott hozzáférési aláírását a tárfiókhoz, a következő PowerShell-parancsokat hajthatja végre. Cserélje le a helyőrzőket, és adja meg a szükséges engedélyt.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

A megosztott hozzáférésű aláírás-hitelesítés használatához a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **az AzureTableStorage**tulajdonságra kell állítani. |Igen |
| sasUri között | Adja meg a közös hozzáférésű aláírás URI-jának SAS URI-ját a táblához. <br/>Jelölje meg ezt a mezőt SecureStringként, hogy biztonságosan tárolhatja a Data Factory-ban. SAS-jogkivonatot is helyezhet az Azure Key Vaultban az automatikus rotáció kihasználásához és a jogkivonat-rész eltávolításához. További részleteket az [Azure Key Vault-cikkben](store-credentials-in-key-vault.md) a következő minták és áruházi hitelesítő adatok című cikkben talál. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy a saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

>[!NOTE]
>Ha az "AzureStorage" típusú kapcsolt szolgáltatás használata, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja ezt az új "AzureTableStorage" csatolt szolgáltatástípus megy előre.

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

**Példa: fiókkulcs tárolása az Azure Key Vaultban**

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

Amikor közös hozzáférésű hozzáférési kód URI-t hoz létre, vegye figyelembe a következő pontokat:

- Állítsa be a megfelelő olvasási/írási engedélyeket az objektumokhoz a csatolt szolgáltatás (olvasás, írás, írás/írás) adat-előállítóban való használatával.
- Megfelelően állítsa be **a lejárati időt.** Győződjön meg arról, hogy a storage-objektumokhoz való hozzáférés nem jár le a folyamat aktív időszakán belül.
- Az URI-t a megfelelő táblaszinten kell létrehozni az igény alapján.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz az Azure Table adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Azure Table-be és az Azure Table-ből történő másoláshoz állítsa az adatkészlet típustulajdonságát az **AzureTable beállításra.** A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát **az AzureTable**beállításra kell állítani. |Igen |
| tableName |A tábla neve a tábla tároló adatbázispéldányában, amelyre a csatolt szolgáltatás hivatkozik. |Igen |

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

### <a name="schema-by-data-factory"></a>Séma adatgyár szerint

Sémamentes adattárak, például az Azure Table esetében a Data Factory az alábbi módokon következtet a sémából:

* Ha az oszlopleképezést másolási tevékenységben adja meg, a Data Factory a forrásoldali oszloplista segítségével olvassa be az adatokat. Ebben az esetben, ha egy sor nem tartalmaz értéket egy oszlophoz, null értéket ad meg.
* Ha nem adja meg az oszlopleképezést a másolási tevékenységben, a Data Factory az adatok első sorának használatával következtet a sémából. Ebben az esetben, ha az első sor nem tartalmazza a teljes sémát (pl. néhány oszlop null értékkel rendelkezik), néhány oszlop kimarad a másolási művelet eredményében.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az Azure Table forrás és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-table-as-a-source-type"></a>Az Azure Table forrástípusként

Ha adatokat szeretne másolni az Azure Table programból, állítsa be a forrástípust az **AzureTableSource**másolási tevékenységben. A következő tulajdonságokat a másolási tevékenység **forrása** szakasz támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás típustulajdonságát **az AzureTableSource**mezőre kell állítani. |Igen |
| azureTableSourceQuery |Az adatok olvasásához használja az egyéni táblatárolási lekérdezést. Tekintse meg a következő szakaszban található példákat. |Nem |
| azureTableSourceIgnoreTableNotFound |Azt jelzi, hogy engedélyezi-e a tábla kivételének nem létezését.<br/>Az engedélyezett **értékek: Igaz** és **Hamis** (alapértelmezett). |Nem |

### <a name="azuretablesourcequery-examples"></a>példák az azureTableSourceQuery

>[!NOTE]
>Az Azure Table lekérdezési művelet e-egy időadatát 30 másodperc alatt [kikényszeríti az Azure Table szolgáltatás.](https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations) Megtudhatja, hogy miként optimalizálhatja a lekérdezést a Design ból a cikk [lekérdezéséhez.](../storage/tables/table-storage-design-for-query.md)

Az Azure Data Factoryban, ha az adatokat egy datetime típusú oszlophoz szeretné szűrni, olvassa el ezt a példát:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Ha az adatokat karakterlánctípus-oszlopra szeretné szűrni, olvassa el ezt a példát:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Ha a folyamat paramétert használja, a datetime értéket az előző mintáknak megfelelő formátumba adja.

### <a name="azure-table-as-a-sink-type"></a>Az Azure Table mint fogadótípus

Az Azure Table adatok másolásához állítsa be a fogadó típusát a másolási tevékenységben az **AzureTableSink**elemre. A következő tulajdonságokat a másolási tevékenység **fogadó** szakasza támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának típustulajdonságát **az AzureTableSink**beállításra kell állítani. |Igen |
| azureTableDefaultPartitionKeyValue érték |A fogadó által használható alapértelmezett partíciókulcs-érték. |Nem |
| azureTablePartitionKeyName |Adja meg annak az oszlopnak a nevét, amelynek értékeit partíciókulcsként használják. Ha nincs megadva, az "AzureTableDefaultPartitionKeyValue" lesz a partíciókulcs. |Nem |
| azureTableRowKeyName |Adja meg annak az oszlopnak a nevét, amelynek oszlopértékeit sorkulcsként használják. Ha nincs megadva, minden sorhoz használjon GUID azonosítót. |Nem |
| azureTableInsertType |Az Azure Table-be beillesztendő mód. Ez a tulajdonság azt szabályozza, hogy a kimeneti tábla egyező partícióval és sorkulcsokkal rendelkező meglévő sorai tették-e lecserélve vagy egyesítve a kimeneti tábla meglévő sorait. <br/><br/>Az engedélyezett értékek **az egyesítés** (alapértelmezett) és **a csere**. <br/><br> Ez a beállítás a sor szintjén, nem pedig a táblázat szintjén érvényes. Egyik lehetőség sem törli azokat a sorokat a kimeneti táblából, amelyek nem léteznek a bemenetben. A beállításegyesítési és cserebeállítások működéséről az [Entitás beszúrása vagy egyesítése,](https://msdn.microsoft.com/library/azure/hh452241.aspx) valamint az [Entitás beszúrása vagy cseréje](https://msdn.microsoft.com/library/azure/hh452242.aspx)című témakörben olvashat. |Nem |
| writeBatchSize |Adatokat szúr be az Azure Table-be, ha writeBatchSize vagy writeBatchTimeout találat.<br/>Az engedélyezett értékek egész szám (sorok száma). |Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout |Adatokat szúr be az Azure Table-be, ha writeBatchSize vagy writeBatchTimeout találat.<br/>Az engedélyezett értékek időtartomány. Erre példa a "00:20:00" (20 perc). |Nem (az alapértelmezett érték 90 másodperc, a tárolóügyfél alapértelmezett időtúlszáma) |

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

Rendeljen hozzá egy forrásoszlopot egy céloszlophoz a **"translator"** tulajdonság használatával, mielőtt a céloszlopot azureTablePartitionKeyName néven használhatna.

A következő példában a DivisionID forrásoszlop a DivisionID céloszlophoz van rendelve:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

A "DivisionID" a partíciókulcs.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Adattípus-leképezés az Azure Table-hez

Amikor adatokat másol az Azure Table-ből és az Azure Table-be, a következő leképezéseket használja a rendszer az Azure Table adattípusaiból a Data Factory köztes adattípusokba. Ha többet szeretne tudni arról, hogy a másolási tevékenység hogyan rendeli le a forrássémát és az adattípust a fogadóhoz, olvassa el a [Séma- és adattípus-hozzárendelések című témakört.](copy-activity-schema-and-type-mapping.md)

Amikor adatokat helyez át az Azure Table-be és onnan, az Azure Table által meghatározott alábbi [leképezések](https://msdn.microsoft.com/library/azure/dd179338.aspx) az Azure Table OData-típusokból .NET típusúak, és fordítva.

| Azure Table adattípusa | Adatgyár köztes adattípusa | Részletek |
|:--- |:--- |:--- |
| Edm.Bináris |bájt[] |Legfeljebb 64 KB bájtok ból álló tömb. |
| Edm.Boolean |Bool |Logikai érték. |
| Edm.DateTime |DateTime |64 bites érték koordinált világidőben (UTC) kifejezve. A támogatott DateTime tartomány 1601. (C.E.), UTC. A tartomány 9999. |
| Edm.Double |double |64 bites lebegőpontos érték. |
| Edm.Guid |Guid |128 bites globálisan egyedi azonosító. |
| Edm.Int32 |Int32 |Egy 32 bites egész szám. |
| Edm.Int64 |Int64 |Egy 64 bites egész szám. |
| Edm.String |Sztring |UTF-16 kódolású érték. A karakterlánc-értékek legfeljebb 64 KB-ot tehetnek ki. |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>További lépések
A Data Factory másolási tevékenysége által forrásként és fogadóként támogatott adattárak listáját a Támogatott adattárak című témakörben [tetszését.](copy-activity-overview.md#supported-data-stores-and-formats)
