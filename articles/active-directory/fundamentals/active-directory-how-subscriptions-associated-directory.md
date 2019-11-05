---
title: Meglévő Azure-előfizetés hozzáadása a bérlőhöz – Azure Active Directory | Microsoft Docs
description: Útmutató meglévő Azure-előfizetés Azure Active Directory bérlőhöz való hozzáadásához.
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
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473395"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure-előfizetés hozzárendelése vagy hozzáadása a Azure Active Directory bérlőhöz

Az Azure-előfizetés megbízhatósági kapcsolatban áll Azure Active Directory (Azure AD) szolgáltatással, ami azt jelenti, hogy az előfizetés megbízik az Azure AD-ben a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban az Azure AD-címtárban, de az egyes előfizetések csak egyetlen címtárban bízhatnak meg.

Ha az előfizetése lejár, elveszíti a hozzáférést az előfizetéshez társított összes többi erőforráshoz. Az Azure AD-címtár azonban továbbra is az Azure-ban marad, így egy másik Azure-előfizetéssel rendelheti hozzá és kezelheti a címtárat.

Az összes felhasználó rendelkezik egyetlen *kezdőkönyvtár* -címtárral a hitelesítéshez. A felhasználók azonban más címtárakban is lehetnek vendégként. Az Azure AD-ben az egyes felhasználók otthoni és vendég könyvtára is megtekinthető.

> [!Important]
> Ha egy másik címtárhoz társít egy előfizetést, a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elvesztik a hozzáférést. A klasszikus előfizetési rendszergazdák (a szolgáltatás rendszergazdája és a társ-rendszergazdák) is elvesztik a hozzáférést. 
>
> A szabályzat-hozzárendeléseket a rendszer az előfizetésből is eltávolítja, ha az előfizetés egy másik címtárhoz van társítva.
> 
> Emellett az Azure Kubernetes-szolgáltatás (ak) fürtjét áthelyezheti egy másik előfizetésbe, vagy áthelyezheti a fürt tulajdonosának előfizetését egy új bérlőre, így a fürt elveszti a szerepkör-hozzárendelések és az egyszerű szolgáltatások jogosultságai miatti működését. További információ az AK-ról: [Azure Kubernetes Service (ak)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Előzetes teendők

Az előfizetés hozzárendelése vagy hozzáadása előtt el kell végeznie a következő feladatokat:

1. Tekintse át az alábbi módosításokat, és hogy milyen hatással lehet rájuk:

    - Azok a felhasználók, akik a RBAC használatával rendeltek szerepköröket, elvesztik a hozzáférést
    - A szolgáltatás-rendszergazda és a társ-rendszergazdák elvesztik a hozzáférést
    - Ha rendelkezik kulcstartókkal, nem lesznek elérhetők, és a társítás után ki kell javítani őket.
    - Ha bármilyen felügyelt identitással rendelkezik olyan erőforrásokhoz, mint például a Virtual Machines vagy a Logic Apps, újra engedélyeznie kell, vagy újra létre kell hoznia őket a társítás után.
    - Ha regisztrált Azure Stack van, akkor újra regisztrálnia kell a társítás után

1. Jelentkezzen be egy olyan fiókkal, amely a következőket használja:
    - [Tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör-hozzárendelést tartalmaz az előfizetéshez. További információ a tulajdonosi szerepkör hozzárendeléséről: az [Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
    - Az előfizetéshez társított aktuális könyvtárban és az új címtárban is megtalálható, ahol társítani szeretné az előfizetést. További információ a más címtárakhoz való hozzáférésről: [How do Azure Active Directory admins Add B2B Collaboration Users?](../b2b/add-users-administrator.md).

1. Győződjön meg arról, hogy nem használ Azure Cloud Service Providers (CSP) előfizetést (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), a Microsoft belső előfizetését (MS-AZR-0015P) vagy egy Microsoft Imagine-előfizetést (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Meglévő előfizetés társítása az Azure AD-címtárral

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [Azure Portal előfizetések lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Válassza a **címtár módosítása**lehetőséget.

    ![Előfizetések lap, a módosítási könyvtár lehetőség kiemelve](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Tekintse át a megjelenő figyelmeztetéseket, majd kattintson a **módosítás**gombra.

    ![Módosítsa a könyvtár lapot, és jelenítse meg a módosítani kívánt könyvtárat](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Az előfizetéshez tartozó címtár módosul, és a rendszer sikert jelző üzenetet kap.

    ![A címtár változásával kapcsolatos sikeres üzenet](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. A **címtár-kapcsoló** használatával lépjen az új könyvtárba. Több órát is igénybe vehet, hogy minden megfelelően megjelenjen. Ha úgy tűnik, hogy túl sokáig tart, ellenőrizze az áthelyezett előfizetés **globális előfizetési szűrőjét** , és győződjön meg róla, hogy nem rejtett. Előfordulhat, hogy ki kell jelentkeznie a Azure Portalból, és újra be kell jelentkeznie, hogy láthassa az új könyvtárat. 

    ![Címtár-kapcsoló oldal, a minta adataival](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetés könyvtárának módosítása szolgáltatás szintű művelet, így nem befolyásolja az előfizetés számlázási tulajdonjogát. A fiók rendszergazdája továbbra is módosíthatja a szolgáltatás rendszergazdáját a [Account Center](https://account.azure.com/subscriptions)webhelyről. Az eredeti könyvtár törléséhez át kell vinnie az előfizetés számlázási tulajdonosát egy új fiók Rendszergazdájába. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átadása másik fiókra](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Hozzárendelés utáni lépések
Miután hozzárendelt egy előfizetést egy másik címtárhoz, előfordulhat, hogy további lépéseket kell végrehajtania a műveletek folytatásához.

1. Ha rendelkezik kulcstartóval, módosítania kell a Key Vault bérlői AZONOSÍTÓját. További információ: [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../../key-vault/key-vault-subscription-move-fix.md).

2. Ha rendszerhez rendelt felügyelt identitásokat használ az erőforrásokhoz, újra engedélyeznie kell ezeket. Ha felhasználó által hozzárendelt felügyelt identitásokat használ, ezeket újra létre kell hoznia. A felügyelt identitások ismételt engedélyezése vagy újbóli létrehozása után újra létre kell hoznia az identitásokhoz rendelt engedélyeket. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).

3. Ha az előfizetést használó Azure Stack regisztrált, újra kell regisztrálnia. További információ: [Azure stack regisztrálása az Azure](/azure-stack/operator/azure-stack-registration)-ban.



## <a name="next-steps"></a>További lépések

- Új Azure AD-bérlő létrehozásához tekintse meg a következő témakört: [hozzáférés Azure Active Directory új bérlő létrehozásához](active-directory-access-create-new-tenant.md)

- Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- További információ a szerepkörök az Azure AD-ben való hozzárendeléséről: [címtárbeli szerepkörök társítása a felhasználókhoz Azure Active Directory](active-directory-users-assign-role-azure-portal.md) használatával
