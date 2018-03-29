---
title: Adatok másolása az Azure Data Factory használatával HTTP-forrás |} Microsoft Docs
description: 'Útmutató: adatok másolása egy felhőalapú vagy helyszíni HTTP-forrás támogatott fogadó adattárolókhoz egy Azure Data Factory-folyamat a másolási tevékenység használatával.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 3aca66d6922273e78b5100948f1b868c6c9b56af
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával HTTP-végpont
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-http-connector.md)
> * [2. verzió – Előzetes verzió](connector-http.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység az Azure Data Factory adatokat másolni egy HTTP-végpont. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [V1-es HTTP-összekötőjét](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

HTTP-forrás adatok bármely támogatott fogadó adattárolóhoz másolhatja. Adattároló források/mosdók, a másolási tevékenység által támogatott listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a HTTP-összekötő támogatja:

- Adatok beolvasása a HTTP/s végpont HTTP-n keresztül **beolvasása** vagy **POST** metódust.
- A következő hitelesítési adatok beolvasása: **névtelen**, **alapvető**, **kivonatoló**, **Windows**, és  **ClientCertificate**.
- A HTTP-válasz, mint a Másolás- vagy elemzés azt a [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md).

Ez az összekötő különbségének és a [webes tábla összekötő](connector-web-table.md) a, hogy az utóbbi használt tábla tartalom kibontása HTML weblapot.

>[!TIP]
>Adatok beolvasása az ADF HTTP-összekötő konfigurálása előtt a HTTP-kérelem teszteléséhez megtudjuk az API-specifikáció fejléc és a szervezet követelményeinek, és például a Postman vagy böngészőt eszközök segítségével ellenőrizheti azokat.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok részletesen bemutatják megadhatók a Data Factory tartozó entitások HTTP-összekötőhöz használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Kapcsolódó HTTP-szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **HttpServer**. | Igen |
| url | A webkiszolgáló alap URL-címe | Igen |
| enableServerCertificateValidation | Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése, HTTP-végponttal való kapcsolódáskor. | Nem, alapértelmezett érték true |
| authenticationType | Megadja a hitelesítési típus. Két érték engedélyezett: **névtelen**, **alapvető**, **kivonatoló**, **Windows**, **ClientCertificate**. <br><br> Tekintse meg a további tulajdonságok és adott hitelesítési típusok JSON-példák a táblázat alatti részek kulcsattribútumokkal. | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

### <a name="using-basic-digest-or-windows-authentication"></a>Basic, a kivonatoló vagy a Windows-hitelesítés használatával

"AuthenticationType" tulajdonság beállítása **alapvető**, **kivonatoló**, vagy **Windows**, és adja meg az alábbi tulajdonságokat és a korábbi leírt általános tulajdonságai a szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| Felhasználónév | A felhasználónév, a HTTP-végpont elérésére. | Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Igen |

**Példa**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate hitelesítéssel

ClientCertificate hitelesítés használatához állítsa "authenticationType" tulajdonságot **ClientCertificate**, és adja meg az előző szakaszban leírt általános tulajdonságainak együtt a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| embeddedCertData | A Base64 kódolású tanúsítványának adatait. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| CertThumbprint | A tanúsítványtároló Self-hosted integrációs futásidejű számítógépre telepített tanúsítvány ujjlenyomatát. Csak akkor, ha önálló üzemeltetett integrációs futásidejű van megadva a connectVia vonatkozik. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| jelszó | A tanúsítványhoz tartozó jelszót. Ez a mező megjelölése a SecureString tárolja biztonságos helyen, a Data factoryban vagy [hivatkozik az Azure Key Vault tárolt titkos kulcs](store-credentials-in-key-vault.md). | Nem |

Ha a "certThumbprint" használatakor a hitelesítéshez, és a tanúsítvány telepítve van a helyi számítógép személyes tárolójában, olvasási engedélyt ad a Self-hosted integrációs futásidejű szeretné:

1. Indítsa el a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul, amelynek célpontja a **helyi számítógép**.
2. Bontsa ki a **tanúsítványok**, **személyes**, és kattintson a **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes tárolóba, és válassza ki **feladataival** -> **titkos kulcsok kezelése...**
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználói fiók alatt integrációs futásidejű Host szolgáltatás (DIAHostService) fut. az olvasási joggal rendelkező tanúsítványhoz.

**1. példa: certThumbprint használatával**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: embeddedCertData használatával**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a szakasz a HTTP-adatkészlet által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása HTTP, a adatkészlet típus tulajdonságának beállítása **HttpFile**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **HttpFile** | Igen |
| relativeUrl | Az erőforrás adatokat tartalmazó relatív URL-CÍMÉT. Ha ez a tulajdonság nincs megadva, csak a megadott URL-cím a társított szolgáltatás definíciójának szolgál. | Nem |
| requestMethod | HTTP-metódus.<br/>Két érték engedélyezett **beolvasása** (alapértelmezés) vagy **Post**. | Nem |
| additionalHeaders | További HTTP-kérelemfejlécekben. | Nem |
| requestBody | A HTTP-kérelmek törzsében. | Nem |
| Formátumban | Ha azt szeretné, hogy **adatainak lekérése, a HTTP-végpont-van** nélkül, és másolja a fájlalapú tárolóhoz elemzése, hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha azt szeretné, a HTTP-válasz tartalom elemzése során másolása, a következő formátumban típusú támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [az Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquet formátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszok. |Nem |
| Tömörítés | Adja meg a típus és az adatok tömörítése szintjét. További információkért lásd: [támogatott formátumok és a tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

**1. példa: használja a Get metódust (alapértelmezett)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**2. példa: Post metódussal**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a HTTP-forrás által támogatott tulajdonságokról listáját tartalmazza.

### <a name="http-as-source"></a>HTTP forrásaként

Adatok másolása HTTP, állítsa be a forrás típusa a másolási tevékenység **HttpSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **HttpSource** | Igen |
| httpRequestTimeout | Időtúllépés (időtartam) válaszol a HTTP-kérelem. Az időtúllépés is válaszol, nem lehet olvasni a válasz adatokat időtúllépés.<br/> Alapértelmezett érték: 00:01:40  | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).