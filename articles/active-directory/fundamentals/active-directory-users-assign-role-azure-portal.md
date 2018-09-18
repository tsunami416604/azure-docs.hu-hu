---
title: Címtárbeli szerepkörök hozzárendelése az Azure Active Directory felhasználók |} A Microsoft Docs
description: Ismerje meg, hogyan címtárbeli szerepkörök hozzárendelése a felhasználókhoz az Azure Active Directoryval.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: b73df5ec0381e83c54c8cd9f8c0335448def0c6d
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733042"
---
# <a name="how-to-assign-roles-and-administrators-to-users-with-azure-active-directory"></a>Hogyan: szerepkörök és a Rendszergazdák hozzárendelése a felhasználókhoz az Azure Active Directoryval
Ha egy felhasználó a szervezet Azure Active Directory (Azure AD) erőforrásainak kezelésére szóló engedélyre van szüksége, hozzá kell rendelnie a felhasználó egy megfelelő szerepkört az Azure AD-ben a felhasználó beleegyezését kéri az hajtsa végre a műveleteket alapján.

Az elérhető szerepkörök kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Felhasználók hozzáadásával kapcsolatos további információkért lásd: [új felhasználók hozzáadása az Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Szerepkörök hozzárendelése
Egy gyakori módja az Azure AD-szerepköröket hozzárendelni egy felhasználóhoz a **címtárbeli szerepkör** oldal a felhasználó számára.

Privileged Identity Management (PIM) használatával szerepköröket is rendelhet. További részletes információ a PIM használatát tervezi, tekintse meg a [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Szerepkör hozzárendelése felhasználóhoz
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a címtár egy globális rendszergazdai fiók használatával.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, és keressen rá, és válassza ki a felhasználót a szerepkör-hozzárendelés beolvasása. Ha például _Alain Charon_.

3. Az a **Alain Charon - profil** lapon jelölje be **címtárbeli szerepkör**.

    A **Alain Charon - címtárszerepkör** lap jelenik meg.

4. Válassza ki **szerepkör hozzáadása**, válassza ki a szerepkört Alain hozzárendelése (például _alkalmazás-rendszergazda_), majd **kiválasztása**.

    ![Címtár szerepkörök lapot, amely a kijelölt szerepkör](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Az alkalmazás-rendszergazda szerepkör van rendelve a Alain Charon, és megjelenik a **Alain Charon - címtárszerepkör** lapot.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása
Ha a szerepkör-hozzárendelés eltávolítása egy felhasználótól van szüksége, műveletek végezhetők, amelyek a a **Alain Charon - címtárbeli szerepkör** lap.

### <a name="to-remove-a-role-assignment-from-a-user"></a>A felhasználó szerepkör-hozzárendelés eltávolítása

1. Válassza ki **Azure Active Directory**válassza **felhasználók**, és keressen rá, és válassza ki a felhasználót az eltávolított szerepkör-hozzárendelés beolvasása. Ha például _Alain Charon_.

2. Válassza ki **címtárbeli szerepkör**válassza **alkalmazás-rendszergazda**, majd válassza ki **Remove szerepkör**.

    ![Szerepkörök könyvtárlap, amelyen a kijelölt szerepkört és az Eltávolítás lehetőség](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Az alkalmazás-rendszergazda szerepkör Alain Charon törlődik, és már nem jelenik meg a **Alain Charon - címtárszerepkör** lapot.

## <a name="next-steps"></a>További lépések
- [Adja hozzá, vagy a felhasználók törlése](add-users-azure-active-directory.md)

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md)

Vagy más felhasználói felügyeleti feladatokat, például a Microsoftra hozzárendelése, házirendekkel és megosztása, felhasználói fiókok is végezhet. Egyéb elérhető műveletekkel kapcsolatos további információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).


