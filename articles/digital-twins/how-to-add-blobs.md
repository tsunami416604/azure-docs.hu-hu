---
title: Blobok hozzáadása az Azure digitális Twins objektumok |} A Microsoft Docs
description: 'Útmutató: blobok hozzáadása az Azure digitális Twins objektumokat.'
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: ffd7d71c33b569b396b9f8babf8105968ee525b9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263067"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Blobok hozzáadása az Azure digitális Twins objektumok

Blobok strukturálatlan ábrázolása a gyakori fájltípusokból, mint például a képek és a naplók legyenek. Blobok nyomon követheti, hogy milyen típusú adatokat képviselik MIME-típus használatával (például: "image/jpeg") és a metaadatokat (név, leírás, típusa és stb).

Az Azure digitális Twins támogatja a blobok csatolása eszköz, a tárolóhelyek és a felhasználó számára. Blobok jelenthetik a profilképét, a felhasználó, eszköz fénykép, videó, térkép, egy belső vezérlőprogram zip, JSON-adatokat, egy napló, stb.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Blobok – áttekintés feltöltése

Használhatja a több részből álló kéréseket az eszközspecifikus végpontokat és a megfelelő funkciók blobok feltöltése.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob metaadatai

Mellett **Content-Type** és **tartalomtípus-szabályozó**, az Azure digitális Twins többrészes blobkérelmek meg kell adnia a megfelelő JSON-törzse. Melyik JSON-törzse elküldése zajlik HTTP-kérési művelet típusától függ.

A négy fő JSON-sémák a következők:

![JSON-sémáinak][1]

JSON-blob metaadatainak megfelel a következő modellhez:

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
| **parentId** | Karakterlánc | A szülőentitás a blob társítandó (szóköz, eszközök vagy felhasználók) |
| **name** |Karakterlánc | A blob emberi mobilbarát nevét |
| **type** | Karakterlánc | Blob - típusa nem használható *típus* és *typeId*  |
| **typeId** | Egész szám | A blob azonosítója – nem használható *típus* és *typeId* |
| **Altípus** | Karakterlánc | A blob - altípusa nem használható *altípus* és *subtypeId* |
| **subtypeId** | Egész szám | A blob - altípus azonosítója nem használható *altípus* és *subtypeId* |
| **description** | Karakterlánc | A blob testre szabott leírása |
| **megosztás** | Karakterlánc | E megoszthatók a blob - számbavételi [`None`, `Tree`, `Global`] |

BLOB metaadatainak mindig van megadva az első adatrészletben a **Content-Type** `application/json` vagy mint egy `.json` fájlt. A fájlokban tárolt adatokhoz a második adattömb van megadva, és bármely támogatott MIME-típusát.

A Swagger-dokumentáció ezen modell sémák teljes részletesen ismerteti.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Ismerje meg, olvassa el a dokumentációja használatáról [hogyan generáljon a swaggerrel](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Blobok válaszának adatai

Külön-külön visszaadott blobok felelnek meg a következő JSON-séma:

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
| **id** | Karakterlánc | A BLOB egyedi azonosítója |
| **name** |Karakterlánc | A blob emberi mobilbarát nevét |
| **parentId** | Karakterlánc | A szülőentitás a blob társítandó (szóköz, eszközök vagy felhasználók) |
| **type** | Karakterlánc | Blob - típusa nem használható *típus* és *typeId*  |
| **typeId** | Egész szám | A blob azonosítója – nem használható *típus* és *typeId* |
| **Altípus** | Karakterlánc | A blob - altípusa nem használható *altípus* és *subtypeId* |
| **subtypeId** | Egész szám | A blob - altípus azonosítója nem használható *altípus* és *subtypeId* |
| **megosztás** | Karakterlánc | E megoszthatók a blob - számbavételi [`None`, `Tree`, `Global`] |
| **description** | Karakterlánc | A blob testre szabott leírása |
| **contentInfos** | Tömb | Adja meg a strukturálatlan metaadat-információkat, beleértve a verzió |
| **fullName** | Karakterlánc | A blob teljes neve |
| **spacePaths** | Karakterlánc | A terület elérési útja |

BLOB metaadatainak mindig van megadva az első adatrészletben a **Content-Type** `application/json` vagy mint egy `.json` fájlt. A fájlokban tárolt adatokhoz a második adattömb van megadva, és bármely támogatott MIME-típusát.

### <a name="blob-multipart-request-examples"></a>BLOB többrészes kérelem példák

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Töltsön fel egy szöveges fájlt blobként, és társíthatja azt egy szóközt, győződjön meg arról, hogy hitelesített HTTP POST-kérelmet:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

A következő szervezethez:

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
| USER_DEFINED_BOUNDARY | A többrészes tartalom határcsoport neve |

A következő kódot az ugyanazon blob feltöltése, a osztály használatával egy .NET megvalósítását [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

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

Végül [cURL](https://curl.haxx.se/) felhasználók is kérést többrészes űrlap azonos módon:

![Eszköz blobok][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Érték | Csere erre |
| --- | --- |
| YOUR_TOKEN | Az érvényes OAuth 2.0 jogkivonat |
| YOUR_SPACE_ID | A hely társításához a blob azonosítója |
| PATH_TO_FILE | A szöveges fájl elérési útja |

Egy sikeres bejegyzés az új azonosítója a blob (vörös színnel korábban) adja vissza.

## <a name="api-endpoints"></a>API-végpontok

A következő szakaszok ismertetik a core blobbal kapcsolatos API-végpontokat és azok a funkciók.

### <a name="devices"></a>Eszközök

Blobok eszközöket csatlakoztathat. Az alábbi képen látható a Swagger-dokumentáció, a felügyeleti API-k. Azt adja meg az API-végpontjainak eszközzel kapcsolatos blob használat és a szükséges útvonal paramétereket, átadandó őket.

![Eszköz blobok][2]

Ha például frissítése vagy blob létrehozása és csatolása a blob egy eszközön, győződjön meg arról, egy hitelesített JAVÍTANI a HTTP-kérést:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blob azonosítója |

Sikeres kérések vissza JSON-objektum, mint [fentebb leírt](#blobModel).

### <a name="spaces"></a>Szóközök

Blobok csatlakoztathat szóközöket is. Az alábbi képen végpontjai terület API blobok kezeléséért felelős. Ezekre a végpontokra átadandó elérési út paramétereit is felsorolja.

![Lemezterület-blobok][3]

Például adja vissza egy blob egy szóközzel csatolt, győződjön meg arról, egy hitelesített HTTP GET kérést:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blob azonosítója |

Sikeres kérések vissza JSON-objektum, mint [fentebb leírt](#blobModel).

Egyazon végpont a PATCH-kérés metaadatok leírását frissíti, és létrehozza a blob verzióit. A HTTP-kérelem a PATCH metódust, bármilyen szükséges meta és többrészes űrlapadatok keresztül történik.

### <a name="users"></a>Felhasználók

Blobok csatlakoztathat felhasználói modellek (például társítása a profilképét). Az alábbi képen látható megfelelő felhasználói API-végpontok és a szükséges útvonal paramétereket, például `id`:

![Felhasználói blobok][4]

Például beolvasni egy blob egy felhasználó csatlakozik, győződjön meg arról, az űrlap adat egy hitelesített HTTP GET kérés:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt blob azonosítója |

Sikeres kérések vissza JSON-objektum, mint [fentebb leírt](#blobModel).

## <a name="common-errors"></a>Gyakori hibák

Általános hiba magában foglalja a nem biztosítja a megfelelő fejléc-információkat:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Ez a hiba elhárításához győződjön meg arról, hogy a teljes kérést rendelkezik egy megfelelő **Content-Type** fejléc:

* `multipart/mixed`
* `multipart/form-data`

Azt is ellenőrizze, hogy egyes többrészes adattömbök rendelkezik egy megfelelő **Content-Type** igény szerint.

## <a name="next-steps"></a>További lépések

- Az Azure digitális Twins Swagger dokumentációja kapcsolatos további információkért olvassa el [használata az Azure digitális Twins Swagger](how-to-use-swagger.md).

- Postman – blobok feltöltése, olvassa el [Postman konfigurálása](./how-to-configure-postman.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG