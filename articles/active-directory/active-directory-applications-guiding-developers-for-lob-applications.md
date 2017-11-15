---
title: "Az Azure AD alkalmazások fejlesztéséhez |} Microsoft Docs"
description: "Az informatikai szakembereknek készült Ez a cikk útmutatást nyújt a Azure-alkalmazások integrálása az Active Directoryval."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76bb8d239b54b5236d077b98a5908c230cdc31a1
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Az Azure Active Directory-üzleti alkalmazások fejlesztéséhez
Ez az útmutató áttekintést fejlesztése az üzletági (LoB) alkalmazások az Azure Active Directory (AD). A célközönség Active Directory vagy Office 365 globális rendszergazdák.

## <a name="overview"></a>Áttekintés
Az Azure AD integrált alkalmazások lehetőséget ad a felhasználók a szervezet egyszeri bejelentkezéshez és az Office 365 a. Mivel az alkalmazás a hitelesítési házirend alkalmazására vonatkozó vezérlésére biztosít az Azure AD lehetőséget. További információt a feltételes hozzáférés és a többtényezős hitelesítés (MFA) tekintse meg az alkalmazások védelme [konfigurálása hozzáférési szabályok](active-directory-conditional-access-azure-portal-get-started.md).

Regisztrálja az alkalmazást az Azure Active Directoryt. Az alkalmazás regisztrálása, az azt jelenti, hogy a fejlesztők használhatják-e az Azure AD hitelesíti a felhasználókat, és kérjen hozzáférést a felhasználói erőforrások, például az e-mailek, naptár és dokumentumok.

Bármelyik tag a könyvtár (nem vendégek) regisztrálhatja az alkalmazás, más néven a *alkalmazásobjektum létrehozása*.

Az alkalmazásnak lehetővé teszi, hogy a felhasználók úgy tegye a következőket:

* Az alkalmazás számára az Azure AD felismerhető identitás lekérése
* Egy vagy több titkok/kulcsot, az alkalmazás segítségével hitelesíti magát az AD beolvasása
* Az alkalmazás az Azure portálon egy egyéni nevét, emblémáját, stb. arculatát.
* Az Azure AD hitelesítési szolgáltatások alkalmazni az alkalmazásra, többek között:

  * Szerepköralapú hozzáférés-vezérlés (RBAC)
  * Az Azure Active Directory oAuth-engedélyezési kiszolgálóként (az API-k által az alkalmazás biztonságos)
* Deklarálja az alkalmazás működéséhez szükséges szükséges engedélyekkel, vártnak, beleértve:

      - Alkalmazásengedélyek (csak a globális rendszergazdák). Példa: egy másik Azure AD alkalmazás vagy szerepkör tagsági képest az Azure erőforrás, erőforráscsoport, vagy előfizetés szerepkör tagjának kell lennie
      - Delegált engedélyek (felhasználói). Például: az Azure AD-bejelentkezés, és olvasási profil

> [!NOTE]
> Alapértelmezés szerint minden tag kérelmet tud regisztrálni. A regisztrációhoz meghatározott tagjainak alkalmazások engedélyek korlátozása, lásd: [hogyan alkalmazások felvétele az Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Mi a globális rendszergazdának kell tennie segítségével a fejlesztők legyen az alkalmazás készen áll a termelési itt található:

* Hozzáférési szabályok (hozzáférési házirend vagy MFA) konfigurálása
* Az alkalmazás felhasználói kiosztása és felhasználók hozzárendelése konfigurálása
* Ne jelenjen meg többé az alapértelmezett felhasználói hozzájárulás élmény

## <a name="configure-access-rules"></a>Hozzáférési szabályok konfigurálása
A Szolgáltatottszoftver-alkalmazásoknál alkalmazás hozzáférési szabályok konfigurálása. Például többtényezős hitelesítés megkövetelése, vagy csak felhasználók férhessenek hozzá a megbízható hálózatokon. Ez a részletes érhetők el a dokumentum [konfigurálása hozzáférési szabályok](active-directory-conditional-access-azure-portal-get-started.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Az alkalmazás felhasználói kiosztása és felhasználók hozzárendelése konfigurálása
Alapértelmezés szerint felhasználók folyik a hozzárendelése nem férhet hozzá az alkalmazásokhoz. Ha az alkalmazás elérhetővé teszi a szerepkörök, vagy ha azt szeretné, hogy az alkalmazás a felhasználó hozzáférési panel megjeleníteni, felhasználó-hozzárendelés elvégzéséhez szükséges.

[Felhasználó-hozzárendelés igénylése](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Ha az Azure AD Premium vagy a nagyvállalati mobilitási csomag (EMS) előfizető, erősen ajánlott csoportok használatával. Csoportok hozzárendelése az alkalmazás lehetővé teszi a csoport folyamatos hozzáférés-kezelés a tulajdonosra delegálására. A csoport létrehozásához, vagy kérje meg a felelős fél létrehozni a csoportot, a csoport felügyeleti funkció segítségével a szervezetében.

[Felhasználók hozzárendelése egy alkalmazáshoz](active-directory-applications-guiding-developers-assigning-users.md)  
[Csoportok hozzárendelése egy alkalmazáshoz](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Felhasználói hozzájárulás mellőzése
Alapértelmezés szerint minden felhasználó végighalad a hozzájárulási élmény való bejelentkezéshez. Lehet, hogy a hozzájárulási élmény kérni a felhasználókat adhat engedélyeket egy alkalmazást, disconcerting felhasználók számára nem ismeri az ilyen döntések meghozatalában.

A megbízható alkalmazások egyszerűbbé teszik a felhasználói élmény hozzájárul ahhoz, hogy a szervezet nevében az alkalmazás által.

További információ a felhasználói hozzájárulás és a hozzájárulásukat adják észlel, az Azure-ban, a következő témakörben: [alkalmazások integrálása az Azure Active Directoryval](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure AD alkalmazásproxy a helyszíni alkalmazások biztonságos távoli hozzáférés engedélyezése](active-directory-application-proxy-get-started.md)
* [Azure feltételes hozzáférés előzetes verziója SaaS-alkalmazásokhoz](active-directory-conditional-access-azure-portal-get-started.md)
* [Az Azure ad-val alkalmazásokhoz való hozzáférés kezelése](active-directory-managing-access-to-apps.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
