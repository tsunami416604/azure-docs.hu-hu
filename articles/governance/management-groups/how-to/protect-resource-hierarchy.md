---
title: Az erőforrás-hierarchia elleni védelem – Azure-szabályozás
description: Ismerje meg, hogyan védhető az erőforrás-hierarchia olyan hierarchia-beállításokkal, amelyek tartalmazzák az alapértelmezett felügyeleti csoport beállítását.
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: 60c184d176ae62c1af525db656c56a83422cb94a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83837415"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Az erőforrás-hierarchia elleni védelem

Az erőforrások, erőforráscsoportok, előfizetések, felügyeleti csoportok és bérlők együttesen alkotják az erőforrás-hierarchiát. A gyökérszintű felügyeleti csoport beállításai, például az egyéni RBAC szerepkörei vagy a Azure Policy házirend-hozzárendelések hatással lehetnek az erőforrás-hierarchiában található minden egyes erőforrásra. Fontos, hogy megvédje az erőforrás-hierarchiát olyan változásokkal szemben, amelyek negatív hatással lehetnek az összes erőforrásra.

A felügyeleti csoportok már rendelkeznek hierarchia-beállításokkal, amelyek segítségével a bérlői rendszergazda vezérelheti ezeket a viselkedéseket. Ez a cikk ismerteti az összes rendelkezésre álló hierarchia-beállítást és azok beállításának módját.

## <a name="rbac-permissions-for-hierarchy-settings"></a>RBAC engedélyek a hierarchia beállításaihoz

A hierarchia beállításainak konfigurálásához a következő két RBAC művelet szükséges a legfelső szintű felügyeleti csoportban:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Ezek a műveletek csak a hierarchia beállításainak olvasását és frissítését teszik lehetővé a felhasználóknak. A műveletek nem biztosítanak semmilyen más hozzáférést a felügyeleti csoport hierarchiájának vagy erőforrásainak a hierarchiában. Mindkét művelet a beépített RBAC szerepkör- **hierarchia beállítások rendszergazdájánál**érhető el.

## <a name="setting---default-management-group"></a>Alapértelmezett felügyeleti csoport beállítása

Alapértelmezés szerint a rendszer a bérlőn belül hozzáadott új előfizetést adja hozzá a gyökérszintű felügyeleti csoport tagjaként. Ha a szabályzat-hozzárendelések, a szerepköralapú hozzáférés-vezérlés (RBAC) és más irányítási szerkezetek hozzá vannak rendelve a legfelső szintű felügyeleti csoporthoz, azonnal érvénybe lépnek az új előfizetések. Emiatt számos szervezet nem alkalmazza ezeket a szerkezeteket a legfelső szintű felügyeleti csoportba, még akkor is, ha ez a kívánt hely a hozzárendeléshez. Más esetekben az új előfizetések esetében szigorúbban korlátozó vezérlőkre van szükség, de az összes előfizetéshez nem rendelhető hozzá. Ez a beállítás mindkét használati esetet támogatja.

Azáltal, hogy lehetővé teszi az új előfizetések alapértelmezett felügyeleti csoportjának meghatározását, a szervezeti szintű irányítási szerkezetek alkalmazhatók a legfelső szintű felügyeleti csoportra, és egy külön felügyeleti csoport, amelynek a szabályzat-hozzárendelései vagy az új előfizetéshez jobban illeszkedő RBAC-hozzárendelések is meghatározhatók.

A beállítás konfigurálásához a [hierarchia beállításait](/rest/api/resources/hierarchysettings) REST API végpontot nevezzük. Ehhez használja a következő REST API URI és szövegtörzs formátumot. Cserélje le a `{rootMgID}` értéket a gyökér felügyeleti csoportjának azonosítójával, és a `{defaultGroupID}` felügyeleti csoport azonosítójának használatával az alapértelmezett felügyeleti csoport lesz:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Kérelem törzse

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Ha az alapértelmezett felügyeleti csoportot vissza szeretné állítani a gyökérszintű felügyeleti csoportra, használja ugyanazt a végpontot, és állítsa a **defaultManagementGroup** értékre `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Beállítás – engedélyezés szükséges

Alapértelmezés szerint bármely felhasználó új felügyeleti csoportokat hozhat létre a bérlőn belül. A bérlők rendszergazdái dönthetnek úgy, hogy csak bizonyos felhasználók számára kívánják biztosítani a következetesség és a megfelelőség fenntartását a felügyeleti csoport hierarchiájában. Ha engedélyezve van, a felhasználónak a `Microsoft.Management/managementGroups/write` felügyeleti csoporton kell megadnia a műveletet az új alárendelt felügyeleti csoportok létrehozásához.

A beállítás konfigurálásához a [hierarchia beállításait](/rest/api/resources/hierarchysettings) REST API végpontot nevezzük. Ehhez használja a következő REST API URI és szövegtörzs formátumot. Ez az érték egy _logikai_érték, ezért **igaz** vagy **hamis** értéket adjon meg az értékhez. A **true** értékkel engedélyezheti a felügyeleti csoport hierarchiájának védelmét:

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Kérelem törzse

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Ha vissza szeretné kapcsolni a beállítást, használja ugyanazt a végpontot, és állítsa a **requireAuthorizationForGroupCreation** értéket **hamis**értékre.

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](../create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](../manage.md)
