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
ms.openlocfilehash: 546abdae5d7c03bb0d4b49f9485fe06b521fdc1d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722221"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Egyéni rendszergazdai szerepkörök a Azure Active Directoryban (előzetes verzió)

Ez a cikk azt ismerteti, hogyan értelmezhető az új egyéni RBAC (szerepköralapú hozzáférés-vezérlés) és az erőforrás-hatókörök a Azure Active Directoryban (Azure AD). Az egyéni RBAC-szerepkörök felfedik a [beépített szerepkörök](directory-assign-admin-roles.md) alapjául szolgáló engedélyeket, így saját egyéni szerepköröket hozhat létre és szervezhet. Az erőforrás-hatókörök lehetővé teszik az egyéni szerepkör hozzárendelését bizonyos erőforrások (például egy alkalmazás) kezeléséhez anélkül, hogy hozzáférést kellene adni az összes erőforráshoz (az összes alkalmazáshoz).

Az egyéni RBAC-szerepkörök használatával történő engedélyezés kétlépéses folyamat. Először hozzon létre egy egyéni szerepkör-definíciót, és adjon hozzá engedélyeket az előre beállított listából. Ezek ugyanazok a beépített szerepkörökben használt engedélyek. Miután létrehozta a szerepkört, hozzárendelheti valakihöz egy szerepkör-hozzárendelés létrehozásával. Ez a kétlépéses folyamat lehetővé teszi, hogy egyetlen szerepkört hozzon létre, és számos alkalommal rendeljen hozzá különböző hatókörökben. Egy egyéni szerepkör hozzárendelhető a címtár hatóköréhez, vagy hozzárendelhető egy objektum hatóköréhez is. Egy objektum hatóköre például egyetlen alkalmazás lehet. Így ugyanazt a szerepkört a címtárban található összes alkalmazáshoz hozzárendelheti, majd a contoso költségelszámolás alkalmazáshoz is.

Az egyéni RBAC-szerepkörök első kiadása magában foglalja az alkalmazások regisztrálásához szükséges engedélyek hozzárendelésére szolgáló szerepkör létrehozását. Idővel a szervezeti erőforrásokhoz (például vállalati alkalmazásokhoz, felhasználókhoz és eszközökhöz) további engedélyek lesznek hozzáadva.

Előzetes verziójú funkciók:

- A portál felhasználói felületének frissítései egyéni szerepkörök létrehozásához és kezeléséhez, valamint a felhasználókhoz való hozzárendeléséhez a szervezetre kiterjedő hatókörben
- Egy előzetes verziójú PowerShell-modul új parancsmagokkal a következőhöz:
  - Egyéni szerepkörök létrehozása és kezelése
  - Egyéni szerepkörök kiosztása szervezeti szintű vagy alkalmazáson belüli regisztrációs hatókörrel
  - Beépített szerepkörök kiosztása a szervezetre kiterjedő hatókörben (paritás a GA-parancsmagokkal)
  - Azure AD-Graph API támogatás

Az Azure AD szerepköralapú hozzáférés-vezérlés az Azure AD nyilvános előzetes verziója, amely minden fizetős Azure AD-licenccel elérhető. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Az Azure AD szerepköralapú hozzáférés-vezérlésének ismertetése

Az Azure AD szerepköralapú hozzáférés-vezérlés lehetővé teszi, hogy olyan szerepköröket rendeljen hozzá, amelyek testre szabhatók, hogy csak egyetlen Azure AD-erőforráson engedélyezze az engedélyezett műveleteket. Az Azure AD szerepköralapú hozzáférés az Azure szerepköralapú hozzáférés-vezérléséhez hasonló fogalmakon működik ([Azure RBAC](../../role-based-access-control/overview.md) for Azure erőforrás-hozzáférés, de az Azure ad szerepköralapú hozzáférés-vezérlése a Microsoft Graph és az Azure RBAC alapul, Azure Resource Manager. Mindkét rendszer azonban a szerepkör-hozzárendeléseken alapuló függvények alapján működik.

### <a name="role-assignments"></a>Szerepkör-hozzárendelések

Az Azure AD szerepköralapú hozzáférés-vezérlés használatával történő hozzáférés vezérlése az engedélyek kikényszeríthető **szerepkör**-hozzárendeléseinek létrehozása. A szerepkör-hozzárendelés három elemet tartalmaz:

- Rendszerbiztonsági tag
- Szerepkör-definíció
- Erőforrás hatóköre

A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza. Szerepkör-hozzárendeléseket a Azure Portal, az Azure AD PowerShell és a Graph API használatával [hozhat létre](roles-create-custom.md) . Külön is megtekintheti [az egyéni szerepkörhöz tartozó hozzárendeléseket](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a SalesForce alkalmazás hatókörében a Chris Green hozzá lett rendelve az [alkalmazás-rendszergazdai](directory-assign-admin-roles.md#application-administrator) szerepkörhöz. János nem fér hozzá más alkalmazások felügyeletéhez, hacsak nem egy másik szerepkör-hozzárendelés részét képezik.

![A szerepkör-hozzárendelés az engedélyek kikényszerített és három részből áll](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Rendszerbiztonsági tag

A rendszerbiztonsági tag az Azure AD-erőforrásokhoz való hozzáférést biztosító felhasználót vagy egyszerű szolgáltatást jelöli. A *felhasználó* olyan személy, aki Azure Active Directory felhasználói profillal rendelkezik. Egy *egyszerű szolgáltatásnév* az alkalmazások és szolgáltatások által az adott Azure ad-erőforrások eléréséhez használt biztonsági identitás.

A rendszerbiztonsági tag hasonló a felhasználói identitáshoz, mert a felhasználónevet, jelszót vagy tanúsítványt jelöli, de nem felhasználó helyett egy alkalmazásra vagy szolgáltatásra.

### <a name="role"></a>Role

A szerepkör-definíció vagy szerepkör az engedélyek gyűjteménye. A szerepkör-definíció felsorolja az Azure AD-erőforrásokon végrehajtható műveleteket, például a létrehozás, olvasás, frissítés és törlés lehetőségeit. Az Azure AD két típusú szerepkörrel rendelkezik:

- A Microsoft által létrehozott beépített szerepkörök nem módosíthatók. A globális rendszergazda beépített szerepkör minden Azure AD-erőforrásra vonatkozó engedélyekkel rendelkezik.
- A szervezet által létrehozott és kezelt egyéni szerepkörök.

### <a name="scope"></a>Scope

A hatókör az adott Azure AD-erőforráson engedélyezett műveletek korlátozása. Ha szerepkört rendel hozzá, testreszabhatja a szerepkört, hogy a művelet hatókörének definiálásával korlátozza a rendszergazda által engedélyezett műveleteket. Ha például a fejlesztőknek nincs szükségük az összes alkalmazás teljes felügyeletére, az Azure AD egyéni szerepköreivel engedélyezheti, hogy csak az alkalmazások regisztrációját kezeljék.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- Egyéni szerepkör-hozzárendelések létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](roles-create-custom.md) használatával
- [Egyéni szerepkör hozzárendeléseinek megtekintése](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
