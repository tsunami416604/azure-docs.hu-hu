---
title: Adatok másolása HTTP-forrásból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat egy felhőből vagy helyszíni HTTP-forrásból a támogatott fogadó adattárakegy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 730efb552ef218cc5a5ce6a984d20b4e23b364ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416937"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Adatok másolása HTTP-végpontról az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-http-connector.md)
> * [Aktuális verzió](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása http-végpontról. A cikk az [Azure Data Factory másolási tevékenységére](copy-activity-overview.md)épül, amely általános áttekintést nyújt a másolási tevékenységről.

A különbség a HTTP-összekötő, a [REST-összekötő](connector-rest.md) és a [webtábla-összekötő](connector-web-table.md) a következők:

- **A REST-összekötő** kifejezetten támogatja a RESTful API-kból történő adatok másolását; 
- **A HTTP-összekötő** általános fontosságú, ha bármely HTTP-végpontról lekéri az adatokat, például fájl letöltéséhez. Mielőtt a REST-összekötő elérhetővé válik, előfordulhat, hogy a HTTP-összekötő használatával másolja az adatokat a RESTful API-ból, amely támogatott, de kevésbé funkcionális a REST-összekötővel összehasonlítva.
- **A webtábla-összekötő** html weblapról nyeri ki a táblázat tartalmát.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a HTTP-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A HTTP-forrásból bármely támogatott fogadó adattárba adatokat másolhat. A Másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)

Ezzel a HTTP-összekötővel a következőkre használható:

- Adatok beolvasása HTTP/S-végpontról a HTTP **GET** vagy **POST** metódusokkal.
- Az adatok beolvasása a következő hitelesítések egyikével: **Névtelen**, **Alapfokú,** **Kivonatoló**, **Windows**vagy **Ügyféltanúsítvány**.
- Másolja a HTTP-választ másként, vagy elemezze [azt támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md)használatával.

> [!TIP]
> Http-adatlekérési kérelem tesztelése a Data Factory HTTP-összekötőjének konfigurálása előtt, ismerje meg a fejléc- és törzskövetelmények API-specifikációját. Használhatja eszközök, mint a Postman vagy a webböngésző érvényesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a HTTP-összekötőre jellemző Data Factory-entitások definiálásához használható tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A HTTP-hez csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **HttpServer (HttpServer**) tulajdonságra kell állítani. | Igen |
| url | A webkiszolgáló alap URL-címe. | Igen |
| enableServerCertificateValidation | Adja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését, amikor HTTP-végponthoz csatlakozik. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, állítsa ezt a tulajdonságot **false**értékűre. | Nem<br /> (az alapértelmezett **igaz)** |
| authenticationType | A hitelesítés típusát adja meg. Az engedélyezett értékek: **Névtelen**, **Alap,** **Kivonatoló**, **Windows**és **ClientCertificate**. <br><br> Tekintse meg a szakaszokat, amelyek követik ezt a táblázatot a további tulajdonságok és JSON-minták ezekhez a hitelesítési típusokhoz. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, a rendszer az alapértelmezett Azure-integrációs futásidőt használja. |Nem |

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használata

Állítsa a **authenticationType** tulajdonságot **Alap ,** **Kivonatvagy** **Windows tulajdonságra.** Az előző szakaszban ismertetett általános tulajdonságokon kívül adja meg a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | A HTTP-végpont eléréséhez használandó felhasználónév. | Igen |
| jelszó | A felhasználó jelszava (a **felhasználónév** értéke). Jelölje meg ezt **SecureString** a mezőt SecureString-típusként, hogy biztonságosan tárolhatja a Data Factory-ban. Az [Azure Key Vaultban tárolt titkos fájlokra](store-credentials-in-key-vault.md)is hivatkozhat. | Igen |

**Példa**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>Ügyféltanúsítvány-hitelesítés használata

Az Ügyféltanúsítvány-hitelesítés használatához állítsa a **authenticationType** tulajdonságot **ClientCertificate (ClientCertificate)** tulajdonságra. Az előző szakaszban ismertetett általános tulajdonságokon kívül adja meg a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| embeddedCertData | Base64 kódolású tanúsítványadatok. | Adja meg **a embeddedCertData** vagy **a certThumbprint parancsot.** |
| certThumbprint | A saját üzemeltetésű integrációs üzemidő-gép tanúsítványtárolójára telepített tanúsítvány ujjlenyomata. Csak akkor érvényes, ha a **connectVia** tulajdonságban meg van adva az integrációs futásidő saját üzemeltetésű típusa. | Adja meg **a embeddedCertData** vagy **a certThumbprint parancsot.** |
| jelszó | A tanúsítványhoz társított jelszó. Jelölje meg ezt **SecureString** a mezőt SecureString-típusként, hogy biztonságosan tárolhatja a Data Factory-ban. Az [Azure Key Vaultban tárolt titkos fájlokra](store-credentials-in-key-vault.md)is hivatkozhat. | Nem |

Ha **a hitelesítéshez a certThumbprint parancsot** használja, és a tanúsítvány telepítve van a helyi számítógép személyes tárolójában, adjon olvasási engedélyt a saját üzemeltetésű integrációs futásidőnek:

1. Nyissa meg a Microsoft Management Console (MMC) konzolt. Adja hozzá a **Tanúsítványok** beépülő modult, amely a **Helyi számítógép célt célozza.**
2. **Bontsa** > ki a**Személyes**tanúsítványok csomópontot, majd válassza a **Tanúsítványok**lehetőséget.
3. Kattintson a jobb gombbal a tanúsítványra a személyes tárolóból, majd válassza **a Minden feladat** > **személyes kulcs kezelése parancsot.**
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amely alatt az integrációs futtatóállomás-szolgáltatás (DIAHostService) fut, és olvasási hozzáféréssel rendelkezik a tanúsítványhoz.

**1. példa: A certThumbprint használata**

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

**2. példa: A embeddedCertData használata**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A http-beállítások alatt `location` a következő tulajdonságok támogatottak a HTTP-hez:

| Tulajdonság    | Leírás                                                  | Kötelező |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Az adatkészlet `location` típustulajdonságát **HttpServerLocation (HttpServerLocation )** tulajdonságra kell állítani. | Igen      |
| relativeUrl | Az adatokat tartalmazó erőforrás relatív URL-címe. A HTTP-összekötő adatokat másol `[URL specified in linked service][relative URL specified in dataset]`az egyesített URL-címből: .   | Nem       |

> [!NOTE]
> A támogatott HTTP-kérelem hasznos adatmérete körülbelül 500 KB. Ha a hasznos adatméretet szeretné átadni a webes végpont nagyobb, mint 500 KB, fontolja meg a hasznos adatkötegelés kisebb adattömbökben.

**Példa:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz a HTTP-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md) 

### <a name="http-as-source"></a>HTTP mint forrás

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A http-beállítások alatt `storeSettings` a következő tulajdonságok támogatottak a HTTP-hez:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A típustulajdonságot `storeSettings` **HttpReadSettings (HttpReadSettings**) tulajdonságra kell állítani. | Igen      |
| requestMethod (requestMethod)            | A HTTP módszer. <br>Az engedélyezett értékek a következők: **Bek** és a Könyvelés ( **Bek**) és Felad . | Nem       |
| addtionalHeaders         | További HTTP-kérelemfejlécek.                             | Nem       |
| requestBody              | A HTTP-kérelem törzse.                               | Nem       |
| httpRequestTimeout           | A HTTP-kérelem időtúlértéke **(a TimeSpan** érték) a válasz lekéréséhez. Ez az érték a válasz időtúlértéke, nem pedig a válaszadatok olvasásához szükséges időtúlérték. Az alapértelmezett érték **00:01:40**. | Nem       |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozó kapcsolatok száma. Csak akkor adja meg, ha korlátozni szeretné az egyidejű kapcsolatot az adattárhoz. | Nem       |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
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

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak, mint a visszamenőleges kompatibilitás. Javasoljuk, hogy a fenti szakaszokban említett új modellt használja, és az ADF szerzői felhasználói felülete áttért az új modell létrehozására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészletmodell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **HttpFile**típusúra kell állítani. | Igen |
| relativeUrl | Az adatokat tartalmazó erőforrás relatív URL-címe. Ha ez a tulajdonság nincs megadva, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. | Nem |
| requestMethod (requestMethod) | A HTTP módszer. Az engedélyezett értékek a következők: **Bek** és a Könyvelés ( **Bek**) és Felad . | Nem |
| további fejlécek | További HTTP-kérelemfejlécek. | Nem |
| requestBody | A HTTP-kérelem törzse. | Nem |
| Formátum | Ha a HTTP-végpontból elemzés nélkül szeretné beolvasni az adatokat, majd az adatokat fájlalapú tárolóba szeretné másolni, hagyja ki a **formátumszakaszt** a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a HTTP-válasz tartalmat másolás közben szeretné elemezni, a következő fájlformátum-típusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. A **formátum**csoportban állítsa a **típustulajdonságot** ezen értékek egyikére. További információ: [JSON formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Szöveg formátum,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [Parketta formátum.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. További információt a [Támogatott fájlformátumok és tömörítési kodekek című témakörben talál.](supported-file-formats-and-compression-codecs-legacy.md#compression-support)<br/><br/>Támogatott típusok: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**.<br/>Támogatott szintek: **Optimális** és **leggyorsabb**. |Nem |

> [!NOTE]
> A támogatott HTTP-kérelem hasznos adatmérete körülbelül 500 KB. Ha a hasznos adatméretet szeretné átadni a webes végpont nagyobb, mint 500 KB, fontolja meg a hasznos adatkötegelés kisebb adattömbökben.

**1. példa: A Get metódus használata (alapértelmezett)**

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

**2. példa: A Bejegyzés módszer használata**

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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásmodellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságának** **httpSource**beállításra kell állítva. | Igen |
| httpRequestTimeout | A HTTP-kérelem időtúlértéke **(a TimeSpan** érték) a válasz lekéréséhez. Ez az érték a válasz időtúlértéke, nem pedig a válaszadatok olvasásához szükséges időtúlérték. Az alapértelmezett érték **00:01:40**.  | Nem |

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

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)
