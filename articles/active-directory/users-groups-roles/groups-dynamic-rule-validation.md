---
title: Szabályok ellenőrzése a dinamikus csoporttagsághoz (előzetes verzió) - Azure AD | Microsoft dokumentumok
description: Hogyan tesztelhet tagokat egy tagsági szabály egy dinamikus csoportok az Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115520"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Dinamikus csoporttagsági szabály ellenőrzése (előzetes verzió) az Azure Active Directoryban

Az Azure Active Directory (Azure AD) most már biztosítja a dinamikus csoportszabályok érvényesítéséhez (nyilvános előzetes verzióban) érvényesítéséhez. A **Szabályok érvényesítése** lapon ellenőrizheti a dinamikus szabályt a mintacsoport tagjaival szemben, hogy ellenőrizze, hogy a szabály a várt módon működik-e. A dinamikus csoportszabályok létrehozásakor vagy frissítésekor a rendszergazdák tudni szeretnék, hogy egy felhasználó vagy egy eszköz a csoport tagja lesz-e. Ez segít kiértékelni, hogy a felhasználó vagy az eszköz megfelel-e a szabályfeltételeknek, és segítséget nyújt a hibaelhárításhoz, ha a tagság nem várható.

## <a name="step-by-step-walk-through"></a>Részletes lépéslépés

Első lépésekhez nyissa meg az **Azure Active Directory** > **csoportok**. Jelöljön ki egy meglévő dinamikus csoportot, vagy hozzon létre egy új dinamikus csoportot, és kattintson a dinamikus tagsági szabályokra. Ezután megjelenik a **Szabályok érvényesítése** lap.

![A Szabályok érvényesítése lap megkeresése és kezdés egy meglévő szabállyal](./media/groups-dynamic-rule-validation/validate-tab.png)

A Szabályok ellenőrzése lapon **kiválaszthatja** a felhasználókat a tagságuk érvényesítéséhez. Egyszerre 20 felhasználó vagy eszköz választható ki.

![Felhasználók hozzáadása a meglévő szabály érvényesítéséhez](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Miután kiválasztotta a felhasználókat vagy eszközöket a választóból, és a **Select lehetőséget,** az érvényesítés automatikusan elindul, és megjelennek az érvényesítési eredmények.

![A szabályérvényesítés eredményeinek megtekintése](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Az eredmények jelzik, hogy a felhasználó tagja-e a csoportnak vagy sem. Ha a szabály érvénytelen, vagy hálózati probléma merül fel, az eredmény **ismeretlen**lesz. **Ismeretlen esetén**a részletes hibaüzenet ismerteti a szükséges problémát és műveleteket.

![A szabályérvényesítés eredményeinek részleteinek megtekintése](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Módosíthatja a szabályt, és a tagságok érvényesítése aktiválódik. Annak megtekintéséhez, hogy a felhasználó miért nem tagja a csoportnak, kattintson a "Részletek megtekintése" gombra, és az ellenőrzés részletei a szabályt alkotó minden egyes kifejezés eredményét jelenítik meg. A kilépéshez kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések

- [Dinamikus tagsági szabályok csoportokhoz](groups-dynamic-membership.md)
