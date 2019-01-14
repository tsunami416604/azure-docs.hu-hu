---
title: Egy HTTP-forrásból származó adatok másolása az Azure Data Factory használatával |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat egy felhőbeli vagy helyszíni HTTP-forrás támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/202018
ms.author: jingwang
ms.openlocfilehash: b092509c1029bbff028da6bf94b9f7dbd1068b16
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020030"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával egy HTTP-végpontot

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-http-connector.md)
> * [Aktuális verzió](connector-http.md)

Ez a cikk ismerteti az Azure Data Factory másolási tevékenység használatával adatokat másol egy HTTP-végpontot. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

A HTTP-összekötő a különbséget a [REST közötti összekötő](connector-rest.md) és a [webes táblázat összekötő](connector-web-table.md) vannak:

- **REST-összekötő** kifejezetten az adatok másolása a RESTful API-k; támogatása 
- **HTTP-összekötő** általános adatokat lekérni bármilyen HTTP-végpontot, például fájl letöltéséhez. Mielőtt REST közötti összekötő elérhetővé válik, akkor fordulhat elő, a HTTP-összekötő használatával adatait átmásolhatja RESTful API-t, amely akkor támogatott, de kisebb működési összehasonlítva és REST közötti összekötő.
- **Webes tábla összekötő** kivonatok tábla egy HTML-weblap tartalmat.

## <a name="supported-capabilities"></a>Támogatott képességek

Másolhat adatokat egy HTTP-forrás bármely támogatott fogadó adattárba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

A HTTP-összekötő-használhatja:

- A HTTP-adatokat lekérni egy HTTP-vagy Https-végpont **első** vagy **POST** módszereket.
- Adatok beolvasása a következő hitelesítések egyikének használatával: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows**, vagy **ClientCertificate**.
- A HTTP-válasz való másolása – elemezni a használatával, vagy [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> A Data Factoryban a HTTP-összekötő konfigurálása előtt tesztelése a HTTP-kérést, az adatok beolvasásáért, ismerje meg az API-specifikációnak, fejléc és a szervezet követelményeinek. Eszközök, mint például a Postman vagy egy webböngésző segítségével ellenőrzése.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével konkrétan a HTTP-összekötő a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A HTTP-beli társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **HttpServer**. | Igen |
| url | Az alap URL-cím a webkiszolgálóhoz. | Igen |
| enableServerCertificateValidation | Adja meg, hogy engedélyezze a kiszolgálói SSL-tanúsítvány hitelesítése egy HTTP-végpontot való csatlakozáskor. Ha a HTTPS-kiszolgáló egy önaláírt tanúsítványt használ, a tulajdonság értéke **hamis**. | Nem<br /> (az alapértelmezett érték **igaz**) |
| authenticationType | A hitelesítési típust határoz meg. Engedélyezett értékek a következők **névtelen**, **alapszintű**, **kivonatoló**, **Windows**, és **ClientCertificate**. <br><br> Ez a táblázat további tulajdonságok és JSON-minták ezeket a hitelesítési típusokat az alábbi szakaszok a megtekintéséhez. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Használhatja az Azure integrációs modul és a egy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, ezt a tulajdonságot használja az alapértelmezett Azure integrációs modul. |Nem |

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használatával

Állítsa be a **authenticationType** tulajdonságot **alapszintű**, **kivonatoló**, vagy **Windows**. Az előző szakaszban leírt általános tulajdonságaihoz adja meg a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| Felhasználónév | A felhasználónév, a HTTP-végpont elérésére használhat. | Igen |
| jelszó | A felhasználó jelszava (a **felhasználónév** érték). Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |

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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate hitelesítés használatával

ClientCertificate hitelesítés használatához állítsa a **authenticationType** tulajdonságot **ClientCertificate**. Az előző szakaszban leírt általános tulajdonságaihoz adja meg a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| embeddedCertData | Base64-kódolású tanúsítvány adatait. | Adja meg **embeddedCertData** vagy **certThumbprint**. |
| certThumbprint | A saját üzemeltetésű integrációs modul gép tanúsítványtár telepített tanúsítvány ujjlenyomatával. Csak ha a saját üzemeltetésű integrációs modulok típusának meg van adva a alkalmazni a **connectVia** tulajdonság. | Adja meg **embeddedCertData** vagy **certThumbprint**. |
| jelszó | A tanúsítványhoz tartozó jelszót. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Nem |

Ha **certThumbprint** hitelesítést és a tanúsítvány a helyi számítógép személyes tárolójában van telepítve, olvasási engedélyek hozzárendelése a saját üzemeltetésű integrációs modul:

1. Nyissa meg a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul célzó **helyi számítógép**.
2. Bontsa ki a **tanúsítványok** > **személyes**, majd válassza ki **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes, és válassza **feladatok** > **titkos kulcsok kezelése**.
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználói fiók, amely alatt az Integration Runtime Gazdaszolgáltatásának (DIAHostService) fut, olvasási hozzáférés a tanúsítványt.

**1. példa: CertThumbprint használatával**

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

**2. példa: EmbeddedCertData használatával**

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

Ez a szakasz felsorolja, amely támogatja a HTTP-adatkészlet tulajdonságai. 

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Adatok másolása HTTP, állítsa be a **típus** tulajdonság, az adatkészlet **HttpFile**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **HttpFile**. | Igen |
| relativeurl tulajdonságok közül | Az erőforrás, amely tartalmazza az adatok relatív URL-CÍMÉT. Ez a tulajdonság nincs megadva, csak az URL-cím a társított szolgáltatás definíciójában megadott használ. | Nem |
| requestMethod | A HTTP-metódust. Engedélyezett értékek a következők **első** (alapértelmezett), és **Post**. | Nem |
| additionalHeaders | További HTTP-kérelemfejlécek. | Nem |
| RequestBody | A HTTP-kérelem törzsét. | Nem |
| Formátum | Szeretné-e adatokat lekérni a HTTP-végpontot,-van nélkül elemzés azt, majd másolja az adatokat egy fájlalapú tárolón hagyja ki a **formátum** szakasz is a bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha meg szeretné elemezni a HTTP-válasz tartalma másolása során, formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és **ParquetFormat**. A **formátum**állítsa be a **típus** tulajdonságát a következő értékek egyikét. További információkért lásd: [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format), [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format). |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Támogatott típusok: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek:  **Optimális** és **leggyorsabb**. |Nem |

> [!NOTE]
> A támogatott HTTP-kérelem hasznos adatainak mérete körülbelül 500 KB-os. Ha a webes végpontra átadni kívánt adattartalom-méretkorlát nagyobb 500 KB-nál, fontolja meg az adattartalomban kisebb adattömbökben kötegelés.

**1. példa: A Get metódussal (alapértelmezett)**

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

**2. példa: A Post metódussal**

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

Ez a szakasz felsorolja, amely támogatja a HTTP-forrás tulajdonságai.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP forrásként

Adatok másolása HTTP, állítsa be **adatforrástípust** a másolási tevékenység az **HttpSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát **HttpSource**. | Igen |
| httpRequestTimeout | Az időtúllépés (a **TimeSpan** érték) válaszol a HTTP-kérelem. Az értéke az időkorlátot kapott választ, nem választ adatokat olvasni az időkorlátot. Az alapértelmezett érték **00:01:40**.  | Nem |

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
