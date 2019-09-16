---
title: Adatok másolása az Azure Data Factory (előzetes verzió) használatával Jira |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Jira támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.openlocfilehash: 14bcded4e3074023aa496588f5bd79eedbe889cf
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008919"
---
# <a name="copy-data-from-jira-using-azure-data-factory-preview"></a>Adatok másolása az Azure Data Factory (előzetes verzió) használatával Jira

Ez a cikk az Azure Data Factory a másolási tevékenység használatával adatokat másol a Jira módját ismerteti. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és küldjön visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a JIRA-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Másolhat adatokat Jira bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Jira összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Jira-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Jira** | Igen |
| host | Az IP-cím vagy a gazdagép a Jira szolgáltatás neve. (például jira.example.com)  | Igen |
| port | A Jira-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP portra. Az alapértelmezett érték 443-as akkor, ha a kapcsolaton keresztül a HTTPS vagy a 8080-as, ha HTTP Protokollon keresztül csatlakozik.  | Nem |
| username | Jira-szolgáltatás eléréséhez használt felhasználónév.  | Igen |
| password | A felhasználónév mezőben megadott felhasználónév megfelelő jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Megadja, hogy a data source végpontok HTTPS segítségével titkosítja. Az alapértelmezett érték: true.  | Nem |
| useHostVerification | Megadja a kiszolgálói tanúsítvány a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik az állomás neve kötelező legyen-e. Az alapértelmezett érték: true.  | Nem |
| usePeerVerification | Megadja, hogy ellenőrizze a kiszolgáló identitását, ha SSL-kapcsolaton keresztül kapcsolódik. Az alapértelmezett érték: true.  | Nem |

**Példa**

```json
{
    "name": "JiraLinkedService",
    "properties": {
        "type": "Jira",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Jira adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatmásolás Jira, állítsa be a type tulajdonság, az adatkészlet **JiraObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **JiraObject** | Igen |
| tableName | A tábla neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

**Példa**

```json
{
    "name": "JiraDataset",
    "properties": {
        "type": "JiraObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Jira linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Jira forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="jirasource-as-source"></a>JiraSource forrásként

Adatok másolása Jira, állítsa be a forrás típusaként a másolási tevékenység **JiraSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **JiraSource** | Igen |
| query | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Nem (Ha a "tableName" adatkészlet paraméter van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromJira",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Jira input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "JiraSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
