---
title: Az Azure AD önkiszolgáló csoportkezelési szolgáltatás beállítása |} A Microsoft Docs
description: Biztonsági vagy Office 365-csoportok létrehozása és kezelése az Azure Active Directoryban, valamint biztonsági és Office 365-csoporttagság igénylése
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: get-started-article
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-jan
ms.openlocfilehash: 6402b2f9b76f476e72cc017f9b2461cb3366b4cc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191499"
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre

A felhasználók saját biztonsági vagy Office 365-csoportokat hozhatnak létre és kezelhetnek az Azure Active Directoryban (Azure AD-ben). A felhasználók ezenkívül biztonsági és Office 365-csoporttagságot igényelhetnek, amelyet ezután a csoport tulajdonosa elfogadhat vagy elutasíthat. A csoporttagság napi szintű felügyelete olyan személyeknek adható ki, akik tisztában vannak az adott tagság üzleti környezetével. Az önkiszolgáló csoportkezelési szolgáltatások kizárólag biztonsági és Office 365-csoportok esetében érhetőek el, levelezési címmel rendelkező biztonsági csoportok vagy terjesztési listák esetében nem.

Az önkiszolgáló csoportkezelési szolgáltatások két forgatókönyvet: 

* **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférésének kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzáférést ad az új csoportnak az alkalmazáshoz, és hozzáadja a csoporthoz az összes olyan személyt, aki már hozzáféréssel rendelkezik az alkalmazáshoz. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanezt az engedélyt biztosítja egy vezetőnek egy eltérő üzleti csoportban, akkor a személy a saját felhasználóinak a hozzáférését is kezelheti. Sem a vállalat tulajdonosa, sem pedig a vezető nem tekintheti meg vagy kezelheti egymás felhasználóit. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
* **Önkiszolgáló csoportkezelés** – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel. Ezek a felhasználók szeretnének egymás csapatának hozzáférést adni a saját webhelyükhöz. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. A SaaS-alkalmazás rendszergazdája adhat hozzáférési jogot a SharePoint Online webhelyhez tartozó alkalmazáshoz. Ettől kezdve az elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## <a name="make-a-group-available-for-user-self-service"></a>Csoport elérhetővé tétele önkiszolgáló felhasználói tevékenységhez
1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Felhasználók és csoportok** lehetőséget, majd válassza a **Csoportbeállítások** elemet.
3. Állítsa az **Önkiszolgáló csoportkezelés engedélyezve** elemet **Igen** értékűre.
4. Állítsa **A felhasználók létrehozhatnak biztonsági csoportokat** vagy **A felhasználók létrehozhatnak Office 365-csoportokat** elemet **Igen** értékűre.
  * Ha ezek a beállítások engedélyezve vannak, a címtárban lévő összes felhasználó létrehozhat új biztonsági csoportokat, és tagokat vehet fel ezekbe a csoportokba. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen. Ha pedig a csoport házirend-beállítása ezt lehetővé teszi, más felhasználók is kérhetik a felvételüket a csoportokba. 
  * Ha ezek a beállítások le vannak tiltva, a felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azokat a meglévő csoportokat, amelyeknek a tulajdonosai. Ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

A **Biztonsági csoportok kezelésére jogosult felhasználók** és az **Office 365-csoportok kezelésére jogosult felhasználók** beállítás használatával még részletesebben vezérelheti a felhasználók önkiszolgáló csoportkezelési hozzáférését. Ha **A felhasználók létrehozhatnak csoportokat** beállítás engedélyezve van, a bérlőben lévő összes felhasználó létrehozhat új csoportokat, és tagokat vehet fel ezekbe a csoportokba. Ha ezeket **Néhány** értékre állítja be, a csoportkezelést egyes felhasználói csoportokra korlátozza. Ha ez a kapcsoló **Some** értékre (bizonyos felhasználókra) van állítva, hozzá kell adnia a felhasználókat az SSGMSecurityGroupsUsers csoporthoz, mielőtt új csoportokat hozhatnának létre és felhasználókat adhatnának hozzájuk. Ha az **Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók** és az **Office 365 csoportok kezelésére jogosult felhasználók** **Minden felhasználó** értékre van beállítva, a bérlő összes felhasználója számára lehetővé válik új csoportok létrehozása.

A **Biztonsági csoportok kezelésére jogosult csoport** és az **Office 365 csoportok kezelésére jogosult csoport** használatával megadhat egyetlen csoportot, amelynek tagjai használhatják az önkiszolgáló funkciót.

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
