---
author: baanders
description: fájl belefoglalása a lépések áttekintéséhez és az engedélyek előfeltételeinek megadása az Azure digitális Twins telepítésekor
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: b87d45a8be68a77df7b06ebd6e80562ccbe0e444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009652"
---
>[!NOTE]
>Ezeknek a műveleteknek a célja, hogy egy felhasználó elvégezze az Azure-előfizetéshez tartozó erőforrások és felhasználói hozzáférés felügyeletét. Bár egyes lépések alacsonyabb engedélyekkel is végezhetők, az ezekkel az engedélyekkel rendelkező személyekkel való együttműködéshez teljes mértékben be kell állítania egy használható példányt. Tekintse meg a további információkat az [*Előfeltételek: szükséges engedélyek*](#prerequisites-permission-requirements) szakaszban alább.

Az új Azure Digital Twins-példány teljes beállítása három részből áll:
1. **A példány létrehozása**
2. **Felhasználói hozzáférési engedélyek beállítása**: az Azure-felhasználóknak az Azure Digital Twins *tulajdonos (előzetes verzió)* szerepkörrel kell rendelkezniük az Azure digitális Twins-példányon, hogy kezelni tudják az informatikai és az adatkezelési jogosultságokat. Ebben a lépésben az Azure-előfizetéshez tartozó tulajdonos/rendszergazda rendeli hozzá ezt a szerepkört az Azure Digital Twins-példányt kezelő személyhez. Ez lehet önmaga vagy valaki más a szervezetben.
3. **Ügyfélalkalmazások hozzáférési engedélyeinek beállítása**: gyakori, hogy ír egy ügyfélalkalmazás, amely a példányával való interakcióra lesz felhasználva. Ahhoz, hogy az ügyfélalkalmazás hozzáférhessen az Azure Digital Twins-hoz, be kell állítania egy alkalmazás- *regisztrációt* [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , hogy az ügyfélalkalmazás a példány hitelesítésére használja majd.

A folytatáshoz szüksége lesz egy Azure-előfizetésre. [Itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)megadhat egyet ingyenesen.

## <a name="prerequisites-permission-requirements"></a>Előfeltételek: engedélyezési követelmények

A cikkben szereplő összes lépés végrehajtásához rendelkeznie kell egy olyan [szerepkörrel az előfizetésben](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) , amely a következő engedélyekkel rendelkezik:
* Azure-erőforrások létrehozása és kezelése
* Az Azure-erőforrásokhoz való felhasználói hozzáférés kezelése (beleértve az engedélyek megadását és delegálását)

A követelménynek megfelelő általános szerepkörök a *tulajdonos*, a *fiók rendszergazdája*vagy a *felhasználói hozzáférés rendszergazdája* és a *közreműködő*kombinációja. A szerepkörök és engedélyek teljes körű ismertetését, beleértve a más szerepkörökhöz tartozó engedélyeket, a [*klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és az Azure ad-szerepköröket*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) az Azure RBAC dokumentációjában találhatja meg.

Ha szeretné megtekinteni a szerepkörét az előfizetésében, látogasson el a Azure Portal [előfizetések oldalára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) (ezt a hivatkozást használhatja, vagy keresse meg az *előfizetéseket* a portál keresősáv használatával). Keresse meg az Ön által használt előfizetés nevét, és tekintse meg a szerepkörét a *saját szerepkör* oszlopban:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="A Azure Portal előfizetések lapja, amely tulajdonosként jeleníti meg a felhasználót" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Ha úgy találja, hogy az érték *közreműködő*, vagy egy másik olyan szerepkör, amely nem rendelkezik a fentiekben leírt engedélyekkel, felveheti a kapcsolatot a felhasználóval az előfizetésében *, amely rendelkezik* ezekkel az engedélyekkel (például egy előfizetés tulajdonosával vagy a fiók rendszergazdájával), és az alábbi módokon végezheti el a műveletet:
* Kérje meg, hogy végezze el a jelen cikkben szereplő lépéseket az Ön nevében
* Kérje meg, hogy emelje a szerepkörét az előfizetésben, így Ön is jogosult lesz a folytatásra. Attól függően, hogy ez megfelelő-e, a szervezettől és a benne lévő szerepkörtől függ.