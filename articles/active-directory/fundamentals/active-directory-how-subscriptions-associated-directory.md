---
title: Meglévő Azure-előfizetés hozzáadása a bérlőhöz – Azure Active Directory |} A Microsoft Docs
description: Leírja egy meglévő Azure-előfizetés hozzáadása az Azure Active Directory-bérlővel.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3322e49c6fdc590b785806f67b5081700bf8b37b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264896"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directory-bérlővel

Azure-előfizetés az Azure Active Directoryval (Azure AD), ami azt jelenti, hogy az előfizetés bizalmi kapcsolatok az Azure AD-felhasználók, szolgáltatások és eszközök hitelesítéséhez megbízhatósági kapcsolattal rendelkezik. Több előfizetés is megbízhat ugyanabban az Azure AD-címtárat, de minden előfizetésben csak megbízható egyetlen címtárban.

Ha az előfizetés lejár, nem fér hozzá az előfizetéshez tartozó összes többi erőforrást. Az Azure AD-címtár azonban továbbra is, az Azure-ban, így társítani, és kezelheti a címtár másik Azure-előfizetést.

Minden felhasználó rendelkezik-e egy *otthoni* hitelesítési címtárat. Azonban a felhasználók lehet Vendég is más címtárakban. Mindkét az otthoni és a Vendég címtárakat látja minden felhasználó számára az Azure ad-ben.

> [!Important]
> Ha egy előfizetés másik címtárhoz, használatával szerepkörrel rendelkező felhasználók társítania [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) elveszítik a hozzáférésüket. Hagyományos előfizetés-rendszergazda (szolgáltatás-rendszergazda és Társrendszergazdák) is elveszíti a hozzáférést.
> 
> Emellett az Azure Kubernetes Service (AKS)-fürt egy másik előfizetésbe való áthelyezését, vagy a fürt tulajdonos előfizetés áthelyezése egy új bérlőt hatására a fürt elveszítené funkciók miatt elveszett szerepkör-hozzárendelések és a szolgáltatás egyszerű jogosultságokat. Aks-sel kapcsolatos további információkért lásd: [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/).

## <a name="before-you-begin"></a>Előkészületek

Mielőtt hozzárendelése, vagy adja hozzá az előfizetés, el kell végeznie az alábbi feladatokat:

1. Tekintse át a módosításokat, és hogyan, hatással lehetnek a következők közül:

    - RBAC szerepkörök rendelt felhasználók elveszíti a hozzáférését
    - Szolgáltatás-rendszergazda és Társrendszergazdák elveszíti a hozzáférést
    - Ha bármely kulcstartók, nem érhető el legyenek, és javítsa ki őket a hozzárendelés után kell
    - Ha egy regisztrált Azure Stack, kell újraregisztrálni a hozzárendelés után

1. Jelentkezzen be egy olyan fiókkal, amely:
    - Rendelkezik egy [tulajdonos](../../role-based-access-control/built-in-roles.md#owner) az előfizetéshez tartozó szerepkör-hozzárendelés. A tulajdonosi szerepkör hozzárendelése kapcsolatos információkért lásd: [rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).
    - Mindkét az aktuális könyvtárban, amely rendelkezik az előfizetéshez tartozó és az új címtárban, hogy hol szeretne társítani az előfizetéshez, a jövőben van. Bevezetés a más címtárakhoz való hozzáférésről kapcsolatos további információkért lásd: [hogyan Azure Active Directory-rendszergazdák hozzá B2B együttműködési felhasználókat?](../b2b/add-users-administrator.md).

1. Győződjön meg arról, hogy nem használja az Azure Cloud Service szolgáltatók (CSP) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P) előfizetést, a Microsoft Internal (MS-AZR - 0015P) előfizetést vagy a Microsoft Imagine-(MS-AZR - 0144P) előfizetést.
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Meglévő előfizetés társítása az Azure AD-címtárral

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Válassza ki **módosítsa a könyvtárat**.

    ![Előfizetések lapon, a módosítás directory opció kiemelésével](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Tekintse át a figyelmeztetéseket, amelyek jelenik meg, és válassza ki **módosítás**.

    ![Módosítsa a címtár lapján módosítsa a könyvtárat megjelenítése](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    A címtár megváltozott az előfizetés, és megjelenik a sikert jelző üzenet.

    ![Sikert jelző üzenettel kapcsolatos címtár módosítása](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    
4. Használja a **Directory környezetválasztó** , nyissa meg az új címtárban. Akár 10 percig is eltarthat, amíg minden megfelelően megjelenik.

    ![Környezetválasztó könyvtárlap, mintául szolgáló adatokkal](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetés címtárának módosítása egy szolgáltatásiszint-művelet, így ez nincs hatással az előfizetés számlázási tulajdonjogát. A fiók rendszergazdája továbbra is módosíthatja a szolgáltatás-rendszergazda, a [Account Center](https://account.azure.com/subscriptions). Törli az eredeti címtárat, át kell adnia az előfizetés számlázási tulajdonjogának átadása egy új fiókrendszergazdának. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>POST társítás lépések
Miután egy előfizetés másik címtárhoz történő társításához lehet további lépéseket is végre kell hajtania, ha folytatni működését.

1. Ha bármely kulcstartók, módosítania kell a kulcstartó bérlőazonosítója. További információkért lásd: [egy kulcstartó Bérlőazonosítójának módosítása az előfizetés áthelyezése után](../../key-vault/key-vault-subscription-move-fix.md).

2. Ha regisztrált az Azure Stack ezt az előfizetést, akkor újra regisztrálnia kell. További információkért lásd: [regisztrálása az Azure Stack az Azure-ral](../../azure-stack/azure-stack-registration.md).



## <a name="next-steps"></a>További lépések

- Hozhat létre egy új Azure AD-bérlőhöz, lásd: [hozzáférés Azure Active Directory új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Szerepkörök hozzárendelése az Azure ad-ben kapcsolatos további információkért lásd: [címtárbeli szerepkörök hozzárendelése az Azure Active Directory felhasználók](active-directory-users-assign-role-azure-portal.md)
