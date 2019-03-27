---
title: Az Azure digitális Twins szerepköralapú hozzáférés-vezérlés megismerése |} A Microsoft Docs
description: Ismerje meg a hitelesítés digitális Twins a szerepköralapú hozzáférés-vezérléssel.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyrana
ms.openlocfilehash: 2d437f3fd96c38bebac52d6bb9dacf2c01b20a3c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443690"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Szerepköralapú hozzáférés-vezérlés az Azure digitális Twins

Az Azure digitális Twins lehetővé teszi, hogy pontos hozzáférés-vezérlést az adott adatok, erőforrások és a térbeli grafikon a műveletek. Így részletes szerepkör és a szerepköralapú hozzáférés-vezérlés (RBAC) nevű engedélykezelés keresztül hajtja végre. Az RBAC áll _szerepkörök_ és _szerepkör-hozzárendelések_. Szerepkörök a jogosultsági szinteket, azonosíthatja. Szerepkör-hozzárendelések szerepkör társítása egy felhasználóhoz vagy eszközhöz.

RBAC használata esetén is engedélyt kapni:

- Egy felhasználó.
- Egy eszköz.
- Egy egyszerű szolgáltatást.
- Felhasználó által definiált függvény.
- A tartományhoz tartozó összes felhasználó.
- Egy bérlő.

A hozzáférés mértékét is finomhangolható.

Az RBAC sajátossága, hogy a térbeli graph le az engedélyek öröklődnek.

## <a name="what-can-i-do-with-rbac"></a>Mire használhatom az RBAC-t?

A fejlesztő rbac-t használhatja:

- Adja meg a felhasználó képes kezelni az eszközöket egy teljes épület, vagy csak egy adott szoba vagy emelet.
- Egy rendszergazdai globális hozzáférés az összes térbeli gráf csomópontjai egy teljes grafikon, vagy csak a gráf szakasz megadása.
- Adjon meg egy támogatási szakértő olvasási hozzáférést a grafikon, kivéve a tárelérési kulcsok.
- Adja meg egy tartományi olvasási hozzáférés a gráf összes objektum minden tagja.

## <a name="rbac-best-practices"></a>Az RBAC-ajánlott eljárások

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Szerepkörök

### <a name="role-definitions"></a>Szerepkör-definíciók

Egy szerepkör-definíció olyan engedélyeket és a egy szerepkört alkotó más attribútumok gyűjteménye. Egy szerepkör-definíció tartalmazza az engedélyezett műveleteket, többek között *létrehozás*, *OLVASÁSI*, *frissítés*, és *törlése* , hogy minden objektum az adott szerepkör végezhet. Azt is megadja, amelyhez objektum típusok engedélyek vonatkoznak.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> A teljes definíciók az előző szerepkörök lekéréséhez a/szerepkörök API lekérdezése.
> További információért olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md#all).

### <a name="object-identifier-types"></a>Objektumtípus azonosítója

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> Ismerje meg, hogyan kell engedélyeket megadni az egyszerű szolgáltatást, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md#grant).

A következő referencia-dokumentációval kapcsolatos cikkek ismertetik:

- Hogyan [lekérdezés vagy egy felhasználó Objektumazonosítójának](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- Hogyan [Objektumazonosítójának beszerzése egy egyszerű szolgáltatást](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal).
- Hogyan [az Azure AD-bérlő Objektumazonosítójának lekéréséhez](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Egy digitális Twins Azure szerepkör-hozzárendelés egy objektumot, például egy felhasználó vagy az Azure AD-bérlővel, hozzárendeli egy szerepkör és a egy szóközt. Engedélyek valamennyi olyan objektumhoz tartozó, hogy a hely. A terület a teljes térbeli graph alatta tartalmazza.

Például egy felhasználó van megadva a szerepkör egy szerepkör-hozzárendelés `DeviceInstaller` a legfelső szintű csomópontja által térbeli grafikon, amely egy épület jelöli. A felhasználó ezután olvasása és frissítése az adott csomópont és a az épület más gyermek szóközöket.

Egy címzettnek engedélyeket, szerepkör-hozzárendelés létrehozása. Visszavonni az engedélyeket, a szerepkör-hozzárendelés eltávolítása.

>[!IMPORTANT]
> Tudjon meg többet a szerepkör-hozzárendelések olvasásával [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).

## <a name="next-steps"></a>További lépések

- További információk létrehozása és kezelése az Azure digitális Twins szerepkör-hozzárendeléseket, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).
