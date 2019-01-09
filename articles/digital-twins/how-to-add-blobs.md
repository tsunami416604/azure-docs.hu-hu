---
title: Blobok hozzáadása az Azure digitális Twins objektumok |} A Microsoft Docs
description: 'Útmutató: blobok hozzáadása az Azure digitális Twins objektumokat.'
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 36f4caac38f2f4891af6f61b78b55c7eff15eae4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116738"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Blobok hozzáadása az Azure digitális Twins objektumok

Blobok strukturálatlan ábrázolása a gyakori fájltípusokból, mint például a képek és a naplók legyenek. Blobok nyomon követheti, hogy milyen típusú adatokat képviselik MIME-típus használatával (például: "image/jpeg") és a metaadatokat (név, leírás, típusa és stb).

Az Azure digitális Twins támogatja a blobok csatolása eszköz, a tárolóhelyek és a felhasználó számára. Blobok jelenthetik a profilképét, a felhasználó, eszköz fénykép, videó, térkép, egy belső vezérlőprogram zip, JSON-adatokat, egy napló, stb.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Blobok feltöltése: áttekintés

Használhatja a több részből álló kéréseket az eszközspecifikus végpontokat és a megfelelő funkciók blobok feltöltése.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob metaadatai

Mellett **Content-Type** és **tartalomtípus-szabályozó**, az Azure digitális Twins többrészes blobkérelmek meg kell adnia a megfelelő JSON-törzse. Melyik JSON-törzse elküldése zajlik HTTP-kérési művelet típusától függ.

A négy fő JSON-sémák a következők:

![JSON-sémáinak][1]

A Swagger-dokumentáció ezen modell sémák teljes részletesen ismerteti.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Ismerje meg, olvassa el a dokumentációja használatáról [hogyan generáljon a swaggerrel](./how-to-use-swagger.md).

### <a name="examples"></a>Példák

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

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Mindkét példák:

1. Győződjön meg arról, hogy a fejlécek belefoglalása: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Győződjön meg arról, hogy a szervezet többrészes:

   - Az első rész tartalmazza a szükséges blob metaadatait.
   - A második rész a szöveges fájl tartalmaz.

1. Győződjön meg arról, hogy a szöveges fájl van megadva `Content-Type: text/plain`.

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

Sikeres kérelem adja vissza egy **DeviceBlob** JSON-objektum a válaszban. **DeviceBlob** objektumok felelnek meg a következő JSON-séma:

| Attribútum | Típus | Leírás | Példák |
| --- | --- | --- | --- |
| **DeviceBlobType** | Karakterlánc | Egy blob kategóriát, amelyek egy eszközhöz | `Model` és `Specification` |
| **DeviceBlobSubtype** | Karakterlánc | Egy blob alkategória-nál több jellemző **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification`, és `FunctionalSpecification` |

> [!TIP]
> Az előző táblázatban használatával kezelni kérelem sikeresen visszaadott adatokat.

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

Egyazon végpont a PATCH-kérés metaadatok leírását frissíti, és hozza létre a blob új verzióit. A HTTP-kérelem a PATCH metódust, bármilyen szükséges meta és többrészes űrlapadatok keresztül történik.

Sikeres műveletek visszaadása egy **SpaceBlob** objektum, amely megfelel a következő sémának. Visszaadott adatok felhasználásához használhatja azt.

| Attribútum | Típus | Leírás | Példák |
| --- | --- | --- | --- |
| **SpaceBlobType** | Karakterlánc | Egy terület csatolható blob kategória | `Map` és `Image` |
| **SpaceBlobSubtype** | Karakterlánc | Egy blob alkategória-nál több jellemző **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap`, és `WayfindingMap` |

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

A visszaküldött JSON (**UserBlob** objektumok) megfelel-e a következő JSON-modellek:

| Attribútum | Típus | Leírás | Példák |
| --- | --- | --- | --- |
| **UserBlobType** | Karakterlánc | Egy felhasználó társítható blob kategória | `Image` és `Video` |
| **UserBlobSubtype** |  Karakterlánc | Egy blob alkategória-nál több jellemző **UserBlobType** | `ProfessionalImage`, `VacationImage`, és `CommercialVideo` |

## <a name="common-errors"></a>Gyakori hibák

Gyakori hiba, hogy a megfelelő fejléc-információkat tartalmazza:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>További lépések

- Az Azure digitális Twins Swagger dokumentációja kapcsolatos további információkért olvassa el [használata az Azure digitális Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
