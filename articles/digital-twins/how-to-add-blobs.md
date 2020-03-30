---
title: Blobok hozzáadása objektumokhoz - Azure Digital Twins | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá blobokat a felhasználókhoz, az eszközökhöz és a terekhez az Azure Digital Twins alkalmazásban.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75929644"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Blobok hozzáadása objektumokhoz az Azure Digital Twins-ben

A blobok a gyakori fájltípusok, például a képek és a naplók strukturálatlan ábrázolásai. A blobok mime-típus (például "image/jpeg") és metaadatok (név, leírás, típus és így tovább) használatával követik nyomon, hogy milyen típusú adatokat képviselnek.

Az Azure Digital Twins támogatja a blobok eszközökhöz, terekhez és felhasználókhoz csatolását. A blobok egy felhasználó profilképét, eszközképet, videót, térképet, firmware zipet, JSON-adatokat, naplót stb.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Blobok feltöltése – áttekintés

Többrészes kérelmek használatával blobok feltöltése adott végpontokra és azok megfelelő funkciókat.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob metaadatai

A **tartalomtípus** és **a tartalom-kiállítás**mellett az Azure Digital Twins blob többrészes kérelmeknek meg kell adniuk a megfelelő JSON-törzset. Melyik JSON-törzset kell beküldeni, a folyamatban lévő HTTP-kérelemművelet től függ.

A négy fő JSON-séma a következő:

[![JSON-sémák](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

A JSON blob metaadatai megfelelnek a következő modellnek:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribútum | Típus | Leírás |
| --- | --- | --- |
| **zárójel** | Sztring | A blobot társítandó szülőentitás (szóközök, eszközök vagy felhasználók) |
| **név** |Sztring | A blob emberbarát neve |
| **Típus** | Sztring | A blob típusa - nem használható *típus* és *typeId*  |
| **gépazonosító** | Egész szám | A blobtípus-azonosító nem használható *típus* és *typeId* |
| **Altípus** | Sztring | A blob altípus - nem használható *altípus* és *altípusazonosító* |
| **altípusazonosító** | Egész szám | A blob altípusazonosítója nem használható *altípusés* *altípusazonosító* |
| **Leírás** | Sztring | A blob testreszabott leírása |
| **Megosztása** | Sztring | A blob megosztható-e -`None` `Tree`felsorak [ , ] `Global` |

Blob metaadatok mindig megadva, mint az első `.json` adattömb **tartalom-típus** `application/json` vagy fájlként. A fájladatok a második adattömbben vannak megadva, és bármilyen támogatott MIME típusúak lehetnek.

A Swagger dokumentáció részletesen ismerteti ezeket a modellsémákat.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Tudjon meg többet a referenciadokumentáció használatáról a [Swagger használata](./how-to-use-swagger.md)című elolvasásával.

### <a name="blobs-response-data"></a>Blobok válaszadatai

Az egyénileg visszaadott blobok megfelelnek a következő JSON-sémának:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribútum | Típus | Leírás |
| --- | --- | --- |
| **id** | Sztring | A blob egyedi azonosítója |
| **név** |Sztring | A blob emberbarát neve |
| **zárójel** | Sztring | A blobot társítandó szülőentitás (szóközök, eszközök vagy felhasználók) |
| **Típus** | Sztring | A blob típusa - nem használható *típus* és *typeId*  |
| **gépazonosító** | Egész szám | A blobtípus-azonosító nem használható *típus* és *typeId* |
| **Altípus** | Sztring | A blob altípus - nem használható *altípus* és *altípusazonosító* |
| **altípusazonosító** | Egész szám | A blob altípusazonosítója nem használható *altípusés* *altípusazonosító* |
| **Megosztása** | Sztring | A blob megosztható-e -`None` `Tree`felsorak [ , ] `Global` |
| **Leírás** | Sztring | A blob testreszabott leírása |
| **contentInfos (contentInfos)** | Tömb | Strukturálatlan metaadat-adatokat ad meg, beleértve a verziót is |
| **fullName (fullName)** | Sztring | A blob teljes neve |
| **spacePaths (helytaszti görbé** | Sztring | A tér elérési útja |

Blob metaadatok mindig megadva, mint az első `.json` adattömb **tartalom-típus** `application/json` vagy fájlként. A fájladatok a második adattömbben vannak megadva, és bármilyen támogatott MIME típusúak lehetnek.

### <a name="blob-multipart-request-examples"></a>Blob többrészes kérelem példák

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Ha egy szövegfájlt blobként szeretne feltölteni, és egy szóközhöz szeretné társítani, kérjen hitelesített HTTP POST-kérelmet a következő helyre:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

A következő testtel:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Érték | Csere erre |
| --- | --- |
| USER_DEFINED_BOUNDARY | Többrészes tartalomhatár neve |

A következő kód ugyanannak a blobfeltöltésnek a .NET implementációja a [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent)osztály használatával:

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Végül, [cURL](https://curl.haxx.se/) felhasználók is, hogy többrészes formában kéri az azonos módon:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Érték | Csere erre |
| --- | --- |
| YOUR_TOKEN | Az érvényes OAuth 2.0 token |
| YOUR_SPACE_ID | A blob társításához a terület azonosítója |
| PATH_TO_FILE | A szövegfájl elérési útja |

[![cURL példa](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

A sikeres POST visszaadja a blob új azonosítóját.

## <a name="api-endpoints"></a>API-végpontok

A következő szakaszok ismertetik a core blobkapcsolatos API-végpontok és azok funkcióit.

### <a name="devices"></a>Eszközök

Blobokat csatolhat az eszközökhöz. Az alábbi képen a Swagger referencia dokumentációa a felügyeleti API-k. Meghatározza az eszközhöz kapcsolódó API-végpontok blob-fogyasztás és a szükséges elérési út paramétereit, hogy adja át őket.

[![Eszközblobok](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Ha például frissíteni vagy létrehozni szeretne egy blobot, és csatolni szeretné a blobot egy eszközhöz, kérjen hitelesített HTTP PATCH-kérelmet a következő helyre:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blobazonosító |

A sikeres kérelmek egy JSON-objektumot [adnak vissza a korábban leírtak](#blobs-response-data)szerint.

### <a name="spaces"></a>Terek

Blobokat is csatolhat a szóközökhöz. Az alábbi kép felsorolja a blobok kezeléséért felelős összes terület API-végpontot. Emellett felsorolja az okat a végpontokba átadandó elérési út paramétereket is.

[![Térblobok](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Ha például egy területhez csatolt blobot szeretne visszaadni, kérjen hitelesített HTTP GET-kérelmet a következő képpen:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blobazonosító |

A sikeres kérelmek egy JSON-objektumot [adnak vissza a korábban leírtak](#blobs-response-data)szerint.

Az ugyanahhoz a végponthoz irányuló PATCH-kérelem frissíti a metaadatok leírását, és létrehozza a blob verzióit. A HTTP-kérelem a PATCH metóduson keresztül történik, a szükséges meta-adatokkal és a többrészes űrlapadatokkal együtt.

### <a name="users"></a>Felhasználók

Blobokat csatlakoztathat a felhasználói modellekhez (például profilkép társítására). Az alábbi képen a megfelelő felhasználói API-végpontok `id`és a szükséges elérési út paraméterek láthatók, például:

[![Felhasználói blobok](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Például egy felhasználóhoz csatolt blob lekéréséhez küldjön egy hitelesített HTTP GET-kérelmet a szükséges űrlapadatokkal a következő célokra:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blobazonosító |

A sikeres kérelmek egy JSON-objektumot [adnak vissza a korábban leírtak](#blobs-response-data)szerint.

## <a name="common-errors"></a>Gyakori hibák

* Gyakori hiba, hogy nem adja meg a megfelelő fejlécadatokat:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  A hiba elhárításához ellenőrizze, hogy az általános kérelem rendelkezik-e megfelelő **Content-Type** fejléccel:

     * `multipart/mixed`
     * `multipart/form-data`

  Ellenőrizze azt is, hogy minden *többrészes adattömb* rendelkezik-e a megfelelő **tartalomtípussal.**

* Egy második gyakori hiba akkor merül fel, amikor több blob van hozzárendelve ugyanahhoz az erőforráshoz a [térbeli intelligencia grafikonon:](concepts-objectmodel-spatialgraph.md)

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > Az **üzenetattribútum** az erőforrástól függően változik. 

  A térbeli diagramon belül minden erőforráshoz csak egy blob (mindegyik típusú) csatolható. 

  A hiba megoldásához frissítse a meglévő blobot a megfelelő API HTTP PATCH művelet használatával. Ezzel lecseréli a meglévő blob adatokat a kívánt adatokat.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Digital Twins Swagger referenciadokumentációjáról, olvassa el [az Azure digital twins swagger használata](how-to-use-swagger.md)című részét.

- Blobok postáson keresztüli feltöltéséhez olvassa el [a Postman konfigurálása](./how-to-configure-postman.md)című olvasni.