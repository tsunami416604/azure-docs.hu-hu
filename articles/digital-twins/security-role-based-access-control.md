---
title: A szerepköralapú hozzáférés-vezérlés ismertetése – Azure digitális Twins | Microsoft Docs
description: Ismerje meg a szerepköralapú hozzáférés-vezérlést és az engedélyek kezelését az Azure digitális Twins szolgáltatásban.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: efa19cdd1dd0827fd0e88f533b94f69bd7b99ca9
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307265"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Szerepköralapú hozzáférés-vezérlés az Azure-beli digitális Ikrekben

Az Azure digitális Twins szolgáltatással pontos hozzáférés-vezérlést végezhet a térbeli gráf bizonyos adataihoz, erőforrásaihoz és műveleteihez. Ezt a részletes szerepkör és a [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) nevű engedélyek kezelése teszi lehetővé. A RBAC _szerepkörökből_ és _szerepkör-hozzárendelésből_áll. A szerepkörök határozzák meg az engedélyek szintjét. A szerepkör-hozzárendelések egy szerepkört társítanak egy felhasználóhoz vagy eszközhöz.

A RBAC használatával a következő engedélyek adhatók meg:

- Egy felhasználó.
- Egy eszköz.
- Egy egyszerű szolgáltatásnév.
- Felhasználó által definiált függvény.
- A tartományhoz tartozó összes felhasználó.
- Egy bérlő.

A hozzáférés foka is finomítható.

A RBAC egyedi, hogy az engedélyek öröklik a térbeli gráfot.

## <a name="what-can-i-do-with-rbac"></a>Mire használhatom az RBAC-t?

A fejlesztők a következő RBAC használhatják:

- Lehetővé teszi a felhasználónak, hogy egy teljes épülethez vagy csak egy adott helyiséghez vagy emeleten kezelje az eszközöket.
- Globális hozzáférés biztosítása a teljes gráfhoz tartozó összes térbeli gráf-csomóponthoz, vagy csak a gráf egy szakaszához.
- A hozzáférési kulcsok kivételével olvasási hozzáférést biztosíthat a gráfhoz.
- A tartomány minden tagjának adjon olvasási hozzáférést az összes gráf objektumhoz.

## <a name="rbac-best-practices"></a>RBAC ajánlott eljárások

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Szerepkörök

### <a name="role-definitions"></a>Szerepkör-definíciók

A szerepkör-definíció az engedélyek és a szerepkört alkotó egyéb attribútumok gyűjteménye. A szerepkör-definíció felsorolja az engedélyezett műveleteket, beleértve az adott szerepkörrel rendelkező objektumok *létrehozását*, *olvasását*, *frissítését*és *törlését* is. Azt is meghatározza, hogy az engedélyek mely objektumtípusok esetében érvényesek.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Az előző szerepkörök teljes definíciójának lekéréséhez kérdezze le a System/roles API-t.
> További információ a [szerepkör-hozzárendelések létrehozásával és kezelésével](./security-create-manage-role-assignments.md#retrieve-all-roles)kapcsolatban.

### <a name="object-identifier-types"></a>Objektumazonosító típusai

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Megtudhatja, hogyan adhat engedélyeket a szolgáltatás számára a [szerepkör-hozzárendelések létrehozásával és kezelésével](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal).

Az alábbi dokumentációs cikkek a következőket írják le:

- [Lekérdezés vagy a felhasználó objektumazonosító-azonosítója](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- [Egy egyszerű szolgáltatásnév azonosítójának beszerzése](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- [Az Azure ad-bérlőhöz tartozó objektumazonosító beolvasása](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Egy Azure digitális Twins szerepkör-hozzárendelés társít egy objektumot, például egy felhasználót vagy egy Azure AD-bérlőt egy szerepkörrel és egy hellyel. A rendszer engedélyeket kap az adott területhez tartozó összes objektumra. A terület az alatta lévő teljes térbeli gráfot tartalmazza.

A felhasználó például egy olyan szerepkör-hozzárendelést kap, amely egy épületet jelképező térbeli gráf gyökérszintű csomópontjának `DeviceInstaller` szerepkörét adja meg. A felhasználó ezután elolvashatja és frissítheti az adott csomóponthoz tartozó eszközöket, valamint az épületben lévő összes többi gyermeket is.

Ha engedélyeket szeretne adni egy címzettnek, hozzon létre egy szerepkör-hozzárendelést. Az engedélyek visszavonásához távolítsa el a szerepkör-hozzárendelést.

>[!IMPORTANT]
> További információ a szerepkör-hozzárendelésekről a [szerepkör-hozzárendelések létrehozásával és kezelésével](./security-create-manage-role-assignments.md)kapcsolatban.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni az Azure digitális Twins szerepkör-hozzárendelések létrehozásáról és kezeléséről, olvassa el a [szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című témakört

- További információ [Az Azure](https://docs.microsoft.com/azure/role-based-access-control/)-beli RBAC.
