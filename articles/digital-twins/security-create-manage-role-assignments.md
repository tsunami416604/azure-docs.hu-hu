---
title: Hozzon létre, és az Azure digitális Twins szerepkör-hozzárendelések kezeléséhez |} A Microsoft Docs
description: Hozzon létre, és az Azure digitális Twins szerepkör-hozzárendelések kezeléséhez.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72a42e273029bd42d77531953ff5cbfc0fe5c295
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810899"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Létrehozása és kezelése az Azure digitális Twins szerepkör-hozzárendelések

Az Azure digitális Twins használ a szerepköralapú hozzáférés-vezérlés ([RBAC](./security-role-based-access-control.md)) erőforrásokhoz való hozzáférés kezelésére.

## <a name="role-assignments-overview"></a>Szerepkör-hozzárendelések – áttekintés

Minden egyes szerepkör-hozzárendelés megfelel a következő-definíciót:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

Az alábbi táblázat ismerteti az egyes tulajdonságokat:

| Attribútum | Name (Név) | Szükséges | Típus | Leírás |
| --- | --- | --- | --- | --- |
| Szerepkör azonosítója | Szerepkör-definíció azonosítója | Igen | Karakterlánc | A kívánt szerepkör-hozzárendelés egyedi azonosítója. Keresse meg a szerepkör-definíciók és azok azonosító lekérdezésekor a rendszer API-t, vagy tekintse át az alábbi táblázat szerint. |
| objectId | Objektumazonosító | Igen | Karakterlánc | Az Azure Active Directory azonosítója, azonosítója vagy tartománynév számára. Milyen vagy akikkel a szerepkör-hozzárendelés hozzá van rendelve. A szerepkör-hozzárendelés vzhledem ke svému társított typu kell formázni. Az a `DomainName` objectId objectIdType, kell kezdődnie az `“@”` karakter. |
| objectIdType | Objektumtípus azonosítója | Igen | Karakterlánc | Milyen típusú használt objektumazonosító. Lásd: **ObjectIdTypes támogatott** alatt. |
| elérési út | Terület elérési útja | Igen | Karakterlánc | A teljes elérési útvonal a `Space` objektum. Például: `/{Guid}/{Guid}`. Ha egy azonosítót a szerepkör-hozzárendelést a teljes grafikon van szüksége, adja meg `"/"`. Ezt a karaktert jelöli meg a legfelső szintű, de a használata nem ajánlott. A minimális jogosultság elvének mindig követik. |
| tenantId | Bérlőazonosító | Változó | Karakterlánc | A legtöbb esetben az Azure Active Directory-bérlő azonosítója. Nem engedélyezett a `DeviceId` és `TenantId` ObjectIdTypes. Szükséges `UserId` és `ServicePrincipalId` ObjectIdTypes. A tartománynév ObjectIdType esetén nem kötelező. |

### <a name="supported-role-definition-identifiers"></a>Támogatott szerepkör-definíció azonosítók

Minden egyes szerepkör-hozzárendelés társítja az Azure digitális Twins környezetben egy entitás egy szerepkör-definíció.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Támogatott objektumtípus azonosítója

Korábban a **objectIdType** attribútum jelent meg.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Szerepkör-hozzárendelési műveletek

Az Azure digitális Twins támogatja a teljes *létrehozása*, *OLVASÁSA*, és *törlése* műveleteket a szerepkör-hozzárendeléseket. *FRISSÍTÉS* műveletek kezelése szerepkör-hozzárendelések hozzáadásával, szerepkör-hozzárendelés eltávolítása vagy módosítása a [térbeli intelligencia Graph](./concepts-objectmodel-spatialgraph.md) csomópontokat, amelyek segítségével a szerepkör-hozzárendeléseit a hozzáférést.

![Szerepkör-hozzárendelés végpontok][1]

A megadott Swagger dokumentációja további információkat az összes rendelkezésre álló API végpontok tanúsítványkérési műveletek és definíciók tartalmazza.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Engedélyek az egyszerű szolgáltatás számára

Az egyszerű szolgáltatás engedélyek egyike gyakran az első lépéseket megteheti az Azure digitális Twins használatakor. Jár:

1. Bejelentkezés az Azure-példányba PowerShell-lel.
1. A szolgáltatásnév adatait beszerzése.
1. Az egyszerű szolgáltatás hozzárendelése a kívánt szerepkört.

Az Alkalmazásazonosító Önnek van megadva, az Azure Active Directoryban. További információk konfigurálása és kiépítése az Azure Active Directory digitális Twins tudnivalókért olvassa el a [rövid](./quickstart-view-occupancy-dotnet.md).

Miután az alkalmazás azonosítója, hajtsa végre a következő PowerShell-parancsokat:

```shell
Login-AzureRmAccount
Get-AzureRmADServicePrincipal -ApplicationId  <ApplicationId>
```

A felhasználó a **rendszergazdai** szerepkör is hozzárendelheti a terület-rendszergazdai szerepkör a felhasználó hitelesített HTTP POST-kérelmet, így az URL-cím:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Az alábbi JSON-törzse:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Az összes szerepkör lekérése

![Helyrendszer-szerepkörök][2]

Elérhető szerepkörök (szerepkör-definíciók) listában, győződjön meg arról, egy hitelesített HTTP GET kérést:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

A kérelem sikeres adja vissza egy JSON-tömb minden egyes szerepkörhöz rendelt bejegyzéseket:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Ellenőrizze a megadott szerepkör-hozzárendelés

Ellenőrizze a megadott szerepkör-hozzárendelés, győződjön meg arról, egy hitelesített HTTP GET kérést:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Hodnota parametru** | **Kötelező** |  **Típus** |  **Leírás** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True (Igaz) | Karakterlánc |   A felhasználói azonosító objectIdType objectid azonosítója. |
| YOUR_PATH | True (Igaz) | Karakterlánc |   A kiválasztott útvonal a hozzáférés ellenőrzéséhez. |
| YOUR_ACCESS_TYPE |  True (Igaz) | Karakterlánc |   A hozzáférés típusa kereséséhez. |
| YOUR_RESOURCE_TYPE | True (Igaz) | Karakterlánc |  Ellenőrizze az erőforrás. |

A kérelem sikeres egy logikai értéket ad vissza `true` vagy `false` jelzi, hogy a hozzáférés típusa van rendelve a felhasználó a megadott elérési út és erőforrás.

### <a name="get-role-assignments-by-path"></a>Szerepkör-hozzárendelések beolvasása elérési út alapján

Egy elérési utat az összes szerepkör-hozzárendelések beolvasása, győződjön meg arról, egy hitelesített HTTP GET kérést:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Érték | Csere erre |
| --- | --- |
| YOUR_PATH | A hely teljes elérési útja |

A kérelem sikeres JSON-tömböt ad vissza az egyes szerepkör-hozzárendelés a kiválasztott társított **elérési út** paramétert:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Engedély visszavonása

Egy címzettet az engedélyek visszavonása, törölje a szerepkör-hozzárendelés azáltal, hogy egy hitelesített HTTP DELETE kérelmet:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | A **azonosító** , a szerepkör-hozzárendelés eltávolítása |

Egy sikeres DELETE kérelem a 204 válasz állapota adja vissza. Ellenőrizze a szerepkör-hozzárendelés eltávolításának [ellenőrzése](#check) attól függ, hogy a szerepkör-hozzárendelés rendelkezik-e továbbra is.

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

Szerepkör-hozzárendelés létrehozásához hajtsa végre az URL-cím hitelesített HTTP POST-kérelmet:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Győződjön meg arról, hogy a JSON-törzse megfelel-e a következő mintát követik:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

A kérelem sikeres adja vissza egy 201 válasz állapota, valamint a **azonosító** az újonnan létrehozott szerepkör-hozzárendelés:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Konfigurációs példák

Az alábbi példák bemutatják, hogyan konfigurálhatja a JSON-törzse a leggyakrabban előforduló szerepkör-hozzárendelés számos forgatókönyv.

* **Példa**: Egy felhasználónak egy emelet egy bérlői tárhely rendszergazdai hozzáféréssel kell rendelkeznie.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Példa**: Az alkalmazás futása eszközeit és érzékelőit szimulálása tesztelési forgatókönyvekkel.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Példa**: Egy tartomány részét képezik az összes felhasználó megkapja a tárolóhelyek eszközök, érzékelők és felhasználók számára olvasási hozzáférést. Ezt a hozzáférést a megfelelő kapcsolódó objektumokat tartalmaz.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins szerepkör-alapú-hozzáférés-vezérlési áttekintéséhez olvassa el a [szerepkör – base-hozzáférés-vezérlési](./security-authenticating-apis.md).

Az Azure digitális Twins API hitelesítéssel kapcsolatban, olvassa el [API-hitelesítés](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
