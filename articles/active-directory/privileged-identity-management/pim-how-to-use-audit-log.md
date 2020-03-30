---
title: Az Azure AD-szerepkörök naplójelentési jelentésmegtekintése az Azure AD PIM-ben | Microsoft dokumentumok
description: Megtudhatja, hogyan tekintheti meg az Azure AD-szerepkörök naplózási előzményeit az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329508"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Az Azure AD-szerepkörök naplózási előzményeinek megtekintése a kiemelt identitáskezelésben

A kiemelt identitáskezelés (PIM) naplózási előzmények használatával megtekintheti az összes szerepkör-hozzárendelések és aktiválások az elmúlt 30 nap ban az összes kiemelt szerepkörök. Ha az Azure Active Directory (Azure AD) szervezettevékenységének teljes naplózási előzményeit szeretné látni, beleértve a rendszergazdai, végfelhasználói és szinkronizálási tevékenységet, használhatja az [Azure Active Directory biztonsági és tevékenységjelentéseit.](../reports-monitoring/overview-reports.md)

## <a name="determine-your-version-of-pim"></a>A PIM verziójának meghatározása

2019 novemberétől kezdődően a kiemelt identitáskezelés Azure AD-szerepkörök része frissül egy új verzióra, amely megfelel az Azure-erőforrás-szerepkörök élményének. Ez további funkciókat, valamint [a meglévő API-t is módosítja.](azure-ad-roles-features.md#api-changes) Az új verzió bevezetés alatt, hogy mely eljárásokat követi ebben a cikkben attól függ, hogy a kiemelt identitáskezelés jelenleg rendelkezik-e. Ebben a szakaszban ismertetett lépéseket követve határozza meg, hogy a Kiemelt identitáskezelés melyik verzióját használja. Miután ismeri a kiemelt identitáskezelés verzióját, kiválaszthatja a cikkben az adott verziónak megfelelő eljárásokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörben van.
1. Nyissa meg **az Azure AD kiemelt identitáskezelés .** Ha az áttekintő oldal tetején szalaghirdetés található, kövesse a cikk **Új verzió** lapján található utasításokat. Ellenkező esetben kövesse az **Előző verzió** lap utasításait.

    [![Az Azure AD szerepkörök új verziója](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Válassza ki a saját verziójának lapját")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Új verzió](#tab/new)

Az alábbi lépésekkel megtekintheti az Azure AD-szerepkörök naplózási előzményeit.

## <a name="view-resource-audit-history"></a>Erőforrás-naplózási előzmények megtekintése

Az erőforrás-naplózás az Azure AD-szerepkörökhöz társított összes tevékenységnézetét ismerteti.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza **az Erőforrás-naplózás lehetőséget.**

1. Az előzmények szűrése előre meghatározott dátum vagy egyéni tartomány használatával.

    ![Erőforrás-naplózási lista szűrőkkel](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Saját naplózás megtekintése

Az auditom lehetővé teszi, hogy megtekinthesse személyes szerepkör-tevékenységét.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza ki azt az erőforrást, amelynek a naplózási előzményeit meg szeretné tekinteni.

1. Válassza **a Saját naplózás lehetőséget.**

1. Az előzmények szűrése előre meghatározott dátum vagy egyéni tartomány használatával.

    ![Az aktuális felhasználó naplózási listája](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

## <a name="view-audit-history"></a>Naplózási előzmények megtekintése

Az alábbi lépésekkel megtekintheti az Azure AD-szerepkörök naplózási előzményeit.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki a [kiemelt szerepkör-rendszergazdai](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör tagja.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. Válassza az **Azure AD-szerepkörök lehetőséget.**

1. Válassza a **Címtárszerepkörök naplózási előzményeit.**

    A naplózási előzményektől függően egy oszlopdiagram jelenik meg az összes aktiválással, a napi maximális aktiválásokkal és a napi átlagos aktiválásokkal együtt.

    [![Az Azure AD szerepkörök új verziója](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Címtárszerepkörök naplózási előzményeinek megtekintése")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    A lap alján egy táblázat jelenik meg, amely a rendelkezésre álló naplózási előzményekben szereplő egyes műveletekre vonatkozó információkat tartalmazza. Az oszlopok jelentése a következő:

    | Oszlop | Leírás |
    | --- | --- |
    | Time | Amikor a művelet történt. |
    | Requestor (Kérelmező) | Az a felhasználó, aki a szerepkör aktiválását vagy módosítását kérte. Ha az érték az **Azure System,** ellenőrizze az Azure naplózási előzmények további információkért. |
    | Műveletek | A kérelmező által megtett intézkedések. A műveletek lehetnek hozzárendelés, hozzárendelés visszavonása, aktiválása, inaktiválása vagy AddedOutsidePIM. |
    | Tag | Egy szerepkört aktiváló vagy hozzárendelt felhasználó. |
    | Szerepkör | A felhasználó által hozzárendelt vagy aktivált szerepkör. |
    | Érvelés | Az aktiválás során az okmezőbe beírt szöveg. |
    | Lejárat | Amikor egy aktivált szerepkör lejár. Csak a jogosult szerepkör-hozzárendelésekre vonatkozik. |

1. A naplózási előzmények rendezéséhez kattintson az **Idő**, **Művelet**és **Szerepkör** gombra.

## <a name="filter-audit-history"></a>Naplózati előzmények szűrése

1. A naplózási előzmények lap tetején kattintson a **Szűrő** gombra.

    Megjelenik **a Diagramparaméter-frissítések** ablaktábla.

1. Az **Időtartományban**válasszon ki egy időtartományt.

1. A **Szerepkörök mezőben**jelölje be a jelölőnégyzeteket a megtekinteni kívánt szerepkörök jelzéséhez.

    ![Diagramparaméterek frissítése ablaktábla](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. A szűrt naplózási előzmények megtekintéséhez válassza a **Kész** lehetőséget.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Ok, jóváhagyó és jegyszám beszerezni a jóváhagyási eseményeket

1. Jelentkezzen be az [Azure Portalon](https://aad.portal.azure.com) kiemelt szerepkör-rendszergazdai szerepkör-engedélyekkel, és nyissa meg az Azure AD-t.
1. Válassza **a Naplók naplózása**lehetőséget.
1. A **Szolgáltatás** szűrő használatával csak a kiemelt identitáskezelési szolgáltatás naplózási eseményeit jelenítheti meg. A **Naplók naplózása** lapon a következőket teheti:

    - Tekintse meg a naplózási esemény okát az **Állapot oka** oszlopban.
    - Tekintse meg a jóváhagyó a **Kezdeményezte (aktor)** oszlopban a "Tag hozzáadása a szerepkörkérelemhez jóváhagyva" esemény.

    [![Az Azure AD szerepkörök új verziója](media/pim-how-to-use-audit-log/filter-audit-logs.png "A PIM szolgáltatás naplójának szűrése")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Válasszon ki egy naplóeseményt a jegyszám megtekintéséhez a **Részletek** ablaktábla **Tevékenység** lapján.
  
    [![Az Azure AD szerepkörök új verziója](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "A naplózási esemény jegyszámának ellenőrzése")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. A kérelmező (a szerepkört aktiváló személy) a **Részletek** ablaktábla **Részletek** lapján tekintheti meg a naplózási eseményt. Az Azure AD-szerepköröknek két céltípusa van:

    - A szerepkör (**típus** = szerepkör)
    - A kérelmező (**típus** = Felhasználó)

A közvetlenül a jóváhagyási esemény feletti naplóesemény általában egy olyan esemény, amelyben a "Tag hozzáadása a szerepkörhöz befejeződött" esemény, ahol a **Kezdeményezett (aktor)** a kérelmező. A legtöbb esetben nem kell megtalálnia a kérelmezőt a jóváhagyási kérelemben egy naplózási szempontból.

---

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrás-szerepkörök tevékenységének és naplózási előzményeinek megtekintése a kiemelt identitáskezelésben](azure-pim-resource-rbac.md)
