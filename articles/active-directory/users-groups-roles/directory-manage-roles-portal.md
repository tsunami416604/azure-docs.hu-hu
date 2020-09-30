---
title: Rendszergazdai szerepkör engedélyeinek megtekintése és társítása – Azure AD | Microsoft Docs
description: Most már megtekintheti és kezelheti az Azure AD rendszergazdai szerepkör tagjait a portálon. Azok számára, akik gyakran kezelik a szerepkör-hozzárendeléseket.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e548e99cc60d67b477fd087b993764bf7f223592
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541186"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Rendszergazdai szerepkörök megtekintése és kiosztása az Azure Active Directoryban

Most már megtekintheti és kezelheti a rendszergazdai szerepkörök összes tagját a Azure Active Directory-portálon. Ha gyakran felügyeli a szerepkör-hozzárendeléseket, valószínűleg ezt a folyamatot fogja előnyben részesíteni. És ha már megértette, hogy "mi a fene do ezek a szerepkörök?", az egyes Azure AD-rendszergazdai szerepkörök engedélyeinek részletes listáját láthatja.

## <a name="view-all-roles"></a>Az összes szerepkör megtekintése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **Azure Active Directory**lehetőséget.

1. Válassza a **szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listájának megtekintéséhez.

1. Válassza ki az egyes sorok jobb oldalán lévő három pontot a szerepkör engedélyeinek megtekintéséhez. Válasszon egy szerepkört a szerepkörhöz hozzárendelt felhasználók megtekintéséhez. Ha az alábbi ábrán nem látható, olvassa el a [Kiemelt szerepkörökhöz tartozó hozzárendelések megtekintése](#view-assignments-for-privileged-roles) című témakört annak ellenőrzéséhez, hogy PRIVILEGED Identity Management (PIM) van-e.

    ![szerepkörök listája az Azure AD-portálon](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Saját szerepkörök megtekintése

Egyszerűen megtekintheti saját engedélyeit is. Válassza ki a **szerepkört** a **szerepkörök és rendszergazdák** lapon, hogy megtekintse a jelenleg Önhöz rendelt szerepköröket.

## <a name="view-assignments-for-privileged-roles"></a>Kiemelt szerepkörök hozzárendeléseinek megtekintése

A felügyelet **a PIM-ben** lehetőségre kattintva további felügyeleti lehetőségeket biztosíthat. A Kiemelt szerepkörű rendszergazdák az "állandó" (a szerepkörben mindig aktív) hozzárendeléseket "jogosult" értékre módosíthatják (csak akkor, ha emelt szintű). Ha nem rendelkezik Privileged Identity Managementval, akkor továbbra is kiválaszthatja a **felügyelet a PIM-ben** lehetőséget a próbaverzióra való feliratkozáshoz. A Privileged Identity Management [prémium szintű Azure ad P2-licencet](../privileged-identity-management/subscription-requirements.md)igényel.

![rendszergazdai szerepkör tagjainak listája](./media/directory-manage-roles-portal/member-list.png)

Ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, egyszerűen hozzáadhat vagy eltávolíthat tagokat, szűrheti a listát, vagy kijelölhet egy tagot az aktív hozzárendelt szerepkörök megjelenítéséhez.

> [!Note]
> Ha rendelkezik egy prémium szintű Azure AD-licenccel, és már használja a Privileged Identity Management-t, az összes szerepkör-felügyeleti feladat elvégzése a Privilege Identity Management szolgáltatásban történik, és nem az Azure AD-ben.
>
> ![A PIM-ben felügyelt Azure AD-szerepkörök olyan felhasználók számára, akik már használják a PIM-t, és prémium P2 licenccel rendelkeznek](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Felhasználó szerepkör-engedélyeinek megtekintése

A szerepkör tagjainak megtekintésekor válassza a **Leírás** lehetőséget a szerepkör-hozzárendelés által biztosított engedélyek teljes listájának megtekintéséhez. A lap a kapcsolódó dokumentációra mutató hivatkozásokat tartalmaz, amelyek segítséget nyújtanak a címtár szerepköreinek kezelésében.

![Képernyőkép, amely a "globális rendszergazda – Leírás" lapot jeleníti meg.](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Szerepkör-hozzárendelések letöltése

Egy adott szerepkör összes hozzárendelésének letöltéséhez a **szerepkörök és rendszergazdák** lapon válasszon ki egy szerepkört, majd válassza a **szerepkör-hozzárendelések letöltése**lehetőséget. Egy CSV-fájl, amely felsorolja a szerepkör összes hatókörében lévő hozzárendeléseket.

![szerepkör összes hozzárendelésének letöltése](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Szerepkör kiosztása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) globális rendszergazda vagy Kiemelt szerepkörű rendszergazdai jogosultságokkal, és válassza a **Azure Active Directory**lehetőséget.

1. Válassza a **szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listájának megtekintéséhez.

1. Válasszon ki egy szerepkört a hozzárendelések megtekintéséhez.

    ![Képernyőfelvétel: a "felhasználói rendszergazda – hozzárendelések" lap, amelyen a "Manage in PIM" művelet van kiválasztva.](./media/directory-manage-roles-portal/member-list.png)

1. Válassza a **hozzárendelések hozzáadása** lehetőséget, és válassza ki a hozzárendelni kívánt szerepköröket. A felügyelet **a PIM-ben** lehetőségre kattintva további felügyeleti lehetőségeket biztosíthat. Ha az alábbi ábrán nem látható, olvassa el a [Kiemelt szerepkörök hozzárendelésének megtekintése](#view-assignments-for-privileged-roles) című témakört annak ellenőrzéséhez, hogy a PIM-ban van-e.

    ![rendszergazdai szerepkör engedélyeinek listája](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
