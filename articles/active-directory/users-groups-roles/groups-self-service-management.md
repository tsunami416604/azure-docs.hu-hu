---
title: Önkiszolgáló csoport felügyeletének beállítása – Azure Active Directory | Microsoft Docs
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
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097696"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Önkiszolgáló csoport felügyeletének beállítása Azure Active Directory 

Lehetővé teheti a felhasználók számára, hogy saját biztonsági csoportokat vagy Office 365-csoportokat hozzanak létre és kezeljenek Azure Active Directoryban (Azure AD). A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoport felügyeleti funkciói nem érhetők el levelezésre képes biztonsági csoportokhoz vagy terjesztési listához.

## <a name="self-service-group-membership-defaults"></a>Önkiszolgáló csoporttagság alapértelmezett értékei

Ha a biztonsági csoportokat a Azure Portal vagy az Azure AD PowerShell használatával hozza létre, akkor csak a csoport tulajdonosai frissíthetik a tagságot. A [hozzáférési panelen](https://account.activedirectory.windowsazure.com/r#/joinGroups) létrehozott biztonsági csoportok és az összes Office 365-csoport minden felhasználó számára elérhető, függetlenül attól, hogy tulajdonosa által jóváhagyott vagy automatikusan jóváhagyott-e. A hozzáférési panelen módosíthatja a tagsági beállításokat a csoport létrehozásakor.

Létrehozott csoportok | Biztonsági csoport alapértelmezett viselkedése | Office 365-csoport alapértelmezett viselkedése
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Csak a tulajdonosok adhatnak hozzá tagokat<br>Látható, de nem érhető el a hozzáférési panelen való csatlakozáshoz | Megnyitás az összes felhasználóhoz való csatlakozáshoz
[Azure Portal](https://portal.azure.com) | Csak a tulajdonosok adhatnak hozzá tagokat<br>Látható, de nem érhető el a hozzáférési panelen való csatlakozáshoz<br>A tulajdonos nincs automatikusan hozzárendelve a csoport létrehozásakor | Megnyitás az összes felhasználóhoz való csatlakozáshoz
[Hozzáférési panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Megnyitás az összes felhasználóhoz való csatlakozáshoz<br>A tagsági beállítások megváltoztathatók a csoport létrehozásakor | Megnyitás az összes felhasználóhoz való csatlakozáshoz<br>A tagsági beállítások megváltoztathatók a csoport létrehozásakor

## <a name="self-service-group-management-scenarios"></a>Önkiszolgáló csoportos felügyeleti forgatókönyvek

* **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférésének kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzárendeli a hozzáférést az alkalmazáshoz az új csoporthoz, és hozzáadja a csoportot az alkalmazáshoz már hozzáférő összes személyhez. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanezt az engedélyt egy másik üzleti csoportban lévő felettesnek adja meg, akkor a felhasználó a saját csoporttagok számára is kezelheti a hozzáférést. Sem az üzleti tulajdonos, sem a felettes nem tekintheti meg és nem kezelheti egymás csoportjának tagságait. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
* **Önkiszolgáló csoportkezelés** – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel. Ezek a felhasználók szeretnének egymás csapatának hozzáférést adni a saját webhelyükhöz. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. A SaaS-alkalmazás rendszergazdája adhat hozzáférési jogot a SharePoint Online webhelyhez tartozó alkalmazáshoz. Ettől kezdve az elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## <a name="make-a-group-available-for-user-self-service"></a>Csoport elérhetővé tétele önkiszolgáló felhasználói tevékenységhez

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Felhasználók és csoportok** lehetőséget, majd válassza a **Csoportbeállítások** elemet.
3. Állítsa az **Önkiszolgáló csoportkezelés engedélyezve** elemet **Igen** értékűre.
4. Állítsa **A felhasználók létrehozhatnak biztonsági csoportokat** vagy **A felhasználók létrehozhatnak Office 365-csoportokat** elemet **Igen** értékűre.
   * Ha ezek a beállítások engedélyezve vannak, a címtárban lévő összes felhasználó létrehozhat új biztonsági csoportokat, és tagokat vehet fel ezekbe a csoportokba. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen. Ha pedig a csoport házirend-beállítása ezt lehetővé teszi, más felhasználók is kérhetik a felvételüket a csoportokba. 
   * Ha ezek a beállítások le vannak tiltva, a felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azokat a meglévő csoportokat, amelyeknek a tulajdonosai. Ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

A **Biztonsági csoportok kezelésére jogosult felhasználók** és az **Office 365-csoportok kezelésére jogosult felhasználók** beállítás használatával még részletesebben vezérelheti a felhasználók önkiszolgáló csoportkezelési hozzáférését. Ha **A felhasználók létrehozhatnak csoportokat** beállítás engedélyezve van, a bérlőben lévő összes felhasználó létrehozhat új csoportokat, és tagokat vehet fel ezekbe a csoportokba. Nem adhat meg olyan személyeket, akik létrehozhatják saját csoportjaikat. Csak olyan személyeket adhat meg, akik egy csoport tulajdonosának egy másik csoport tagjává teszik.

Ha olyan felhasználókat állít be, akik használhatják az **önkiszolgáló biztonsági csoportokat** és a felhasználók számára, **akik az Office 365-csoportokat kezelhetik** **Igen**, akkor a bérlő összes felhasználója számára engedélyezheti az új csoportok létrehozását.

A **Biztonsági csoportok kezelésére jogosult csoport** és az **Office 365 csoportok kezelésére jogosult csoport** használatával megadhat egyetlen csoportot, amelynek tagjai használhatják az önkiszolgáló funkciót.

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
