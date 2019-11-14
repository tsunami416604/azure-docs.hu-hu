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
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2f61792235d13ef801cffb3a69c32df54f058a9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073557"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directory-bérlővel

Az Azure-előfizetés megbízhatósági kapcsolatban áll Azure Active Directory (Azure AD) szolgáltatással. Egy előfizetés megbízik az Azure AD-ben a felhasználók, szolgáltatások és eszközök hitelesítéséhez.

Több előfizetés is megbízhat ugyanabban az Azure AD-címtárban. Az egyes előfizetések csak egyetlen címtárban bízhatnak meg.

Ha az előfizetés lejár, nem fér hozzá az előfizetéshez tartozó összes többi erőforrást. Az Azure AD-címtár azonban továbbra is az Azure-ban marad. A címtárat egy másik Azure-előfizetéssel is hozzárendelheti és kezelheti.

Az összes felhasználó rendelkezik egyetlen *kezdőkönyvtár* -címtárral a hitelesítéshez. A felhasználók más címtárakban is lehetnek vendégként. Mindkét az otthoni és a Vendég címtárakat látja minden felhasználó számára az Azure ad-ben.

> [!Important]
> Ha egy előfizetést társít egy másik címtárhoz, a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elveszítik a hozzáférésüket. A klasszikus előfizetési rendszergazdák, köztük a szolgáltatás-rendszergazda és a társ-rendszergazdák is elvesztik a hozzáférést.
>
> A szabályzat-hozzárendeléseket a rendszer az előfizetésből is eltávolítja, ha az előfizetés egy másik címtárhoz van társítva.
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

- Jelentkezzen be egy olyan fiókkal, amely:

  - [Tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör-hozzárendelést tartalmaz az előfizetéshez. További információ a tulajdonosi szerepkör hozzárendeléséről: az [Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
  - Az aktuális könyvtárban és az új könyvtárban is megtalálható. Az aktuális könyvtár társítva van az előfizetéshez. Az új könyvtárat az előfizetéshez társítja. További információ egy másik címtár elérésének beszerzéséről: [Azure Active Directory B2B együttműködési felhasználók hozzáadása a Azure Portal](../b2b/add-users-administrator.md).

- Győződjön meg arról, hogy nem használja az Azure Cloud Service szolgáltatók (CSP) (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P) előfizetést, a Microsoft Internal (MS-AZR - 0015P) előfizetést vagy a Microsoft Imagine-(MS-AZR - 0144P) előfizetést.

## Előfizetés hozzárendelése egy címtárhoz<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

A meglévő előfizetés Azure AD-címtárhoz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [az Azure portal előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Válassza ki **módosítsa a könyvtárat**.

    ![Előfizetések lapon, a módosítás directory opció kiemelésével](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Tekintse át a figyelmeztetéseket, amelyek jelenik meg, és válassza ki **módosítás**.

    ![Módosítsa a címtár lapján módosítsa a könyvtárat megjelenítése](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    A címtár megváltozott az előfizetés, és megjelenik a sikert jelző üzenet.

    ![A címtár változásával kapcsolatos sikeres üzenet](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

A **switch Directory** használatával lépjen az új könyvtárba. Több órát is igénybe vehet, hogy minden megfelelően megjelenjen. Ha úgy tűnik, hogy túl sokáig tart, ellenőrizze a **globális előfizetés szűrőjét**. Győződjön meg arról, hogy az áthelyezett előfizetés nem rejtett. Előfordulhat, hogy ki kell jelentkeznie a Azure Portal, majd újra be kell jelentkeznie az új könyvtár megtekintéséhez.

![Címtár-kapcsoló oldal, a minta adataival](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetés címtárának módosítása egy szolgáltatásiszint-művelet, így ez nincs hatással az előfizetés számlázási tulajdonjogát. A fiók rendszergazdája továbbra is módosíthatja a szolgáltatás-rendszergazda, a [Account Center](https://account.azure.com/subscriptions). Az eredeti könyvtár törléséhez át kell vinnie az előfizetés számlázási tulajdonosát egy új fiók Rendszergazdájába. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átadása másik fiókra](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Társítás utáni lépések

Miután hozzárendelt egy előfizetést egy másik címtárhoz, előfordulhat, hogy a következő feladatokat kell végrehajtania a műveletek folytatásához:

- Ha rendelkezik kulcstartóval, módosítania kell a Key Vault bérlői AZONOSÍTÓját. További információ: [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../../key-vault/key-vault-subscription-move-fix.md).

- Ha a rendszer által hozzárendelt felügyelt identitásokat használta az erőforrásokhoz, újra engedélyeznie kell ezeket az identitásokat. Ha felhasználó által hozzárendelt felügyelt identitásokat használt, ezeket az identitásokat újra létre kell hoznia. A felügyelt identitások ismételt engedélyezése vagy újbóli létrehozása után újra létre kell hoznia az identitásokhoz rendelt engedélyeket. További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../managed-identities-azure-resources/overview.md).

- Ha az előfizetést használó Azure Stack regisztrált, újra kell regisztrálnia. További információ: [Azure stack regisztrálása az Azure](/azure-stack/operator/azure-stack-registration)-ban.

## <a name="next-steps"></a>További lépések

- Új Azure AD-bérlő létrehozásához tekintse meg a rövid útmutató [: új bérlő létrehozása a Azure Active Directory-ben](active-directory-access-create-new-tenant.md)című témakört.

- Ha többet szeretne megtudni a Microsoft Azure az erőforrás-hozzáférés szabályozásáról, tekintse meg a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure RBAC-szerepköröket és az Azure ad rendszergazdai szerepköreit](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Ha többet szeretne megtudni a szerepkörök Azure AD-ben való hozzárendeléséről, tekintse meg a [rendszergazdai és nem rendszergazdai szerepkörök társítása a Azure Active Directoryhoz a felhasználók számára](active-directory-users-assign-role-azure-portal.md)című témakört.
