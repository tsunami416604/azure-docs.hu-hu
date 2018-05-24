---
title: Meglévő Azure-előfizetés hozzáadása az Azure AD-címtárhoz | Microsoft Docs
description: Meglévő előfizetés hozzáadása az Azure AD-címtárhoz
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6664a2b9bb6375314090bf4dae9190c7d52565d2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz

Ez a cikk az Azure-előfizetések és az Azure Active Directory (Azure AD) közötti kapcsolatról tartalmaz információt, illetve arról, hogy miként adható hozzá egy meglévő előfizetés az Azure AD-címtárhoz. Az Azure-előfizetés megbízhatósági kapcsolatban áll az Azure AD-vel, ami azt jelenti, hogy ezt a címtárat megbízhatónak tekinti a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy megbízható címtárral rendelkezhet. 

Az előfizetés és a címtár közötti megbízhatósági kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn. Ha egy előfizetés lejár, akkor az előfizetéssel társított egyéb erőforrások hozzáférése is lejár. Az Azure AD-címtár azonban elérhető marad az Azure-ban, így társíthat hozzá egy másik előfizetést, és az új előfizetéssel kezelheti a címtár felhasználóit.

Minden felhasználó egyetlen saját címtárral rendelkezik, amely hitelesíti őt, de más címtárakban lehet vendég is. Az Azure AD-ben a felhasználó azokat a címtárakat látja, amelyeknek a felhasználói fiókja a tagja vagy vendége.

## <a name="before-you-begin"></a>Előkészületek

* Olyan fiókkal kell bejelentkeznie, amely RBAC tulajdonosi hozzáféréssel rendelkezik az előfizetéshez.
* Egy olyan fiókkal kell bejelentkeznie, amely az előfizetéshez jelenleg társított címtárban és abban a címtárban is létezik, amelyhez az előfizetést hozzá szeretné rendelni. További információ a más címtárakhoz való hozzáférésről: [Hogyan adhat hozzá az Azure Active Directory-rendszergazda vállalatközi együttműködési felhasználókat?](active-directory-b2b-admin-add-users.md)
* Ez a szolgáltatás nem érhető el CSP- (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P) és Microsoft Imagine- (MS-AZR-0144P) előfizetések esetén.

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Meglévő előfizetés társítása az Azure AD-címtárral

1. Jelentkezzen be, és válasszon ki egy előfizetést az [Azure Portal Előfizetés oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Kattintson a **Címtár módosítása** elemre.

    ![A Címtár módosítása gomb képernyőképe](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Tekintse át a figyelmeztetéseket. A címtár módosításakor minden [Szerepköralapú hozzáférés-vezérlés (RBAC-)](../role-based-access-control/role-assignments-portal.md) felhasználó és minden előfizetés-rendszergazda elveszíti a hozzárendelt hozzáférését.
4. Válasszon egy címtárat.

    ![A Címtár módosítása felhasználói felület képernyőképe](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Kattintson a **Módosítás** elemre.
6. Sikerült! A címtárváltóval lépjen az új címtárra. Akár 10 percig is eltarthat, amíg minden megfelelően megjelenik.

    ![A sikeres címtármódosítást jelző értesítés képernyőképe](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![A váltó képernyőképe](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


Az előfizetés áthelyezése az összes Azure Key Vaultra is kihat, ezért a műveletek folytatása előtt [módosítsa a kulcstároló bérlőazonosítóját](../key-vault/key-vault-subscription-move-fix.md).

Az előfizetés címtárának módosítása szolgáltatásszintű művelet. Ez nincs hatással az előfizetés számlázási tulajdonjogára, és a fiók rendszergazdája továbbra is módosíthatja a szolgáltatás rendszergazdáját a [Fiókközpontban](https://account.azure.com/subscriptions). Ha törölni kívánja az eredeti címtárat, át kell adnia az előfizetés számlázási tulajdonjogát egy új fiókrendszergazdának. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>További lépések

* További információ új Azure AD-címtár ingyenes létrehozásáról: [Azure Active Directory-bérlő beszerzése](develop/active-directory-howto-tenant.md)
* További információ az Azure-előfizetések számlázási tulajdonjogának átadásáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](../role-based-access-control/rbac-and-directory-admin-roles.md)
* A szerepkörök Azure AD-ben történő hozzárendeléséről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
