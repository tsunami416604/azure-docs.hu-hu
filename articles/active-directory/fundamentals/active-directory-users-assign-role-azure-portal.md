---
title: Azure AD-szerepkörök kiosztása a felhasználók számára – Azure Active Directory | Microsoft Docs
description: Útmutatás a rendszergazdai és nem rendszergazdai szerepkörök hozzárendeléséhez a Azure Active Directoryrel rendelkező felhasználók számára.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a468b5f43e8b024dd1da6e382049f8cce6de295
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797365"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Rendszergazdai és nem rendszergazdai szerepkörök kiosztása a felhasználóknak a Azure Active Directory

Azure Active Directory (Azure AD) esetén, ha az egyik felhasználónak engedélyre van szüksége az Azure AD-erőforrások kezeléséhez, hozzá kell rendelnie azokat egy olyan szerepkörhöz, amely a szükséges engedélyeket biztosítja. Az Azure-erőforrásokat és az Azure AD-erőforrásokat kezelő szerepköröket kezelő szerepkörökkel kapcsolatos információkért lásd: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).

További információ az elérhető Azure AD-szerepkörökről: [rendszergazdai szerepkörök Kiosztása Azure Active Directoryban](../users-groups-roles/directory-assign-admin-roles.md). A felhasználók hozzáadásával kapcsolatban lásd: [új felhasználók hozzáadása Azure Active Directoryhoz](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Szerepkörök hozzárendelése

Az Azure AD-szerepkörök felhasználóhoz való hozzárendelésének általános módja a **hozzárendelt szerepkörök** lap egy felhasználó számára. Azt is megteheti, hogy a jogosultságot a Privileged Identity Management (PIM) használatával az adott szerepkörhöz rendeli. További információ a PIM használatáról: [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

> [!Note]
> Ha rendelkezik egy prémium szintű Azure AD P2-licenccel, és már használja a PIM-t, az összes szerepkör-felügyeleti feladatot a rendszer a [Privileged Identity Management felhasználói felületén](../users-groups-roles/directory-manage-roles-portal.md)hajtja végre.
>
> ![A PIM-ben felügyelt Azure AD-szerepkörök olyan felhasználók számára, akik már használják a PIM-t, és prémium P2 licenccel rendelkeznek](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Szerepkör hozzárendelése felhasználóhoz

1. Lépjen a [Azure Portal](https://portal.azure.com/) , és jelentkezzen be egy globális rendszergazdai fiókkal a címtárhoz.

2. Keresse meg és válassza ki az **Azure Active Directoryt**.

      ![Azure Active Directory Azure Portal keresése](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Válassza a **Felhasználók** lehetőséget.

4. Keresse meg és válassza ki a szerepkör-hozzárendelést beolvasó felhasználót. Például _Alain Charon_.

      ![Minden felhasználó lap – válassza ki a felhasználót](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Az **Alain Charon-profil** lapon válassza a **hozzárendelt szerepkörök**elemet.

    Megjelenik az **Alain Charon-rendszergazdai szerepkörök** lap.

6. Válassza a **hozzárendelések hozzáadása**lehetőséget, válassza ki az Alain-hoz hozzárendelni kívánt szerepkört (például _alkalmazás-rendszergazda_), majd válassza a **kiválasztás**lehetőséget.

    ![Hozzárendelt szerepkörök lap – a kiválasztott szerepkör megjelenítése](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Az alkalmazás-rendszergazdai szerepkör hozzá van rendelve az Alain Charon, és megjelenik az **Alain Charon-felügyeleti szerepkörök** lapon.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Ha el kell távolítania a szerepkör-hozzárendelést egy felhasználótól, azt is megteheti az **Alain Charon-felügyeleti szerepkörök** lapon.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Szerepkör-hozzárendelés eltávolítása a felhasználótól

1. Válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**lehetőséget, majd keresse meg és válassza ki azt a felhasználót, aki a szerepkör-hozzárendelést eltávolítja. Például _Alain Charon_.

2. Válassza a **hozzárendelt szerepkörök**lehetőséget, válassza az **alkalmazás rendszergazdája**lehetőséget, majd válassza a **hozzárendelés eltávolítása**lehetőséget.

    ![Hozzárendelt szerepkörök lap, amely megjeleníti a kiválasztott szerepkört és az Eltávolítás lehetőséget.](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Az alkalmazás-rendszergazdai szerepkör el lett távolítva az Alain Charon-ből, és már nem jelenik meg az **Alain Charon-felügyeleti szerepkörök** lapon.

## <a name="next-steps"></a>További lépések

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md)

A kipróbálható egyéb felhasználói felügyeleti feladatok a [Azure Active Directory felhasználói felügyeleti dokumentációban](../users-groups-roles/index.yml)érhetők el.
