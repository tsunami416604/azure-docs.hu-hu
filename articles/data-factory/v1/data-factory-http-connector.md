---
title: Adatok áthelyezése forrásból HTTP - Azure |} Microsoft Docs
description: További tudnivalók az adatok mozgatása egy helyszíni vagy felhőalapú HTTP forrássá Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 29281843dc1b375182eb3dafe95ad86c89217671
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052273"
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Adatok áthelyezése az Azure Data Factory használatával HTTP forrásból származó
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-http-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-http.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [HTTP-összekötőt, a V2](../connector-http.md).


Ez a cikk ismerteti a másolási tevékenység használata az Azure Data Factory tárolt adatok mozgatása egy helyszíni/felhőbeli HTTP-végpont támogatott fogadó adattárat. Ez a cikk épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikket, amely megadja az adatmozgás általános áttekintést a másolási tevékenység és a támogatott adatforrások/mosdók adattárolókhoz listáját.

Adat-előállító jelenleg támogatja egyéb adattárakhoz HTTP forrásból származó csak áthelyezése adatokat, de nem az adatok áthelyezése más adatok egy HTTP-helyen tárolja.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott esetek és hitelesítési típusok
A HTTP-összekötő segítségével adatokat lekérnie **felhő- és a helyszíni HTTP/s-végpont** HTTP-n keresztül **beolvasása** vagy **POST** metódust. A következő hitelesítési típusok támogatottak: **névtelen**, **alapvető**, **kivonatoló**, **Windows**, és  **ClientCertificate**. Jegyezze fel ezt az összekötőt különbségének és a [webes tábla összekötő](data-factory-web-table-connector.md) van: tábla tartalma kibontani HTML weblap használt.

Amikor adatokat másol egy helyszíni HTTP-végpont, a helyszíni környezetben vagy az Azure virtuális gép adatkezelési átjárót kell telepítenie. Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikkben tájékozódhat az adatkezelési átjáró és az átjáró beállításával kapcsolatos részletes útmutatás.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely HTTP forrásból származó adatokat különböző eszközök/API-k használatával helyezi át a feldolgozási sor hozhatja létre.

- Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

- Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. A HTTP-adatforrás adatainak másolása Azure Blob Storage minták JSON, lásd: [JSON példák](#json-examples) Ez a cikk szakasza.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a társított szolgáltatás JSON-elemek szerepelnek HTTP jellemző leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type | A type tulajdonságot kell beállítani: `Http`. | Igen |
| url | A webkiszolgáló alap URL-címe | Igen |
| authenticationType | Megadja a hitelesítési típus. Két érték engedélyezett: **névtelen**, **alapvető**, **kivonatoló**, **Windows**, **ClientCertificate**. <br><br> Tekintse meg a további tulajdonságok és adott hitelesítési típusok JSON-példák a táblázat alatti részek kulcsattribútumokkal. | Igen |
| enableServerCertificateValidation | Adja meg, hogy a kiszolgálói SSL-tanúsítvány hitelesítése engedélyezése, ha forrás HTTPS-webkiszolgálón. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, állítsa FALSE értékre. | Nem, alapértelmezett érték true |
| gatewayName | Neve az adatkezelési átjáró HTTP a helyszíni adatforráshoz kapcsolódni. | Igen, ha a helyszíni HTTP forrásból származó adatok másolása. |
| encryptedCredential | Titkosított hitelesítő adatokat a HTTP-végpont elérésére. Automatikusan létrehozott másolása varázsló vagy a ClickOnce felugró párbeszédpanel a hitelesítő adatok konfigurálásakor. | Nem. Csak akkor, ha az adatok másolása helyi HTTP-kiszolgáló alkalmazni. |

Lásd: [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró a felhő közötti](data-factory-move-data-between-onprem-and-cloud.md) helyszíni HTTP összekötő adatforráshoz tartozó hitelesítő adatok beállítása vonatkozó további információért.

### <a name="using-basic-digest-or-windows-authentication"></a>Basic, a kivonatoló vagy a Windows-hitelesítés használatával

Állítsa be `authenticationType` , `Basic`, `Digest`, vagy `Windows`, és adja meg a HTTP-összekötő fent bevezetett általános ők mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| felhasználónév | Felhasználónév a HTTP-végpont elérésére. | Igen |
| jelszó | A felhasználó (felhasználónév) jelszavát. | Igen |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Példa: Basic, a kivonatoló vagy a Windows-hitelesítés használatával

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate hitelesítéssel

Egyszerű hitelesítést használ, állítsa be `authenticationType` , `ClientCertificate`, és adja meg a HTTP-összekötő fent bevezetett általános ők mellett az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| embeddedCertData | A személyes információcseréhez kapcsolódó (PFX) fájl bináris adatok Base64-kódolású tartalmát. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| certThumbprint | A tanúsítványtároló átjáró számítógépre telepített tanúsítvány ujjlenyomatát. Csak akkor, ha a helyszíni HTTP forrásból származó adat másolása alkalmazni. | Adja meg a `embeddedCertData` vagy `certThumbprint`. |
| jelszó | A tanúsítványhoz tartozó jelszót. | Nem |

Ha `certThumbprint` hitelesítés és a tanúsítvány telepítése a helyi számítógép személyes tanúsítványokat tartalmazó tárolójában kell az olvasási engedélyt az átjárószolgáltatás:

1. Indítsa el a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul, amelynek célpontja a **helyi számítógép**.
2. Bontsa ki a **tanúsítványok**, **személyes**, és kattintson a **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes tárolóba, és válassza ki **feladataival**->**titkos kulcsok kezelése...**
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználói fiók, amely alatt az adatkezelési átjáró gazdaszolgáltatása fut az olvasási joggal rendelkező tanúsítvány.  

#### <a name="example-using-client-certificate"></a>Példa: ügyfél tanúsítványt használ.
A kapcsolódó szolgáltatás hivatkozások a data factory egy helyszíni HTTP-webkiszolgálón. Az adatkezelési átjáró telepítve a számítógépen telepített ügyféltanúsítványt használ.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Példa: ügyfél-tanúsítványt használ egy fájlban
A kapcsolódó szolgáltatás hivatkozások a data factory egy helyszíni HTTP-webkiszolgálón. Az adatkezelési átjáró telepítése egy ügyfél tanúsítványfájlt, a gép használ.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A **typeProperties** szakasz eltérő adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz típusú adatkészlet **Http** tulajdonságai a következők

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A dataset típusának megadása. meg kell `Http`. | Igen |
| relativeUrl | Az erőforrás adatokat tartalmazó relatív URL-CÍMÉT. Ha nincs megadva, csak a megadott URL-cím a társított szolgáltatás definíciójának használja. <br><br> Dinamikus URL-cím létrehozásához használható [adat-előállító funkciók és rendszerváltozók](data-factory-functions-variables.md), pl. "relativeUrl": "$$Text.Format (" / személyes/jelentés? hónap = {0:yyyy}-{0:MM} & fmt = csv', SliceStart) ". | Nem |
| requestMethod | HTTP-metódus. Két érték engedélyezett **beolvasása** vagy **POST**. | Nem. Az alapértelmezett szint a `GET`. |
| additionalHeaders | További HTTP-kérelemfejlécekben. | Nem |
| requestBody | A HTTP-kérelmek törzsében. | Nem |
| Formátumban | Ha azt szeretné, hogy egyszerűen **lekérik az adatokat, HTTP-végpont-van** nélkül elemzés azt, hagyja ki a formátumot beállítások. <br><br> Ha azt szeretné, a HTTP-válasz tartalom elemzése során másolása, a következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. További információkért lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

### <a name="example-using-the-get-default-method"></a>Példa: a GET (alapértelmezett) metódussal

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>Példa: a POST metódussal

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

Tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység viszont eltérőek a tevékenységek minden típusának. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha a forrás, a másolási tevékenység jelenleg típusú **HttpSource**, a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| httpRequestTimeout | Időtúllépés (időtartam) válaszol a HTTP-kérelem. Az időtúllépés is válaszol, nem lehet olvasni a válasz adatokat időtúllépés. | Nem. Alapértelmezett érték: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Támogatott formátumú és tömörítés
Lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikk részletei.

## <a name="json-examples"></a>JSON-példák
Az alábbi példában adja meg a minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). HTTP forrásból származó adatok másolása az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** a forrásokban, sem a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Példa: Adatok másolása az HTTP-forrás az Azure-Blobtárolóba
Ez a minta a Data Factory megoldást a következő adat-előállító entitásokat tartalmaz:

1. A társított szolgáltatás típusa [HTTP](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [Http](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [HttpSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol adatokat egy HTTP-forrás egy Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

### <a name="http-linked-service"></a>Kapcsolódó HTTP-szolgáltatás
A példa a kapcsolódó HTTP-szolgáltatás a névtelen hitelesítést. Lásd: [HTTP társított szolgáltatás](#linked-service-properties) szakasz a különböző típusú hitelesítés használható.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>A bemeneti HTTP-adatkészlet
Beállítás **külső** való **igaz** tájékoztatja a Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1).

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>A másolási tevékenység-feldolgozási folyamat

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **HttpSource** és **fogadó** típusúra **BlobSink**.

Lásd: [HttpSource](#copy-activity-properties) a HttpSource által támogatott tulajdonságok listája.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
