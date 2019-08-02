---
title: Meglévő Azure-előfizetés hozzáadása a bérlőhöz – Azure Active Directory |} A Microsoft Docs
description: Leírja egy meglévő Azure-előfizetés hozzáadása az Azure Active Directory-bérlővel.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a64bad11f5b83ddd7f6d7236ffed4ff4a6e39c2c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561866"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directory-bérlővel

Az Azure-előfizetés megbízhatósági kapcsolatban áll Azure Active Directory (Azure AD) szolgáltatással, ami azt jelenti, hogy az előfizetés megbízik az Azure AD-ben a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban az Azure AD-címtárat, de minden előfizetésben csak megbízható egyetlen címtárban.

Ha az előfizetés lejár, nem fér hozzá az előfizetéshez tartozó összes többi erőforrást. Az Azure AD-címtár azonban továbbra is, az Azure-ban, így társítani, és kezelheti a címtár másik Azure-előfizetést.

Az összes felhasználó rendelkezik egyetlen *kezdőkönyvtár* -címtárral a hitelesítéshez. Azonban a felhasználók lehet Vendég is más címtárakban. Mindkét az otthoni és a Vendég címtárakat látja minden felhasználó számára az Azure ad-ben.

> [!Important]
> Ha egy másik címtárhoz társít egy előfizetést, a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elvesztik a hozzáférést. A klasszikus előfizetési rendszergazdák (a szolgáltatás rendszergazdája és a társ-rendszergazdák) is elvesztik a hozzáférést.
> 
> Emellett az Azure Kubernetes-szolgáltatás (ak) fürtjét áthelyezheti egy másik előfizetésbe, vagy áthelyezheti a fürt tulajdonosának előfizetését egy új bérlőre, így a fürt elveszti a szerepkör-hozzárendelések és az egyszerű szolgáltatások jogosultságai miatti működését. További információ az AK-ról: [Azure Kubernetes Service (ak)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Előkészületek

Mielőtt hozzárendelése, vagy adja hozzá az előfizetés, el kell végeznie az alábbi feladatokat:

1. Tekintse át az alábbi módosításokat, és hogy milyen hatással lehet rájuk:

    - Azok a felhasználók, akik a RBAC használatával rendeltek szerepköröket, elvesztik a hozzáférést
    - A szolgáltatás-rendszergazda és a társ-rendszergazdák elvesztik a hozzáférést
    - Ha rendelkezik kulcstartókkal, nem lesznek elérhetők, és a társítás után ki kell javítani őket.
    - Ha bármilyen felügyelt identitással rendelkezik olyan erőforrásokhoz, mint például a Virtual Machines vagy a Logic Apps, újra engedélyeznie kell, vagy újra létre kell hoznia őket a társítás után.
    - Ha regisztrált Azure Stack van, akkor újra regisztrálnia kell a társítás után

1. Jelentkezzen be egy olyan fiókkal, amely:
    - Tulajdonosi [](../../role-based-access-control/built-in-roles.md#owner) szerepkör-hozzárendelést tartalmaz az előfizetéshez. További információ a tulajdonosi szerepkör hozzárendeléséről: az [Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
    - Mindkét az aktuális könyvtárban, amely rendelkezik az előfizetéshez tartozó és az új címtárban, hogy hol szeretne társítani az előfizetéshez, a jövőben van. Bevezetés a más címtárakhoz való hozzáférésről kapcsolatos további információkért lásd: [hogyan Azure Active Directory-rendszergazdák hozzá B2B együttműködési felhasználókat?](../b2b/add-users-administrator.md).

1. Győződjön meg arról, hogy nem használja az Azure Cloud Service szolgáltatók (CSP) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P) előfizetést, a Microsoft Internal (MS-AZR - 0015P) előfizetést vagy a Microsoft Imagine-(MS-AZR - 0144P) előfizetést.
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Meglévő előfizetés társítása az Azure AD-címtárral

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Válassza ki **módosítsa a könyvtárat**.

    ![Előfizetések lapon, a módosítás directory opció kiemelésével](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Tekintse át a figyelmeztetéseket, amelyek jelenik meg, és válassza ki **módosítás**.

    ![Módosítsa a címtár lapján módosítsa a könyvtárat megjelenítése](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    A címtár megváltozott az előfizetés, és megjelenik a sikert jelző üzenet.

    ![A címtár változásával kapcsolatos sikeres üzenet](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. A **címtár-kapcsoló** használatával lépjen az új könyvtárba. Több órát is igénybe vehet, hogy minden megfelelően megjelenjen. Ha úgy tűnik, hogy túl sokáig tart, ellenőrizze az áthelyezett előfizetés **globális előfizetési szűrőjét** , hogy az ne legyen egyszerűen rejtve.

    ![Címtár-kapcsoló oldal, a minta adataival](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetés címtárának módosítása egy szolgáltatásiszint-művelet, így ez nincs hatással az előfizetés számlázási tulajdonjogát. A fiók rendszergazdája továbbra is módosíthatja a szolgáltatás-rendszergazda, a [Account Center](https://account.azure.com/subscriptions). Törli az eredeti címtárat, át kell adnia az előfizetés számlázási tulajdonjogának átadása egy új fiókrendszergazdának. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Hozzárendelés utáni lépések
Miután hozzárendelt egy előfizetést egy másik címtárhoz, előfordulhat, hogy további lépéseket kell végrehajtania a műveletek folytatásához.

1. Ha rendelkezik kulcstartóval, módosítania kell a Key Vault bérlői AZONOSÍTÓját. További információ: [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../../key-vault/key-vault-subscription-move-fix.md).

2. Ha rendszerhez rendelt felügyelt identitásokat használ az erőforrásokhoz, újra engedélyeznie kell ezeket. Ha felhasználó által hozzárendelt felügyelt identitásokat használ, ezeket újra létre kell hoznia. A felügyelt identitások ismételt engedélyezése vagy újbóli létrehozása után újra létre kell hoznia az identitásokhoz rendelt engedélyeket. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).

3. Ha az előfizetést használó Azure Stack regisztrált, újra kell regisztrálnia. További információ: [Azure stack regisztrálása az Azure](/azure-stack/operator/azure-stack-registration)-ban.



## <a name="next-steps"></a>További lépések

- Hozhat létre egy új Azure AD-bérlőhöz, lásd: [hozzáférés Azure Active Directory új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Szerepkörök hozzárendelése az Azure ad-ben kapcsolatos további információkért lásd: [címtárbeli szerepkörök hozzárendelése az Azure Active Directory felhasználók](active-directory-users-assign-role-azure-portal.md)
