---
title: Adatok másolása HTTP-forrásokból Azure Data Factory használatával | Microsoft Docs
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni HTTP-forrásról a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: cdd7cfcb9d835c2ccac1dc367b9a1b34b509e8cf
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276427"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Adatok másolása HTTP-végpontból Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-http-connector.md)
> * [Aktuális verzió](connector-http.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok HTTP-végpontról történő másolásához. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

A HTTP-összekötő, a REST- [összekötő](connector-rest.md) és a [webes tábla összekötője](connector-web-table.md) közötti különbség a következő:

- A **Rest-összekötő** kifejezetten támogatja a REST API-k adatainak másolását; 
- A **http-összekötő** általános az adatok bármely http-végpontból való lekéréséhez, például a fájl letöltéséhez. A REST-összekötő elérhetővé válása előtt előfordulhat, hogy a HTTP-összekötőt használja az adatok REST API-ból való másolásához, amely támogatott, de kevésbé működik a REST-összekötőhöz képest.
- A **web Table Connector** kibontja a táblázat tartalmát egy HTML-weboldalról.

## <a name="supported-capabilities"></a>Támogatott képességek

A HTTP-forrásokból származó adatok bármely támogatott fogadó adattárba másolhatók. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Ezt a HTTP-összekötőt a következő célra használhatja:

- Adatok lekérése HTTP/S-végpontról a HTTP **Get** vagy **post** metódusok használatával.
- Az alábbi hitelesítések egyikével kérheti le az adatkérést: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows**vagy **ClientCertificate**.
- Másolja a HTTP-választ a-ként, vagy elemezze a [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md)használatával.

> [!TIP]
> Ha a HTTP-összekötő Data Factoryban való konfigurálása előtt szeretne tesztelni egy HTTP-kérelmet az adatlekérdezéshez, ismerkedjen meg a fejléc és a törzs követelményeivel kapcsolatos API-specifikációval. Az érvényesítéshez használhatja a Poster vagy a webböngésző eszközeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel meghatározhatja Data Factory a HTTP-összekötőre jellemző entitásokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A HTTP társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **HttpServer**értékre kell beállítani. | Igen |
| url | A webkiszolgáló alap URL-címe. | Igen |
| enableServerCertificateValidation | Itt adhatja meg, hogy engedélyezi-e a kiszolgáló SSL-tanúsítványának érvényesítését egy HTTP-végponthoz való csatlakozáskor. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, állítsa **hamis**értékre a tulajdonságot. | Nem<br /> (az alapértelmezett érték **igaz**) |
| authenticationType | Megadja a hitelesítési típust. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows**és **ClientCertificate**. <br><br> A következő szakaszokban találhat további tulajdonságokat és JSON-mintákat a fenti hitelesítési típusokhoz. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használata

Állítsa a **authenticationType** tulajdonságot **alapszintű**, **kivonatoló**vagy **Windows**értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName | A HTTP-végpont eléréséhez használandó Felhasználónév. | Igen |
| password | A felhasználó jelszava (a **userName** érték). Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate-hitelesítés használata

A ClientCertificate-hitelesítés használatához állítsa a **authenticationType** tulajdonságot **ClientCertificate**értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| embeddedCertData | Base64 kódolású tanúsítvány-adattartalom. | Adjon meg **embeddedCertData** vagy **certThumbprint**. |
| certThumbprint | A saját üzemeltetésű Integration Runtime gép tanúsítványtárolójában telepített Tanúsítvány ujjlenyomata. Csak akkor érvényes, ha a Integration Runtime saját üzemeltetésű típusa van megadva a **connectvia tulajdonsággal** tulajdonságban. | Adjon meg **embeddedCertData** vagy **certThumbprint**. |
| password | A tanúsítványhoz társított jelszó. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |

Ha **certThumbprint** használ a hitelesítéshez, és a tanúsítvány a helyi számítógép személyes tárolójába van telepítve, adja meg az olvasási engedélyeket a saját üzemeltetésű Integration Runtime számára:

1. Nyissa meg a Microsoft Management Console (MMC) programot. Adja hozzá a **helyi számítógépet**tároló **tanúsítványok** beépülő modult.
2. Bontsa ki a **tanúsítványok** > **személyes**csomópontot, majd válassza a **tanúsítványok**lehetőséget.
3. Kattintson a jobb gombbal a tanúsítványra a személyes tárolóban, majd válassza a **minden feladat** > **titkos kulcsok kezelése**lehetőséget.
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amelyben a Integration Runtime gazda szolgáltatás (DIAHostService) fut, és olvasási hozzáféréssel rendelkezik a tanúsítványhoz.

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. 

- A **Parquet, a tagolt szöveget, a Avro és a bináris formátumot**a [parketta, a tagolt szöveg, a Avro és a bináris formátum adatkészlet](#format-based-dataset) szakaszban találja.
- Más formátumok, például az **ork/Avro/JSON formátum**esetében tekintse meg a [más formátumú adatkészlet](#other-format-dataset) szakaszt.

### <a name="format-based-dataset"></a>Parketta, tagolt szöveg, Avro és bináris formátum adatkészlet

Ha a **parketta, a tagolt szöveg vagy a bináris formátum**között szeretne adatokat másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a Format-alapú adatkészlet és a támogatott beállítások területen. A http `location` a következő tulajdonságokat támogatja a formátum-alapú adatkészlet beállításai között:

| Tulajdonság    | Leírás                                                  | Szükséges |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Az adatkészletben `location` található Type tulajdonságot **HttpServerLocation**értékre kell állítani. | Igen      |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe.       | Nem       |

> [!NOTE]
> A HTTP-kérések támogatott mérete körülbelül 500 KB. Ha a webes végpontnak átadni kívánt hasznos adatok mérete meghaladja a 500 KB-ot, érdemes lehet kisebb adattömbökbe felvenni a hasznos adatokat.

> [!NOTE]
> A következő szakaszban említett, a Parquet/Text formátumot tartalmazó **HttpFile** -típus adatkészlete továbbra is támogatott a visszamenőleges kompatibilitás érdekében a másolási/keresési tevékenységekhez. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

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

### <a name="other-format-dataset"></a>Egyéb formátumú adatkészlet

Az adatok HTTP-ből az **ork/JSON formátumban**való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **HttpFile**értékre kell állítani. | Igen |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. Ha nincs megadva ez a tulajdonság, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. | Nem |
| requestMethod | A HTTP-metódus. Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | Nem |
| additionalHeaders | További HTTP-kérelmek fejlécei. | Nem |
| requestBody | A HTTP-kérelem törzse. | Nem |
| format | Ha az adatokat a HTTP-végpontról kívánja lekérdezni, az elemzés nélkül, majd másolja az adatokat egy fájl alapú tárolóba, ugorja át a **Formátum** szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a HTTP-válasz tartalmát a másolás során szeretné elemezni, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. A **Formátum**alatt állítsa be a **Type** tulajdonságot az alábbi értékek egyikére. További információ: JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format), [szöveges formátum](supported-file-formats-and-compression-codecs.md#text-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), ork- [Formátum](supported-file-formats-and-compression-codecs.md#orc-format)és [parketta formátum](supported-file-formats-and-compression-codecs.md#parquet-format). |Nem |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Támogatott típusok: **Gzip**, **deflate**, **BZip2**és **ZipDeflate**.<br/>Támogatott szintek:  **Optimális** és **leggyorsabb**. |Nem |

> [!NOTE]
> A HTTP-kérések támogatott mérete körülbelül 500 KB. Ha a webes végpontnak átadni kívánt hasznos adatok mérete meghaladja a 500 KB-ot, érdemes lehet kisebb adattömbökbe felvenni a hasznos adatokat.

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

**2. példa: A post metódus használata**

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

Ez a szakasz a HTTP-forrás által támogatott tulajdonságok listáját tartalmazza.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP forrásként

- A **parketta, a tagolt szöveg, a Avro és a bináris formátum**másolásához tekintse meg a [parketta, a tagolt szöveg, a Avro és a bináris formátum forrás](#format-based-source) szakaszát.
- Más formátumokból, például az **ork/Avro/JSON formátumból**való másoláshoz tekintse meg a [más formátumú forrás](#other-format-source) szakaszt.

#### <a name="format-based-source"></a>Parketta, tagolt szöveg, Avro és bináris formátum forrása

Ha a **parketta, a tagolt szöveg vagy a bináris formátum**adatait szeretné másolni, tekintse meg a [parketta formátumát](format-parquet.md), a [tagolt szöveg formátumát](format-delimited-text.md), a [Avro formátumát](format-avro.md) és a [bináris formátumú](format-binary.md) cikket a másolási tevékenység forrására és a támogatott beállításokra vonatkozóan. A http `storeSettings` a következő tulajdonságokat támogatja a Format-alapú másolási forrás beállításaiban:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A Type tulajdonságot `storeSettings` a **HttpReadSetting**értékre kell állítani. | Igen      |
| requestMethod            | A HTTP-metódus. <br>Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | Nem       |
| addtionalHeaders         | További HTTP-kérelmek fejlécei.                             | Nem       |
| requestBody              | A HTTP-kérelem törzse.                               | Nem       |
| requestTimeout           | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Az alapértelmezett érték a **00:01:40**. | Nem       |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | Nem       |

> [!NOTE]
> A Parquet/tagolt szöveg formátuma esetén a következő szakaszban említett, **HttpSource** típusú másolási tevékenység továbbra is támogatott a visszafelé kompatibilitás érdekében. Azt javasoljuk, hogy ezt az új modellt fogja használni, és az ADF szerzői felhasználói felülete átváltott az új típusok létrehozásához.

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSetting",
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

#### <a name="other-format-source"></a>Egyéb formátum forrása

Az adatok HTTP-ből **ork/JSON formátumban**való másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **HttpSource**értékre kell állítani. | Igen |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Az alapértelmezett érték a **00:01:40**.  | Nem |

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

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).
