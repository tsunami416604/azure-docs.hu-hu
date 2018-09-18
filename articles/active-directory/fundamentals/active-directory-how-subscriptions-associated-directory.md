---
title: Meglévő Azure-előfizetés hozzáadása az Azure Active Directory-bérlőhöz |} A Microsoft Docs
description: Ismerje meg, hogy egy meglévő Azure-előfizetés hozzáadása az Azure Active Directory-bérlővel.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: dd62b22eca40a214c5b08a9bc48815e40fe90e47
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984084"
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Hogyan: társítása vagy az Azure-előfizetés hozzáadása az Azure Active Directoryhoz
Az Azure-előfizetést egy Azure Active Directory (Azure AD), ami azt jelenti, hogy az előfizetés bizalmi kapcsolatok az Azure AD-felhasználók, szolgáltatások és eszközök hitelesítéséhez megbízhatósági kapcsolattal rendelkezik. Több előfizetés is megbízhat ugyanabban az Azure AD-címtárat, de minden előfizetésben csak megbízható egyetlen címtárban.

Ha az előfizetés lejár, nem fér hozzá az előfizetéshez tartozó összes többi erőforrást. Az Azure AD-címtár azonban továbbra is, az Azure-ban, így társítani, és kezelheti a címtár másik Azure-előfizetést.

Minden felhasználó a hitelesítéshez egyetlen "saját" címtárral rendelkezik. Azonban a felhasználók lehet Vendég is más címtárakban. Mindkét az otthoni és a Vendég címtárakat látja minden felhasználó számára az Azure ad-ben.

>[!Important]
>Az összes [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) hozzárendelt hozzáféréssel, és minden előfizetés-rendszergazda felhasználók elveszíti a hozzáférést az előfizetés után történt változtatásokat. Emellett ha bármely kulcstartók, fog is vonatkoznak rá az előfizetés áthelyezése. Amely javításához kell [a kulcstartó Bérlőazonosítójának módosítása az](../../key-vault/key-vault-subscription-move-fix.md) művelet folytatása előtt.


## <a name="before-you-begin"></a>Előkészületek
Mielőtt hozzárendelése, vagy adja hozzá az előfizetés, el kell végeznie az alábbi feladatokat:

- Jelentkezzen be egy olyan fiókkal, amely:
    - Rendelkezik **RBAC tulajdonosi** hozzáférést az előfizetéshez.

    - Mindkét az aktuális könyvtárban, amely rendelkezik az előfizetéshez tartozó és az új címtárban, hogy hol szeretne társítani az előfizetéshez, a jövőben van. Bevezetés a más címtárakhoz való hozzáférésről kapcsolatos további információkért lásd: [hogyan Azure Active Directory-rendszergazdák hozzá B2B együttműködési felhasználókat?](../b2b/add-users-administrator.md).

- Győződjön meg arról, hogy nem használja az Azure Cloud Service szolgáltatók (CSP) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P) előfizetést, a Microsoft Internal (MS-AZR - 0015P) előfizetést vagy a Microsoft Imagine-(MS-AZR - 0144P) előfizetést.
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Meglévő előfizetés társítása az Azure AD-címtárral
1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Válassza ki **módosítsa a könyvtárat**.

    ![Előfizetések lapon, a módosítás directory opció kiemelésével](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Tekintse át a figyelmeztetéseket, amelyek jelenik meg, és válassza ki **módosítás**.

    ![Módosítsa a címtár lapján módosítsa a könyvtárat megjelenítése](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    A címtár megváltozott az előfizetés, és megjelenik a sikert jelző üzenet.

    ![Sikeres műveletről tájékoztató üzenet](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. A címtárváltóval lépjen az új címtárban. Akár 10 percig is eltarthat, amíg minden megfelelően megjelenik.

    ![Környezetválasztó könyvtárlap](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetés címtárának módosítása egy szolgáltatásiszint-művelet, így ez nincs hatással az előfizetés számlázási tulajdonjogát. A fiók rendszergazdája továbbra is módosíthatja a szolgáltatás-rendszergazda, a [Account Center](https://account.azure.com/subscriptions). Törli az eredeti címtárat, át kell adnia az előfizetés számlázási tulajdonjogának átadása egy új fiókrendszergazdának. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>További lépések

- Hozhat létre egy új Azure AD-bérlőhöz, lásd: [hozzáférés Azure Active Directory új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Szerepkörök hozzárendelése az Azure ad-ben kapcsolatos további információkért lásd: [címtárbeli szerepkörök hozzárendelése az Azure Active Directory felhasználók](active-directory-users-assign-role-azure-portal.md)
