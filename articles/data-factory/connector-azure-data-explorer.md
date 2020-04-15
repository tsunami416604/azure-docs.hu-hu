---
title: Adatok másolása az Azure Data Explorer be vagy onnan
description: Megtudhatja, hogyan másolhat adatokat az Azure Data Explorerbe vagy onnan egy Azure Data Factory-folyamat másolási tevékenységhasználatával.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 5f0eada5184f2c8e36b0deb5a9d27a8a9230776d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382763"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Explorer be- vagy kii-kezelőprogramból az Azure Data Factory használatával

Ez a cikk azt ismerteti, hogy miként használhatja a másolási tevékenységet az Azure Data Factoryban az [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)be vagy onnan történő másoláshoz. A [másolási tevékenység áttekintéséről](copy-activity-overview.md) szóló cikkre épül, amely általános áttekintést nyújt a másolási tevékenységről.

>[!TIP]
>Az Azure Data Factory és az Azure Data Explorer integrációjához általában az [Azure Data Explorer integrálása az Azure Data Factory szolgáltatással](/azure/data-explorer/data-factory-integration)című cikkből olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Data Explorer-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Bármely támogatott forrásadattárból adatokat másolhat az Azure Data Explorerbe. Az Azure Data Explorerből adatokat is másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárolók listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

>[!NOTE]
>Az adatok másolása az Azure Data Explorer be vagy onnan egy helyszíni adattáron keresztül saját üzemeltetésű integrációs futtatókörnyezet használatával a 3.14-es és újabb verzió ban is támogatott.

Az Azure Data Explorer-összekötővel a következőket teheti:

* Adatok másolása az Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésével **egyszerű szolgáltatással.**
* Forrásként kql (Kusto) lekérdezéssel olvassa be az adatokat.
* Fogadóként adatokat fűzhet a céltáblához.

## <a name="getting-started"></a>Első lépések

>[!TIP]
>Az Azure Data Explorer-összekötő forgatókönyve: [Adatok másolása/az Azure Data Explorerből az Azure Data Factory használatával,](/azure/data-explorer/data-factory-load-data) [valamint az adatbázisból az Azure Data Explorerbe történő tömeges másolása](/azure/data-explorer/data-factory-template)című témakörben található.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az Azure Data Explorer-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az Azure Data Explorer-összekötő egyszerű szolgáltatáshitelesítést használ. Az egyszerű szolgáltatás leéséhez és az engedélyek megadásához kövesse az alábbi lépéseket:

1. Regisztráljon egy alkalmazásentitást az Azure Active Directoryban az [alkalmazás regisztrálása egy Azure AD-bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című lépés lépéseit követve. Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatásnév a megfelelő engedélyeket az Azure Data Explorerben. A szerepkörökkel és engedélyekkel, valamint az engedélyek kezelésével kapcsolatos részletes információkért tekintse meg az [Azure Data Explorer adatbázis-engedélyeinek kezelése](/azure/data-explorer/manage-database-permissions) című témakört. Általában a következőket kell tennie:

    - **Forrásként**legalább az **adatbázis-megjelenítő** szerepkört adja meg az adatbázisnak
    - **Fogadóként**legalább az **adatbázis betöltési** szerepkörét adja meg az adatbázisnak

>[!NOTE]
>Amikor a Data Factory felhasználói felületét használja a szerkesztéshez, a bejelentkezési felhasználói fiók az Azure Data Explorer-fürtök, adatbázisok és táblák listázására szolgál. Adja meg manuálisan a nevet, ha nincs engedélye ezekhez a műveletekhez.

Az Azure Data Explorer csatolt szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **az AzureDataExplorer**beállításra kell állítani. | Igen |
| endpoint | Az Azure Data Explorer-fürt végponturl-címe `https://<clusterName>.<regionName>.kusto.windows.net`a következő formátummal: . | Igen |
| adatbázis | Az adatbázis neve. | Igen |
| Bérlő | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Ezt a [Kusto-kapcsolati karakterlánc](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"Hatóságazonosító" néven ismeri. Az egérmutató támpontozásával az Azure Portal jobb felső sarkában. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfélazonosítóját. Ezt "AAD alkalmazásügyfél-azonosítónak" nevezzük a [Kusto kapcsolati karakterláncban.](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) | Igen |
| servicePrincipalKey | Adja meg az alkalmazás kulcsát. Ezt "AAD alkalmazáskulcsnak" [nevezzük a Kusto-kapcsolati karakterláncban.](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties) Jelölje meg ezt a mezőt **SecureStringként** a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt biztonságos adatokra.](store-credentials-in-key-vault.md) | Igen |

**Hivatkozott szolgáltatástulajdonságok példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek az Azure Data Factory ban](concepts-datasets-linked-services.md)című témakörben tartalmazza. Ez a szakasz felsorolja az Okat, amelyeket az Azure Data Explorer adatkészlet támogatja.

Az adatok Azure Data Explorer bemásolásához állítsa az adatkészlet típustulajdonságát az **AzureDataExplorerTable mezőre.**

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **az AzureDataExplorerTable mezőre**kell állítani. | Igen |
| tábla | Annak a táblának a neve, amelyre a csatolt szolgáltatás hivatkozik. | Igen a mosogató; Nem a forráshoz |

**Például adatkészlet tulajdonságai:**

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Azure Data Factory folyamatai és tevékenységei című témakörben tartalmazza.](concepts-pipelines-activities.md) Ez a szakasz az Azure Data Explorer-források és -fogadók által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-explorer-as-source"></a>Az Azure Data Explorer mint forrás

Ha adatokat szeretne másolni az Azure Data Explorerből, állítsa be a **típustulajdonságot** a Tevékenységforrás másolása forrásban az **AzureDataExplorerSource programba.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságát** a következőre kell állítani: **AzureDataExplorerSource** | Igen |
| lekérdezés | [KQL formátumban](/azure/kusto/query/)megadott írásvédett kérelem . Használja az egyéni KQL lekérdezést hivatkozásként. | Igen |
| queryTimeout | A lekérdezési kérelem előtti várakozási idő idővel idővel elévül. Az alapértelmezett érték 10 min (00:10:00); megengedett maximális érték 1 óra (01:00:00). | Nem |
| noTruncation | Azt jelzi, hogy csonkolja-e a visszaadott eredményhalmazt. Alapértelmezés szerint az eredmény 500 000 rekord vagy 64 megabájt (MB) után csonkul. A csonkolás erősen ajánlott a tevékenység helyes viselkedésének biztosítása érdekében. |Nem |

>[!NOTE]
>Alapértelmezés szerint az Azure Data Explorer forrása mérete 500 000 rekord vagy 64 MB. Ha az összes rekordot csonkolás nélkül `set notruncation;` szeretné beolvasni, a lekérdezés elején is megadhatja. További információt a Lekérdezési korlátok című [témakörben talál.](https://docs.microsoft.com/azure/kusto/concepts/querylimits)

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

### <a name="azure-data-explorer-as-sink"></a>Az Azure Data Explorer fogadóként

Ha adatokat szeretne másolni az Azure Data Explorerbe, állítsa be a típustulajdonságot a másolási tevékenység fogadójában az **AzureDataExplorerSink**mezőre. A másolási tevékenység **fogadója** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **típustulajdonságát** a következőre kell állítani: **AzureDataExplorerSink**. | Igen |
| betöltés: MappingName | Egy Kusto táblában előre létrehozott [leképezés](/azure/kusto/management/mappings#csv-mapping) neve. Az oszlopok leképezése a forrásból az Azure Data Explorer (amely az [összes támogatott forrástárolók és formátumok,](copy-activity-overview.md#supported-data-stores-and-formats)beleértve a CSV/JSON/Avro formátumok), használhatja a másolási tevékenység [oszlop leképezés](copy-activity-schema-and-type-mapping.md) (implicit név szerint vagy explicit módon konfigurálva) és/vagy az Azure Data Explorer leképezések. | Nem |
| további tulajdonságok | Egy tulajdonságtáska, amely az Azure Data Explorer-fogadó által még nem beállított betöltési tulajdonságok megadásához használható. Pontosabban hasznos lehet a betöltési címkék megadásához. További információ az [Azure Data Explore adatbetöltési dokumentumból.](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html) | Nem |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokról további információt a [Keresgaszíntevékenység](control-flow-lookup-activity.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

* Az Azure Data Factory ban a másolási tevékenység által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszetős ekben.

* További információ arról, hogyan [másolhat adatokat az Azure Data Factoryból az Azure Data Explorerbe.](/azure/data-explorer/data-factory-load-data)
