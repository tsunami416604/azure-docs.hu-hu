---
title: Az az Azure-erőforrások hozzáférésének megismerése |} Microsoft Docs
description: Ez a témakör a fogalmakat ismerteti az előfizetés rendszergazdáihoz segítségével vezérelheti, erőforrás-hozzáférés az Azure-portálon
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro;
ms.openlocfilehash: 3be2026e480f33a7b8d403d375614310695613da
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266720"
---
# <a name="understanding-resource-access-in-azure"></a>Az az Azure-erőforrások hozzáférésének megismerése

Hozzáférés-vezérlés az Azure-ban elindul egy számlázási szempontjából. Azure-fiók esetén érhető el, látogasson el a tulajdonosa a [Azure Account Center](https://account.azure.com), a fiók rendszergazdai AA van. Előfizetések számlázási tárolója, de úgy is működnek, mint a biztonsági határ: minden előfizetés rendelkezik egy szolgáltatás rendszergazdai (SA) számára adja hozzá, távolítsa el, és módosítsa az adott előfizetés Azure-erőforrások használatával a [Azure-portálon](https://portal.azure.com/). Az alapértelmezett biztonsági Társítás az új előfizetés az AA, de az AA módosíthatja a biztonsági Társítás az az Azure Account Center.

<br><br>![Azure-fiókra][1]

Előfizetések is könyvtár van társítva. A directory egy felhasználók csoportját határozza meg. Ezek lehetnek a felhasználók a munkahelyi vagy iskolai, hozott létre a könyvtár, vagy külső vendégfelhasználók el. Az előfizetések által hozzárendelt szolgáltatás-rendszergazdai (SA) vagy Társadminisztrátorának (CA); directory felhasználók egy részhalmazát érhetők el az egyetlen kivétel, hogy az örökölt összetevők miatt, Microsoft-fiók (korábbi nevén Windows Live ID Azonosítóval) rendelhető rendszergazdai (SA) vagy a CA anélkül, hogy a címtárban található.

<br><br>![Hozzáférés-vezérlés az Azure-ban][2]

Az Azure-portálon belül funkció lehetővé teszi, hogy a bejelentkezett egy Microsoft Account segítségével módosítsa a könyvtárat, amelyhez társítva egy előfizetési SA-k. Ez a művelet hatással vannak a hozzáférés-vezérlés előfizetésben rendelkezik.

<br><br>![Egyszerű felhasználói bejelentkezési folyamat][3]

Az egyszerű esetében egy szervezet (Contoso) például számlázási kényszerítése, és hozzáférés-vezérlés ugyanazokat az előfizetések között. Ez azt jelenti, hogy a könyvtár egy olyan Azure-fiók tulajdonában lévő előfizetések társítva. Az Azure-portálon való sikeres bejelentkezés esetén a felhasználók látni két gyűjtemények erőforrások (az előző ábrán narancssárga kitaláltak):

* Könyvtárak ahol a felhasználói fiók létezik-e (vagy fel van véve egy idegen rendszerbiztonsági tag forrása). Vegye figyelembe, hogy a könyvtár bejelentkezési, akkor nem kapcsolódik a számítási, ezért a címtárak mindig megjelennek, függetlenül attól, hol jelentkezett.
* Előfizetések, amelyek hozzárendelve a bejelentkezéshez használt könyvtár, és amely a felhasználó elérheti (Ha olyan rendszergazdai (SA) vagy a CA) részét képező erőforrásokat.

<br><br>![A felhasználó több előfizetések és könyvtárak][4]

Több címtárban előfizetések felhasználók eltávolíthatnak-Váltás az aktuális környezetben, az Azure portál használatával az előfizetés-szűrőt. A színfalak könyvtárt az eredmény egy külön bejelentkezési, de ez teszi lehetővé zökkenőmentesen egyszeri bejelentkezés (SSO).

Lehet, hogy a műveletek, például az erőforrások áthelyezése másik előfizetések nehezebb előfizetések egyetlen nézetének miatt. Az erőforrás-áthelyezés végrehajtásához az első használat szükség lehet a **könyvtár szerkesztése** parancsot az előfizetések oldalán a **beállítások** rendelje hozzá a az előfizetések ugyanabban a könyvtárban.

## <a name="next-steps"></a>További lépések
* Az Azure-előfizetések rendszergazdáinak módosításáról további információ: [Azure-rendszergazdai szerepkörök felvétele vagy módosítása](../billing/billing-add-change-azure-subscription-administrator.md)
* Hogyan Azure Active Directory vonatkozik-e az Azure-előfizetéshez további információkért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directoryval](../active-directory/active-directory-how-subscriptions-associated-directory.md)
* A szerepkörök Azure AD-ben történő hozzárendeléséről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

<!--Image references-->
[1]: ./media/rbac-and-directory-admin-roles/IC707931.png
[2]: ./media/rbac-and-directory-admin-roles/IC707932.png
[3]: ./media/rbac-and-directory-admin-roles/IC707933.png
[4]: ./media/rbac-and-directory-admin-roles/IC707934.png
