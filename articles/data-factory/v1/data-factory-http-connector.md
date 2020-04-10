---
title: Adatok áthelyezése HTTP-forrásból – Azure
description: Megtudhatja, hogyan helyezhet át adatokat egy helyszíni vagy felhőbeli HTTP-forrásból az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 36592151385a08d75b9b34e85bfa9d62342fc8cd
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991569"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Adatok áthelyezése HTTP-forrásból az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-http-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-http.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha az Azure Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [HTTP-összekötő a V2-ben című témakört.](../connector-http.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése egy helyszíni vagy felhőalapú HTTP-végpont egy támogatott fogadó adattárba. Ez a cikk az [Adatok áthelyezése a Másolási tevékenység használatával](data-factory-data-movement-activities.md)épül fel, amely általános áttekintést nyújt az adatok mozgásáról a Másolási tevékenység használatával. A cikk felsorolja azokat az adattárakat is, amelyeket a Másolási tevékenység forrásként és fogadóként támogat.

A Data Factory jelenleg csak a HTTP-forrásból más adattárolókba való adatáthelyezést támogatja. Nem támogatja az adatok más adattárakból HTTP-célba való áthelyezését.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott forgatókönyvek és hitelesítési típusok

Ezzel a HTTP-összekötővel lekérheti az adatokat *egy felhőből és egy helyszíni HTTP/S-végpontból* is a HTTP **GET** vagy **posta** metódusok használatával. A következő hitelesítési típusok támogatottak: **Névtelen**, **Alap,** **Kivonatoló**, **Windows**és **ClientCertificate**. Figyelje meg az összekötő és a [webtábla-összekötő](data-factory-web-table-connector.md)közötti különbséget. A webtábla-összekötő html weblapról nyeri ki a táblázat tartalmát.

Amikor adatokat másol egy helyszíni HTTP-végpontról, telepítenie kell az adatkezelési átjárót a helyszíni környezetben vagy egy Azure virtuális gépben. Az Adatkezelési átjáróról és az átjáró beállításával kapcsolatos részletes útmutatásról az [Adatok áthelyezése a helyszíni helyek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md)című témakörben olvashat.

## <a name="get-started"></a>Bevezetés

Létrehozhat egy folyamatot, amely másolási tevékenységgel rendelkezik az adatok HTTP-forrásból történő áthelyezéséhez különböző eszközök vagy API-k használatával:

- A folyamatok létrehozásának legegyszerűbb módja az Adatok másolása varázsló használata. A folyamat nak az Adatok másolása varázslóval történő létrehozásáról az [Oktatóanyag: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md)című témakörben ismer.

- A következő eszközökkel is létrehozhat egy folyamatot: a **Visual Studio,** az **Azure PowerShell,** az **Azure Resource Manager sablon,** a **.NET API**vagy a REST **API.** A másolási tevékenységet tartalmazó folyamat létrehozásáról a [Másolási tevékenység oktatóanyagcímű témakörben](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)talál részletes útmutatást. A JSON-minták, amelyek adatokat másolnak egy HTTP-forrásból az Azure Blob storage, lásd a [JSON-példák.](#json-examples)

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi táblázat a HTTP-hez csatolt szolgáltatásra jellemző JSON-elemeket ismerteti:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type | A **típustulajdonságnak** **Http**-re kell állítania. | Igen |
| url | A webkiszolgáló alap URL-címe. | Igen |
| authenticationType | A hitelesítés típusát adja meg. Az engedélyezett értékek: **Névtelen**, **Alap,** **Kivonatoló**, **Windows**és **ClientCertificate**. <br><br> A cikk későbbi szakaszaiban további tulajdonságokat és JSON-mintákat tartalmaz ezekhez a hitelesítési típusokhoz. | Igen |
| enableServerCertificateValidation | Itt adható meg, hogy engedélyezze-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését, ha a forrás HTTPS-webkiszolgáló. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, állítsa ezt **hamisra.** | Nem<br /> (az alapértelmezett **igaz)** |
| átjárónév | A helyszíni HTTP-forráshoz való csatlakozáshoz használt adatkezelési átjárópéldány neve. | Igen, ha adatokat másol egy helyszíni HTTP-forrásból |
| titkosított hitelesítő adatok | A HTTP-végpont eléréséhez használt titkosított hitelesítő adat. Az érték automatikusan létrejön, amikor konfigurálja a hitelesítési adatokat a Másolás varázslóban, vagy a **Kattintásminta** párbeszédpanelen. | Nem<br /> (csak akkor érvényes, ha adatokat másol egy helyszíni HTTP-kiszolgálóról) |

A helyszíni HTTP-összekötő adatforrásainak hitelesítő adatainak beállításáról az [Adatok áthelyezése a helyszíni források és a felhő között az Adatkezelési átjáró használatával című](data-factory-move-data-between-onprem-and-cloud.md)témakörben talál további információt.

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használata

Állítsa **az authenticationType típust** **Alapszintű**, **Kivonatoló**vagy **Windows beállításra.** Az előző szakaszokban ismertetett általános HTTP-összekötőtulajdonságokon kívül állítsa be a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| userName (Felhasználónév) | A HTTP-végpont eléréséhez használandó felhasználónév. | Igen |
| jelszó | A felhasználó jelszava (**felhasználónév**). | Igen |

**Példa: Alapszintű, kivonatoló vagy Windows-hitelesítés használata**

```json
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

### <a name="using-clientcertificate-authentication"></a>Ügyféltanúsítvány-hitelesítés használata

Az alapfokú hitelesítés használatához állítsa a **authenticationType** programot **az Ügyféltanúsítvány elemre.** Az előző szakaszokban ismertetett általános HTTP-összekötőtulajdonságokon kívül állítsa be a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| embeddedCertData | A PFX fájl bináris adatainak Base64 kódolású tartalma. | **EmbeddedCertData** vagy **certThumbprint** megadása |
| certThumbprint | Az átjárógép tanúsítványtárolójára telepített tanúsítvány ujjlenyomata. Csak akkor érvényes, ha adatokat másol egy helyszíni HTTP-forrásból. | **EmbeddedCertData** vagy **certThumbprint** megadása |
| jelszó | A tanúsítványhoz társított jelszó. | Nem |

Ha **a hitelesítéshez a certThumbprint parancsot** használja, és a tanúsítvány telepítve van a helyi számítógép személyes tárolójában, adjon olvasási engedélyt az átjárószolgáltatásnak:

1. Nyissa meg a Microsoft Management Console (MMC) konzolt. Adja hozzá a **Tanúsítványok** beépülő modult, amely a **Helyi számítógép célt célozza.**
2. **Bontsa** > ki a**Személyes**tanúsítványok csomópontot, majd válassza a **Tanúsítványok**lehetőséget.
3. Kattintson a jobb gombbal a tanúsítványra a személyes tárolóból, majd válassza **a Minden feladat** >**személyes kulcs kezelése parancsot.**
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amely alatt az Adatkezelési átjárógazdaszolgáltatás fut, és olvasási hozzáféréssel rendelkezik a tanúsítványhoz.  

**Példa: Ügyféltanúsítvány használata**

Ez a kapcsolt szolgáltatás az adat-előállítót egy helyszíni HTTP-webkiszolgálóhoz kapcsolja. Olyan ügyféltanúsítványt használ, amely telepítve van azon a számítógépen, amelyen telepítve van az Adatkezelési átjáró.

```json
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

**Példa: Ügyféltanúsítvány használata fájlban**

Ez a kapcsolt szolgáltatás az adat-előállítót egy helyszíni HTTP-webkiszolgálóhoz kapcsolja. Ügyféltanúsítvány-fájlt használ azon a számítógépen, amelyen telepítve van az adatkezelési átjáró.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

A JSON-fájl egyes szakaszai, például a struktúra, az elérhetőség és a szabályzat, hasonlóak az összes adatkészlettípushoz (Azure SQL Database, Azure Blob storage, Azure Table storage).

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása című](data-factory-create-datasets.md)témakörben található.

A **typeProperties** szakasz az adatkészlet egyes típusaiesetében eltérő. A **TypeProperties (TypeProperties)** szakasz az adatok adattárban való helyéről nyújt tájékoztatást. A **Http** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típusát** **Http**-re kell állítani. | Igen |
| relativeUrl | Az adatokat tartalmazó erőforrás relatív URL-címe. Ha az elérési út nincs megadva, csak a csatolt szolgáltatásdefinícióban megadott URL-címet használja a program. <br><br> Dinamikus URL létrehozásához használhatja a [Data Factory függvényeket és a rendszerváltozókat.](data-factory-functions-variables.md) Példa: **relativeUrl** **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | Nem |
| requestMethod (requestMethod) | A HTTP módszer. Az engedélyezett értékek a **GET** és **a POST**. | Nem <br />(alapértelmezett érték **GET)** |
| további fejlécek | További HTTP-kérelemfejlécek. | Nem |
| requestBody | A HTTP-kérelem törzse. | Nem |
| Formátum | Ha az *adatokat egy HTTP-végpontból* szeretné lekérni elemzés nélkül, hagyja ki a **formátumbeállítást.** <br><br> Ha a HTTP-válasz tartalmat másolás közben szeretné elemezni, a következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. További információ: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és [Parketta formátum.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. Támogatott típusok: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. További információt a [Fájl- és tömörítési formátumok az Azure Data Factoryban című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

**Példa: A GET (alapértelmezett) metódus használata**

```json
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

**Példa: A POST módszer használata**

```json
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

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása című témakörben található.](data-factory-create-pipelines.md) 

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. A másolási tevékenység esetében a tulajdonságok a források és a fogadók típusától függően változnak.

Jelenleg, ha a forrás a Másolási tevékenység a **HttpSource** típusú, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| httpRequestTimeout | A HTTP-kérelem időtúlértéke **(a TimeSpan** érték) a válasz lekéréséhez. Ez az időtúllépések a válaszhoz, nem pedig a válaszadatok olvasásához. | Nem<br />(alapértelmezett érték: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok

További [információk: Fájl- és tömörítési formátumok az Azure Data Factoryban.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples"></a>Példák json-példák

Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. A példák bemutatják, hogyan másolhatja az adatokat egy HTTP-forrásból az Azure Blob storage. Azonban az adatok *közvetlenül* a források bármelyikéből átmásolhatók az Azure Data Factory másolási tevékenység használatával [támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) fogadókbármelyikébe.

**Példa: Adatok másolása HTTP-forrásból az Azure Blob storage-ba**

A Data Factory megoldás ehhez a mintához a következő Data Factory entitásokat tartalmazza:

*   [HTTP](#linked-service-properties)típusú csatolt szolgáltatás .
*   [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
*   [Http](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
*   [HttpSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)másolási tevékenységet használó [folyamat.](data-factory-create-pipelines.md)

A minta óránként másolja az adatokat egy HTTP-forrásból egy Azure blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

### <a name="http-linked-service"></a>HTTP-hez csatolt szolgáltatás

Ez a példa a HTTP-hez csatolt szolgáltatást használja névtelen hitelesítéssel. A [http-alapú szolgáltatás](#linked-service-properties) ban a különböző típusú hitelesítések használható.

```json
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

### <a name="azure-storage-linked-service"></a>Azure storage-hoz csatolt szolgáltatás

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP bemeneti adatkészlet

A **külső** **true** beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

```json
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

### <a name="azure-blob-output-dataset"></a>Azure blobkimeneti adatkészlet

Az adatok óránként új blobba vannak írva (**gyakoriság:** **óra**, **időköz**: **1**).

```json
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

### <a name="pipeline-that-uses-a-copy-activity"></a>Másolási tevékenységet használó folyamat

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva. A másolási tevékenység óránként fut. A folyamat JSON-definíciójában a **forrástípus** **HttpSource,** a **fogadó** típusa pedig **BlobSink**lesz.

A **HttpSource** által támogatott tulajdonságok listáját a HttpSource című témakörben [tésszi része.](#copy-activity-properties)

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
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
> Ha oszlopokat szeretne leképezni egy forrásadatkészletből egy fogadó adatkészlet oszlopaihoz, olvassa [el az Adatkészlet-oszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Az Azure Data Factory adatmozgatási (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről és optimalizálási lehetőségekről a [Tevékenység másolása teljesítmény- és hangolási útmutatóban](data-factory-copy-activity-performance.md)olvashat.
