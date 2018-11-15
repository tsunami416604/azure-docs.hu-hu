---
title: Eszköz csatlakoztatása az Azure digitális Ikrekhez és a hitelesítési |} A Microsoft Docs
description: Az Azure digitális Twins használatával csatlakozhat, és eszközök hitelesítéséhez.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: f032e3ebf6a10411057cd6d41df0cad6248f328b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636237"
---
# <a name="create-and-manage-role-assignments"></a>Szerepkör-hozzárendelések létrehozása és felügyelete

Az Azure digitális Twins használ a szerepköralapú hozzáférés-vezérlés ([RBAC](./security-role-based-access-control.md)) erőforrásokhoz való hozzáférés kezelésére.

Minden egyes szerepkör-hozzárendelés tartalmazza:

* **Objektumazonosító**: egy Azure Active Directory-azonosítója, azonosítója vagy tartománynév
* **Objektumtípus azonosítója**
* **Szerepkör-definíció Azonosítóját**
* **Terület elérési útja**
* **Bérlőazonosító**: A legtöbb esetben egy Azure Active Directory-bérlő Azonosítóját

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="role-definition-identifiers"></a>Szerepkör-definíció azonosítók

Az alábbi táblázat bemutatja, mit is beszerezhetők a/szerepkörök API lekérdezésével.

| **Szerepkör** | **azonosító** |
| --- | --- |
| Hely rendszergazdája | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Felhasználói rendszergazda| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Eszközadminisztrátor | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Kulcs-rendszergazda | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Jogkivonat rendszergazdák | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Felhasználó | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Támogatási szakértő | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Eszköz-telepítő | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Átjáróeszköz | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Támogatott ObjectIdTypes

A támogatott `ObjectIdTypes`:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

```plaintext
HTTP POST YOUR_MANAGEMENT_API_URL/roleassignments
```

| **Name (Név)** | **Szükséges** | **Típus** | **Leírás** |
| --- | --- | --- | --- |
| Szerepkör azonosítója| Igen |Karakterlánc | A szerepkör-definíció azonosítója. Keresse meg a szerepkör-definíciók és azok azonosítók a rendszer API lekérdezésével. |
| objectId | Igen |Karakterlánc | A szerepkör-hozzárendelés, amelyek a társított típusa szerint kell formázni objektum azonosítója. Az a `DomainName` ObjectId ObjectIdType, kell kezdődnie az `“@”` karakter. |
| objectIdType | Igen |Karakterlánc | A szerepkör-hozzárendelés típusa. A következő sorokat a táblában egyikének kell lennie. |
| tenantId | Változó | Karakterlánc |A bérlőazonosító. Nem engedélyezett a `DeviceId` és `TenantId` ObjectIdTypes. Szükséges `UserId` és `ServicePrincipalId` ObjectIdTypes. A tartománynév ObjectIdType esetén nem kötelező. |
| elérési út * | Igen | Karakterlánc |A teljes elérési útvonal a `Space` objektum. Például: `/{Guid}/{Guid}`. Ha egy azonosítót a szerepkör-hozzárendelést a teljes grafikon van szüksége, adja meg `"/"`. Ezt a karaktert jelöli meg a legfelső szintű, de a használata nem ajánlott. A minimális jogosultság elvének mindig követik. |

## <a name="sample-configuration"></a>Példa konfigurációja

Ebben a példában egy felhasználó egy emelet egy bérlői tárhely felügyeleti hozzá kell férnie.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Ebben a példában az alkalmazás futása eszközeit és érzékelőit szimulálása tesztelési forgatókönyvekkel.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Egy tartomány részét képezik az összes felhasználó megkapja a tárolóhelyek eszközök, érzékelők és felhasználók számára olvasási hozzáférést. Ezt a hozzáférést a megfelelő kapcsolódó objektumokat tartalmaz.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Használat beolvasni a szerepkör-hozzárendelés beolvasása.

```plaintext
HTTP GET YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| **Name (Név)** | **A** | **Szükséges** |    **Típus** |  **Leírás** |
| --- | --- | --- | --- | --- |
| YOUR_PATH | Útvonal | True (Igaz) | Karakterlánc |    A hely teljes elérési útja |

Szerepkör-hozzárendelés törlése a törlés használatával.

```plaintext
HTTP DELETE YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ID
```

| **Name (Név)** | **A** | **Szükséges** | **Típus** | **Leírás** |
| --- | --- | --- | --- | --- |
| YOUR_ROLE_ID | Útvonal | True (Igaz) | Karakterlánc | Szerepkör-hozzárendelés azonosítója |

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [API-hitelesítés](./security-authenticating-apis.md).
