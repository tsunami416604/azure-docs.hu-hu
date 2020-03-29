---
title: Adatok másolása a Presto-ból az Azure Data Factory használatával (előzetes verzió)
description: Megtudhatja, hogyan másolhatja az adatokat a Presto-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927805"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Adatok másolása a Presto-ból az Azure Data Factory használatával (előzetes verzió)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Presto. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést adhat nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Presto-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Presto-ból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Presto-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Presto csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **Presto** | Igen |
| gazda | A Presto-kiszolgáló IP-címe vagy állomásneve. (azaz 192 168 222 160)  | Igen |
| serverVersion | A Presto szerver verziója. (azaz 0,148-t)  | Igen |
| Katalógus | A kiszolgálóval szembeni összes kérelem katalóguskörnyezete.  | Igen |
| port | A Presto-kiszolgáló által az ügyfélkapcsolatok figyelésére használt TCP-port. Az alapértelmezett érték 8080.  | Nem |
| authenticationType | A Presto-kiszolgálóhoz való csatlakozáshoz használt hitelesítési mechanizmus. <br/>Az engedélyezett értékek a következők: **Névtelen**, **LDAP** | Igen |
| felhasználónév | A Presto-kiszolgálóhoz való csatlakozáshoz használt felhasználónév.  | Nem |
| jelszó | A felhasználónévnek megfelelő jelszó. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Nem |
| enableSsl | Itt adható meg, hogy a kiszolgálóval létesített kapcsolatok SSL-rel titkosítva legyenek-e. Az alapértelmezett érték a hamis.  | Nem |
| trustedCertPath | A megbízható hitelesítésszolgáltatói tanúsítványokat tartalmazó .pem fájl teljes elérési útja a kiszolgáló SSL-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha ssl-t használ saját üzemeltetésű infravörös rendszeren. Az alapértelmezett érték az infravörös kapcsolattal telepített cacerts.pem fájl.  | Nem |
| useSystemTrustStore | Itt adható meg, hogy a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból származó hitelesítésszolgáltatói tanúsítványt használjon.Specifies whether to use a ca certificate from the system trust store or from a specified PEM file. Az alapértelmezett érték a hamis.  | Nem |
| allowHostNameCNMismatch | Itt adható meg, hogy az SSL-kapcsolaton keresztül imitomárk hozad-e a kiszolgáló állomásnevének megfelelő hitelesítésszolgáltató által kiállított SSL-tanúsítványnevet. Az alapértelmezett érték a hamis.  | Nem |
| allowSelfSignedServerCert | Itt adható meg, hogy engedélyezze-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | Nem |
| timeZoneID | A kapcsolat által használt helyi időzóna. Ehhez a beállításhoz érvényes értékek az IANA időzóna-adatbázisában vannak megadva. Az alapértelmezett érték a rendszer időzónája.  | Nem |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Presto adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Presto programból, állítsa az adatkészlet típustulajdonságát **PrestoObject (PrestoObject ) tulajdonságra.** A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következő re van állítva: **PrestoObject** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság a visszamenőleges kompatibilitás érdekében támogatott. Használja `schema` `table` és új munkaterheléshez. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Presto-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="presto-as-source"></a>Presto mint forrás

Ha adatokat szeretne másolni a Presto programból, állítsa a forrástípust a másolási tevékenységben a **PrestoSource mezőre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **PrestoSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM MyTable"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

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

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
