---
title: Adatok másolása, vagy az Azure az adatkezelőt az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat, vagy az Azure az adatkezelőt az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 03/04/2019
ms.author: orspod
ms.openlocfilehash: 41cdae310fb9c2fc66ec9ed78ddc21596c9a5ba9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317849"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Adatok másolása, vagy az Azure az adatkezelőt az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához, vagy a [Azure adatkezelő](../data-explorer/data-explorer-overview.md). Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Bármely támogatott forrásadattárból adatokat másolhatja az Adatkezelőbe az Azure. Másolhat adatokat is az Azure Data Explorer bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md) tábla.

>[!NOTE]
>3,14-es verziótól kezdve támogatja az adatok és-tárolókról az Azure Data Explorer /, a helyi integrációs modul használatával a helyszíni adattárban való másolását.

Az Azure Data Explorer-összekötő teszi lehetővé tegye a következőket:

* Adatok másolása az Azure Active Directory (Azure AD) alkalmazástoken-hitelesítésének a használatával egy **szolgáltatásnév**.
* Egy forrásként egy KQL (Kusto) lekérdezés segítségével adatok beolvasása.
* A céltábla adatok hozzáfűzése, fogadóként.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Azure adatkezelő összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Data Explorer-összekötő szolgáltatás egyszerű hitelesítést használ. Kövesse az alábbi lépéseket egy egyszerű szolgáltatást, és engedélyek megadása:

1. Regisztráljon az Azure Active Directoryban (Azure AD) application entitás a következő [regisztrálja az alkalmazást az Azure AD-bérlő](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:

    - Alkalmazásazonosító
    - Alkalmazáskulcs
    - Bérlőazonosító

2. Adja meg a szolgáltatás egyszerű megfelelő engedéllyel az Azure Data Explorer. Tekintse meg [adatbázis-engedélyek kezelése az Azure Data Explorer](../data-explorer/manage-database-permissions.md) szerepkörök és engedélyek, valamint az engedélyek kezelése forgatókönyv vonatkozó részletes információkkal együtt. Általánosságban véve kell

    - **Forrásként**, adjon meg legalább **adatbázis megjelenítő** szerepkör az adatbázishoz.
    - **Fogadóként**, adjon meg legalább **adatbázis módon eredményesen dolgozható** szerepkör az adatbázishoz.

>[!NOTE]
>Hozzon létre az ADF felhasználói felület használatakor a műveletek a társított szolgáltatás adatbázisok listázása vagy adatkészlet lévő táblákat felsoroló a szolgáltatásnévhez tartozó magasabb szintű jogosultsággal rendelkező engedély lehet szükség. Azt is megteheti Ha szeretné, adja meg manuálisan adatbázis és tábla nevét. Másolja a tevékenység végrehajtási működik, mindaddig, amíg az olvasási/írási adatok megfelelő jogosultságot kap az egyszerű szolgáltatás.

Azure Data Explorer társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **AzureDataExplorer** | Igen |
| endpoint | Végpont fürt URL-címét az Azure Data Explorer, a következő formátumban, `https://<clusterName>.<regionName>.kusto.windows.net `. | Igen |
| adatbázis | Adatbázis neve. | Igen |
| bérlő | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Az Azure portal jobb felső sarkában az egérrel viszi, lekéréséhez. | Igen |
| servicePrincipalId | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| servicePrincipalKey | Adja meg az alkalmazáskulcsot. Jelölje meg a mező egy **SecureString** tárolja biztonságos helyen a Data Factory áttekintése, vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

**Társított szolgáltatás tulajdonságait. példa:**

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Azure Data Explorer adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az Azure az adatkezelőt, állítsa be a type tulajdonság, az adatkészlet **AzureDataExplorerTable**.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **AzureDataExplorerTable** | Igen |
| tábla | A tábla, amely hivatkozik a társított szolgáltatás neve. | Igen, a fogadó; Nincs forrás esetében |

**Adatkészlet tulajdonságai példa**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure Data Explorer forrásaként és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-data-explorer-as-source"></a>Az Azure Data Explorer forrásként

Adatok másolása az Azure az adatkezelőt, állítsa be a **típus** tulajdonság a másolási tevékenység forrásból való **AzureDataExplorerSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát: **AzureDataExplorerSource** | Igen |
| lekérdezés | Megadott olvasási kérést egy [KQL formátum](/azure/kusto/query/). Használja az egyéni KQL lekérdezés referenciaként. | Igen |
| queryTimeout | A várakozási idő előtt a lekérdezési kérés túllépi az időkorlátot. Alapértelmezett érték 10 perc (00: 10:00); engedélyezett maximális értéke 1 óra (01: 00:00). | Nem |

>[!NOTE]
>Alapértelmezés szerint az Azure Data Explorer forrás rendelkezik 500 000 rekord vagy 64 MB-os méretkorlátot. Csonkolási nélkül a rekordok lekéréséhez is megadhat `set notruncation;` , a lekérdezés elején. Tekintse meg [lekérdezési korlátok](https://docs.microsoft.com/en-us/azure/kusto/concepts/querylimits) a további részleteket.

**Példa**

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

### <a name="azure-data-explorer-as-sink"></a>Az Azure Data Explorer pedig a fogadó

Adatok másolása az Azure az adatkezelőt, állítsa be a type tulajdonság, a másolási tevékenység fogadó **AzureDataExplorerSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát: **AzureDataExplorerSink** | Igen |
| ingestionMappingName | Egy előre létrehozott nevét **[CSV leképezés](/azure/kusto/management/mappings#csv-mapping)** Kusto táblán. Az oszlopok forrásból az Adatkezelőbe az Azure - vonatkozik, amelyek leképezése **[összes támogatott forrás-tárolók/formátumok](copy-activity-overview.md#supported-data-stores-and-formats)** stb formázza a CSV vagy JSON/Avro is beleértve, használhatja a másolási tevékenység [oszlop leképezés](copy-activity-schema-and-type-mapping.md) (név alapján implicit vagy explicit módon konfigurált) és/vagy az Azure Data Explorer CSV-leképezések. | Nem |

**Példa**

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
                "ingestionMappingName": "<optional csv mapping name>"
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

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).