---
title: "Adatok másolása az Azure Data Factory (béta) használatával Apache Impala |} Microsoft Docs"
description: "Útmutató: adatok másolása az Apache Impala támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 4766e19b1823bdb737be8a90b3e2e2bfe4e48ab9
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-apache-impala-using-azure-data-factory-beta"></a>Adatok másolása az Azure Data Factory (béta) használatával Apache Impala

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok másolása az Apache Impala. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

Apache Impala adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Az Azure Data Factory kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz, ezért nem szükséges manuálisan kell telepítenie minden olyan illesztőprogram ezt az összekötőt használja.

## <a name="getting-started"></a>Bevezetés

A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való Apache Impala összekötő adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Apache Impala társított szolgáltatásnak az alábbi tulajdonságok esetén támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Apache Impala** | Igen |
| állomás | A kiszolgáló IP-címét vagy állomásnevét kiszolgálónevét az Apache Impala. (Ez 192.168.222.160)  | Igen |
| port | A TCP-portot, amelyen az Apache Impala kiszolgáló ügyfélkapcsolatokat. Az alapértelmezett érték: 21050.  | Nem |
| AuthenticationType | A használandó hitelesítési típus. <br/>Két érték engedélyezett: **névtelen**, **SASLUsername**, **UsernameAndPassword** | Igen |
| felhasználónév | A felhasználónév az Apache Impala kiszolgálóhoz való hozzáféréshez. Az alapértelmezett értéke névtelen SASLUsername használatakor.  | Nem |
| jelszó | A jelszó, a felhasználónév megfelelő UsernameAndPassword használata esetén. Ez a mező megjelölése a SecureString tárolja biztonságos helyen az ADF, vagy a jelszó tárolása az Azure Key Vault választhat, és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtása során – további információhoz [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Meghatározza, hogy a kapcsolat titkosítása SSL használatával. Az alapértelmezett értéke hamis.  | Nem |
| trustedCertPath | Megbízható Hitelesítésszolgáltatói tanúsítványok ellenőrzése a kiszolgáló SSL-en keresztül kapcsolódáskor tartalmazó .pem fájl teljes elérési útja. Ez a tulajdonság csak akkor állítható, önálló üzemeltetett infravörös SSL használatakor Az alapértelmezett érték a cacerts.pem fájlt az infravörös telepített:  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer megbízható áruházból vagy a megadott PEM-fájl egy Hitelesítésszolgáltatói tanúsítványt használjon-e. Az alapértelmezett értéke hamis.  | Nem |
| allowHostNameCNMismatch | Megadja, hogy egy hitelesítésszolgáltató által kiállított SSL tanúsítvány nevének egyeznie kell a gazdagép nevével a kiszolgáló SSL-en keresztül kapcsolódáskor-e. Az alapértelmezett értéke hamis.  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi a kiszolgáló önaláírt tanúsítványokat. Az alapértelmezett értéke hamis.  | Nem |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhatja Self-hosted integrációs futásidejű vagy Azure integrációs futásidejű (ha az adattároló nyilvánosan elérhető). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "Apache ImpalaLinkedService",
    "properties": {
        "type": "Apache Impala",
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör az Apache Impala dataset által támogatott tulajdonságokról.

Adatok másolása az Apache Impala, állítsa be a type tulajdonságot az adathalmaz **Apache ImpalaObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "Apache ImpalaDataset",
    "properties": {
        "type": "Apache ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Apache Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Apache Impala forrás által támogatott tulajdonságokról.

### <a name="apache-impalasource-as-source"></a>Apache ImpalaSource forrásaként

Adatok másolása Apache Impala, állítsa be a forrás típusa a másolási tevékenység **Apache ImpalaSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **Apache ImpalaSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromApache Impala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Apache Impala input dataset name>",
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
                "type": "Apache ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések
Azure Data Factory támogatott adattárolókhoz adatok listáját tárolja, a következő témakörben: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
