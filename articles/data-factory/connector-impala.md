---
title: Az Azure Data Factory (előzetes verzió) használatával adatokat másol az Impala |} A Microsoft Docs
description: 'Útmutató: adatok másolása az Impala a támogatott fogadó adattárakba egy data factory-folyamatot egy másolási tevékenység használatával.'
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: ed29fb99025dbc69b9dae6a996f444954a7d88d1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123419"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-preview"></a>Adatmásolás az Impala Azure Data Factory (előzetes verzió) használatával

Ez a cikk az Azure Data Factory másolási tevékenység használatával adatokat másol az Impala módját ismerteti. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Próbálja ki, és visszajelzést. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat Impala bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

 A Data Factory-kapcsolat beépített illesztőprogramot tartalmaz. Így nem kell manuálisan telepítenie az összekötő használatához illesztőprogramot.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások az Impala-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságok támogatottak az Impala-beli társított szolgáltatást.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Impala**. | Igen |
| gazdagép | IP-cím vagy a gazdagép neve az Impala-kiszolgáló (azaz 192.168.222.160).  | Igen |
| port | Az Impala-kiszolgáló az ügyfélkapcsolatok figyeléséhez használt TCP portra. Az alapértelmezett 21050 értéke.  | Nem |
| authenticationType | A használandó hitelesítés típusa. <br/>Engedélyezett értékek a következők **névtelen**, **SASLUsername**, és **UsernameAndPassword**. | Igen |
| felhasználónév | Az Impala-kiszolgáló eléréséhez használt felhasználónév. Az alapértelmezett érték: névtelen, SASLUsername használatakor.  | Nem |
| jelszó | A jelszót, amely megfelel a felhasználó nevének UsernameAndPassword használatakor. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Meghatározza, hogy-e a kiszolgálóhoz vannak titkosítása SSL használatával. Az alapértelmezett érték **hamis**.  | Nem |
| trustedCertPath | A .pem-fájlt, amely tartalmazza a segítségével ellenőrizze a kiszolgálón, ha SSL-kapcsolaton keresztül csatlakozik a megbízható Hitelesítésszolgáltatói tanúsítvány teljes elérési útja. Ez a tulajdonság beállítható csak akkor, ha a helyi integrációs modul az SSL Protokollt használja. Az alapértelmezett érték a cacerts.pem fájlt az integration runtime telepítve.  | Nem |
| useSystemTrustStore | Megadja, hogy a Hitelesítésszolgáltatói tanúsítvány használatára, a rendszer megbízható áruházból vagy egy adott PEM-fájl. Az alapértelmezett érték **hamis**.  | Nem |
| allowHostNameCNMismatch | Meghatározza a kiszolgáló állomásneve megfelelően, ha SSL-kapcsolaton keresztül csatlakozik egy hitelesítésszolgáltató által kiállított SSL-tanúsítvány neve kötelező legyen-e. Az alapértelmezett érték **hamis**.  | Nem |
| allowSelfSignedServerCert | Megadja, hogy, hogy a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett érték **hamis**.  | Nem |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz az Impala adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása az Impala, állítsa be a type tulajdonság, az adatkészlet **ImpalaObject**. Egy adatkészlet ilyen további típus-specifikus tulajdonság nincs.

**Példa**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Impala forrástípus által támogatott tulajdonságok listáját tartalmazza.

### <a name="impala-as-a-source-type"></a>Impala-forrás típusa

Adatok másolása az Impala, állítsa be a forrás típusaként a másolási tevékenység **ImpalaSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **ImpalaSource**. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével olvassa el az adatokat. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
