---
title: "Az Azure AD fiókok megosztása |} Microsoft Docs"
description: "Ismerteti, hogyan Azure Active Directory lehetővé teszi, hogy a szervezetek biztonságosan megosztani a fiókok a helyszíni alkalmazások és a fogyasztói felhőszolgáltatások."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: e2d77104-d978-46a3-bfea-03ffdf3b61e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3b6a83d91ec5d8466669655d6c3bd7ae7b42dd2f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="sharing-accounts-with-azure-ad"></a>Fiókok megosztása az Azure ad szolgáltatással
## <a name="overview"></a>Áttekintés
Néha szervezetek egyetlen felhasználónév és jelszó a szüksége több ember, amely két esetben általában akkor fordul elő:

* Történő hozzáféréskor alkalmazásokat, amelyek minden felhasználóhoz egyedi bejelentkezés és jelszó kérése, hogy a helyszíni alkalmazások vagy a felhasználó a felhőalapú szolgáltatások (például vállalati közösségi fiókok).
* Többfelhasználós környezet létrehozásakor. Lehetséges, hogy egy egyetlen, helyi fiók emelt szintű jogosultságokkal, és központi telepítése, a felügyeleti és a helyreállítási tevékenységeket szolgál. Például a helyi "globális rendszergazda" fiók számára az Office 365 vagy a rendszergazdafiók a Salesforce-ban.

Hagyományosan ezek a fiókok osztanak meg a hitelesítő adatokat (felhasználónév és jelszó) terjesztése a megfelelő személyeknek, vagy tárolja őket egy megosztott hely, ahol több megbízható ügynök hozzájuk férhetnek.

A hagyományos megosztási modellnek több hátrányai:

* Új alkalmazásokhoz való hozzáférés engedélyezése kell terjeszteni a hitelesítő adatokat mindenki számára, amelyek engedéllyel kell rendelkeznie.
* Minden megosztott alkalmazás a saját egyedi készletének megosztott hitelesítő adatokat igénylő felhasználók menthetik a hitelesítő adatok több példányban lehet szükség. Ha a felhasználók sok hitelesítő adatok megjegyzése, a kockázat növeli a kockázatos gyakorlatát használhatja. (például írás a jelszavak le).
* Nem sikerült megállapítani, ki férhet hozzá az alkalmazás.
* Nem sikerült megállapítani a kik *elért* kérelmet.
* Ha el szeretné távolítani a hozzáférést egy alkalmazáshoz, ki kell frissítenie kell a hitelesítő adatokat és terjesztenie a Mindenki hozzá kell férnie az alkalmazást.

## <a name="azure-active-directory-account-sharing"></a>Az Azure Active Directory fiók megosztása
Az Azure AD egy új megközelítését ismerteti megosztott fiókok használatát, amely kiküszöböli a hátrányai is.

Az Azure AD-rendszergazda konfigurálja az adott alkalmazáshoz megfelelő egy felhasználó hozzáférhessen a hozzáférési panelen, majd válassza az egyszeri bejelentkezés legjobb típusú alkalmazásokat. Egy adott típusú *jelszó-alapú egyszeri bejelentkezést*, lehetővé teszi, hogy az Azure AD egy "broker" típusú összekötőként az alkalmazáshoz a bejelentkezési folyamat során.

Felhasználói bejelentkezés egyszer a szervezeti fiókjukkal. Ez a fiók megegyezik egy rendszeresen használják az asztalon vagy e-mailek elérésére. Ugyanakkor felderítése és hozzáférési csak ezeket az alkalmazásokat, amelyek vannak rendelve. A megosztott-fiókokkal ebben a listában, az alkalmazások tartalmazhatnak tetszőleges számú megosztott hitelesítési adatok. Ne feledje, vagy írja le a különböző fiókokról, lehetséges, hogy használja a végfelhasználó nem szükséges.

Megosztott fiókok csak nem növeli a felügyeletet, valamint javítja a használhatóság, akkor is a megfelelő biztonság érdekében. A hitelesítő adatok használatát a felhasználók a megosztott jelszó nem látható, de ahelyett, hogy kérjen engedélyt azokhoz, a jelszó használatát egy hitelesítési vezénylését folyamat részeként. További egyes jelszó SSO alkalmazások az Azure AD-be rendszeresen váltása (frissítés) jelszavak használata lehetőséget ad. A rendszer használja nagy, összetett jelszót, amely növeli a fiók biztonságát. A rendszergazda könnyen megadni vagy visszavonni a hozzáférést egy alkalmazást, tudni fogja, kik férhetnek hozzá a fiókot, és ki fért hozzá azt a múltban.

Az Azure AD a nagyvállalati mobilitási csomag (EMS), prémium vagy alapszintű kiadásra esetén támogatja a megosztott fiókokat licenccel rendelkező felhasználók, minden típusú jelszó egyszeri bejelentkezés alkalmazások között. Bármely több ezer előre integrált alkalmazások, az alkalmazás katalógusában fiókok megoszthatja, és hozzáadhatja a saját jelszó hitelesítése alkalmazás [egyéni SSO alkalmazások](active-directory-enterprise-apps-manage-sso.md).

Az Azure AD-funkciókat, amelyek lehetővé teszik a fiók megosztása a következők:

* [Jelszó egyszeri bejelentkezést.](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Jelszó egyszeri bejelentkezés ügynök
* [Csoport-hozzárendelés](active-directory-accessmanagement-self-service-group-management.md)
* Egyéni jelszó alkalmazások
* [Alkalmazás használati irányítópult/jelentések](active-directory-passwords-get-insights.md)
* Végfelhasználói hozzáférés portálok
* [Alkalmazás proxy](active-directory-application-proxy-get-started.md)
* [Active Directory Marketplace-ről](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Egy fiók megosztása
Az Azure AD használatával megoszthatja a fiók, meg kell:

* Alkalmazás hozzáadása [alkalmazásgyűjtemény](https://azure.microsoft.com/marketplace/active-directory/) vagy [egyéni alkalmazás](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
* A jelszó egyszeri bejelentkezés (SSO) az alkalmazás beállítása
* Használjon [biztonságicsoport-alapú hozzárendelés](active-directory-accessmanagement-group-saasapps.md) , és jelölje be a megosztott hitelesítő adatok megadása
* Választható lehetőség: az egyes alkalmazások, például a Facebook, a Twitter és a LinkedIn, engedélyezheti a kívánt beállítást [az Azure AD automatikus jelszó során](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Végezhet is megosztott fiókja biztonságát erősítik a multi-factor Authentication (MFA) (További információ [biztonságossá tétele az alkalmazások az Azure ad-val](../multi-factor-authentication/multi-factor-authentication-get-started.md)) és delegálhatja, hogy kik férhetnek hozzá a használóalkalmazásokkezelése[ Az Azure AD-önkiszolgáló](active-directory-accessmanagement-self-service-group-management.md) felügyeleti csoportban.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Feltételes hozzáféréssel rendelkező alkalmazások védelme](active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoportkezelési felügyeleti/SSAA](active-directory-accessmanagement-self-service-group-management.md)

