---
title: Azure-beli rendszergazdai szerepkörök előzetes verziója testreszabható engedélyekkel – Azure Active Directory | Microsoft Docs
description: Az Identitáskezelés delegálásához egyéni Azure AD-szerepköröket tekinthet meg. Az Azure-szerepkörök kezelése a Azure Portal, a PowerShell vagy a Graph API használatával.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eabf29b10814d19e89c21f27ec66fce5355c9bfb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880690"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Egyéni rendszergazdai szerepkörök a Azure Active Directoryban (előzetes verzió)

Ez a cikk az új egyéni szerepköralapú hozzáférés-vezérlés (RBAC) és a Azure Active Directory (Azure AD) erőforrás-hatókörök megismerését ismerteti. Az egyéni RBAC-szerepkörök felfedik a [beépített szerepkörök](directory-assign-admin-roles.md) alapjául szolgáló engedélyeket, így saját egyéni szerepköröket hozhat létre és szervezhet. Ez a megközelítés lehetővé teszi, hogy a hozzáférést részletesebben adja meg, mint a beépített szerepkörök, ha szükséges. Az egyéni RBAC-szerepkörök első kiadása magában foglalja az alkalmazások regisztrálásához szükséges engedélyek hozzárendelésére szolgáló szerepkör létrehozását. Idővel a szervezeti erőforrásokhoz (például vállalati alkalmazásokhoz, felhasználókhoz és eszközökhöz) további engedélyek lesznek hozzáadva.  

Emellett az egyéni RBAC-szerepkörök erőforrás-hozzárendeléseket is támogatnak a hagyományos szervezeti szintű hozzárendeléseken felül. Ez a megközelítés lehetővé teszi, hogy hozzáférést biztosítson bizonyos erőforrásokhoz (például egy alkalmazás regisztrálásához) anélkül, hogy hozzáférést kellene adni az összes erőforráshoz (az összes alkalmazás regisztrációja).

Az Azure AD szerepköralapú hozzáférés-vezérlés az Azure AD nyilvános előzetes verziója, amely minden fizetős Azure AD-licenccel elérhető. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Az Azure AD szerepköralapú hozzáférés-vezérlésének ismertetése

Az egyéni RBAC-szerepkörök használatával történő engedélyezés kétlépéses folyamat, amely magában foglalja az egyéni szerepkör-definíciók létrehozását, majd a szerepkör-hozzárendelés használatával történő hozzárendelését. Az egyéni szerepkör-definíciók egy előre definiált listából felvett engedélyek gyűjteményei. Ezek az engedélyek ugyanazok, mint a beépített szerepkörökben.  

A szerepkör-definíció létrehozása után hozzárendelheti valakihöz egy szerepkör-hozzárendelés létrehozásával. A szerepkör-hozzárendelések egy adott hatókörben lévő szerepkör-definícióban lévő engedélyeket biztosítanak valakinek. Ez a kétlépéses folyamat lehetővé teszi, hogy egy szerepkör-definíciót hozzon létre, és számos alkalommal rendeljen hozzá különböző hatókörökben. A hatókör határozza meg azon erőforrások készletét, amelyekhez a szerepkör tagja hozzáfér. A leggyakoribb hatókör az egész szervezetre kiterjedő (szervezeti szintű) hatókör. Az egyéni szerepkör a szervezeti szintű hatókörhöz rendelhető, ami azt jelenti, hogy a szerepkör tagja rendelkezik a szervezet összes erőforrásához szükséges szerepkör-jogosultságokkal. Az objektumok hatóköréhez egyéni szerepkör is hozzárendelhető. Egy objektum hatóköre például egyetlen alkalmazás lehet. Így ugyanaz a szerepkör a szervezet összes alkalmazásához hozzárendelhető a Sally-hez, majd a contoso költségelszámolás alkalmazáshoz is.  

Az Azure AD-RBAC az [Azure szerepköralapú hozzáférés-vezérléséhez](../../role-based-access-control/overview.md)hasonló fogalmakon működnek. A különbség az, hogy az Azure RBAC az Azure-erőforrásokhoz, például a virtuális gépekhez és webhelyekhez való hozzáférést vezérli, az Azure AD-RBAC pedig az Azure AD-hez Mindkét rendszer kihasználja a szerepkör-definíciók és a szerepkör-hozzárendelések fogalmát.

### <a name="role-assignments"></a>Szerepkör-hozzárendelések

A szerepkör-hozzárendelés az a folyamat, amellyel a szerepkör-definíciót egy felhasználóhoz csatolhatja egy adott hatókörben a hozzáférés biztosítása céljából. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza. A szerepkör-hozzárendelés három elemet tartalmaz:
- Felhasználó
- Szerepkör-definíció
- Erőforrás hatóköre

Szerepkör-hozzárendeléseket a Azure Portal, az Azure AD PowerShell vagy a Graph API használatával [hozhat létre](roles-create-custom.md) . Megtekintheti [az egyéni szerepkörhöz tartozó hozzárendeléseket](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)is.

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a Chris Green hozzá lett rendelve az alkalmazás-regisztrációs rendszergazda egyéni szerepkörhöz a contoso widget Builder alkalmazás-regisztráció hatókörében. Ez a hozzárendelés csak az adott alkalmazás regisztrálásakor engedélyezi, hogy Chris az alkalmazás regisztrációs rendszergazdai szerepkörének engedélyeit.

![A szerepkör-hozzárendelés az engedélyek kikényszerített és három részből áll](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Rendszerbiztonsági tag

A rendszerbiztonsági tag az Azure AD-erőforrásokhoz való hozzáférést biztosító felhasználót jelöli. A *felhasználó* olyan személy, aki Azure Active Directory felhasználói profillal rendelkezik.

### <a name="role"></a>Role

A szerepkör-definíció vagy szerepkör az engedélyek gyűjteménye. A szerepkör-definíció felsorolja az Azure AD-erőforrásokon végrehajtható műveleteket, például a létrehozás, olvasás, frissítés és törlés lehetőségeit. Az Azure AD két típusú szerepkörrel rendelkezik:

- A Microsoft által létrehozott beépített szerepkörök nem módosíthatók. A globális rendszergazda beépített szerepkör minden Azure AD-erőforrásra vonatkozó engedélyekkel rendelkezik.
- A szervezet által létrehozott és kezelt egyéni szerepkörök.

### <a name="scope"></a>Scope

A hatókör egy adott Azure AD-erőforrásra vonatkozó engedélyezett műveletek korlátozása szerepkör-hozzárendelés részeként. Egy szerepkör hozzárendelésével megadhat egy hatókört, amely korlátozza a rendszergazda hozzáférését egy adott erőforráshoz. Ha például egyéni szerepkört szeretne kiadni egy fejlesztőnek, de csak egy adott alkalmazás regisztrálását szeretné kezelni, akkor a szerepkör-hozzárendelésben a megadott alkalmazás-regisztráció hatókörként is megadható.

  > [!Note]
  > Az egyéni szerepköröket a címtár hatóköre és az erőforrás hatóköre szerint lehet hozzárendelni. A felügyeleti egység hatóköre még nem rendelhető hozzá.
  > A beépített szerepkörök a címtár hatókörében, és bizonyos esetekben a felügyeleti egység hatókörében is hozzárendelhetők. Az objektumok hatóköre még nem rendelhető hozzá.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- Egyéni szerepkör-hozzárendelések létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](roles-create-custom.md) használatával
- [Egyéni szerepkör hozzárendeléseinek megtekintése](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
