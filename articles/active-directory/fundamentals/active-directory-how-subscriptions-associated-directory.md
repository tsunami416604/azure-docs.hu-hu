---
title: Meglévő Azure-előfizetés hozzáadása a bérlőhöz – Azure AD
description: Útmutató meglévő Azure-előfizetés Azure Active Directory bérlőhöz való hozzáadásához.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457926"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz

Az Azure-előfizetés megbízhatósági kapcsolatban áll Azure Active Directory (Azure AD) szolgáltatással. Egy előfizetés megbízik az Azure AD-ben a felhasználók, szolgáltatások és eszközök hitelesítéséhez.

Több előfizetés is megbízhat ugyanabban az Azure AD-címtárban. Az egyes előfizetések csak egyetlen címtárban bízhatnak meg.

Ha az előfizetése lejár, elveszíti a hozzáférést az előfizetéshez társított összes többi erőforráshoz. Az Azure AD-címtár azonban továbbra is az Azure-ban marad. A címtárat egy másik Azure-előfizetéssel is hozzárendelheti és kezelheti.

Az összes felhasználó rendelkezik egyetlen *kezdőkönyvtár* -címtárral a hitelesítéshez. A felhasználók más címtárakban is lehetnek vendégként. Az Azure AD-ben az egyes felhasználók otthoni és vendég könyvtára is megtekinthető.

> [!Important]
> Ha egy előfizetést társít egy másik címtárhoz, a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elveszítik a hozzáférésüket. A hagyományos előfizetés-rendszergazdák, köztük a szolgáltatásadminisztrátor és a társrendszergazdák is elveszítik a hozzáférésüket.
>
> A szabályzat-hozzárendelések is törlődnek az előfizetésből, ha az előfizetés egy másik címtárhoz van társítva.
>
> Ha áthelyezi az Azure Kubernetes-szolgáltatási (ak-) fürtöt egy másik előfizetésbe, vagy áthelyezi a fürt tulajdonosának előfizetését egy új bérlőre, akkor a fürt elveszti a szerepkör-hozzárendelések és a szolgáltatásnév jogosultságai miatti működőképességét. További információ az AK-ról: [Azure Kubernetes Service (ak)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Előkészületek

Az előfizetés hozzárendelése vagy hozzáadása előtt végezze el a következő feladatokat:

- Tekintse át az alábbi módosításokat, és hogy milyen hatással lehet rájuk:

  - Azok a felhasználók, akik a RBAC használatával rendeltek szerepköröket, elvesztik a hozzáférést
  - A szolgáltatás-rendszergazda és a társ-rendszergazdák elvesztik a hozzáférést
  - Ha rendelkezik kulcstartókkal, nem lesznek elérhetők, és a társítás után ki kell javítani őket.
  - Ha bármilyen felügyelt identitással rendelkezik olyan erőforrásokhoz, mint például a Virtual Machines vagy a Logic Apps, a társítás után újra engedélyeznie kell vagy újra létre kell hoznia azokat.
  - Ha regisztrált Azure Stack van, akkor újra regisztrálnia kell a társítás után

- Jelentkezzen be egy olyan fiókkal, amely a következőket használja:

  - [Tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör-hozzárendelést tartalmaz az előfizetéshez. További információ a tulajdonosi szerepkör hozzárendeléséről: az [Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
  - Az aktuális könyvtárban és az új könyvtárban is megtalálható. Az aktuális könyvtár társítva van az előfizetéshez. Az új könyvtárat az előfizetéshez társítja. További információ egy másik címtár elérésének beszerzéséről: [Azure Active Directory B2B együttműködési felhasználók hozzáadása a Azure Portal](../b2b/add-users-administrator.md).

- Győződjön meg arról, hogy nem használ Azure Cloud Service Providers (CSP) előfizetést (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), a Microsoft belső előfizetését (MS-AZR-0015P) vagy egy Microsoft Imagine-előfizetést (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Előfizetés hozzárendelése egy címtárhoz<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

A meglévő előfizetés Azure AD-címtárhoz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [Azure Portal előfizetések lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Válassza a **címtár módosítása**lehetőséget.

    ![Előfizetések lap, a módosítási könyvtár lehetőség kiemelve](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Tekintse át a megjelenő figyelmeztetéseket, majd kattintson a **módosítás**gombra.

    ![Módosítsa a könyvtár lapot, és jelenítse meg a módosítani kívánt könyvtárat](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Az előfizetéshez tartozó címtár módosul, és a rendszer sikert jelző üzenetet kap.

    ![A címtár változásával kapcsolatos sikeres üzenet](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

A **switch Directory** használatával lépjen az új könyvtárba. Több órát is igénybe vehet, hogy minden megfelelően megjelenjen. Ha úgy tűnik, hogy túl sokáig tart, ellenőrizze a **globális előfizetés szűrőjét**. Győződjön meg arról, hogy az áthelyezett előfizetés nem rejtett. Előfordulhat, hogy ki kell jelentkeznie a Azure Portal, majd újra be kell jelentkeznie az új könyvtár megtekintéséhez.

![Címtár-kapcsoló oldal, a minta adataival](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetés könyvtárának módosítása szolgáltatás szintű művelet, így nem befolyásolja az előfizetés számlázási tulajdonjogát. A fiók rendszergazdája továbbra is módosíthatja a szolgáltatás rendszergazdáját a [Account Center](https://account.azure.com/subscriptions)webhelyről. Az eredeti könyvtár törléséhez át kell vinnie az előfizetés számlázási tulajdonosát egy új fiók Rendszergazdájába. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átadása másik fiókra](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Társítás utáni lépések

Miután hozzárendelt egy előfizetést egy másik címtárhoz, előfordulhat, hogy a következő feladatokat kell végrehajtania a műveletek folytatásához:

- Ha rendelkezik kulcstartóval, módosítania kell a Key Vault bérlői AZONOSÍTÓját. További információ: [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../../key-vault/general/subscription-move-fix.md).

- Ha a rendszer által hozzárendelt felügyelt identitásokat használta az erőforrásokhoz, újra engedélyeznie kell ezeket az identitásokat. Ha felhasználó által hozzárendelt felügyelt identitásokat használt, ezeket az identitásokat újra létre kell hoznia. A felügyelt identitások ismételt engedélyezése vagy újbóli létrehozása után újra létre kell hoznia az identitásokhoz rendelt engedélyeket. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).

- Ha az előfizetést használó Azure Stack regisztrált, újra kell regisztrálnia. További információ: [Azure stack regisztrálása az Azure](/azure-stack/operator/azure-stack-registration)-ban.

## <a name="next-steps"></a>További lépések

- Új Azure AD-bérlő létrehozásához tekintse meg a rövid útmutató [: új bérlő létrehozása a Azure Active Directory-ben](active-directory-access-create-new-tenant.md)című témakört.

- Ha többet szeretne megtudni a Microsoft Azure az erőforrás-hozzáférés szabályozásáról, tekintse meg a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure RBAC-szerepköröket és az Azure ad rendszergazdai szerepköreit](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Ha többet szeretne megtudni a szerepkörök Azure AD-ben való hozzárendeléséről, tekintse meg a [rendszergazdai és nem rendszergazdai szerepkörök társítása a Azure Active Directoryhoz a felhasználók számára](active-directory-users-assign-role-azure-portal.md)című témakört.
