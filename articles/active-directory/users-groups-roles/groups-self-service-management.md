---
title: Önkiszolgáló csoportkezelés – Azure Active Directory beállítása |} A Microsoft Docs
description: Biztonsági vagy Office 365-csoportok létrehozása és kezelése az Azure Active Directoryban, valamint biztonsági és Office 365-csoporttagság igénylése
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5584e4ecc78a8805135cb3f36bd5f5cd7079129
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767073"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Önkiszolgáló csoportkezelés az Azure Active Directory beállítása 

Engedélyezheti a felhasználók létrehozása és kezelése a saját biztonsági vagy Office 365-csoportok az Azure Active Directoryban (Azure AD). A csoport tulajdonosának jóváhagyása vagy elutasítása tagsági kérelmeket és is csoporttagság delegálására. Az önkiszolgáló csoportkezelési szolgáltatások levelezési címmel rendelkező biztonsági csoportok vagy terjesztési listák esetében nem érhetők el. 

## <a name="self-service-group-membership-defaults"></a>Önkiszolgáló csoport tagsági alapértelmezései

Ha biztonsági csoportokat hoz létre az Azure Portalon vagy Azure AD PowerShell-lel, csak a csoport tulajdonosai tagságot is frissítheti. A létrehozott biztonsági csoportok a [hozzáférési panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) , és az összes Office 365-csoportok lehet csatlakozni az összes felhasználó számára, hogy tulajdonosa által jóváhagyott, vagy automatikusan jóváhagyta-e. A hozzáférési panelen módosíthatja a csoport létrehozásakor tagsági lehetőségek.

A létrehozott csoportok | Biztonsági csoport alapértelmezett viselkedése | Az Office 365-csoport alapértelmezett viselkedése
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Csak a tulajdonosok adhat hozzá tagokat<br>Látható, de nem lehet csatlakozni a hozzáférési panelen | Nyissa meg az összes felhasználó csatlakozni
[Azure Portal](https://portal.azure.com) | Csak a tulajdonosok adhat hozzá tagokat<br>Látható, de nem lehet csatlakozni a hozzáférési panelen<br>Tulajdonos nincs hozzárendelve automatikusan csoport létrehozásakor | Nyissa meg az összes felhasználó csatlakozni
[Hozzáférési panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Nyissa meg az összes felhasználó csatlakozni<br>Tagsági beállítások módosíthatók, ha a csoport létrehozása | Nyissa meg az összes felhasználó csatlakozni<br>Tagsági beállítások módosíthatók, ha a csoport létrehozása

## <a name="self-service-group-management-scenarios"></a>Önkiszolgáló csoport felügyeleti forgatókönyvek

* **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférésének kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzáférést ad az új csoportnak az alkalmazáshoz, és hozzáadja a csoporthoz az összes olyan személyt, aki már hozzáféréssel rendelkezik az alkalmazáshoz. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanezt az engedélyt biztosítja egy vezetőnek egy eltérő üzleti csoportban, akkor e személy hozzáférését is kezelheti saját csoport tagjainak. Nem a vállalat tulajdonosa, és a kezelő nem tekintheti meg vagy kezelheti egymás csoporttagságok. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
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
