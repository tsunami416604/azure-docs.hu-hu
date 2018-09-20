---
title: Adatok másolása az Azure Data Factory használatával Phoenix |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Phoenix támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 09/19/2018
ms.author: jingwang
ms.openlocfilehash: 78e432bf526ad270ae8543ad1be40727ed560d4b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367899"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával Phoenix 

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol a Phoenix használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat a Phoenix bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolat beépített illesztőprogramot tartalmaz, ezért nem kell manuálisan telepítenie az összes illesztőprogram ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások Phoenix összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Phoenix társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **Phoenix** | Igen |
| gazdagép | Az IP-cím vagy a gazdagép a Phoenix kiszolgáló neve. (azaz 192.168.222.160)  | Igen |
| port | A Phoenix-kiszolgálói az ügyfélkapcsolatok figyeléséhez használt TCP portra. Az alapértelmezett érték: 8765. Ha csatlakozik az Azure Hdinsight, meg a 443-as portot. | Nem |
| httpPath | Részleges URL-cím a Phoenix-kiszolgálóhoz. (azaz /gateway/sandbox/phoenix/version). Adja meg `/hbasephoenix0` Ha Hdinsight-fürt segítségével.  | Nem |
| authenticationType | A Phoenix-kiszolgálóhoz való csatlakozáshoz használt hitelesítési mechanizmusa. <br/>Engedélyezett értékek a következők: **névtelen**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Igen |
| felhasználónév | A Phoenix-kiszolgálóhoz való csatlakozáshoz használt felhasználónév.  | Nem |
| jelszó | A felhasználónévhez tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Itt adhatja meg, e-kiszolgálóhoz a rendszer SSL használatával titkosítja. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| trustedCertPath | A .pem-fájlt tartalmazó ellenőrzésének folyamatát a kiszolgálón, ha SSL-kapcsolaton keresztül kapcsolódik a megbízható Hitelesítésszolgáltatói tanúsítvány teljes elérési útja. Ez a tulajdonság csak akkor állítható, ha SSL-lel a saját üzemeltetésű Az alapértelmezett érték a telepített bemutathatja cacerts.pem fájlt:  | Nem |
| useSystemTrustStore | Megadja, hogy a Hitelesítésszolgáltatói tanúsítvány használatára, a rendszer megbízható áruházból vagy egy adott PEM-fájl. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| allowHostNameCNMismatch | Meghatározza a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül kapcsolódik egy hitelesítésszolgáltató által kiállított SSL-tanúsítvány neve kötelező legyen-e. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy, hogy a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke FALSE (hamis).  | Nem |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

>[!NOTE]
>Ha a fürt nem támogatja a fix kiszolgálású munkamenetek például a HDInsight, explicit módon csomópont index a http-elérési út beállítás végén, például adja meg a `/hbasephoenix0` helyett `/hbasephoenix`.

**Példa**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Phoenix adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatokat másol a Phoenix, az adatkészlet, a type tulajdonság beállítása **PhoenixObject**. Egy adatkészlet ilyen további típus-specifikus tulajdonság nincs.

**Példa**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Phoenix forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="phoenixsource-as-source"></a>PhoenixSource forrásként

Adatok másolása a Phoenix, állítsa be a forrás típusaként a másolási tevékenység **PhoenixSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **PhoenixSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
