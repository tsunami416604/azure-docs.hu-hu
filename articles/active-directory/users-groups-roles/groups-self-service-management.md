---
title: Önkiszolgáló csoportkezelés beállítása – Azure Active Directory | Microsoft dokumentumok
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
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298003"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Önkiszolgáló csoportkezelés beállítása az Azure Active Directoryban 

Engedélyezheti a felhasználóknak, hogy saját biztonsági csoportokat vagy Office 365-csoportokat hozzanak létre és kezeljenek az Azure Active Directoryban (Azure AD). A csoport tulajdonosa jóváhagyhatja vagy elutasíthatja a tagsági kérelmeket, és delegálhatja a csoporttagság ellenőrzését. Az önkiszolgáló csoportfelügyeleti funkciók nem érhetők el a levelezésre képes biztonsági csoportok hoz vagy terjesztési listákhoz.

## <a name="self-service-group-membership-defaults"></a>Az önkiszolgáló csoporttagság alapértelmezései

Ha biztonsági csoportokat hoz létre az Azure Portalon vagy az Azure AD PowerShell használatával, csak a csoport tulajdonosai frissíthetik a tagságot. Az [Access panelen](https://account.activedirectory.windowsazure.com/r#/joinGroups) és az Összes Office 365-csoportban önkiszolgáló által létrehozott biztonsági csoportok minden felhasználó számára elérhetők, függetlenül attól, hogy a tulajdonos által jóváhagyott vagy automatikusan jóváhagyott. Az Access panelen a csoport létrehozásakor módosíthatja a tagsági beállításokat.

Csoportok létrehozva | A biztonsági csoport alapértelmezett viselkedése | Az Office 365-csoport alapértelmezett viselkedése
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Csak a tulajdonosok adhatnak hozzá tagokat<br>Látható, de nem érhető el az Access panelen való csatlakozáshoz | Megnyitás az összes felhasználó csatlakozásához
[Azure-portál](https://portal.azure.com) | Csak a tulajdonosok adhatnak hozzá tagokat<br>Látható, de nem érhető el az Access panelen való csatlakozáshoz<br>A tulajdonos nincs automatikusan hozzárendelve a csoport létrehozásakor | Megnyitás az összes felhasználó csatlakozásához
[Hozzáférési panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Megnyitás az összes felhasználó csatlakozásához<br>A csoport létrehozásakor a tagsági beállítások módosíthatók | Megnyitás az összes felhasználó csatlakozásához<br>A csoport létrehozásakor a tagsági beállítások módosíthatók

## <a name="self-service-group-management-scenarios"></a>Önkiszolgáló csoportkezelési forgatókönyvek

* **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférésének kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzáférést rendel az alkalmazáshoz az új csoporthoz, és hozzáadja a csoporthoz az összes olyan embert, aki már hozzáfér az alkalmazáshoz. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanazt az engedélyt adja egy másik üzleti csoport ban lévő vezetőnek, akkor az adott személy kezelheti a saját csoporttagjainak hozzáférését is. Sem a vállalkozás tulajdonosa, sem a vezető nem tekintheti meg és nem kezelheti egymás csoporttagságait. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
* **Önkiszolgáló csoportkezelés** – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel. Hozzáférést akarnak adni egymás csapatainak a webhelyeikhez. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. A SaaS-alkalmazás rendszergazdája adhat hozzáférési jogot a SharePoint Online webhelyhez tartozó alkalmazáshoz. Ettől kezdve az elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## <a name="make-a-group-available-for-user-self-service"></a>Csoport elérhetővé tétele önkiszolgáló felhasználói tevékenységhez

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza **a Csoportok**lehetőséget, majd az **Általános** beállítások lehetőséget.
1. Állítsa **be, hogy a Tulajdonosok a Hozzáférési panelen a csoporttagsági kérelmeket** **Igen-re**kezelheti.
1. Állítsa **a Hozzáférés panel csoportokhoz való hozzáférésének korlátozása** **nem**beállítását.
1. Ha beállítja, a **felhasználók biztonsági csoportokat hozhatnak létre az Azure Portalokon,** vagy **a Felhasználók office 365-csoportokat hozhat létre az Azure-portálokon**

    - **Igen:** Az Azure AD-szervezet minden felhasználója új biztonsági csoportokat hozhat létre, és tagokat adhat hozzá ezekhez a csoportokhoz. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen. Ha a csoport házirend-beállítása lehetővé teszi, más felhasználók is létrehozhatnak kérelmeket a csoportokhoz való csatlakozásra.
    - **Nem**: A felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azon meglévő csoportokat, amelyeknek tulajdonosuk. Ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

Használhatja **a Tulajdonosok, akik csoporttulajdonosként is rendelhetnek tagokat az Azure Portalokon,** és **a tulajdonosok, akik az Azure Portalokon csoporttulajdonosként rendelhetnek tagokat,** hogy részletesebb hozzáférés-szabályozást érjenek el a felhasználók önkiszolgáló csoportkezelése felett.

Amikor a felhasználók csoportokat hozhatnak létre, a szervezet minden felhasználója új csoportokat hozhat létre, majd alapértelmezett tulajdonosként tagokat adhat ezekhez a csoportokhoz. Nem adhat meg olyan személyeket, akik saját csoportokat hozhatnak létre. Csak akkor adhat meg személyeket, ha egy másik csoporttagot csoporttulajdonossá szeretne tenni.

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
