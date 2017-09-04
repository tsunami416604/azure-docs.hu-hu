---
title: "Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz? | Microsoft Docs"
description: "Bejelentkezés a Microsoft Azure-ba és a kapcsolódó problémák, például az Azure-előfizetések és az Azure Active Directory közötti kapcsolat."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2f8cc774b6cc245a4000e5c6924a8bb817707bc
ms.contentlocale: hu-hu
ms.lasthandoff: 08/24/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)
Ez a cikk az Azure-előfizetések és az Azure Active Directory (Azure AD) közötti kapcsolatról tartalmaz információt.

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Az Azure-előfizetése és az Azure AD közötti kapcsolat
Az Azure-előfizetés megbízhatósági kapcsolatban áll az Azure AD-vel, ami azt jelenti, hogy ezt a címtárat megbízhatónak tekinti a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy megbízható címtárral rendelkezhet. 

Az előfizetés és a címtár közötti megbízhatósági kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn. Ha egy előfizetés lejár, akkor az előfizetéssel társított egyéb erőforrások hozzáférése is lejár. A címtár azonban elérhető marad az Azure-ban, így társíthat hozzá más előfizetéseket, és folytathatja a címtár felhasználóinak kezelését.

![az előfizetések társításának módját ábrázoló diagram](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Az Azure AD másképp működik, mint az Azure-előfizetés többi szolgáltatása. A többi Azure-szolgáltatás az Azure-előfizetésnek van alárendelve. Az Azure AD-ben látható tartalmak azonban nem változnak az előfizetés alapján. Az Azure AD a bejelentkezett felhasználó alapján engedélyezi a hozzáférést a címtárakhoz.

Minden felhasználó egyetlen saját címtárral rendelkezik, amely hitelesíti őt, de más címtárakban lehet vendég is. Az Azure AD-ben a felhasználó csak azokat a címtárakat látja, amelyeknek a felhasználói fiókja a tagja. A címtárak a helyszíni Active Directoryval is szinkronizálhatók.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Az Azure AD és a felhőszolgáltatásbeli előfizetések
Az Azure AD alapvető fontosságú címtár- és identitáskezelési funkciókat biztosít a Microsoft olyan felhőszolgáltatásai számára, mint például a következők:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Az Azure AD szolgáltatáshoz ingyenesen hozzáférhet, ha regisztrál bármelyik fenti Microsoft-felhőszolgáltatásra. Ha további Azure-előfizetéseket kíván hozzáadni egy Azure AD-címtárhoz, be kell jelentkeznie egy Microsoft-fiókkal. Ha például egy Microsoft-fiókkal regisztrál az Azure-ra, egy munkahelyi vagy iskolai fiókkal pedig egy másik Microsoft-felhőszolgáltatásra, akkor két Azure AD-példánya lesz:
1. Az Azure-előfizetés alapértelmezett címtára. Ehhez a címtárhoz vagy a további létrehozott címtárakhoz hozzáadhat további Azure-előfizetéseket, ha a Microsoft-fiókjával jelentkezik be, mert akkor az Azure hitelesíteni tudja.
2. A munkahelyi vagy iskolai fiók kezdőcímtára. Ha munkahelyi vagy iskolai fiókkal jelentkezik be az Azure-ba, nem adhat hozzá Azure-előfizetést a meglévő címtárhoz, mivel a munkahelyi vagy iskolai fiók nem hitelesíthető közvetlenül az Azure-ral. 
 
További információ az Azure-előfizetések rendszergazdáinak módosításáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Javaslatok az előfizetés és a címtár kezeléséhez
Az Azure-előfizetések rendszergazdai szerepkörei kezelik az Azure-előfizetéshez társított erőforrásokat. Ez a szakasz ismerteti az Azure-előfizetések rendszergazdái és az Azure AD-címtár rendszergazdái közti különbségeket. A rendszergazdai szerepkörökről és az előfizetés kezelésére való használatukról a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles.md) című szakaszban olvashat.

Alapértelmezés szerint a Szolgáltatás-rendszergazda szerepkörhöz van rendelve a regisztráláskor. Ha másoknak is ugyanezzel az előfizetéssel kell bejelentkezniük és elérniük a szolgáltatásokat, társadminisztrátorként felveheti őket. 

Az Azure AD különböző rendszergazdai szerepkörökkel rendelkezik a címtárral és az identitással kapcsolatos funkciók kezeléséhez. Egy címtár globális rendszergazdája például felhasználókat és csoportokat adhat a címtárhoz, vagy a felhasználók többtényezős hitelesítését követelheti meg. A címtárat létrehozó felhasználókhoz a globális rendszergazdai szerepkör van rendelve, és ők rendszergazdai szerepköröket rendelhetnek más felhasználókhoz. Az Azure AD rendszergazdai szerepköreit más szolgáltatások is használják, például az Office 365 és a Microsoft Intune. 

Az Azure-előfizetések rendszergazdái és az Azure AD-címtár rendszergazdái két különböző szerepkörnek számítanak. 
* Az Azure-előfizetés rendszergazdái kezelhetik az erőforrásokat az Azure-ban, és használhatják az Azure AD-t az Azure Portalon (mert az Azure portál maga is egy Azure-erőforrás). 
* A címtár rendszergazdái csak az Azure AD-címtár tulajdonságait kezelhetik.

Egy személy mindkét szerepkörrel rendelkezhet, de erre nincs szükség. Egy felhasználóhoz a címtár globális rendszergazdai szerepköre hozzárendelhető, de nem kell hozzárendelni az Azure-előfizetés szolgáltatás-rendszergazdai vagy társadminisztrátori szerepkörét. Ha a felhasználó nem egy előfizetés rendszergazdája, attól még bejelentkezhet az Azure Portalra, de nem kezelheti a portálon az előfizetéshez tartozó címtárakat. A felhasználó más eszközökkel kezelheti a címtárakat, például az Azure AD PowerShell vagy az Office 365 felügyeleti központ használatával.

## <a name="next-steps"></a>Következő lépések
* További információ az Azure-előfizetések rendszergazdáinak módosításáról: [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](active-directory-understanding-resource-access.md)
* A szerepkörök Azure AD-ben történő hozzárendeléséről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

