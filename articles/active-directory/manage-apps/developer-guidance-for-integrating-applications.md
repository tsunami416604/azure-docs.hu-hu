---
title: Az alkalmazás regisztrálása az Azure Active Directory használatához | Microsoft dokumentumok
description: Az IT Pro számára írt cikk az Azure-alkalmazások Active Directoryval való integrálásának irányelveit ismerteti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108285"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Üzletági alkalmazások fejlesztése az Azure Active Directoryhoz
Ez az útmutató áttekintést nyújt az Azure Active Directory (AD) üzletági (LoB) alkalmazások fejlesztéséről. A célközönség az Active Directory/Office 365 globális rendszergazdái.

## <a name="overview"></a>Áttekintés
Az Azure AD-vel integrált alkalmazások létrehozása lehetővé teszi a szervezet felhasználóiszámára az Office 365-tel való egyszeri bejelentkezést. Miután az alkalmazás az Azure AD-ben lehetővé teszi az alkalmazás hitelesítési szabályzatának vezérlését. A feltételes hozzáférésről és az alkalmazások többtényezős hitelesítéssel (MFA) való védelméről a [Hozzáférési szabályok konfigurálása](../conditional-access/app-based-mfa.md)című témakörben olvashat bővebben.

Regisztrálja az alkalmazást az Azure Active Directory használatához. Az alkalmazás regisztrálása azt jelenti, hogy a fejlesztők az Azure AD segítségével hitelesítheti konkretita felhasználók és hozzáférést kérhet a felhasználói erőforrások, például e-mail, naptár és dokumentumok.

A címtár bármely tagja (nem vendégek) regisztrálhat egy alkalmazást, más néven *alkalmazásobjektum létrehozását.*

Az alkalmazás regisztrálása lehetővé teszi, hogy bármely felhasználó a következőket tegye:

* Az Azure AD által felismert alkalmazás identitásának beszereznie
* Szerezzen be egy vagy több titkos kulcsot/kulcsot, amelyet az alkalmazás az AD-hez való hitelesítéshez használhat
* Az alkalmazást az Azure Portalon egyéni névvel, emblémával stb.
* Az Azure AD engedélyezési funkcióinak alkalmazása az alkalmazásukra, többek között a következőkre:

  * Szerepköralapú hozzáférés-vezérlés (RBAC)
  * Az Azure Active Directory oAuth engedélyezési kiszolgálóként (az alkalmazás által elérhetővé tett API biztonságossá tétele)
* Az alkalmazás várt működéséhez szükséges engedélyek deklarálása, beleértve a következőket:

     - Alkalmazásengedélyek (csak globális rendszergazdákszámára). Például: Szerepkör-tagság egy másik Azure AD-alkalmazásban vagy szerepkör-tagságegy Azure-erőforráshoz, erőforráscsoporthoz vagy előfizetéshez képest
     - Delegált engedélyek (bármely felhasználó). Például: Azure AD, Bejelentkezés és Olvasási profil

> [!NOTE]
> Alapértelmezés szerint bármely tag regisztrálhat egy alkalmazást. Ha tudni szeretné, hogyan korlátozhatja az alkalmazások regisztrálására vonatkozó engedélyeket az adott tagokra, olvassa el az [Alkalmazások hozzáadása az Azure AD-hez című témakört.](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)
>
>

A globális rendszergazdának a következőket kell tennie annak érdekében, hogy a fejlesztők készen álljanak az éles környezetre:

* Hozzáférési szabályok konfigurálása (hozzáférési házirend/MFA)
* Az alkalmazás konfigurálása a felhasználói hozzárendelés megkövetelésére és a felhasználók hozzárendelésére
* Az alapértelmezett felhasználói hozzájárulási élmény letiltása

## <a name="configure-access-rules"></a>Hozzáférési szabályok konfigurálása
Alkalmazásonkénti hozzáférési szabályok konfigurálása a SaaS-alkalmazásokhoz. Megkövetelheti például az MFA-t, vagy csak a megbízható hálózatokon lévő felhasználók számára engedélyezhet hozzáférést. Ennek részletei a [hozzáférési szabályok konfigurálása](../conditional-access/app-based-mfa.md)című dokumentumban találhatók.

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Az alkalmazás konfigurálása a felhasználói hozzárendelés megkövetelésére és a felhasználók hozzárendelésére
Alapértelmezés szerint a felhasználók hozzárendelés nélkül is hozzáférhetnek az alkalmazásokhoz. Ha azonban az alkalmazás szerepköröket tesz elérhetővé, vagy ha azt szeretné, hogy az alkalmazás megjelenjen a felhasználó hozzáférési paneljén, felhasználói hozzárendelést kell igényelnie.

Ha Ön Azure AD Premium vagy Enterprise Mobility Suite (EMS) előfizető, javasoljuk a csoportok használatát. Csoportok hozzárendelése az alkalmazáshoz lehetővé teszi a folyamatos hozzáférés-kezelés delegálását a csoport tulajdonosának. Létrehozhatja a csoportot, vagy megkérheti a szervezet felelős ét, hogy hozza létre a csoportot a csoportkezelési szolgáltatás használatával.

[Felhasználók és csoportok hozzárendelése alkalmazáshoz](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>A felhasználói hozzájárulás letiltása
Alapértelmezés szerint minden felhasználó egy hozzájárulási élményen megy keresztül a bejelentkezéshez. A hozzájárulási élmény, amely arra kéri a felhasználókat, hogy engedélyeket adjanak egy alkalmazásnak, nyugtalanító lehet az ilyen döntések meghozatalában ismeretlen felhasználók számára.

A megbízható alkalmazások esetében egyszerűsítheti a felhasználói élményt, ha a szervezet nevében hozzájárul az alkalmazáshoz.

A felhasználói hozzájárulásról és az Azure-beli hozzájárulási élményről az [Alkalmazások integrálása](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)az Azure Active Directoryval című témakörben talál további információt.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Biztonságos távelérés engedélyezése a helyszíni alkalmazásokhoz az Azure AD alkalmazásproxyval](application-proxy.md)
* [Alkalmazásokhoz való hozzáférés kezelése az Azure AD-vel](what-is-access-management.md)

