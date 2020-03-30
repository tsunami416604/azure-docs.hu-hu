---
title: Egyéni rendszergazdai szerepkörök az Azure Active Directoryban | Microsoft dokumentumok
description: Egyéni Azure AD-szerepkörök előzetes verziója az identitáskezelés delegálása. Azure-szerepkörök kezelése az Azure Portalon, a PowerShell ben vagy a Graph API-ban.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024969"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Egyéni rendszergazdai szerepkörök az Azure Active Directoryban (előzetes verzió)

Ez a cikk ismerteti, hogyan értheti az Azure AD egyéni szerepkörök az Azure Active Directoryban (Azure AD) a szerepkör-alapú hozzáférés-vezérlés és az erőforrás-hatókörök. Egyéni Azure AD-szerepkörök felszínre a [beépített szerepkörök](directory-assign-admin-roles.md)alapjául szolgáló engedélyeket, így létrehozhatja és rendszerezheti a saját egyéni szerepkörök. Ez a megközelítés lehetővé teszi, hogy a hozzáférést részletesebb módon, mint a beépített szerepkörök, amikor szükség van rájuk. Az Azure AD egyéni szerepkörök első kiadása magában foglalja az alkalmazásregisztrációk kezeléséhez szükséges engedélyek hozzárendeléséhez szükséges szerepkör létrehozását. Idővel további engedélyek et adnak hozzá a szervezeti erőforrásokhoz, például a vállalati alkalmazásokhoz, felhasználókhoz és eszközökhöz.  

Emellett az Azure AD egyéni szerepkörök erőforrásonként támogatják a hozzárendeléseket a hagyományosabb szervezeti szintű hozzárendelések mellett. Ez a megközelítés lehetővé teszi, hogy hozzáférést biztosítson bizonyos erőforrások kezeléséhez (például egy alkalmazásregisztrációhoz) anélkül, hogy hozzáférést biztosítana az összes erőforráshoz (az összes alkalmazásregisztrációhoz).

Az Azure AD szerepköralapú hozzáférés-vezérlés az Azure AD nyilvános előzetes verziójú szolgáltatása, és bármely fizetős Azure AD licenccsomaggal elérhető. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Az Azure AD szerepköralapú hozzáférés-vezérlése

Az egyéni Azure AD-szerepkörök használatával adott engedély kétlépéses folyamat, amely magában foglalja egy egyéni szerepkör-definíció létrehozását, majd egy szerepkör-hozzárendelés használatával történő hozzárendelését. Az egyéni szerepkör-definíció egy előre beállított listából hozzáadott engedélyek gyűjteménye. Ezek az engedélyek ugyanazok, mint a beépített szerepkörökben használt engedélyek.  

Miután létrehozta a szerepkör-definíciót, szerepkör-hozzárendelés létrehozásával hozzárendelheti azt egy felhasználóhoz. A szerepkör-hozzárendelés engedélyeket ad a felhasználónak egy adott hatókörben lévő szerepkör-definícióban. Ez a kétlépéses folyamat lehetővé teszi, hogy hozzon létre egy szerepkör-definíciót, és rendelje hozzá többször különböző hatókörökben. A hatókör határozza meg az Azure AD-erőforrások készletét, amelyhez a szerepkör-tag rendelkezik hozzáféréssel. A leggyakoribb hatókör a szervezeti szintű (szervezeti szintű) hatókör. Az egyéni szerepkör org az egész hatókörön is hozzárendelhető, ami azt jelenti, hogy a szerepkörtag szerepkör-engedélyekkel rendelkezik a szervezet összes erőforrására vonatkozóan. Egyéni szerepkör is hozzárendelhető egy objektumhatókörhöz. Egy objektumhatókör reklatot egy alkalmazás lenne. Ugyanaz a szerepkör rendelhető egy felhasználóhoz a szervezet összes alkalmazásában, majd egy másik felhasználóhoz, amely csak a Contoso Költségjelentések alkalmazás hatókörével van elrendelve.  

Az Azure AD beépített és egyéni szerepkörei az [Azure szerepköralapú hozzáférés-vezérléséhez](../../role-based-access-control/overview.md)hasonló fogalmakon működnek. A [két szerepköralapú hozzáférés-vezérlési rendszer közötti különbség](../../role-based-access-control/rbac-and-directory-admin-roles.md) az, hogy az Azure RBAC szabályozza az Azure-erőforrásokhoz, például a virtuális gépekhez vagy az Azure Resource Management használatával szolgáló tároláshoz való hozzáférést, és az Azure AD egyéni szerepkörei szabályozzák az Azure AD-erőforrásokhoz való hozzáférést a Graph API használatával. Mindkét rendszer kihasználja a szerepkör-definíciók és a szerepkör-hozzárendelések fogalmát.

### <a name="role-assignments"></a>Szerepkör-hozzárendelések

A szerepkör-hozzárendelés az az objektum, amely egy szerepkör-definíciót csatol egy adott hatókörben lévő felhasználóhoz az Azure AD erőforrás-hozzáférés biztosításához. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza. A szerepkör-hozzárendelés lényege három elemből áll:

- Felhasználó (az Azure Active Directoryban felhasználói profillal rendelkező személy)
- Szerepkör-definíció
- Erőforrás hatóköre

[Szerepkör-hozzárendelések az](roles-create-custom.md) Azure Portalon, az Azure AD PowerShell vagy a Graph API használatával hozhat létre. Megtekintheti [az egyéni szerepkör hozzárendeléseit](roles-view-assignments.md#view-the-assignments-of-a-role)is.

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában Chris Green az alkalmazásregisztrációs rendszergazda egyéni szerepkört kapott a Contoso Widget Builder alkalmazás regisztrációjának hatókörén. A hozzárendelés csak az adott alkalmazásregisztrációhoz adja meg Kristiának az alkalmazásregisztrációs rendszergazdai szerepkör engedélyeit.

![A szerepkör-hozzárendelés az engedélyek érvényesítésének módját és három részből áll](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Rendszerbiztonsági tag

A rendszerbiztonsági tag azt a felhasználót jelöli, amely hez hozzáférés áll az Azure AD-erőforrásokhoz. A *felhasználó* olyan személy, aki rendelkezik egy felhasználói profilt az Azure Active Directoryban.

### <a name="role"></a>Szerepkör

A szerepkör-definíció vagy szerepkör engedélyek gyűjteménye. A szerepkör-definíció felsorolja az Azure AD-erőforrásokon elvégezhető műveleteket, például a létrehozást, az olvasást, a frissítést és a törlést. Az Azure AD-ben kétféle szerepkör létezik:

- A Microsoft által létrehozott, nem módosítható beépített szerepkörök.
- A szervezet által létrehozott és kezelt egyéni szerepkörök.

### <a name="scope"></a>Hatókör

A hatókör az engedélyezett műveletek korlátozása egy adott Azure AD-erőforrásra egy szerepkör-hozzárendelés részeként. Szerepkör hozzárendelésekénél megadhat egy hatókört, amely korlátozza a rendszergazda hozzáférését egy adott erőforráshoz. Ha például egy fejlesztőnek egyéni szerepkört szeretne adni, de csak egy adott alkalmazásregisztráció kezeléséhez, az adott alkalmazásregisztrációt hatókörként is felveheti a szerepkör-hozzárendelésbe.

  > [!Note]
  > Egyéni szerepkörök rendelhetők a címtárhatókörhöz és az erőforráshatókörhöz. Még nem rendelhetők hozzá a felügyeleti egység hatóköréhez.
  > A beépített szerepkörök címtárhatókörben és bizonyos esetekben felügyeleti egység hatókörénél is hozzárendelhetők. Még nem rendelhetők hozzá az Azure AD erőforráshatókörhöz.

## <a name="required-license-plan"></a>Szükséges licencterv

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- Egyéni szerepkör-hozzárendelések létrehozása [az Azure Portalon, az Azure AD PowerShell ben és a Graph API-ban](roles-create-custom.md)
- [Egyéni szerepkör hozzárendelésének megtekintése](roles-view-assignments.md#view-assignments-of-single-application-scope)
