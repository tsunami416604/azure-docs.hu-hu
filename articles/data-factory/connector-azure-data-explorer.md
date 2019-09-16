---
title: Adatok másolása az Azure Adatkezelőba vagy onnan az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok az Azure Adatkezelőba vagy onnan egy Azure Data Factory folyamat másolási tevékenységének használatával.
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
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010259"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Adatok másolása az Azure Adatkezelőba vagy onnan az Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure- [Adatkezelőba](../data-explorer/data-explorer-overview.md)való másolásához. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Adatkezelő-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Bármely támogatott forrás adattárból az Azure Adatkezelőba másolhat adatok. Az Azure Adatkezelőról bármilyen támogatott fogadó adattárba másolhatja az adatok adatait is. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md) tábla.

>[!NOTE]
>A 3,14-es verzió óta támogatott az adatok másolása az Azure-Adatkezelőba/-ból a helyszíni adattárakba, a saját üzemeltetésű Integration Runtime használatával.

Az Azure Adatkezelő Connector a következőket teszi lehetővé:

* Az Adatmásolás Azure Active Directory (Azure AD) alkalmazás-jogkivonat-hitelesítéssel egy **egyszerű szolgáltatás**használatával.
* Forrásként egy KQL-(Kusto-) lekérdezés használatával lekérheti az adatgyűjtést.
* Fogadóként fűzze hozzá az adatkészlethez.

## <a name="getting-started"></a>Első lépések

>[!TIP]
>Az Azure Adatkezelő Connector használatának áttekintését lásd: [adatok másolása az Azure-ba vagy az Azure-adatkezelő az Azure Data Factory használatával](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Azure Adatkezelő connectorhoz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Adatkezelő-összekötő az egyszerű szolgáltatásnév hitelesítését használja. Kövesse az alábbi lépéseket egy egyszerű szolgáltatásnév beszerzéséhez és az engedélyek megadásához:

1. Regisztráljon az Azure Active Directoryban (Azure AD) application entitás a következő [regisztrálja az alkalmazást az Azure AD-bérlő](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg az egyszerű szolgáltatás megfelelő engedélyét az Azure Adatkezelőban. A szerepkörökkel és engedélyekkel kapcsolatos részletes információkkal és az engedélyek kezelésével kapcsolatos információkért tekintse meg az [Azure adatkezelő Database-engedélyek felügyeletét](../data-explorer/manage-database-permissions.md) ismertető témakört. Általánosságban elmondható, hogy

    - **Forrásként**adjon meg legalább **adatbázis-megjelenítői** szerepkört az adatbázis számára.
    - Fogadóként adjon meg legalább **adatbázis** -betöltési szerepkört az adatbázisához.

>[!NOTE]
>Ha ADF felhasználói felületet használ a létrehozáshoz, a bejelentkezési felhasználói fiók az Azure Adatkezelő-fürtök,-adatbázisok és-táblák listázására szolgál. Ha nincs engedélye az ilyen művelethez, manuálisan adja meg a nevet.

Az Azure Adatkezelő társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **AzureDataExplorer** értékre kell beállítani. | Igen |
| endpoint | Az Azure Adatkezelő-fürt végponti URL-címe, amelynek `https://<clusterName>.<regionName>.kusto.windows.net`formátuma:. | Igen |
| database | Az adatbázis neve. | Igen |
| tenant | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. A [Kusto-kapcsolatok karakterláncában](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)általában a "**szolgáltatói azonosító**" néven is ismert. A Azure Portal jobb felső sarkában található egérrel vigye le. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. Ezt általában "**HRE Application Client ID**"-ként ismeri a Kusto- [kapcsolatok karakterláncában](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Ezt általában "**HRE**"-ként ismeri a [Kusto-kapcsolatok karakterláncában](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

**Társított szolgáltatás tulajdonságai – példa:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Azure Adatkezelő adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni az Azure Adatkezelőba, állítsa az adatkészlet Type (típus) tulajdonságát **AzureDataExplorerTable**értékre.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **AzureDataExplorerTable** értékre kell beállítani. | Igen |
| table | Annak a táblának a neve, amelyre a társított szolgáltatás hivatkozik. | Igen a fogadó számára; Nem a forráshoz |

**Adatkészlet tulajdonságai – példa**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure Adatkezelő forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-explorer-as-source"></a>Azure-Adatkezelő forrásként

Az adatok Azure-Adatkezelőból való másolásához állítsa a **Type (típus** ) tulajdonságot a másolási tevékenység forrása **AzureDataExplorerSource**értékre. A következő tulajdonságok támogatottak a másolási tevékenység **source** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a **következőre** kell beállítani: **AzureDataExplorerSource** | Igen |
| query | Egy [KQL-formátumban](/azure/kusto/query/)megadott írásvédett kérelem. Hivatkozásként használja az egyéni KQL-lekérdezést. | Igen |
| queryTimeout | A lekérdezési kérelem időtúllépése előtti várakozási idő. Az alapértelmezett érték 10 perc (00:10:00); az engedélyezett maximális érték 1 óra (01:00:00). | Nem |
| nincs csonkítás | Azt jelzi, hogy le kell-e vágni a visszaadott eredményhalmaz értékét. Alapértelmezés szerint a rendszer a 500 000-es rekordok vagy az 64 MB után csonkolja az eredményt. Erősen ajánlott a csonkítás a tevékenység megfelelő működéséhez. |Nem |

>[!NOTE]
>Az Azure Adatkezelő-forrás alapértelmezett mérete 500 000 rekord vagy 64 MB. Ha az összes rekordot csonkítás nélkül szeretné lekérni, `set notruncation;` megadhatja a lekérdezés elején. További részletekért tekintse meg a [lekérdezési korlátozásokat](https://docs.microsoft.com/azure/kusto/concepts/querylimits) .

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure-Adatkezelő fogadóként

Az Azure Adatkezelőba való adatmásoláshoz állítsa a másolási tevékenység fogadójának Type (típus) tulajdonságát a **AzureDataExplorerSink**értékre. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát a **következőre** kell beállítani: **AzureDataExplorerSink** | Igen |
| ingestionMappingName | Egy előre létrehozott **[hozzárendelés](/azure/kusto/management/mappings#csv-mapping)** neve egy Kusto táblán. Az oszlopok forrásból az Adatkezelő Azure-ba történő leképezéséhez – amely az **[összes támogatott forrás-és formátumra](copy-activity-overview.md#supported-data-stores-and-formats)** vonatkozik, beleértve a CSV/JSON/Avro formátumokat stb. – a másolási tevékenység [oszlop leképezése](copy-activity-schema-and-type-mapping.md) (implicit módon, név vagy explicit módon konfiguráltként) használható. /vagy Azure Adatkezelő leképezések. | Nem |

**Példa:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>További lépések

* A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

* További információ az [adatok Azure Data Factoryról az Azure Adatkezelőba való másolásáról](/azure/data-explorer/data-factory-load-data).