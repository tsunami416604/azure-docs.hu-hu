---
title: 'Gyors útmutató: felügyeleti csoport létrehozása a portálon'
description: Ebben a rövid útmutatóban a Azure Portal használatával hozzon létre egy felügyeleti csoportot, amely erőforrás-hierarchiába rendezi az erőforrásokat.
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: c8cb7b8bd3ad33d1f315670bfbb1782972e6e97f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89661736"
---
# <a name="quickstart-create-a-management-group"></a>Gyors útmutató: felügyeleti csoport létrehozása

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

### <a name="create-in-portal"></a>Létrehozás a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás**  >  **kezelése + irányítás**lehetőséget.

1. Válassza a **Management groups**lehetőséget.

1. Válassza a **+ felügyeleti csoport hozzáadása**lehetőséget.

   :::image type="content" source="./media/main.png" alt-text="Képernyőfelvétel a felügyeleti csoportok lapról alárendelt felügyeleti csoportok és előfizetések." border="false":::

1. Hagyja meg az **új kijelölt létrehozása** elemet, és töltse ki a felügyeleti csoport azonosítója mezőt.

   - A **felügyeleti csoport azonosítója** a címtár egyedi azonosítója, amely a parancsok ezen a felügyeleti csoporton való elküldésére szolgál. Ez az azonosító nem szerkeszthető a létrehozás után, mivel az egész Azure-rendszeren a csoport azonosítására szolgál. A rendszer automatikusan létrehozza a [gyökérszintű felügyeleti csoportot](./overview.md#root-management-group-for-each-directory) a Azure Active Directory azonosítójaként megadott azonosítóval. Az összes többi felügyeleti csoport esetében rendeljen hozzá egy egyedi azonosítót.
   - A megjelenítendő név mező a Azure Portal belül megjelenő név. A felügyeleti csoport létrehozásakor egy külön megjelenítendő név nem választható mező, és bármikor módosítható.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Képernyőfelvétel a felügyeleti csoportok lapról alárendelt felügyeleti csoportok és előfizetések.":::

1. Válassza a **Mentés** lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott felügyeleti csoport eltávolításához kövesse az alábbi lépéseket:

1. Válassza **a minden szolgáltatás**  >  **kezelése + irányítás**lehetőséget.

1. Válassza a **Management groups**lehetőséget.

1. Keresse meg a fent létrehozott felügyeleti csoportot, jelölje ki, majd válassza a név melletti **részletek** lehetőséget.
   Ezután válassza a **Törlés** lehetőséget, és erősítse meg a kérdést.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)