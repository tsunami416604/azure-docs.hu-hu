---
title: Adatok áthelyezése egy HTTP-forrás – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet át adatokat a helyszíni vagy felhőbeli HTTP-forrás az Azure Data Factory használatával.
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
ms.openlocfilehash: 06eb11132d7e3968850aadb4bfdaa53261f14ada
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167477"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Egy HTTP-forrásból származó adatok áthelyezése az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-http-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-http.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió az Azure Data Factory szolgáltatás használ, tekintse meg [a v2-ben HTTP-összekötő](../connector-http.md).


Ez a cikk ismerteti az Azure Data Factory másolási tevékenység használatával helyezhet át adatokat a helyszíni vagy felhőbeli HTTP-végpontot egy támogatott fogadó adattárba. Ez a cikk számos tekintetben [adatok áthelyezése másolási tevékenységgel](data-factory-data-movement-activities.md), amely adatáthelyezés általános áttekintést mutat be a másolási tevékenység használatával. A cikk az adattárak, amely támogatja a másolási tevékenység a forrásként és fogadóként is megjeleníti.

A Data Factory jelenleg csak helyez át adatokat egy HTTP-forrás pedig más adattárakban támogatja. Más adattárakban helyez át adatokat egy HTTP-cél nem támogatott.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott esetek és hitelesítési típusok

A HTTP-összekötő segítségével adatokat beolvasni *felhő és a egy helyszíni HTTP-vagy Https-végpont* a HTTP protokollal **első** vagy **POST** módszereket. A következő hitelesítési típusok támogatottak: **névtelen**, **alapszintű**, **kivonatoló**, **Windows**, és  **ClientCertificate**. Jegyezze fel ezt az összekötőt közötti különbséget, és a [webes táblázat összekötő](data-factory-web-table-connector.md). A webes táblázat összekötő egy HTML-weblap kigyűjti a tábla tartalmát.

Amikor adatokat másol egy helyszíni HTTP-végpontot, telepítenie kell az adatkezelési átjáró a helyszíni környezetben vagy egy Azure-beli virtuális gépen. Az adatkezelési átjáró és az átjáró beállítása lépésenkénti kapcsolatban lásd: [adatok áthelyezése a felhőbe és a helyszínek](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Bevezetés

Adatok áthelyezése a különböző eszközök vagy API-k használatával egy HTTP-forrás egy másolási tevékenységgel rendelkező folyamatot hozhat létre:

- A folyamat létrehozásának legegyszerűbb módja, hogy az adatok másolása varázslóval. Gyors bemutató folyamat létrehozása adatok másolása varázsló használatával, lásd: [oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

- Folyamatok létrehozására is használhatja a következő eszközök: a **az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, egy **Azure Resource Manager sablon**, a **.NET API**, vagy a **REST API-val**. Egy másolási tevékenységgel rendelkező folyamat létrehozása a részletes útmutatót lásd: a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). JSON-adott adatok másolása a HTTP-forrás az Azure Blob storage-minták, lásd: [JSON példák](#json-examples).

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő táblázat ismerteti a HTTP-beli társított szolgáltatás adott JSON-elemek:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type | A **típus** tulajdonságot állítsa **Http**. | Igen |
| url | Az alap URL-cím a webkiszolgálóhoz. | Igen |
| authenticationType | A hitelesítési típust határoz meg. Engedélyezett értékek a következők **névtelen**, **alapszintű**, **kivonatoló**, **Windows**, és **ClientCertificate**. <br><br> Ez a cikk további tulajdonságokat, és ezeket a hitelesítési típusokat JSON-minták későbbi részeiben hivatkoznak. | Igen |
| enableServerCertificateValidation | Megadja, hogy a kiszolgálói SSL-tanúsítvány hitelesítése engedélyezése, ha a forrás egy HTTPS-webkiszolgálón. Ha a HTTPS-kiszolgáló egy önaláírt tanúsítványt használ, állítsa ezt a beállítást **hamis**. | Nem<br /> (az alapértelmezett érték **igaz**) |
| átjáró neve | Csatlakozhat egy helyszíni HTTP-forrás a Data Management Gateway-példány neve. | Igen, ha adatokat másol egy helyszíni HTTP-forrásból |
| encryptedCredential | A titkosított hitelesítő adatokat a HTTP-végpont elérésére. Az érték automatikusan létrehozott kell, ha a hitelesítési adatokat a másolás varázsló vagy a használatával a **ClickOnce** párbeszédpanel bezárásához. | Nem<br /> (érvényes csak akkor, amikor adatokat másol egy helyszíni HTTP-kiszolgáló) |

HTTP összekötőt a helyszíni adatforrások hitelesítő adatok beállításával kapcsolatos részletekért lásd: [adatok áthelyezése egy helyszíni forrásra, és a felhő között az adatkezelési átjáró segítségével](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használatával

Állítsa be **authenticationType** való **alapszintű**, **kivonatoló**, vagy **Windows**. Általános HTTP összekötő tulajdonságaihoz az előző szakaszokban ismertetett állítsa be a következő tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| felhasználónév | A felhasználónév, a HTTP-végpont elérésére használhat. | Igen |
| jelszó | A felhasználó jelszava (**felhasználónév**). | Igen |

**Példa: Alapszintű, kivonatoló vagy Windows-hitelesítés használatával**

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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate hitelesítés használatával

Alapszintű hitelesítés használatához állítsa **authenticationType** való **ClientCertificate**. Általános HTTP összekötő tulajdonságaihoz az előző szakaszokban ismertetett állítsa be a következő tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| embeddedCertData | Bináris adatok a PFX-fájl Base-64 kódolású tartalmát. | Adja meg **embeddedCertData** vagy **certThumbprint** |
| certThumbprint | Az átjáró számítógépre tanúsítványtár telepített tanúsítvány ujjlenyomatával. Csak akkor, amikor adatokat másol egy helyszíni HTTP-forrás vonatkoznak. | Adja meg **embeddedCertData** vagy **certThumbprint** |
| jelszó | A tanúsítványhoz tartozó jelszót. | Nem |

Ha **certThumbprint** hitelesítést és a tanúsítvány a helyi számítógép személyes tárolójában van telepítve, olvasási engedélyek hozzárendelése az átjárószolgáltatás számára:

1. Nyissa meg a Microsoft Management Console (MMC). Adja hozzá a **tanúsítványok** beépülő modul célzó **helyi számítógép**.
2. Bontsa ki a **tanúsítványok** > **személyes**, majd válassza ki **tanúsítványok**.
3. Kattintson a jobb gombbal a tanúsítványt a személyes, és válassza **feladatok** >**titkos kulcsok kezelése**.
3. Az a **biztonsági** lapon maradva adja hozzá a felhasználói fiók, amely alatt a Data Management Gateway szolgáltatás fut, olvasási hozzáférés a tanúsítványt.  

**Példa: Egy olyan ügyféltanúsítvány használatával.**

A társított szolgáltatás társítja az adat-előállító egy helyszíni HTTP-webkiszolgáló. A gépen, amelyen telepítve van az adatkezelési átjáró telepített ügyfél-tanúsítványt használ.

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

**Példa: Egy olyan ügyféltanúsítvány használatával egy fájlban**

A társított szolgáltatás társítja az adat-előállító egy helyszíni HTTP-webkiszolgáló. A gépen, amelyen telepítve van az adatkezelési átjáró ügyfél tanúsítványfájl használ.

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

Egy adatkészlet JSON-fájlt, például a szerkezetet, rendelkezésre állást és házirend, az egyes szakaszokban hasonlóak az összes adatkészlet esetében (az Azure SQL Database, Azure Blob storage, az Azure Table storage).

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md).

A **typeProperties** szakasz eltér az egyes adatkészlet. A **typeProperties** rész ismerteti az adattárban lévő adatok helyét. A **typeProperties** az adatkészlet a következő szakaszban a **Http** írja be a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** az adatkészlet értékre kell állítani **Http**. | Igen |
| relativeurl tulajdonságok közül | Az erőforrás, amely tartalmazza az adatok relatív URL-CÍMÉT. Az elérési út nincs megadva, csak az URL-cím a társított szolgáltatás definíciójában megadott használ. <br><br> Egy dinamikus URL-cím létrehozásához, használhatja a [Data Factory-függvények és rendszerváltozók](data-factory-functions-variables.md). Példa: **relativeurl tulajdonságok közül**: **$$Text.Format ("/ saját/jelentés? hónap = {0: yyyy}-{0:MM} & fmt = csv", SliceStart)**. | Nem |
| requestMethod | A HTTP-metódust. Engedélyezett értékek a következők **első** és **POST**. | Nem <br />(alapértelmezett érték a **első**) |
| additionalHeaders | További HTTP-kérelemfejlécek. | Nem |
| RequestBody | A HTTP-kérelem törzsét. | Nem |
| Formátum | Ha azt szeretné, hogy *lekérik az adatokat egy HTTP-végpontot,-van* nélkül, elemzés, hagyja ki a **formátum** beállítás. <br><br> Ha meg szeretné elemezni a HTTP-válasz tartalma másolása során, a következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, és **ParquetFormat**. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek: **Optimal** és **leggyorsabb**. További információkért lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

**Példa: A metódussal a GET (alapértelmezett)**

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

**Példa: A POST metódussal**

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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Tulajdonságok, például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). 

Az elérhető tulajdonságok köre az **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenység tulajdonságai a forrásként és fogadóként típusú változhat.

Jelenleg Ha a másolási tevékenység forrása a **HttpSource** írja be a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
| -------- | ----------- | -------- |
| httpRequestTimeout | Az időtúllépés (a **TimeSpan** érték) válaszol a HTTP-kérelem. Az időkorlát kapott választ, nem választ adatokat olvasni az időkorlátot. | Nem<br />(alapértelmezett érték: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok

Lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md) további.

## <a name="json-examples"></a>JSON-példák

Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). A példák bemutatják, hogyan másolhat adatokat egy HTTP-forrásból, az Azure Blob storage. Azonban az adatok átmásolhatók *közvetlenül* egyetlen forrás sem a fogadóként [támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenységgel az Azure Data Factory használatával.

**Példa: Adatok másolása egy HTTP-forrás az Azure Blob storage**

Ez a példa a Data Factory-megoldás a következő Data Factory-entitásokat tartalmazza:

*   A társított szolgáltatás típusa [HTTP](#linked-service-properties).
*   A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [Http](#dataset-properties).
*   Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   A [folyamat](data-factory-create-pipelines.md) , amely rendelkezik egy másolási tevékenység által használt [HttpSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy HTTP-forrás egy Azure-blobból óránként. Ezek a minták a használt JSON-tulajdonságokat a minta az alábbi szakaszok ismertetik.

### <a name="http-linked-service"></a>HTTP-beli társított szolgáltatás

Ebben a példában a HTTP-társított szolgáltatást a névtelen hitelesítést használ. Lásd: [HTTP társított szolgáltatás](#linked-service-properties) a különböző típusú hitelesítést is használhatja.

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

### <a name="azure-storage-linked-service"></a>Az Azure storage társított szolgáltatás

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

Beállítás **külső** való **igaz** a Data Factory szolgáltatás tájékoztatja, hogy az adatkészlet a data factory a külső és a egy adat-előállító tevékenység által előállított nem.

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

Adatok írása egy új blob minden órában (**gyakorisága**: **óra**, **időköz**: **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Másolási tevékenységet használó folyamatot

A folyamat tartalmazza egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva. A másolási tevékenység való óránkénti futásra van ütemezve. A folyamat JSON-definíciót a **forrás** típusa **HttpSource** és a **fogadó** típusa **BlobSink**.

A tulajdonságok listáját, amelyek **HttpSource** támogatott [HttpSource](#copy-activity-properties).

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
> Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Az Azure Data Factory és optimalizálhatja azt hibakeresésének módjai adatáthelyezés (másolási tevékenységgel) teljesítményét befolyásoló legfontosabb tényezők kapcsolatos további információkért tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md).