---
title: Címtárszerepkörök hozzárendelése felhasználókhoz – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a rendszergazdai és nem rendszergazdai szerepkörök hozzárendeléséről az Azure Active Directoryval rendelkező felhasználókhoz.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422893"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Rendszergazdai és nem rendszergazdai szerepkörök hozzárendelése a felhasználókhoz az Azure Active Directoryval
Ha a szervezet egyik felhasználójának engedélyre van szüksége az Azure Active Directory (Azure AD) erőforrások kezeléséhez, hozzá kell rendelnie a felhasználónak egy megfelelő szerepkört az Azure AD-ben, a felhasználó által a szükséges engedélyek végrehajtásához szükséges műveletek alapján.

A rendelkezésre álló szerepkörökről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](../users-groups-roles/directory-assign-admin-roles.md) A felhasználók hozzáadásáról további információt az Új felhasználók hozzáadása az Azure Active Directoryhoz című [témakörben talál.](add-users-azure-active-directory.md)

## <a name="assign-roles"></a>Szerepkörök hozzárendelése
Az Azure AD-szerepkörök felhasználóhoz rendelésének gyakori módja a **címtárszerepkör-lapon** található a felhasználó számára.

Szerepköröket is hozzárendelhet a kiemelt identitáskezelés (PIM) használatával. A PIM használatával kapcsolatos további információkért lásd: [Kiemelt identitáskezelés.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)

### <a name="to-assign-a-role-to-a-user"></a>Szerepkör hozzárendelése felhasználóhoz
1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/) és jelentkezzen be a címtár globális rendszergazdai fiókjával. 

2. Keresse meg és válassza ki az **Azure Active Directoryt**.

      ![Az Azure Portal keresés az Azure Active Directoryban](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Válassza a **Felhasználók** lehetőséget.

4. Keresse meg és jelölje ki a szerepkör-hozzárendelést kapó felhasználót. _Például, Alain Charon_.

      ![Minden felhasználó lap – válassza ki a felhasználót](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Az **Alain Charon - Profil** lapon válassza a **Hozzárendelt szerepkörök**lehetőséget.

    Megjelenik **az Alain Charon - Directory szerepkör** lap.

6. Válassza **a Hozzárendelés hozzáadása**lehetőséget, jelölje ki az Alainhoz hozzárendelni kívánt szerepkört (például az Alkalmazás _rendszergazdáját),_ majd válassza **a Kijelölés lehetőséget.**

    ![Hozzárendelt szerepkörök lapja – a kijelölt szerepkör megjelenítése](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Az alkalmazásrendszergazdai szerepkör Alain Charonhoz van rendelve, és megjelenik az **Alain Charon – Directory szerepkör** lapon.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása
Ha el kell távolítania a szerepkör-hozzárendelést egy felhasználótól, ezt az **Alain Charon - Directory szerepkör** lapról is megteheti.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Szerepkör-hozzárendelés eltávolítása felhasználótól

1. Válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd keresse meg és jelölje ki a szerepkör-hozzárendelést eltávolító felhasználót. _Például, Alain Charon_.

2. Jelölje be **a Hozzárendelt szerepkörök**, majd **az Alkalmazásrendszergazda**, majd **a Hozzárendelés eltávolítása**lehetőséget.

    ![Hozzárendelt szerepkörök lap, a kijelölt szerepkör és az eltávolítási lehetőség megjelenítése](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Az alkalmazásrendszergazdai szerepkör törlődik az Alain Charon ból, és már nem jelenik meg az **Alain Charon – Könyvtár szerepkör** lapon.

## <a name="next-steps"></a>További lépések
- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Profiladatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md)

Más felhasználókezelési feladatokat is végrehajthat, például meghatalmazottakat, házirendek használatával és felhasználói fiókok megosztásával. Az egyéb elérhető műveletekről az [Azure Active Directory felhasználói felügyeleti dokumentációjában](../users-groups-roles/index.yml)olvashat bővebben.


