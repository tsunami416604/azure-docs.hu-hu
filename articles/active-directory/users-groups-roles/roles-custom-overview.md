---
title: Egyéni rendszergazdai szerepkörök a Azure Active Directoryban | Microsoft Docs
description: Az Identitáskezelés delegálásához egyéni Azure AD-szerepköröket tekinthet meg. Az Azure-szerepkörök kezelése a Azure Portal, a PowerShell vagy a Graph API használatával.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915098130c7a8462bc0e009be04a61b5b0a7985d
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795592"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Egyéni rendszergazdai szerepkörök a Azure Active Directoryban (előzetes verzió)

Ez a cikk azt ismerteti, hogyan értelmezhető az Azure AD egyéni szerepkörei a Azure Active Directory (Azure AD) szerepkör-alapú hozzáférés-vezérléssel és erőforrás-hatókörökkel. Az egyéni Azure AD-szerepkörök a [beépített szerepkörök](directory-assign-admin-roles.md)alapjául szolgáló engedélyeket felhasználva hozhatják létre és rendszerezheti saját egyéni szerepköreiket. Ez a megközelítés lehetővé teszi a hozzáférés részletesebb használatát a beépített szerepköröknél, amikor szükségesek. Az Azure AD egyéni szerepköreinek első kiadása magában foglalja az alkalmazások regisztrálásához szükséges engedélyek hozzárendelésére szolgáló szerepkör létrehozását. Idővel a szervezeti erőforrásokhoz (például vállalati alkalmazásokhoz, felhasználókhoz és eszközökhöz) további engedélyek lesznek hozzáadva.  

Az Azure AD egyéni szerepkörei emellett a hagyományos, szervezeti szintű hozzárendeléseken kívül a hozzárendeléseket is támogatják az erőforrások alapján. Ez a megközelítés lehetővé teszi, hogy hozzáférést biztosítson bizonyos erőforrásokhoz (például egy alkalmazás regisztrálásához) anélkül, hogy hozzáférést kellene adni az összes erőforráshoz (az összes alkalmazás regisztrációja).

Az Azure AD szerepköralapú hozzáférés-vezérlés az Azure AD nyilvános előzetes verziója, amely minden fizetős Azure AD-licenccel elérhető. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Az Azure AD szerepköralapú hozzáférés-vezérlésének ismertetése

Az egyéni Azure AD-szerepkörök használatával történő engedélyek megadása kétlépéses folyamat, amely magában foglalja az egyéni szerepkör-definíciók létrehozását, majd a szerepkör-hozzárendelés használatával történő hozzárendelését. Az egyéni szerepkör-definíciók egy előre definiált listából felvett engedélyek gyűjteményei. Ezek az engedélyek ugyanazok, mint a beépített szerepkörökben.  

A szerepkör-definíció létrehozása után hozzárendelheti azt egy felhasználóhoz egy szerepkör-hozzárendelés létrehozásával. A szerepkör-hozzárendelések egy adott hatókörben lévő szerepkör-definícióban lévő engedélyeket biztosítanak a felhasználónak. Ez a kétlépéses folyamat lehetővé teszi, hogy egyetlen szerepkör-definíciót hozzon létre, és több alkalommal rendeljen hozzá különböző hatókörökben. A hatókör határozza meg azon Azure AD-erőforrások készletét, amelyekhez a szerepkör tagja hozzáfér. A leggyakoribb hatókör az egész szervezetre kiterjedő (szervezeti szintű) hatókör. Az egyéni szerepkör a szervezeti szintű hatókörhöz rendelhető, ami azt jelenti, hogy a szerepkör tagja rendelkezik a szervezet összes erőforrásához szükséges szerepkör-jogosultságokkal. Az objektumok hatóköréhez egyéni szerepkör is hozzárendelhető. Egy objektum hatóköre például egyetlen alkalmazás lehet. Ugyanazt a szerepkört hozzárendelheti egy felhasználóhoz a szervezeten belüli összes alkalmazáshoz, majd egy másik felhasználóhoz, amely csak a contoso költségelszámolás alkalmazás hatókörével rendelkezik.  

Az Azure AD beépített és egyéni szerepkörei az [Azure szerepköralapú hozzáférés-vezérléshez (Azure RBAC)](../../role-based-access-control/overview.md)hasonló fogalmakon működnek. A [két szerepköralapú hozzáférés-vezérlési rendszer közötti különbség](../../role-based-access-control/rbac-and-directory-admin-roles.md) az, hogy az Azure RBAC az Azure-erőforrásokhoz, például a virtuális gépekhez vagy a tárolóhoz való hozzáférést az Azure Resource Management használatával szabályozza, az Azure ad egyéni szerepkörei pedig az Azure ad-erőforrásokhoz való hozzáférést Graph API használatával szabályozzák. Mindkét rendszer kihasználja a szerepkör-definíciók és a szerepkör-hozzárendelések fogalmát.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hogyan határozza meg az Azure AD, hogy egy felhasználó hozzáfér-e egy erőforráshoz

Az Azure AD az alábbi magas szintű lépésekkel határozza meg, hogy van-e hozzáférése egy felügyeleti erőforráshoz. Ezeket az információkat a hozzáférési problémák elhárításához használhatja.

1. A felhasználó (vagy az egyszerű szolgáltatásnév) a Microsoft Graph vagy az Azure AD Graph-végponthoz szerzi be a jogkivonatot.

1. A felhasználó Azure Active Directory (Azure AD) API-hívást kezdeményez a kiállított jogkivonat használatával Microsoft Graph vagy Azure AD gráfon keresztül.

1. A körülményektől függően az Azure AD a következő műveletek egyikét veszi igénybe:

    - Kiértékeli a felhasználó szerepkör-tagságát a felhasználó hozzáférési jogkivonatában lévő [wids jogcím](../develop/access-tokens.md) alapján.
    - Az összes olyan szerepkör-hozzárendelés beolvasása, amely közvetlenül vagy csoporttagság útján a felhasználóra vonatkozik, a művelet végrehajtásához szükséges erőforrásra.

1. Az Azure AD meghatározza, hogy az API-hívásban szereplő művelet tartalmazza-e a felhasználó által ehhez az erőforráshoz tartozó szerepköröket.
1. Ha a felhasználó nem rendelkezik szerepkörrel a kért hatókörben lévő művelettel, a hozzáférés nem adható meg. Egyéb esetben hozzáférést biztosít.

### <a name="role-assignments"></a>Szerepkör-hozzárendelések

A szerepkör-hozzárendelés az az objektum, amely egy szerepkör-definíciót csatol egy felhasználóhoz egy adott hatókörben az Azure AD-erőforrásokhoz való hozzáférés biztosítása érdekében. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza. Az alapszintű szerepkör-hozzárendelés három elemet tartalmaz:

- Felhasználó (olyan személy, aki Azure Active Directory felhasználói profillal rendelkezik)
- Szerepkör-definíció
- Erőforrás hatóköre

Szerepkör- [hozzárendeléseket](roles-create-custom.md) a Azure Portal, az Azure ad PowerShell vagy a Graph API használatával hozhat létre. [Megtekintheti az egyéni szerepkörhöz tartozó hozzárendeléseket](roles-view-assignments.md#view-the-assignments-of-a-role)is.

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a Chris Green hozzá lett rendelve az alkalmazás-regisztrációs rendszergazda egyéni szerepkörhöz a contoso widget Builder alkalmazás-regisztráció hatókörében. A hozzárendelés csak az adott alkalmazás regisztrálásához engedélyezi az alkalmazás-regisztrációs rendszergazda szerepkör engedélyeit.

![A szerepkör-hozzárendelés az engedélyek kikényszerített és három részből áll](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Rendszerbiztonsági tag

A rendszerbiztonsági tag az Azure AD-erőforrásokhoz való hozzáférést biztosító felhasználót jelöli. A *felhasználó* olyan személy, aki Azure Active Directory felhasználói profillal rendelkezik.

### <a name="role"></a>Szerepkör

A szerepkör-definíció vagy szerepkör az engedélyek gyűjteménye. A szerepkör-definíció felsorolja az Azure AD-erőforrásokon végrehajtható műveleteket, például a létrehozás, olvasás, frissítés és törlés lehetőségeit. Az Azure AD két típusú szerepkörrel rendelkezik:

- A Microsoft által létrehozott beépített szerepkörök nem módosíthatók.
- A szervezet által létrehozott és kezelt egyéni szerepkörök.

### <a name="scope"></a>Hatókör

A hatókör egy adott Azure AD-erőforrásra vonatkozó engedélyezett műveletek korlátozása szerepkör-hozzárendelés részeként. Egy szerepkör hozzárendelésével megadhat egy hatókört, amely korlátozza a rendszergazda hozzáférését egy adott erőforráshoz. Ha például egyéni szerepkört szeretne kiadni egy fejlesztőnek, de csak egy adott alkalmazás regisztrálását szeretné kezelni, akkor a szerepkör-hozzárendelésben a megadott alkalmazás-regisztráció hatókörként is megadható.

  > [!Note]
  > Az egyéni szerepköröket a címtár hatóköre és az erőforrás hatóköre szerint lehet hozzárendelni. A felügyeleti egység hatóköre még nem rendelhető hozzá.
  > A beépített szerepkörök a címtár hatókörében, és bizonyos esetekben a felügyeleti egység hatókörében is hozzárendelhetők. Az Azure AD-erőforrás hatóköre még nem rendelhető hozzájuk.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Következő lépések

- Egyéni szerepkör-hozzárendelések létrehozása [a Azure Portal, az Azure ad PowerShell és a Graph API](roles-create-custom.md) használatával
- [Egyéni szerepkör hozzárendeléseinek megtekintése](roles-view-assignments.md#view-assignments-of-single-application-scope)