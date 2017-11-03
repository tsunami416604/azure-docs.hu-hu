---
title: "Az az Azure-erőforrások hozzáférésének megismerése |} Microsoft Docs"
description: "Ez a témakör a fogalmakat ismerteti az előfizetés rendszergazdáihoz segítségével vezérelheti, erőforrás-hozzáférés az Azure-portálon"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e5ac068e37d921530272e5eb3dc76d976a86a742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-resource-access-in-azure"></a>Az az Azure-erőforrások hozzáférésének megismerése

Hozzáférés-vezérlés az Azure-ban elindul egy számlázási szempontjából. Azure-fiók esetén érhető el, látogasson el a tulajdonosa a [Azure Accounts Center](https://account.windowsazure.com/subscriptions), a fiók rendszergazdai AA van. Előfizetések számlázási tárolója, de úgy is működnek, mint a biztonsági határ: minden előfizetés rendelkezik egy szolgáltatás rendszergazdai (SA) számára adja hozzá, távolítsa el, és módosítsa az adott előfizetés Azure-erőforrások használatával a [Azure-portálon](https://portal.azure.com/). Az alapértelmezett biztonsági Társítás az új előfizetés az AA, de az AA módosíthatja a rendszergazdai (SA) az Azure Accounts Center a.

<br><br>![Azure-fiókra][1]

Előfizetések is könyvtár van társítva. A directory egy felhasználók csoportját határozza meg. Ezek lehetnek a felhasználók a munkahelyi vagy iskolai, hozott létre a könyvtár, vagy külső felhasználók számára (Ez azt jelenti, hogy a Microsoft Accounts) is lehetnek. Az előfizetések által hozzárendelt szolgáltatás-rendszergazdai (SA) vagy Társadminisztrátorának (CA); directory felhasználók egy részhalmazát érhetők el az egyetlen kivétel, hogy az örökölt összetevők miatt, Microsoft Accounts (korábbi nevén Windows Live ID Azonosítóval) rendelhető rendszergazdai (SA) vagy a CA anélkül, hogy a címtárban található.

<br><br>![Hozzáférés-vezérlés az Azure-ban][2]

A klasszikus Azure portálon belül funkciók lehetővé teszik a Bejelentkezés Microsoft-Account segítségével módosítsa a könyvtárat, amelyhez előfizetéssel társítva van a biztonsági társítások a **könyvtár szerkesztése** parancs a  **Előfizetések** lap **beállítások**. Vegye figyelembe, hogy ez a művelet hatással vannak az előfizetésben a hozzáférés-vezérlés.

> [!NOTE]
> A **könyvtár szerkesztése** parancs a klasszikus Azure portálon munkahelyi bejelentkezett felhasználók számára nem érhető el, vagy iskolai fiókhoz, mivel ezek a fiókok bejelentkezhetnek a, csak azt a könyvtárat, amelyhez tartoznak.
> 
> 

<br><br>![Egyszerű felhasználói bejelentkezési folyamat][3]

Az egyszerű esetében egy szervezet (Contoso) például számlázási kényszerítése, és hozzáférés-vezérlés ugyanazokat az előfizetések között. Ez azt jelenti, hogy a könyvtár egy olyan Azure-fiók tulajdonában lévő előfizetések társítva. A klasszikus Azure portálra sikeres bejelentkezés esetén a felhasználók látni két gyűjtemények erőforrások (az előző ábrán narancssárga kitaláltak):

* Könyvtárak ahol a felhasználói fiók létezik-e (vagy fel van véve egy idegen rendszerbiztonsági tag forrása). Vegye figyelembe, hogy a könyvtár bejelentkezési, akkor nem kapcsolódik a számítási, ezért a címtárak mindig megjelennek, függetlenül attól, hol jelentkezett.
* Előfizetések, amelyek hozzárendelve a bejelentkezéshez használt könyvtár, és amely a felhasználó elérheti (Ha olyan rendszergazdai (SA) vagy a CA) részét képező erőforrásokat.

<br><br>![A felhasználó több előfizetések és könyvtárak][4]

Több címtárban előfizetések felhasználóknál váltson az aktuális környezetben, a klasszikus Azure portál használatával az előfizetés-szűrőt. A színfalak könyvtárt az eredmény egy külön bejelentkezési, de ez teszi lehetővé zökkenőmentesen egyszeri bejelentkezés (SSO).

Lehet, hogy a műveletek, például az erőforrások áthelyezése másik előfizetések nehezebb előfizetések egyetlen nézetének miatt. Az erőforrás-áthelyezés végrehajtásához az első használat szükség lehet a **könyvtár szerkesztése** parancsot az előfizetések oldalán a **beállítások** rendelje hozzá a az előfizetések ugyanabban a könyvtárban.

## <a name="next-steps"></a>Következő lépések
* Az Azure-előfizetések rendszergazdáinak módosításáról további információ: [Azure-rendszergazdai szerepkörök felvétele vagy módosítása](../billing/billing-add-change-azure-subscription-administrator.md)
* Hogyan Azure Active Directory vonatkozik-e az Azure-előfizetéshez további információkért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directoryval](active-directory-how-subscriptions-associated-directory.md)
* A szerepkörök Azure AD-ben történő hozzárendeléséről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles.md).

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png
