---
title: "Adatok másolása az Google BigQuery Azure Data Factory (béta) használatával |} Microsoft Docs"
description: "Útmutató: adatok másolása Google BigQuery támogatott fogadó adattárolókhoz a másolási tevékenység használatával a data factory-folyamathoz."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Adatok másolása az Google BigQuery Azure Data Factory (béta) használatával

Ez a cikk a másolási tevékenység használható az Azure Data Factory adatokat másolni Google BigQuery módját ismerteti. Buildekről nyújtanak a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatásnak, amely általában a rendelkezésre álló, 1 verziót [másolási tevékenység az 1-es verziójú](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Ez az összekötő jelenleg bétaverziójú. Próbálja ki, és küldjön visszajelzést. Ne használja éles környezetben.

## <a name="supported-capabilities"></a>Támogatott képességei

Google BigQuery adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott adatforrások vagy mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

 Adat-előállító kapcsolódásának engedélyezése beépített illesztőprogramot tartalmaz. Emiatt nem kell manuálisan a ezzel az összekötővel az illesztőprogram telepítéséhez.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják adat-előállító tartozó entitások megadhatók a Google BigQuery összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

A Google BigQuery társított szolgáltatásnak az alábbi tulajdonságok esetén támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot meg kell **GoogleBigQuery**. | Igen |
| Projekt | Az alapértelmezett BigQuery projekt irányuló lekérdezésben projekt azonosítója.  | Igen |
| additionalProjects | Nyilvános projektazonosítók vesszővel elválasztott listája BigQuery projekt eléréséhez.  | Nem |
| requestGoogleDriveScope | E Google meghajtó hozzáférést kérni. Google meghajtó hozzáférés lehetővé teszi, hogy a támogatás a Google meghajtóról adatokkal BigQuery adatok egyesítése összevont táblák esetében. Az alapértelmezett érték **hamis**.  | Nem |
| authenticationType | A hitelesítéshez használt OAuth 2.0 hitelesítési módszert. ServiceAuthentication csak Self-hosted integrációs futásidejű is használhatók. <br/>Két érték engedélyezett **ServiceAuthentication** és **UserAuthentication**. | Igen |
| refreshToken | A frissítési jogkivonat BigQuery hozzáférés hitelesítése a UserAuthentication használt Google kapott. Ez a mező szerint tárolja biztonságos helyen a Data Factory SecureString jelölheti meg. Is Azure Key Vault tárolni a jelszó és lehetővé teszik a másolási tevékenység lekéréses ott adatmásolás végrehajtásakor. További tudnivalókért lásd: [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Nem |
| e-mail | A szolgáltatás e-mail Fiókazonosító ServiceAuthentication használt. Csak a Self-hosted integrációs futásidejű használható.  | Nem |
| keyFilePath | A fájl teljes elérési útja a .p12 kulcs, amely segítségével hitelesíti a szolgáltatási fiók e-mail címét. Csak a Self-hosted integrációs futásidejű használható.  | Nem |
| trustedCertPath | A teljes elérési útja a .pem fájl, amely tartalmazza a megbízható Hitelesítésszolgáltatói tanúsítvány segítségével ellenőrizze a kiszolgáló SSL-en keresztül csatlakoztatásakor. Ez a tulajdonság csak akkor, ha az SSL használatát Self-hosted integrációs futásidejű állítható be. Az alapértelmezett érték: a telepített integrációs futásidejű cacerts.pem fájlt.  | Nem |
| useSystemTrustStore | Megadja, hogy a CA-tanúsítvány használatára, a rendszer megbízható áruházból vagy egy megadott .pem fájlt. Az alapértelmezett érték **hamis**.  | Nem |

**Példa**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a témakör a Google BigQuery dataset által támogatott tulajdonságokról.

Adatok másolása Google BigQuery, az adatkészlet típus tulajdonságának beállítása **GoogleBigQueryObject**. Nincs ilyen típusú dataset további típusra vonatkozó tulajdonság.

**Példa**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a rész felsorolja a Google BigQuery forrástípus által támogatott tulajdonságokról.

### <a name="googlebigquerysource-as-a-source-type"></a>A forrás típusaként GoogleBigQuerySource

Adatok másolása Google BigQuery, állítsa be a forrás típusa a másolási tevékenység **GoogleBigQuerySource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani **GoogleBigQuerySource**. | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés segítségével adatokat olvasni. Például: `"SELECT * FROM MyTable"`. | Igen |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
Források és mosdók adat-előállítóban másolási tevékenység által támogatott adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).
