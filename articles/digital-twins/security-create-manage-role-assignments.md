---
title: Szerepkör-hozzárendelések létrehozása és kezelése az Azure Digital Twinsban | Microsoft Docs
description: Szerepkör-hozzárendelések létrehozása és kezelése az Azure Digital Ikrekben.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: lyhughes
ms.custom: seodec18
ms.openlocfilehash: 968ae62344f99edf8eb46eb62a4cf13f300c868f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815640"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Szerepkör-hozzárendelések létrehozása és kezelése az Azure Digital Ikrekben

Az Azure Digital Twins szerepköralapú hozzáférés-vezérlést ([RBAC](./security-role-based-access-control.md)) használ az erőforrásokhoz való hozzáférés kezelésére.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>Szerepkör-hozzárendelések áttekintése

Minden szerepkör-hozzárendelés megfelel a következő definíciónak:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

Az alábbi táblázat az egyes attribútumokat ismerteti:

| Attribútum | Name (Név) | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| Szerepkörazonosítónak | Szerepkör-definíciós azonosító | Igen | Sztring | A kívánt szerepkör-hozzárendelés egyedi azonosítója. Keresse meg a szerepkör-definíciókat és azok azonosítóját a System API lekérdezésével vagy az alábbi táblázat áttekintésével. |
| objectId | Objektumazonosító | Igen | Karakterlánc | Azure Active Directory azonosító, egyszerű szolgáltatásnév vagy tartománynév. A szerepkör-hozzárendelés hozzárendelése a következőhöz:. A szerepkör-hozzárendelést a hozzá tartozó típusnak megfelelően kell formázni. A `DomainName` objectIdType a objectId `“@”` karakterrel kell kezdődnie. |
| objectIdType | Objektumazonosító típusa | Igen | Sztring | A használt objektumazonosító típusa. Lásd alább a **támogatott ObjectIdTypes** . |
| path | Hely elérési útja | Igen | Sztring | Az `Space` objektum teljes elérési útja. Például: `/{Guid}/{Guid}`. Ha az azonosítónak a teljes gráf szerepkör-hozzárendelésére van szüksége `"/"`, akkor a következőt kell megadnia:. Ez a karakter kijelöli a gyökeret, de a használata nem ajánlott. Mindig kövesse a legalacsonyabb jogosultsági szint elvét. |
| tenantId | Bérlő azonosítója | Változó | Sztring | A legtöbb esetben egy Azure Active Directory bérlő azonosítója. A és `DeviceId` `TenantId` a ObjectIdTypes nem engedélyezett. A és `UserId` `ServicePrincipalId` a ObjectIdTypes szükséges. A tartománynév ObjectIdType nem kötelező megadni. |

### <a name="supported-role-definition-identifiers"></a>Támogatott szerepkör-definíciós azonosítók

Minden szerepkör-hozzárendelés társít egy szerepkör-definíciót az Azure digitális Twins-környezetében lévő entitással.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Támogatott objektumazonosító-típusok

Korábban a **objectIdType** attribútum lett bevezetve.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Szerepkör-hozzárendelési műveletek

Az Azure digitális Twins támogatjaa szerepkör-hozzárendelések teljes létrehozási, *olvasási*és *törlési* műveleteit. A *frissítési* műveletek kezelése szerepkör-hozzárendelések hozzáadásával, szerepkör-hozzárendelések eltávolításával, illetve a szerepkör-hozzárendelések által elérhetővé tett [térbeli intelligencia Graph](./concepts-objectmodel-spatialgraph.md) -csomópontok módosításával történik.

![Szerepkör-hozzárendelési végpontok][1]

A rendelkezésre álló hencegés dokumentációja további információkat tartalmaz az összes elérhető API-végpontról, a kérelmek műveleteiről és a definícióról.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Engedélyek megadása az egyszerű szolgáltatásnév számára

Az Azure-beli digitális Twins-használat során az első lépések egyike az, hogy a szolgáltatáshoz engedélyeket adjon meg. A következőket vonja maga után:

1. Jelentkezzen be az Azure-példányba a PowerShell használatával.
1. Az egyszerű szolgáltatásnév adatainak beszerzése.
1. Rendelje hozzá a kívánt szerepkört az egyszerű szolgáltatáshoz.

Az alkalmazás AZONOSÍTÓját a rendszer a Azure Active Directoryban biztosítja. Ha többet szeretne megtudni a Active Directory Azure Digital ikrek konfigurálásáról és üzembe helyezéséről, olvassa el a [](./quickstart-view-occupancy-dotnet.md)gyors üzembe helyezési útmutatót.

Miután megadta az alkalmazás AZONOSÍTÓját, hajtsa végre a következő PowerShell-parancsokat:

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

A **rendszergazdai** szerepkörrel rendelkező felhasználók ezt követően az URL-címre egy hitelesített http post-kérést rendelhetnek a felhasználóhoz.

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

A következő JSON-törzstel:

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

### <a name="retrieve-all-roles"></a>Összes szerepkör beolvasása

![Rendszerszerepkörök][2]

Az összes elérhető szerepkör (szerepkör-definíció) listázásához hozzon végre egy hitelesített HTTP GET kérelmet a következőre:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Egy sikeres kérelem egy JSON-tömböt ad vissza, amely az egyes hozzárendelt szerepkörökhöz tartozó bejegyzéseket tartalmazza:

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

### <a name="check-a-specific-role-assignment"></a>Adott szerepkör-hozzárendelés keresése

Egy adott szerepkör-hozzárendelés vizsgálatához hozzon végre egy hitelesített HTTP GET kérelmet a következőre:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Paraméter értéke** | **Kötelező** |  **Típus** |  **Leírás** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Karakterlánc |   A UserId-objectIdType objectId. |
| YOUR_PATH | True | Sztring |   A kiválasztott elérési út a hozzáférés-ellenőrzési útvonalhoz. |
| YOUR_ACCESS_TYPE |  True | Sztring |   A kereséshez használandó hozzáférési típus. |
| YOUR_RESOURCE_TYPE | True | Sztring |  Az ellenőrzött erőforrás. |

Egy sikeres kérelem egy logikai értéket `true` `false` ad vissza, amely jelzi, hogy a hozzáférési típus hozzá van-e rendelve a felhasználóhoz a megadott elérési úthoz és erőforráshoz.

### <a name="get-role-assignments-by-path"></a>Szerepkör-hozzárendelések beolvasása elérési út alapján

Egy elérési útra vonatkozó összes szerepkör-hozzárendelés beszerzéséhez hozzon végre egy hitelesített HTTP GET kérelmet a következőre:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Value | Csere erre |
| --- | --- |
| YOUR_PATH | A terület teljes elérési útja |

Egy sikeres kérelem egy JSON-tömböt ad vissza, amely a kiválasztott **path** paraméterhez társított összes szerepkör-hozzárendelést megadja:

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

A címzettek engedélyeinek visszavonásához törölje a szerepkör-hozzárendelést egy hitelesített HTTP-TÖRLÉSi kérelem létrehozásával:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | Az eltávolítandó szerepkör-hozzárendelés **azonosítója** |

A sikeres TÖRLÉSi kérelem egy 204-es válasz állapotot ad vissza. Ellenőrizze a szerepkör-hozzárendelés eltávolítását, ha [ellenőrzi](#check) , hogy a szerepkör-hozzárendelés továbbra is fennáll-e.

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

Szerepkör-hozzárendelés létrehozásához hozzon létre egy hitelesített HTTP POST-kérelmet az URL-címre:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Győződjön meg arról, hogy a JSON-törzs megfelel a következő sémának:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Egy sikeres kérelem a 201-es válasz állapotát és az újonnan létrehozott szerepkör-hozzárendelés azonosítóját fogja visszaadni:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Példák konfigurációra

Az alábbi példák bemutatják, hogyan konfigurálhatja a JSON-törzset számos gyakran észlelt szerepkör-hozzárendelési forgatókönyvben.

* **Példa**: A felhasználónak rendszergazdai hozzáféréssel kell rendelkeznie a bérlői terület emeletéhez.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Példa**: Az alkalmazások tesztelési forgatókönyveket futtatnak az eszközök és érzékelők modellezéséhez.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Példa**: A tartomány részét képező összes felhasználó olvasási hozzáférést kap a tárhelyek, érzékelők és felhasználók számára. Ez a hozzáférés magában foglalja a hozzá tartozó kapcsolódó objektumokat is.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>További lépések

- Az Azure digitális Twins szerepköralapú hozzáférés-vezérlésének áttekintéséhez olvassa el a [szerepköralapú hozzáférés-vezérlés](./security-authenticating-apis.md)című részt.

- Az Azure Digital Twins API-hitelesítéssel kapcsolatos információkért olvassa el az [API-hitelesítés](./security-authenticating-apis.md)című témakört.

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
