---
title: Adatok másolása az Azure Data Factory használatával HTTP-forrás |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat egy felhőbeli vagy helyszíni HTTP-forrás támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
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
ms.date: 08/24/2018
ms.author: jingwang
ms.openlocfilehash: 5afb2fccd5c7b8ca306079941837d854c0b21349
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091717"
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával HTTP-végpont
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-http-connector.md)
> * [Aktuális verzió](connector-http.md)

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatokat másol egy HTTP-végpontot. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

HTTP-forrásból származó adatok másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a HTTP-összekötő támogatja:

- Adatok beolvasása a HTTP-vagy HTTPS-végpont HTTP használatával **első** vagy **POST** metódust.
- A következő hitelesítés használata az adatok beolvasása közben: **névtelen**, **alapszintű**, **kivonatoló**, **Windows**, és  **ClientCertificate**.
- A HTTP-válasz való másolása – az elemzés, vagy a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

Ez az összekötő közötti különbség, és a [webes táblázat összekötő](connector-web-table.md) a, hogy az utóbbi használt webes HTML bontsa ki a tábla tartalmát.

>[!TIP]
>Adatok beolvasása az ADF-ben HTTP-összekötő konfigurálása előtt a HTTP-kérelem ellenőrzéséhez ismerje meg az API-specifikációja fejléc és a szervezet követelményeinek, és eszközökkel, mint például a Postman vagy böngészőt használja ellenőrzése.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások HTTP-összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

HTTP-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **HttpServer**. | Igen |
| url | A webkiszolgáló kiindulási URL-cím | Igen |
| enableServerCertificateValidation | Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése a HTTP-végponthoz való csatlakozáskor. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, állítsa ezt a beállítást hamis. | Nem, alapértelmezett érték az IGAZ |
| authenticationType | A hitelesítési típust határoz meg. Engedélyezett értékek a következők: **névtelen**, **alapszintű**, **kivonatoló**, **Windows**, **ClientCertificate**. <br><br> Tekintse meg a további tulajdonságok és hitelesítési típusokhoz JSON-minták a táblázat alatti részek jelölik. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használatával

"AuthenticationType" tulajdonságát állítsa **alapszintű**, **kivonatoló**, vagy **Windows**, és adja meg a következő tulajdonságokkal együtt az előző leírt általános tulajdonságok a szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| Felhasználónév | A felhasználónév, a HTTP-végpontot. | Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate hitelesítés használatával

ClientCertificate hitelesítés használatához állítsa "authenticationType" tulajdonságot **ClientCertificate**, és adja meg az előző szakaszban leírt általános tulajdonságokkal együtt a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| embeddedCertData | Base64-kódolású tanúsítvány adatait. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| certThumbprint | A helyi Integration Runtime gép tanúsítványtár telepített tanúsítvány ujjlenyomatával. Csak akkor, ha a saját üzemeltetésű integrációs modulok típusának megadott connectVia vonatkozik. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| jelszó | A tanúsítványhoz tartozó jelszót. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |

Ha "certThumbprint" használ a hitelesítéshez, és a tanúsítvány a helyi számítógép személyes tárolójában van telepítve, akkor kell olvasási engedélyt ad a helyi Integration Runtime:

1. Indítsa el a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul, amely célozza meg benne a **helyi számítógép**.
2. Bontsa ki a **tanúsítványok**, **személyes**, és kattintson a **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes, és válassza ki **feladatok** -> **titkos kulcsok kezelése...**
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználó Integration Runtime Gazdaszolgáltatásának (DIAHostService) van futtató fiók, az olvasási hozzáférés a tanúsítványt.

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz a HTTP-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása HTTP, állítsa be a type tulajdonság, az adatkészlet **HttpFile**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **HttpFile** | Igen |
| relativeurl tulajdonságok közül | Az erőforrás, amely tartalmazza az adatok relatív URL-CÍMÉT. Ez a tulajdonság nincs megadva, csak az URL-címet a társított szolgáltatás definíciójában megadott használ. | Nem |
| requestMethod | HTTP-metódust.<br/>Engedélyezett értékek a következők **első** (alapértelmezés) vagy **Post**. | Nem |
| additionalHeaders | További HTTP-kérelemfejlécek. | Nem |
| RequestBody | HTTP-kérelem törzse. | Nem |
| Formátum | Ha azt szeretné, hogy **adatok lekérését, HTTP-végpont-van** nélkül, és másolja a fájlalapú tárolóhoz elemzés, hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha meg szeretné elemezni a HTTP-válasz tartalma másolása során, formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimal** és **leggyorsabb**. |Nem |

>[!NOTE]
>A támogatott HTTP-kérelem hasznos adatainak mérete körülbelül 500 KB-os. Ha a webes végpontra átadni kívánt adattartalom-méretkorlát nagyobb, mint ez, fontolja meg a batch szeletekre.

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

**2. példa: Post-metódus használata**

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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a HTTP-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="http-as-source"></a>HTTP forrásként

Adatok másolása HTTP, állítsa be a forrás típusaként a másolási tevékenység **HttpSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **HttpSource** | Igen |
| httpRequestTimeout | Időtúllépés (időtartam) válaszol a HTTP-kérés. Az időkorlát kapott választ, nem választ adatokat olvasni az időkorlátot.<br/> Alapértelmezett érték: 00:01:40  | Nem |

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
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
