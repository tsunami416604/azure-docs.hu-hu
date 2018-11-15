---
title: Blobok hozzáadása az Azure digitális Twins objektumok |} A Microsoft Docs
description: Blobok hozzáadása az Azure digitális Twins objektumok ismertetése
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688141"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Blobok hozzáadása az Azure digitális Twins objektumok

Blobok legyenek a gyakori fájltípusokból (például a képek és a naplók) strukturálatlan ábrázolásai. Blobok milyen típusú adatokat képviselik MIME-típus használatával nyomon követheti, (például: "image/jpeg") és a metaadatokat (név, leírás, típus stb.).

Az Azure digitális Twins támogatja a Blobok csatolása eszköz, a tárolóhelyek és a felhasználó számára. Blobok hozhat létre egy profilhoz tartozó fotó egy felhasználó, eszköz fényképet, egy videót, térképet vagy a napló számára.

> [!NOTE]
> Ez a cikk feltételezi, hogy:
> * Hogy a példány megfelelően van konfigurálva a felügyeleti API-kérelmek fogadásához.
> * Hogy Ön már helyesen hitelesítve a választott REST-ügyfél használatával.

## <a name="uploading-blobs-an-overview"></a>Blobok feltöltése: áttekintés

Blobok feltöltése a meghatározott végpontokhoz és a megfelelő funkciók többrészes kérelmek szolgálnak.

> [!IMPORTANT]
> Több részből álló kéréseket három alapvető adatokra van szükség. Az Azure digitális Twins:
> * A **Content-Type** fejléc:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * A **tartalomtípus-szabályozó**: `form-data; name="metadata"`.
> * A feltöltendő fájl tartalma.
>
> A pontos **Content-Type** és **tartalomtípus-szabályozó** használati forgatókönyvtől függően eltérőek lehetnek.

Minden egyes többrészes kérelem több darabolja van. Az Azure digitális Twins felügyeleti API-k többrészes kérelmek van felosztva **két** (**2**) az ilyen részeket:

1. Az első rész megadása kötelező, és tartalmazza a Blob metaadatait, például egy kapcsolódó MIME-típus szerint a **Content-Type** és **tartalomtípus-szabályozó** felett.

1. A második rész tartalmazza a tényleges Blob tartalmát (strukturálatlan tartalmát a fájl).  

A két részből egyike sem szükségesek **javítására** kérelmeket. Mindkettő szükséges **POST** vagy -létrehozási műveletek.

### <a name="blob-metadata"></a>Blob metaadatai

Mellett **Content-Type** és **tartalomtípus-szabályozó**, több részből álló kéréseket is meg kell adnia a megfelelő JSON-törzse. Melyik JSON-törzse elküldéséhez végzett HTTP-kérési művelet típusától függ.

A négy fő JSON-sémáinak használja a következők:

![Lemezterület-blobok][1]

A modell sémák a megadott Swagger-dokumentáció teljes részletes leírását.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Ismerje meg, olvassa el a megadott dokumentációja használatáról [hogyan generáljon a swaggerrel](./how-to-use-swagger.md).

### <a name="examples"></a>Példák

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Tételére egy POST-kérelmet, amely egy szöveges fájlt blobként tölt fel, és társítja azt egy-egy szóközzel:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

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

| Paraméter értéke | Csere erre |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | A többrészes tartalom határcsoport neve |

Az azonos Blob feltöltése a .NET megvalósítását lejjebb a osztállyal [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>API-végpontok

Az alábbiakban core végpontok és azok adott funkciókat bemutató áll rendelkezésre.

### <a name="devices"></a>Eszközök

Blobok eszközök csatolhatók. A kép alatt (a Swagger-dokumentáció, a felügyeleti API-k ábrázoló) Itt adhatja meg, eszközzel kapcsolatos API-végpontjainak Blob használat és a szükséges útvonal paramétereket, átadandó őket:

![Eszköz blobok][2]

Ha például frissítése vagy hozzon létre egy blobot, majd csatolja a Blob egy eszköz, PATCH-kérés jön létre:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt Blob azonosítója |

Sikeres kérések egy DeviceBlob JSON-objektumot ad vissza a válaszban. A következő JSON-sémájában DeviceBlobs felelnek meg:

| Attribútum | Típus | Leírás | Példák |
| --- | --- | --- | --- |
| **DeviceBlobType** | Karakterlánc | Egy Blob kategóriát, amelyek egy eszközhöz | `Model` és `Specification` |
| **DeviceBlobSubtype*** | Karakterlánc | Egy Blob alkategória, amely részletesebb DeviceBlobType, mint | `PhysicalModel`, `LogicalModel`, `KitSpecification`, és `FunctionalSpecification` |

> [!TIP]
> A fenti táblázat segítségével kezelni kérelem sikeresen visszaadott adatokat.

### <a name="spaces"></a>Tárolóhelyek

Blobok szóközöket is lehet rendelni. Az alábbi képen végpontjai terület API felelős Blobok együtt őket átadandó paramétereket elérési útja:

![Lemezterület-blobok][3]

Például adja vissza egy Blob egy szóközzel csatolt, győződjön meg arról, egy GET kérelmet kell:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt Blob azonosítója |

PATCH-kérés tételére egyazon végpont lehetővé teszi, hogy frissítse a metaadatok leírását, és hozza létre a Blob egy új verzióját. A HTTP-kérelem minden szükséges meta és többrészes űrlapadatokból együtt a PATCH-metódus használatával jön létre.

Sikeres műveletek adja vissza egy SpaceBlob, amely megfelel a következő sémának, és használható a visszaadott adatok felhasználása:

| Attribútum | Típus | Leírás | Példák |
| --- | --- | --- | --- |
| **SpaceBlobType** | Karakterlánc | Egy terület csatolható Blob kategória | `Map` és `Image` |
| **SpaceBlobSubtype** | Karakterlánc | Egy Blob alkategória, amely részletesebb SpaceBlobType, mint | `GenericMap`, `ElectricalMap`, `SatelliteMap`, és `WayfindingMap` |

### <a name="users"></a>Felhasználók

Blobok felhasználói modellek csatolhatók (például társítsa a profilképét). Az alábbi kép ábrázolja megfelelő felhasználók API-végpontokat, és a szükséges útvonal paramétereket, például egy `id`:

![Felhasználói blobok][4]

Ha például beolvasni egy Blob egy felhasználó csatlakozik, győződjön meg arról, egy GET kéréssel bármely szükséges űrlap-adatokkal való:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_BLOB_ID* | A kívánt Blob azonosítója |

A következő JSON-modellek megfelel az visszaadott JSON (UserBlobs):

| Attribútum | Típus | Leírás | Példák |
| --- | --- | --- | --- |
| **UserBlobType** | Karakterlánc | Egy felhasználó társítható Blob kategória | `Image` és `Video` |
| **UserBlobSubtype** |  Karakterlánc | Egy Blob alkategória, amely részletesebb UserBlobType, mint | `ProfessionalImage`, `VacationImage`, és `CommercialVideo` |

## <a name="common-errors"></a>Gyakori hibák

Nem, többek között a megfelelő fejléc-információkat:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>További lépések

A megadott Azure digitális Twins Swagger dokumentációja kapcsolatos további információkért olvassa el [használata a digitális Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
