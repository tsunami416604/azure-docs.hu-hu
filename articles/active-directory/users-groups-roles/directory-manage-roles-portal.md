---
title: Tagjai rendszergazdai szerepkör és a szerepkör-engedélyek megtekintése az Azure Active Directoryban |} A Microsoft Docs
description: Ezután tekintse meg és kezelése a portálon az Azure AD rendszergazdai szerepkör tagjai. Azok számára, akik gyakran a szerepkör-hozzárendelések kezeléséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/10/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 5a42f48e85eea95211b36e0c08dcb0edb4928a20
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38489922"
---
# <a name="view-members-and-descriptions-of-administrator-roles-in-azure-active-directory"></a>Tagok megtekintése és rendszergazdai szerepkörök az Azure Active Directoryban leírása

Mostantól tekintse meg és kezelheti a rendszergazdai szerepkörök az Azure Active Directory portál összes tagját. Ha gyakran a szerepkör-hozzárendelések kezeléséhez, ez a tapasztalat valószínűleg inkább lesz. És ha Ön elgondolkozott "Mit a kontrola hajtsa végre ezeket a szerepköröket valóban?", az Azure AD-rendszergazdai szerepkörök jogosultságait részletes listáját láthatja.

A saját engedélyek, valamint hogy könnyebbé vált. Kattintson a **munkaköre** gyorsan elérheti a felhasználói oldal összes aktív hozzárendelt szerepkörök listáját. Kattintson a három pontot, jobb oldalán található minden egyes sor megnyitásához a szerepkör részletes leírását.

![az Azure AD-portálhoz szerepkörök listája](./media/directory-manage-roles-portal/role-list.png)

Válassza ki az egész sort hozzárendelt a tagok listájának megtekintéséhez. Választhat **a PIM kezelése** a további felügyeleti lehetőségeket. Kiemelt szerepkörű rendszergazdák módosíthatja a "Állandó" (a szerepkör mindig aktív)-hozzárendelések "Megfelelő" (az a szerepkör csak akkor, ha emelt szintű). Ha nem rendelkezik a PIM, továbbra is választhatja **a PIM kezelése** , Regisztráljon a próbaverzióra. Privileged Identity Management van szükség egy [Azure AD Premium P2-licenccsomag](../privileged-identity-management/subscription-requirements.md).

![egy rendszergazda szerepkör tagjainak listája](./media/directory-manage-roles-portal/member-list.png)

Ha Ön globális rendszergazda vagy egy kiemelt szerepkörű rendszergazda, egyszerűen adja hozzá vagy eltávolíthatja tagok, szűrje a listát, vagy jelöljön ki egy tagot, nyissa meg a felhasználói oldalon tekintheti meg az aktív szerepkörök rendelve. 

## <a name="detailed-role-permissions-in-the-portal"></a>Részletes szerepkör engedélyei a portálon

A szerepkör tagjainak megtekintése közben, válassza ki a **leírás** a szerepkör-hozzárendelés által megadott engedélyek teljes listájának megtekintéséhez. Az oldal segít címtárbeli szerepkörök kezelésére vonatkozó dokumentációt mutató hivatkozásokat tartalmaz.

![egy rendszergazdai szerepkörhöz tartozó engedélyek listája](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>További lépések

* Nyugodtan megoszthatja velünk a [az Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Szerepkörök és a rendszergazda szerepkör-hozzárendelés kapcsolatos további információkért lásd: [rendelhet hozzá rendszergazdai szerepköröket](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek, tekintse meg a [Vendég és tag alapértelmezett felhasználói engedélyek összehasonlítása](../fundamentals/users-default-permissions.md).
