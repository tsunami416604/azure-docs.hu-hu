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
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9bfc7f194730545469e7d17d46c47f7293f016e
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84728894"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Rendszergazdai szerepkörök megtekintése és kiosztása Azure Active Directory

Most már megtekintheti és kezelheti a rendszergazdai szerepkörök összes tagját a Azure Active Directory-portálon. Ha gyakran felügyeli a szerepkör-hozzárendeléseket, valószínűleg ezt a folyamatot fogja előnyben részesíteni. És ha már megértette, hogy "mi a fene do ezek a szerepkörök?", az egyes Azure AD-rendszergazdai szerepkörök engedélyeinek részletes listáját láthatja.

## <a name="view-all-roles"></a>Az összes szerepkör megtekintése

Keresse meg és válassza ki az **Azure Active Directoryt**. Válassza a **szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listájának megtekintéséhez.

Kattintson az egyes sorok jobb oldalán található három pontra a szerepkör részletes leírásának megnyitásához.

![szerepkörök listája az Azure AD-portálon](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Saját szerepkörök megtekintése

Egyszerűen megtekintheti saját engedélyeit is. Válassza ki a **szerepkört** a **szerepkörök és rendszergazdák** lapon, hogy megtekintse a jelenleg Önhöz rendelt szerepköröket.

## <a name="view-assignments-for-a-role"></a>Szerepkör hozzárendeléseinek megtekintése

Kattintson egy szerepkörre a szerepkörhöz hozzárendelt felhasználók megtekintéséhez. A felügyelet **a PIM-ben** lehetőségre kattintva további felügyeleti lehetőségeket biztosíthat. A Kiemelt szerepkörű rendszergazdák az "állandó" (a szerepkörben mindig aktív) hozzárendeléseket "jogosult" értékre módosíthatják (csak akkor, ha emelt szintű). Ha nem rendelkezik a PIM szolgáltatással, akkor továbbra is kiválaszthatja a **felügyelet a PIM-ben** lehetőséget a próbaverzióra való feliratkozáshoz. A Privileged Identity Management [prémium szintű Azure ad P2-licencet](../privileged-identity-management/subscription-requirements.md)igényel.

![rendszergazdai szerepkör tagjainak listája](./media/directory-manage-roles-portal/member-list.png)

Ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, egyszerűen hozzáadhat vagy eltávolíthat tagokat, szűrheti a listát, vagy kijelölhet egy tagot az aktív hozzárendelt szerepkörök megjelenítéséhez.

## <a name="view-a-users-role-permissions"></a>Felhasználó szerepkör-engedélyeinek megtekintése

A szerepkör tagjainak megtekintésekor válassza a **Leírás** lehetőséget a szerepkör-hozzárendelés által biztosított engedélyek teljes listájának megtekintéséhez. A lap a kapcsolódó dokumentációra mutató hivatkozásokat tartalmaz, amelyek segítséget nyújtanak a címtár szerepköreinek kezelésében.

![rendszergazdai szerepkör engedélyeinek listája](./media/directory-manage-roles-portal/role-description.png)

## <a name="next-steps"></a>További lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
