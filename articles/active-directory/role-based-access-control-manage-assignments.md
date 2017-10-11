---
title: "Az Azure erőforrás-hozzáférési hozzárendelések megtekintése |} Microsoft Docs"
description: "Megtekintéséhez és kezeléséhez minden a szerepköralapú hozzáférés-vezérlés hozzárendelések bármely felhasználó vagy csoport az Azure-portálon"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: andredm
ms.openlocfilehash: 72c695d08bdf5de003d51ffb0768184e1e4d00ba
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>A felhasználók és csoportok az Azure portálon access-hozzárendelések megtekintése
> [!div class="op_single_selector"]
> * [Hozzáférés kezelése felhasználó vagy csoport alapján](role-based-access-control-manage-assignments.md)
> * [Hozzáférés kezelése erőforrás alapján](role-based-access-control-configure.md)

Szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure Active Directory (Azure AD) az Azure-erőforrások hozzáférés kezelhető. 

Rendelni, amelyben a Szerepalapú hozzáférés nem részletes, mert az engedélyeket korlátozhatja két módja van:

* **Hatókör:** RBAC szerepkör-hozzárendelések hatóköre egy adott előfizetés, a csoport vagy az erőforrás. A felhasználó által megadott hozzáférési egyetlen erőforrásra nem fér hozzá az összes erőforrását ugyanahhoz az előfizetéshez.
* **Szerepkör:** a hozzárendelés hatókörébe hozzáférés szűkíthető van akár további által szerepkör hozzárendelése. Szerepkörök magas szintű, például a tulajdonos vagy adott, például a virtuális gép olvasó lehet.

Szerepkörök csak rendelhetők hozzá a az előfizetés, az erőforráscsoport vagy a erőforrást, a hozzárendelés hatókörét. De megtekintheti egy adott felhasználó vagy csoport access-hozzárendelések egyetlen helyen. Akkor is, legfeljebb 2000 szerepkör-hozzárendelések minden előfizetésben. 

További információért arról, hogy hogyan [az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése a szerepkör-hozzárendelések segítségével](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Hozzáférés-hozzárendelések megtekintése
A hozzáférés-hozzárendeléseinek egyetlen felhasználó vagy csoport kereséséhez indítsa el az Azure Active Directoryban a [Azure-portálon](http://portal.azure.com).

1. Válassza ki **az Azure Active Directory**. Ha ezt a beállítást nem a navigációs listája látható, válassza ki a **több szolgáltatások** majd görgessen lefelé a Keresés **Azure Active Directory**.
2. Válassza ki **felhasználók és csoportok**, majd **minden felhasználó** vagy **összes csoport**. Ehhez a példához azt összpontosítani egyéni felhasználók számára is.
    ![Kezelheti a felhasználókat és csoportokat az Azure Active Directory – képernyőkép](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Keresse meg a felhasználói név vagy felhasználónév.
4. Válassza ki az **Azure-erőforrások** lehetőséget a felhasználói panelen. Ekkor a felhasználóhoz tartozó összes hozzáférési hozzárendelés megjelenik.

### <a name="read-permissions-to-view-assignments"></a>Olvasási engedélyek hozzárendelések megtekintése
Ezen az oldalon csak az, hogy rendelkezik olvasási engedélye hozzáférés hozzárendelése. Például A előfizetés olvasási hozzáféréssel rendelkezik, és nyissa meg az Azure-erőforrások paneljét, ellenőrizze a felhasználó-hozzárendeléseket. Az előfizetés A hozzáférési hozzárendeléseinek látható, de nem látja, hogy ő is van a hozzáférés-hozzárendelések az előfizetésben a b kiszolgálóra.

## <a name="delete-access-assignments"></a>Hozzáférés-hozzárendelések törlése
Ezen a panelen törölheti a közvetlenül hozzá egy felhasználóhoz vagy csoporthoz rendelt access-hozzárendelések. A hozzáférés hozzárendelése egy szülőcsoportot öröklődött, ha az erőforrás vagy az előfizetés, és kezelheti a van szüksége.

1. A listát az összes a hozzáférés egy felhasználó vagy csoport számára jelölje ki a megfelelőt.
2. Válassza ki **eltávolítása** , majd **Igen** megerősítéséhez.
    ![Távolítsa el a hozzáférés hozzárendelése – képernyőkép](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>Következő lépések

* Ismerkedés a szerepköralapú hozzáférés-vezérlést az [szerepkör-hozzárendelések segítségével az Azure-előfizetés erőforrásokhoz való hozzáférés kezelése](role-based-access-control-configure.md)
* Lásd: [Beépített RBAC-szerepkörök](role-based-access-built-in-roles.md)

