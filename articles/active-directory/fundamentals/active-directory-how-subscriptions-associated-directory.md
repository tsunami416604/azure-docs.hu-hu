---
title: Meglévő Azure-előfizetés hozzáadása a bérlőhöz – Azure AD
description: Útmutató egy meglévő Azure-előfizetés azure-beli Active Directory-bérlőhöz való hozzáadásáról.
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457926"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz

Az Azure-előfizetés megbízhatósági kapcsolatban áll az Azure Active Directoryval (Azure AD). Az előfizetés megbízik az Azure AD-ben a felhasználók, szolgáltatások és eszközök hitelesítéséhez.

Több előfizetés megbízhat ugyanabban az Azure AD-címtárban. Minden előfizetés csak egyetlen könyvtárban bízhat meg.

Ha előfizetése lejár, elveszíti a hozzáférést az előfizetéshez társított összes többi erőforráshoz. Azonban az Azure AD könyvtár marad az Azure-ban. A címtár társítható és kezelheti egy másik Azure-előfizetés használatával.

Minden felhasználó rendelkezik egyetlen *kezdőkönyvtárral* a hitelesítéshez. A felhasználók más könyvtárak vendégei is lehetnek. Az Azure AD-ben megtekintheti az egyes felhasználók otthoni és vendégkönyvtárait is.

> [!Important]
> Amikor egy előfizetést egy másik címtárhoz társít, a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/role-assignments-portal.md) használatával szerepkörrel rendelkező felhasználók elveszítik a hozzáférésüket. A hagyományos előfizetés-rendszergazdák, köztük a szolgáltatásadminisztrátor és a társrendszergazdák is elveszítik a hozzáférésüket.
>
> A szabályzat-hozzárendelések is törlődnek az előfizetésből, ha az előfizetés egy másik címtárhoz van társítva.
>
> Az Azure Kubernetes-szolgáltatás (AKS) fürtjének áthelyezése egy másik előfizetésre, vagy a fürttulajdonában lévő előfizetés áthelyezése egy új bérlőre, a fürt működése megszakad az elveszett szerepkör-hozzárendelések és egyszerű szolgáltatásjogok miatt. Az AKS-ről további információt az [Azure Kubernetes-szolgáltatás (AKS)](https://docs.microsoft.com/azure/aks/)című témakörben talál.


## <a name="before-you-begin"></a>Előkészületek

Az előfizetés társítása vagy hozzáadása előtt tegye a következő feladatokat:

- Tekintse át az alábbi változások listáját és azt, hogy ez milyen hatással lehet Önre:

  - Az RBAC-ot használó szerepkörökhöz rendelt felhasználók elveszítik a hozzáférésüket
  - A szolgáltatásadminisztrátor és a társrendszergazdák elveszítik a hozzáférést
  - Ha van kulcstartója, azok nem érhetők el, és az egyesítés után ki kell javítania őket.
  - Ha olyan erőforrások felügyelt identitásokkal rendelkezik, mint például a virtuális gépek vagy a logikai alkalmazások, újra engedélyeznie kell, vagy újra létre kell hoznia őket a társítás után.
  - Ha regisztrált Azure Stack-et regisztrál, újra regisztrálnia kell azt az asszociáció után

- Jelentkezzen be olyan fiókkal, amely:

  - Tulajdonosszerepkör-hozzárendeléssel rendelkezik az előfizetéshez. [Owner](../../role-based-access-control/built-in-roles.md#owner) A tulajdonosi szerepkör hozzárendeléséről az [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával című témakörben](../../role-based-access-control/role-assignments-portal.md)talál további információt.
  - Az aktuális könyvtárban és az új könyvtárban is létezik. Az aktuális könyvtár az előfizetéshez van társítva. Az új könyvtárat társítja az előfizetéshez. Ha további információra van szükség egy másik címtár eléréséről, olvassa [el az Azure Active Directory B2B együttműködési felhasználóinak hozzáadása az Azure Portalon című témakört.](../b2b/add-users-administrator.md)

- Győződjön meg arról, hogy nem azure-felhőszolgáltatók (CSP) előfizetést (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft Belső előfizetést (MS-AZR-0015P) vagy Microsoft Imagine-előfizetést (MS-AZR-0144P) használ.

## <a name="associate-a-subscription-to-a-directory"></a>Előfizetés társítása címtárhoz<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Ha meglévő előfizetést szeretne társítani az Azure AD-címtárhoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést az [Azure Portal Előfizetések lapján.](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

1. Válassza **a Könyvtár módosítása**lehetőséget.

    ![Előfizetések lap, a Könyvtár módosítása beállítás kiemelve](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Tekintse át a megjelenő figyelmeztetéseket, majd válassza a **Módosítás gombot.**

    ![A könyvtárlap módosítása, a módosításhoz tankönyvtár megjelenítése](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    A könyvtár módosul az előfizetéshez, és sikeres üzenetet kap.

    ![Sikeres üzenet a címtármódosításról](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

A **Switch könyvtár segítségével** lépjen az új könyvtárba. Több órába is beletelhet, amíg minden megfelelően megjelenik. Ha úgy tűnik, hogy túl sokáig tart, ellenőrizze a **Globális előfizetésszűrőt.** Győződjön meg arról, hogy az áthelyezett előfizetés nincs elrejtve. Előfordulhat, hogy ki kell jelentkeznie az Azure Portalról, és újra be kell jelentkeznie az új címtár megtekintéséhez.

![Címtárváltó lapja, mintaadatokkal](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Az előfizetési címtár módosítása szolgáltatásszintű művelet, így nem befolyásolja az előfizetés számlázási tulajdonjogát. A fiókadminisztrátor továbbra is módosíthatja a szolgáltatásrendszergazdát a [Fiókközpontból.](https://account.azure.com/subscriptions) Az eredeti könyvtár törléséhez át kell vinnie az előfizetés számlázási tulajdonjogát egy új fiókkezelőnek. Ha többet szeretne tudni a számlázási tulajdonjog átviteléről, olvassa el az [Azure-előfizetés tulajdonjogának átadása másik fiókba](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Társítás utáni lépések

Miután egy előfizetést egy másik címtárhoz társít, előfordulhat, hogy a műveletek folytatásához a következő feladatokat kell elvégeznie:

- Ha rendelkezik kulcstartókkal, módosítania kell a key vault-bérlői azonosítóját. További információ: [Kulcstartó bérlői azonosító módosítása előfizetés áthelyezése után.](../../key-vault/general/subscription-move-fix.md)

- Ha a rendszer által hozzárendelt felügyelt identitások erőforrások, újra engedélyeznie kell ezeket az identitásokat. Ha felhasználó által hozzárendelt felügyelt identitásokat használt, újra létre kell hoznia ezeket az identitásokat. A felügyelt identitások újbóli engedélyezése vagy újbóli létrehozása után újra létre kell hoznia az identitásokhoz rendelt engedélyeket. További információ: [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)

- Ha regisztrált egy Azure Stack et ezzel az előfizetéssel, újra regisztrálnia kell. További információ: [Register Azure Stack with Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>További lépések

- Új Azure AD-bérlő létrehozásához olvassa [el a rövid útmutató: Új bérlő létrehozása az Azure Active Directoryban című témakört.](active-directory-access-create-new-tenant.md)

- Ha többet szeretne megtudni arról, hogy a Microsoft Azure hogyan szabályozza az erőforrás-hozzáférést, olvassa el a [Klasszikus előfizetéses rendszergazdai szerepkörök, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepkörök című témakört.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Ha többet szeretne tudni aszerepkörök Azure AD-ben való hozzárendeléséről, olvassa [el a Rendszergazdai és nem rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryval rendelkező felhasználókhoz című témakört.](active-directory-users-assign-role-azure-portal.md)
