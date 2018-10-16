---
title: Eszköz csatlakoztatása az Azure digitális Ikrekhez és a hitelesítési ismertetése |} A Microsoft Docs
description: Az Azure digitális Twins használatával csatlakozhat, és eszközök hitelesítéséhez
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324112"
---
# <a name="create-and-manage-role-assignments"></a>Hozzon létre és kezelhetik a szerepkör-hozzárendeléseket

Az Azure digitális Twins használ a szerepköralapú hozzáférés-vezérlés ([RBAC](./security-role-based-access-control.md)) erőforrásokhoz való hozzáférés kezelésére.

Minden egyes szerepkör-hozzárendelés tartalmazza:

* Egy **objektumazonosító** (egy Azure Active Directory-azonosítója, azonosítója vagy tartománynevet).
* Egy **objektumtípus azonosítója**.
* A **szerepkör-definíció Azonosítóját**.
* A **terület elérési útja**.
* (A legtöbb esetben) egy Azure Active Directory **bérlőazonosító**.

## <a name="role-definition-identifiers"></a>Szerepkör-definíció azonosítók

Az alábbi táblázat bemutatja, mit is beszerezhetők a helyrendszer-szerepkörök API lekérdezésével:

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
| GatewayDevice | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Támogatott ObjectIdTypes

A támogatott `ObjectIdTypes` vannak:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Name (Név)** | **Szükséges** | **Típus** | **Leírás** |
| --- | --- | --- | --- |
| Szerepkör azonosítója| Igen |sztring | A szerepkör-definíció azonosítója. Szerepkör-definíciók és a azonosítókról található a rendszer API lekérdezésével. |
| objectId | Igen |sztring | A szerepkör-hozzárendelés, amelyek a társított típusa szerint kell formázni objektum azonosítója. Az a `DomainName` ObjectId ObjectIdType, kell kezdődnie az `“@”` karakter. |
| objectIdType | Igen |sztring | A szerepkör-hozzárendelés típusa. A következő sorokat a táblában egyikének kell lennie. |
| tenantId | Változó | sztring |A bérlőazonosító. Nem engedélyezett a `DeviceId` és `TenantId` ObjectIdTypes. Szükséges `UserId` és `ServicePrincipalId` ObjectIdTypes. A tartománynév ObjectIdType esetén nem kötelező. |
| elérési út * | Igen | sztring |A teljes elérési útvonal a `Space` objektum. Például: `/{Guid}/{Guid}` azonosítót a szerepkör-hozzárendelést a teljes grafikon van szüksége, adja meg a `"/"` (amely a legfelső szintű jelöli). Azonban, hogy használatával nem ajánlott, és **mindig kövesse a minimális jogosultság elvének**. |

## <a name="sample-configuration"></a>Példa konfigurációja

Egy felhasználónak egy emelet egy bérlői tárhely rendszergazdai hozzáféréssel kell rendelkeznie:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Egy alkalmazás, hogy a futtatások vizsgálati eszközök és érzékelők utánzási forgatókönyvek:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

A tartomány része minden felhasználók kapnak olvasási hozzáféréssel tárolóhelyek, érzékelők és a felhasználókat, beleértve azok megfelelő kapcsolódó objektumok:

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Szerepkör-hozzárendelés beolvasása:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Name (Név)** | **A** | **Szükséges** |    **Típus** |  **Leírás** |
| --- | --- | --- | --- | --- |
| Útvonal | Útvonal | True (Igaz) | Sztring | A hely teljes elérési útja |

Szerepkör-hozzárendelés törlése:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Name (Név)** | **A** | **Szükséges** | **Típus** | **Leírás** |
| --- | --- | --- | --- | --- |
| ID (Azonosító) | Útvonal | True (Igaz) | Sztring |   Szerepkör-hozzárendelés azonosítója |

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [API-hitelesítés](./security-authenticating-apis.md).
