---
title: Az Azure AD-vel fiókok megosztása |} A Microsoft Docs
description: Ismerteti, hogyan Azure Active Directory lehetővé teszi a szervezetek biztonságosan megoszthassák a fiókok a helyszíni alkalmazások és fogyasztói felhőalapú szolgáltatásai.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 10/29/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: ddc14c371f2aa56b056dd7f9f1802de081f4ccd9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620697"
---
# <a name="sharing-accounts-with-azure-ad"></a>Az Azure AD-fiókok megosztása
## <a name="overview"></a>Áttekintés
Néha a szervezetnek kell több személy, amely két esetben általában akkor fordul elő egyetlen felhasználónév és jelszó használata:

* Minden felhasználó egy egyedi bejelentkezési és a jelszót igénylő alkalmazások elérésekor e a helyszíni alkalmazások vagy a felhasználó a cloud services (például vállalati közösségimédia-fiókokat).
* Többfelhasználós környezet létrehozásakor. Előfordulhat, hogy egy egyetlen, helyi fiók emelt szintű jogosultságokkal rendelkező és központi telepítéséhez, a felügyelet és a helyreállítási tevékenységeket szolgál. Például a helyi "globális rendszergazda" fiók az Office 365 vagy a rendszergazdai fiók, a Salesforce-ban.

Hagyományosan ezeket a fiókokat osztanak meg a hitelesítő adatok (felhasználónév és jelszó) terjesztése, a megfelelő személyekhez vagy tárolja őket egy megosztott hely, ahol több megbízható ügynök hozzájuk férhetnek.

A hagyományos megosztási modellje több hátrányai:

* Új alkalmazás-hozzáférés engedélyezése között oszthatja el a szükséges hitelesítő adatokat mindenki számára hozzáférés szükséges.
* Minden megosztott alkalmazásban szükség lehet megosztott hitelesítő adatokat igénylő több hitelesítőadat-készletek, ne feledje, hogy a felhasználók a saját egyedi készletét. Ha a felhasználók számos hitelesítő adatok megjegyzése, a kockázat növeli a kockázatos gyakorlatát használhatja. (például jelszavak le írása).
* Nem lehet megállapítani, ki férhet hozzá az alkalmazáshoz.
* Nem állapítható meg, kik *elért* egy alkalmazást.
* Ha el kívánja távolítani a hozzáférést egy alkalmazáshoz, frissítse a hitelesítő adatokat, és mindenki számára, amelyek hozzáférést igénylő terjesztenie rendelkezik.

## <a name="azure-active-directory-account-sharing"></a>Az Azure Active Directory fiók megosztása
Az Azure AD új módszert biztosít a megosztott fiókok használatát, amely megszünteti a hátrányai.

Az Azure AD-rendszergazda konfigurálja az adott alkalmazáshoz megfelelő felhasználó érheti el a hozzáférési panelen, majd válassza az egyszeri bejelentkezés legjobb típusú alkalmazásokat. Ezek a típusok egyikével *jelszavas egyszeri bejelentkezéses*, lehetővé teszi, hogy az Azure AD működjön, a "broker" típusú, amelyet az alkalmazás bejelentkezési folyamata során.

Felhasználói bejelentkezés szervezeti fiókkal egyszer. Ez a fiók megegyezik egy rendszeresen használnak az asztalon vagy e-mailek eléréséhez. Felderíti, és csak a hozzárendelt alkalmazások eléréséhez. A megosztott fiókok esetében az alkalmazások listájának tartalmazhat tetszőleges számú megosztott hitelesítő adatokat. Ne felejtse el, vagy írja fel a különböző fiókokról, lehet, hogy használja a végfelhasználónak nem szükséges.

Közös fiókok nem csak növelni a felügyeletet és a modulok használhatóságának fejlesztésében, is növelheti a biztonságot. A hitelesítő adatok engedélyekkel rendelkező felhasználók nem jelenik meg a megosztott jelszót, de inkább beolvasása egy előkészített hitelesítési folyamat részeként a jelszót használni kívánt engedélyeket. További egyes jelszó SSO-alkalmazások lehetővé teszi az, hogy rendszeres időközönként (frissítés) helyettesítő jelszavak az Azure AD használatával. A rendszer nagy, összetett jelszavakat használja, ami növeli a fiók biztonságát. A rendszergazda könnyen engedélyezheti vagy visszavonhatja a hozzáférést egy alkalmazást, tudja, ki férhet hozzá a fiókot, és ki fért hozzá, az elmúlt.

Az Azure AD támogatja közös fiókok minden olyan nagyvállalati mobilitási csomag (EMS), a prémium szintű vagy alapszintű licenccel rendelkező felhasználók, a különböző típusú jelszó egyszeri bejelentkezéses alkalmazások. Megoszthat bármely több ezer előre integrált alkalmazások az alkalmazás katalógusában, fiókok, és hozzáadhatja a saját jelszó-hitelesítés alkalmazását [egyéni SSO-alkalmazások](../manage-apps/configure-single-sign-on-portal.md).

Fiók megosztásának engedélyezése az Azure AD-funkciók a következők:

* [Jelszavas egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Jelszó egyszeri bejelentkezéses ügynök
* [Csoport-hozzárendelés](groups-self-service-management.md)
* Egyéni jelszó alkalmazások
* [Alkalmazás használati irányítópult és jelentések](../active-directory-passwords-get-insights.md)
* Végfelhasználói hozzáférés portállal
* [Alkalmazásproxy](../manage-apps/application-proxy.md)
* [Az Active Directory Marketplace-ről](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Egy fiók megosztása
Az Azure AD-fiók használata, kell tennie:

* Alkalmazás hozzáadása [alkalmazásgyűjtemény](https://azure.microsoft.com/marketplace/active-directory/) vagy [egyéni alkalmazás](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* A jelszó egyszeri bejelentkezéses (SSO) az alkalmazás konfigurálása
* Használat [Csoportalapú](groups-saasapps.md) és választja, adja meg egy megosztott hitelesítő adatok
* Választható lehetőség: az egyes alkalmazások, például Facebook, Twitter vagy LinkedIn, engedélyezheti a kívánt beállítást [az Azure AD automatikus jelszó vihetők át](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/)

Is teheti a megosztott fiókkal biztonságosabb a multi-factor Authentication (MFA) (További információ [az Azure AD-alkalmazások védelme](../authentication/concept-mfa-whichversion.md)), és képes kezelni, ki férhet hozzá az alkalmazás használatával delegálhat[ Az Azure AD önkiszolgáló](groups-self-service-management.md) felügyeleti csoportban.

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Feltételes hozzáféréssel rendelkező alkalmazások védelme](../active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoport felügyeleti/SSAA](groups-self-service-management.md)
