---
title: Megtekintheti, és rendelje hozzá a rendszergazda szerepkör engedélyeivel – Azure Active Directory |} A Microsoft Docs
description: Ezután tekintse meg és kezelése a portálon az Azure AD rendszergazdai szerepkör tagjai. Azok számára, akik gyakran a szerepkör-hozzárendelések kezeléséhez.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 296fd6edc7f8ca029ea78e86aa605166ba2f585d
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511071"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Megtekintheti, és rendszergazdai szerepkörök hozzárendelése az Azure Active Directory

Mostantól tekintse meg és kezelheti a rendszergazdai szerepkörök az Azure Active Directory portál összes tagját. Ha gyakran a szerepkör-hozzárendelések kezeléséhez, ez a tapasztalat valószínűleg inkább lesz. És ha Ön elgondolkozott "Mit a kontrola hajtsa végre ezeket a szerepköröket valóban?", az Azure AD-rendszergazdai szerepkörök jogosultságait részletes listáját láthatja.

## <a name="view-all-roles"></a>Az összes szerepkör megtekintéséhez

Válassza ki az Azure Active Directoryban, **szerepkörök és a rendszergazdák** az elérhető szerepkörök listájának megtekintéséhez. 

Kattintson a három pontot, jobb oldalán található minden egyes sor megnyitásához a szerepkör részletes leírását.

![az Azure AD-portálhoz szerepkörök listája](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>Saját szerepkörök megtekintése

Legyen könnyen a saját engedélyeit is megtekintheti. Válassza ki **a szerepkör** a a **szerepkörök és a rendszergazdák** oldalon tekintheti meg az Önhöz jelenleg hozzárendelt szerepkörök.

## <a name="view-assignments-for-a-role"></a>Szerepkör hozzárendelések megtekintése

Kattintson a szerepkör a szerepkörhöz rendelt felhasználók megtekintéséhez. Választhat **a PIM kezelése** a további felügyeleti lehetőségeket. Kiemelt szerepkörű rendszergazdák módosíthatja a "Állandó" (a szerepkör mindig aktív)-hozzárendelések "Megfelelő" (az a szerepkör csak akkor, ha emelt szintű). Ha nem rendelkezik a PIM, továbbra is választhatja **a PIM kezelése** , Regisztráljon a próbaverzióra. Privileged Identity Management van szükség egy [Azure AD Premium P2-licenccsomag](../privileged-identity-management/subscription-requirements.md).

![egy rendszergazda szerepkör tagjainak listája](./media/directory-manage-roles-portal/member-list.png)

Ha Ön globális rendszergazda vagy egy kiemelt szerepkörű rendszergazda, egyszerűen adja hozzá vagy eltávolíthatja a tagok, szűrje a listát, vagy jelöljön ki egy tagot a hozzárendelt szerepkörök aktív megtekintéséhez.

## <a name="view-a-users-role-permissions"></a>A felhasználói szerepkör engedélyek megtekintése

A szerepkör tagjainak megtekintése közben, válassza ki a **leírás** a szerepkör-hozzárendelés által megadott engedélyek teljes listájának megtekintéséhez. Az oldal segít címtárbeli szerepkörök kezelésére vonatkozó dokumentációt mutató hivatkozásokat tartalmaz.

![egy rendszergazdai szerepkörhöz tartozó engedélyek listája](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>További lépések

* Nyugodtan megoszthatja velünk a [az Azure AD felügyeleti szerepkörök fórum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Szerepkörök és a rendszergazda szerepkör-hozzárendelés kapcsolatos további információkért lásd: [rendelhet hozzá rendszergazdai szerepköröket](directory-assign-admin-roles.md).
* Az alapértelmezett felhasználói engedélyek, tekintse meg a [Vendég és tag alapértelmezett felhasználói engedélyek összehasonlítása](../fundamentals/users-default-permissions.md).
