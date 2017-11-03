---
title: "Az Azure AD fiókok megosztása |} Microsoft Docs"
description: "Ismerteti, hogyan Azure Active Directory lehetővé teszi, hogy a szervezetek biztonságosan megosztani a fiókok a helyszíni alkalmazások és a fogyasztói felhőszolgáltatások."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e2d77104-d978-46a3-bfea-03ffdf3b61e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: b5a6bad6eca3f0262d5cc2ac01fb3a4eb3676e5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sharing-accounts-with-azure-ad"></a>Fiókok megosztása az Azure ad szolgáltatással
## <a name="overview"></a>Áttekintés
Egyes esetekben a szervezetek szeretné használni, egyetlen felhasználónév és jelszó számára több. Ez általában akkor fordul elő két esetben:

* Ha mindegyik felhasználóhoz egyedi felhasználónevét és jelszavát igénylő alkalmazásokhoz való hozzáférés, hogy a helyszíni alkalmazások vagy a felhasználó a felhőalapú szolgáltatások (pl. vállalati közösségi fiókok).
* Többfelhasználós környezet létrehozásakor. Előfordulhat, hogy egy egyetlen, helyi fiók emelt szintű jogosultságokkal, és központi telepítése, a felügyeleti és a helyreállítási tevékenységeket (pl. a helyi "globális rendszergazda" Office 365) vagy a legfelső szintű fiók a Salesforce-ban is használható.

Hagyományosan ezeket a fiókokat volna megoszthatók a hitelesítő adatok (felhasználónév/jelszó) terjesztése a megfelelő személyeknek, vagy tárolja őket egy megosztott hely, ahol több megbízható ügynök hozzájuk férhetnek.

A hagyományos megosztási modellnek több hátrányai:

* Új alkalmazásokhoz való hozzáférés engedélyezése kell terjeszteni a hitelesítő adatokat mindenki számára, amelyek engedéllyel kell rendelkeznie.
* Minden megosztott alkalmazás a saját egyedi készletének megosztott hitelesítő adatokat igénylő felhasználók menthetik a hitelesítő adatok több példányban lehet szükség. Ha a felhasználók sok hitelesítő adatok megjegyzése, a kockázat növeli az, hogy azok fog igénybe kockázatos eljárásokat. (pl. írás a jelszavak le).
* Nem sikerült megállapítani, ki férhet hozzá az alkalmazás.
* Nem sikerült megállapítani a kik *elért* kérelmet.
* Ha el kell távolítania az alkalmazáshoz való hozzáférés, akkor frissítse a hitelesítő adatokat, és újra ossza ki őket a Mindenki hozzá kell férnie az alkalmazás.

## <a name="azure-active-directory-account-sharing"></a>Az Azure Active Directory fiók megosztása
Az Azure AD egy új megközelítését ismerteti megosztott fiókok használatát, amely kiküszöböli a hátrányai is.

Az Azure AD-rendszergazda konfigurálja az adott alkalmazáshoz megfelelő egy felhasználó hozzáférhessen a hozzáférési panelen, majd válassza az egyszeri bejelentkezés legjobb típusú alkalmazásokat. Egy adott típusú *jelszó-alapú egyszeri bejelentkezést*, lehetővé teszi, hogy az Azure AD egy "broker" típusú összekötőként az alkalmazáshoz a bejelentkezési folyamat során.

Felhasználói bejelentkezés egyszer a szervezeti fiókjukkal. Ez az ugyanazt a fiókot rendszeresen az asztalon vagy e-mailek elérésére használnak. Ugyanakkor felderítése és hozzáférési csak ezeket az alkalmazásokat, amelyek vannak rendelve. A megosztott-fiókokkal ebben a listában, az alkalmazások tartalmazhatnak tetszőleges számú megosztott hitelesítési adatok. Ne feledje, vagy írja le a különböző fiókokról használják a végfelhasználó nem szükséges.

Megosztott fiókok csak nem növeli a felügyeletet, valamint javítja a használhatóság, akkor is a megfelelő biztonság érdekében. A hitelesítő adatok használatát a felhasználók a megosztott jelszó nem látható, de ahelyett, hogy kérjen engedélyt azokhoz, a jelszó használatát egy hitelesítési vezénylését folyamat részeként. Emellett egyes jelszó SSO alkalmazások, az informatikai részleg a lehetőséget, ha az Azure AD rendszeresen váltása (frissítés) nagy, összetett jelszót, fiók biztonságának növelése a jelszót. A rendszergazdák egyszerűen adja meg vagy alkalmazáshoz való hozzáférés visszavonása és is tudja, hogy ki férhet hozzá a fiókot, és ki fért hozzá a múltban.

Az Azure AD támogatja a megosztott fiókokat a bármely nagyvállalati mobilitási csomag (EMS), az alkalmazások aláírásához prémium vagy alapszintű licenccel rendelkező felhasználók, az egyszeri jelszó összes típusa. Bármely több ezer előre integrált alkalmazások, az alkalmazás katalógusában fiókok megoszthatja, és hozzáadhatja a saját jelszó hitelesítése alkalmazás [egyéni SSO alkalmazások](active-directory-sso-integrate-saas-apps.md).

Az Azure AD-funkciókat, amelyek lehetővé teszik a fiók megosztása a következők:

* [Jelszó egyszeri bejelentkezést.](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Jelszó egyszeri bejelentkezés ügynök
* [Csoport-hozzárendelés](active-directory-accessmanagement-self-service-group-management.md)
* Egyéni jelszó alkalmazások
* [Alkalmazás használati irányítópult/jelentések](active-directory-passwords-get-insights.md)
* Felhasználói hozzáférés portálok
* [Alkalmazás proxy](active-directory-application-proxy-get-started.md)
* [Active Directory Marketplace-ről](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Egy fiók megosztása
Az Azure AD segítségével megosztása szüksége lesz a fiók:

* Alkalmazás hozzáadása [alkalmazásgyűjtemény](https://azure.microsoft.com/marketplace/active-directory/) vagy [egyéni alkalmazás](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
* A jelszó egyszeri bejelentkezés (SSO) az alkalmazás beállítása
* Használjon [csoport-alapú hozzárendelés](active-directory-accessmanagement-group-saasapps.md) , és jelölje be a megosztott hitelesítő adatok megadása
* Választható lehetőség: az egyes alkalmazások, például a Facebook, a Twitter és a LinkedIn, engedélyezheti a kívánt beállítást [az Azure AD automatikus jelszó során](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Végezhet is megosztott fiókja biztonságát erősítik a multi-factor Authentication (MFA) (További információ [biztonságossá tétele az alkalmazások az Azure ad-val](../multi-factor-authentication/multi-factor-authentication-get-started.md)) és delegálhatja, hogy kik férhetnek hozzá a használóalkalmazásokkezelése[ Az Azure AD-önkiszolgáló](active-directory-accessmanagement-self-service-group-management.md) felügyeleti csoportban.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Feltételes hozzáféréssel rendelkező alkalmazások védelme](active-directory-conditional-access.md)
* [Önkiszolgáló csoportkezelési felügyeleti/SSAA](active-directory-accessmanagement-self-service-group-management.md)

