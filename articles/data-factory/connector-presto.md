---
title: Adatok másolása a Presto használatával Azure Data Factory (előzetes verzió)
description: Megtudhatja, hogyan másolhat adatok a Presto-ból a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 71bff5e3761d72236e6896733b96bd6e01460e52
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927805"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Adatok másolása a Presto használatával Azure Data Factory (előzetes verzió)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok gyors másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést küldhet nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Presto-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Presto-ból bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a gyors összekötőhöz tartozó Data Factory definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Presto társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Presto** | Igen |
| gazdagép | A Presto-kiszolgáló IP-címe vagy állomásneve. (pl. 192.168.222.160)  | Igen |
| serverVersion | A Presto-kiszolgáló verziója. (például 0,148-t)  | Igen |
| Katalógus | A kiszolgálóval kapcsolatos összes kérelem katalógusának kontextusa.  | Igen |
| port | A Presto-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. Az alapértelmezett érték a 8080.  | Nem |
| authenticationType | A Presto-kiszolgálóhoz való kapcsolódáshoz használt hitelesítési módszer. <br/>Az engedélyezett értékek a következők: **Névtelen**, **LDAP** | Igen |
| felhasználónév | A Presto-kiszolgálóhoz való kapcsolódáshoz használt Felhasználónév.  | Nem |
| jelszó | A felhasználónévnek megfelelő jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Meghatározza, hogy a kiszolgálóval létesített kapcsolatok SSL használatával legyenek titkosítva. Az alapértelmezett érték false (hamis).  | Nem |
| trustedCertPath | A megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazó. PEM fájl teljes elérési útja a kiszolgáló SSL-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, amikor SSL-t használ a saját üzemeltetésű IR-ben. Az alapértelmezett érték az IR-vel telepített hitesítésszolgáltatói. PEM fájl.  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték false (hamis).  | Nem |
| allowHostNameCNMismatch | Megadja, hogy szükséges-e a HITELESÍTÉSSZOLGÁLTATÓ által kiadott SSL-tanúsítvány neve ahhoz, hogy a kiszolgáló állomásneve egyezzen az SSL-kapcsolaton keresztül. Az alapértelmezett érték false (hamis).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték false (hamis).  | Nem |
| timeZoneID | A kapcsolatok által használt helyi időzóna. A beállítás érvényes értékeit az IANA időzóna-adatbázisa határozza meg. Az alapértelmezett érték a rendszer időzónája.  | Nem |

**Példa**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Presto-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok gyors másolásához állítsa az adatkészlet Type (típus) tulajdonságát **PrestoObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **PrestoObject** | Igen |
| séma | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| table | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Új számítási feladatokhoz használjon `schema` és `table`. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Presto forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="presto-as-source"></a>Presto forrásként

Az adatok gyors másolásához állítsa a forrás típusát a másolás tevékenység **PrestoSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **PrestoSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
