---
title: Rendszergazdai szerepkör-engedélyek megtekintése és hozzárendelése - Azure AD | Microsoft dokumentumok
description: Most már láthatja és kezelheti az Azure AD-rendszergazdai szerepkör tagjait a portálon. Azok számára, akik gyakran kezelik a szerepkör-hozzárendeléseket.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900903"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Rendszergazdai szerepkörök megtekintése és hozzárendelése az Azure Active Directoryban

Most már láthatja és kezelheti a rendszergazdai szerepkörök összes tagját az Azure Active Directory portálon. Ha gyakran kezeli a szerepkör-hozzárendeléseket, valószínűleg ezt az élményt részesíti előnyben. És ha valaha is kíváncsi:"Mi a fene csinál ezek a szerepkörök valóban?", láthatja az egyes Azure AD-rendszergazdai szerepkörök engedélyek részletes listáját.

## <a name="view-all-roles"></a>Az összes szerepkör megtekintése

Keresse meg és válassza ki az **Azure Active Directoryt**. Válassza a **Szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listájának megtekintéséhez.

Kattintson a három pontra az egyes sorok jobb oldalán a szerepkör részletes leírásának megnyitásához.

![Szerepkörök listája az Azure AD portálon](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Saját szerepkörök megtekintése

Könnyen megtekintheti a saját engedélyeit is. **Válassza ki a Szerepkör lehetőséget** a **Szerepkörök és rendszergazdák** lapon a jelenleg Önhöz rendelt szerepkörök megtekintéséhez.

## <a name="view-assignments-for-a-role"></a>Szerepkör hozzárendelésének megtekintése

Kattintson egy szerepkörre a szerepkörhöz rendelt felhasználók megtekintéséhez. A **PIM-ben** a Kezelés lehetőséget választhatja a további felügyeleti képességekhez. A kiemelt szerepszintű rendszergazdák módosíthatják az "Állandó" (mindig aktív a szerepkörben) hozzárendelések "Jogosult" (a szerepkör csak akkor, ha emelt szintű). Ha nem rendelkezik PIM-vel, továbbra is **kiválaszthatja a Kezelés a PIM-ben** lehetőséget a próbaverzióra való regisztrációhoz. A kiemelt identitáskezeléshez [Azure AD Premium P2 licenccsomag](../privileged-identity-management/subscription-requirements.md)szükséges.

![rendszergazdai szerepkör tagjainak listája](./media/directory-manage-roles-portal/member-list.png)

Ha Ön globális rendszergazda vagy kiemelt szerepkörrel rendelkező rendszergazda, egyszerűen hozzáadhat vagy eltávolíthat tagokat, szűrheti a listát, vagy kijelölhet egy tagot az aktív hozzárendelt szerepköreik megtekintéséhez.

## <a name="view-a-users-role-permissions"></a>Felhasználó szerepkör-engedélyeinek megtekintése

Amikor egy szerepkör tagjainak megtekintését tekinti meg, válassza a **Leírás** lehetőséget a szerepkör-hozzárendelés által megadott engedélyek teljes listájának megtekintéséhez. A lap hivatkozásokat tartalmaz a megfelelő dokumentációra, amely segít végigvezetni a címtárszerepkörök kezelésén.

![Rendszergazdai szerepkör engedélyeinek listája](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökről és a Rendszergazdai szerepkör-hozzárendelésről a [Rendszergazdai szerepkörök hozzárendelése című témakörben](directory-assign-admin-roles.md)van további témakör.
* Az alapértelmezett felhasználói engedélyeket [az alapértelmezett vendég- és tagjogosultságok összehasonlítása](../fundamentals/users-default-permissions.md)című témakörben olvashatja.
