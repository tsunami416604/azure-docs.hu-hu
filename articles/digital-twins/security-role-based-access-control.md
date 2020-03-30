---
title: A szerepköralapú hozzáférés-vezérlés megismerése - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg a szerepköralapú hozzáférés-vezérlést és az engedélyek kezelését az Azure Digital Twins-ben.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044934"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Szerepköralapú hozzáférés-vezérlés az Azure Digital Twins-ben

Az Azure Digital Twins lehetővé teszi a térbeli grafikonon meghatározott adatok, erőforrások és műveletek pontos hozzáférés-szabályozását. Ezt részletes szerepkör- és engedélykezeléssel, az úgynevezett [szerepköralapú hozzáférés-vezérléssel](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) teszi meg. Az RBAC _szerepkörökből_ és _szerepkör-hozzárendelésekből_áll. A szerepkörök azonosítják az engedélyek szintjét. A szerepkör-hozzárendelések szerepkört társítanak egy felhasználóhoz vagy eszközhöz.

Az RBAC használatával a következő engedélyek adhatók:

- Egy felhasználó.
- Egy szerkezet.
- Egy szolgáltatásvezető.
- Felhasználó által definiált függvény.
- Minden olyan felhasználó, aki egy tartományhoz tartozik.
- Egy bérlő.

A hozzáférés mértéke is finomhangolható.

Az RBAC abban a tekintetben egyedülálló, hogy az engedélyek a térbeli grafikonon öröklődnek.

## <a name="what-can-i-do-with-rbac"></a>Mire használhatom az RBAC-t?

A fejlesztők az RBAC segítségével:

- Lehetővé teszi a felhasználó számára, hogy egy teljes épület, vagy csak egy adott helyiség vagy emelet eszközeit kezelje.
- Rendszergazdai globális hozzáférés megadása az összes térbeli gráfcsomóponthoz egy teljes diagramhoz, vagy csak a diagram egy szakaszához.
- A hozzáférési kulcsok kivételével adjon meg egy támogatási szakembernek olvasási hozzáférést a grafikonhoz.
- A tartomány minden tagjának olvasási hozzáférést adhat az összes diagramobjektumhoz.

## <a name="rbac-best-practices"></a>RBAC gyakorlati tanácsok

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Szerepkörök

### <a name="role-definitions"></a>Szerepkör-definíciók

A szerepkör-definíció engedélyek és egyéb attribútumok gyűjteménye, amelyek szerepkört képeznek. A szerepkör-definíció felsorolja az engedélyezett műveleteket, beleértve a *CREATE,* *READ,* *UPDATE*és *DELETE* műveleteket, amelyeket az adott szerepkörrel rendelkező objektumok végrehajthatnak. Azt is meghatározza, hogy mely objektumtípusokra vonatkoznak az engedélyek.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Az előző szerepkörök teljes definícióinak lekéréséhez kérdezze le a rendszer/szerepkör API-t.
> További információ a [Szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md#retrieve-all-roles)című elírásban.

### <a name="object-identifier-types"></a>Objektumazonosító-típusok

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Megtudhatja, hogy miként adhat engedélyeket az egyszerű szolgáltatásnak a [Szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)című elolvasásával.

A következő dokumentációs cikkek a következőket ismertetik:

- A [felhasználó lekérdezése vagy objektumazonosítója](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Az [egyszerű szolgáltatás objektumazonosítójának beszerzése](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- Az [Azure AD-bérlő objektumazonosítójának lekérése.](../active-directory/develop/quickstart-create-new-tenant.md)

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Az Azure Digital Twins szerepkör-hozzárendelés társít egy objektumot, például egy felhasználó vagy egy Azure AD-bérlő, egy szerepkör és egy szóköz. Az engedélyek az adott területhez tartozó összes objektumhoz engedélyeket kapnak. A tér tartalmazza az alatta lévő teljes térbeli grafikont.

Például egy felhasználó kap egy szerepkör-hozzárendelés a szerepkör `DeviceInstaller` a térbeli gráf gyökércsomópontja, amely egy épületet jelöl. A felhasználó ezután elolvashatja és frissítheti az adott csomópont és az épület összes többi gyermekterének eszközeit.

Ha engedélyeket szeretne adni egy címzettnek, hozzon létre egy szerepkör-hozzárendelést. Az engedélyek visszavonásához távolítsa el a szerepkör-hozzárendelést.

>[!IMPORTANT]
> A szerepkör-hozzárendelések létrehozásáról és kezeléséről a [Szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című olvasnivalócímű cikkben olvashat bővebben.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni az Azure Digital Twins szerepkör-hozzárendelések létrehozásáról és kezeléséről, olvassa el [a Szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című olvasni.

- További információ [az Azure-hoz való RBAC-ról.](https://docs.microsoft.com/azure/role-based-access-control/)
