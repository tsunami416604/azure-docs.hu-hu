---
title: Szerepkör-hozzárendelések létrehozása és kezelése - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg a szerepkör-hozzárendelések létrehozását és kezelését az Azure Digital Twins-en belül.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110404"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Szerepkör-hozzárendelések létrehozása és kezelése az Azure Digital Twins-ben

Az Azure Digital Twins szerepköralapú hozzáférés-vezérlést[(RBAC)](./security-role-based-access-control.md)használ az erőforrásokhoz való hozzáférés kezeléséhez.

## <a name="role-assignments-overview"></a>Szerepkör-hozzárendelések – áttekintés

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

| Attribútum | Név | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| roleId | Szerepkör-definícióazonosító | Igen | Sztring | A kívánt szerepkör-hozzárendelés egyedi azonosítója. A szerepkör-definíciókat és azok azonosítóját a Rendszer API lekérdezésével vagy az alábbi táblázat áttekintésével keresheti meg. |
| objectId | Objektumazonosító | Igen | Sztring | Egy Azure Active Directory-azonosító, egyszerű szolgáltatásobjektum-azonosító vagy tartománynév. A szerepkör-hozzárendelés melyikhöz vagy kihez van rendelve. A szerepkör-hozzárendelést a társított típus szerint kell formázni. Az `DomainName` objectIdType esetében az objectId `“@”` azonosítónak a karakterrel kell kezdődnie. |
| objectIdType típus | Objektumazonosító típusa | Igen | Sztring | A használt objektumazonosító-azonosító fajtája. Lásd: Az alábbi **Supported ObjectIdTypes.** |
| path | Tér elérési útja | Igen | Sztring | Az `Space` objektum teljes elérési útja. Például: `/{Guid}/{Guid}`. Ha egy azonosítónak szüksége van a `"/"`teljes diagram szerepkör-hozzárendelésére, adja meg a mezőt. Ez a karakter jelöli a gyökeret, de használata nem ajánlott. Mindig kövesd a legkisebb kiváltság elvét. |
| tenantId | Bérlőazonosítója | Változó | Sztring | A legtöbb esetben egy Azure Active Directory-bérlői azonosító. Nem engedélyezett `DeviceId` és `TenantId` ObjectIdTypes. Az `UserId` ObjectIdTypes `ServicePrincipalId` típushoz szükséges. Nem kötelező a DomainName ObjectIdType típushoz. |

### <a name="supported-role-definition-identifiers"></a>Támogatott szerepkör-definíciós azonosítók

Minden szerepkör-hozzárendelés társít egy szerepkör-definíciót egy entitáshoz az Azure Digital Twins környezetben.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Támogatott objektumazonosító-típusok

Korábban az **objectIdType** attribútum lett bevezetésre.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Szerepkör-hozzárendelési műveletek

Az Azure Digital Twins támogatja a teljes *CREATE,* *READ*és *DELETE* műveletek szerepkör-hozzárendelések. *Az* UPDATE-műveleteket szerepkör-hozzárendelések hozzáadásával, szerepkör-hozzárendelések eltávolításával vagy a szerepkör-hozzárendelések által hozzáférést biztosító [Térintelligencia-gráf](./concepts-objectmodel-spatialgraph.md) csomópontok módosításával kezeli a rendszer.

[![Szerepkör-hozzárendelés iponttal](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

A mellékelt Swagger referenciadokumentáció további információkat tartalmaz az összes rendelkezésre álló API-végpontokról, a kérelemműveletekről és a definíciókról.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Engedélyek megadása az egyszerű szolgáltatásnak

Az egyszerű szolgáltatás engedélyek megadása gyakran az egyik első lépés, amelyet az Azure Digital Twins használatával való munka során fog tenni. Ez a következőket foglalja magában:

1. Bejelentkezés az Azure-példányba az [Azure CLI-n](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vagy a [PowerShellen](https://docs.microsoft.com/powershell/azure/)keresztül.
1. A szolgáltatásegyszerű adatok beolvasása.
1. A kívánt szerepkör hozzárendelése a szolgáltatásnévhez.

Az alkalmazásazonosítót az Azure Active Directoryban kapja meg. Ha többet szeretne megtudni az Azure Digital Twins konfigurálásáról és kiépítéséről az Active Directoryban, olvassa el a [rövid útmutatót.](./quickstart-view-occupancy-dotnet.md)

Miután rendelkezik az alkalmazásazonosítóval, hajtsa végre az alábbi parancsok egyikét. Az Azure CLI-ben:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

A Powershell ben:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

A **Rendszergazda** szerepkörrel rendelkező felhasználók ezután hozzárendelhetik a Térfelügyelő szerepkört egy felhasználóhoz, ha hitelesített HTTP POST-kérelmet küldaz URL-címre:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

A következő JSON-testtel:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Az összes szerepkör beolvasása

[![Rendszerszerepkörök](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Az összes elérhető szerepkör (szerepkör-definíciók) listázásához kérjen hitelesített HTTP GET-kérelmet:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

A sikeres kérelem egy JSON-tömböt ad vissza, amely minden hozzárendelhető szerepkörbejegyzéseit tartalmazza:

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

### <a name="check-a-specific-role-assignment"></a>Adott szerepkör-hozzárendelés ellenőrzése

Adott szerepkör-hozzárendelés ellenőrzéséhez kérjen hitelesített HTTP GET-kérelmet:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Paraméter értéke** | **Szükséges** |  **Típus** |  **Leírás** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True (Igaz) | Sztring |   A UserId objectIdType objectIdtípus objektumazonosítója. |
| YOUR_PATH | True (Igaz) | Sztring |   A kiválasztott elérési út a hozzáférés ellenőrzéséhez. |
| YOUR_ACCESS_TYPE |  True (Igaz) | Sztring |   *Olvasás*, *Létrehozás*, *Frissítés*vagy *Törlés* |
| YOUR_RESOURCE_TYPE | True (Igaz) | Sztring |  *Eszköz*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedProperty*, *ExtendedProperty*, *Endpoint*, *KeyStore*, *Matcher*, *Ontology*, *Report*, *RoleDefinition*, *Sensor*, *SensorExtendedProperty*, *Space,* *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, SpaceResource , *SpaceRoleAssignment*, *System*, * UerDefinedFunction*, *Felhasználó*, *UserBlobMetadata*vagy *UserExtendedProperty* |

A sikeres kérelem logikai `true` `false` értéket ad vissza, vagy jelzi, hogy a hozzáférési típus hozzá van-e rendelve a felhasználóhoz az adott elérési úthoz és erőforráshoz.

### <a name="get-role-assignments-by-path"></a>Szerepkör-hozzárendelések beszereznie elérési út szerint

Egy elérési út összes szerepkör-hozzárendelésének lekéréséhez kérjen hitelesített HTTP GET-kérelmet:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Érték | Csere erre |
| --- | --- |
| YOUR_PATH | A teljes utat a tér |

A sikeres kérelem egy JSON-tömböt ad vissza a kijelölt **elérési út** paraméterhez társított minden szerepkör-hozzárendeléssel:

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

Ha egy címzetttől szeretne visszavonni egy engedélyt, törölje a szerepkör-hozzárendelést hitelesített HTTP DELETE kérelem benyújtásával:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | Az eltávolítandó szerepkör-hozzárendelés **azonosítója** |

A sikeres DELETE kérelem 204-es válaszállapotot ad vissza. Ellenőrizze a szerepkör-hozzárendelés eltávolítását, [és ellenőrizze,](#check-a-specific-role-assignment) hogy a szerepkör-hozzárendelés továbbra is rendelkezik-e.

### <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

Szerepkör-hozzárendelés létrehozásához küldjön hitelesített HTTP POST-kérelmet az URL-címre:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Ellenőrizze, hogy a JSON-törzs megfelel-e a következő sémának:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

A sikeres kérelem 201 válaszállapotot ad vissza az újonnan létrehozott szerepkör-hozzárendelés **azonosítójával** együtt:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Konfigurációs példák

Az alábbi példák bemutatják, hogyan konfigurálhatja a JSON-törzset számos gyakran előforduló szerepkör-hozzárendelési forgatókönyvben.

* **Példa:** A felhasználónak rendszergazdai hozzáférésre van szüksége a bérlői terület padlójához.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Példa:** Egy alkalmazás futtatja az eszközöket és érzékelőket gúnyoló tesztforgatókönyveket.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Példa:** Minden olyan felhasználó, aki egy tartomány része, olvasási hozzáférést kap a szóközökhöz, érzékelőkhöz és felhasználókhoz. Ez a hozzáférés tartalmazza a hozzájuk tartozó objektumokat.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>További lépések

- Az Azure Digital Twins szerepköralapú hozzáférés-vezérlés áttekintéséhez olvassa el [a Szerepkör-alap hozzáférés-vezérlés című](./security-authenticating-apis.md)olvasni.

- Ha többet szeretne megtudni az Azure Digital Twins API-hitelesítésről, olvassa el [az API-hitelesítést.](./security-authenticating-apis.md)
