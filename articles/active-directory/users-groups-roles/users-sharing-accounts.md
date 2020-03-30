---
title: Fiókok és hitelesítő adatok megosztása – Azure Active Directory | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy az Azure Active Directory hogyan teszi lehetővé a szervezetek számára a helyszíni alkalmazások és a fogyasztói felhőszolgáltatások fiókjainak biztonságos megosztását.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 987c3ee7c90eb0bb793b96eb2771efbb258f16a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565502"
---
# <a name="sharing-accounts-with-azure-ad"></a>Fiókok megosztása az Azure AD-vel

## <a name="overview"></a>Áttekintés

Néha a szervezeteknek egyetlen felhasználónevet és jelszót kell használniuk több személy számára, ami általában két esetben fordul elő:

* Olyan alkalmazások elérésekor, amelyek egyedi bejelentkezést és jelszót igényelnek az egyes felhasználók számára, legyen szó helyszíni alkalmazásokról vagy fogyasztói felhőszolgáltatásokról (például vállalati közösségi médiafiókokról).
* Többfelhasználós környezetek létrehozásakor. Előfordulhat, hogy egyetlen helyi fiókkal rendelkezik, amely emelt szintű jogosultságokkal rendelkezik, és alapvető beállítási, felügyeleti és helyreállítási tevékenységekhez használható. Például az Office 365 helyi "globális rendszergazda" fiókja vagy a Salesforce gyökérfiókja.

Hagyományosan ezeket a fiókokat úgy osztják meg, hogy a hitelesítő adatokat (felhasználónevet és jelszót) a megfelelő személyek nek osztja el, vagy olyan megosztott helyen tárolja őket, ahol több megbízható ügynök is hozzáférhet.

A hagyományos megosztási modellnek számos hátránya van:

* Az új alkalmazásokhoz való hozzáférés engedélyezéséhez a hitelesítő adatokat mindenkinek el kell osztania, akinek hozzáférésre van szüksége.
* Minden megosztott alkalmazásnak saját, egyedi megosztott hitelesítő adatokra van szüksége, amelyek megkövetelik a felhasználóktól, hogy több hitelesítő adatot emlékezzenek. Ha a felhasználóknak sok hitelesítő adatot kell megjegyezniük, a kockázat növeli a kockázatos gyakorlatok igénybevételét. (például jelszavak írása).
* Nem lehet megmondani, hogy ki férhet hozzá egy alkalmazáshoz.
* Nem lehet megmondani, hogy ki fért hozzá egy *alkalmazáshoz.*
* Ha el szeretné távolítani az alkalmazáshoz való hozzáférést, frissítenie kell a hitelesítő adatokat, és újra el kell osztania azokat mindenki nek, akinek hozzáférésre van szüksége az adott alkalmazáshoz.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory-fiókmegosztás

Az Azure AD új megközelítést biztosít a megosztott fiókok használatára, amely kiküszöböli ezeket a hátrányokat.

Az Azure AD-rendszergazda konfigurálja, hogy a felhasználó mely alkalmazásokat érheti el a Hozzáférési panel használatával, és válassza ki az adott alkalmazásnak leginkább megfelelő egyszeri bejelentkezés típusát. Az egyik ilyen típusú, *jelszóalapú egyszeri bejelentkezés ,* lehetővé teszi az Azure AD egyfajta "közvetítőként" az adott alkalmazás bejelentkezési folyamata során.

A felhasználók egyszer jelentkeznek be a szervezeti fiókjukkal. Ez a fiók ugyanaz, amelyet rendszeresen használnak az asztali vagy e-mail eléréséhez. Csak azokat az alkalmazásokat fedezhetik fel és érhetik el, amelyekhez hozzá vannak rendelve. A megosztott fiókok esetén ez az alkalmazáslista tetszőleges számú megosztott hitelesítő adatot tartalmazhat. A végfelhasználónak nem kell megjegyeznie vagy leírnia az általuk használt különböző fiókokat.

A megosztott fiókok nem csak növelik a felügyeletet és javítják a használhatóságot, hanem növelik a biztonságot is. A hitelesítő adatok használatára engedéllyel rendelkező felhasználók nem látják a megosztott jelszót, hanem engedélyt kapnak a jelszó egy összehangolt hitelesítési folyamat részeként történő használatára. Továbbá néhány jelszó-sso-alkalmazás lehetőséget ad az Azure AD használatával rendszeres időközönként váltási (frissítési) jelszavak. A rendszer nagy méretű, összetett jelszavakat használ, ami növeli a fiókbiztonságát. A rendszergazda könnyedén megadhat vagy visszavonhat hozzáférést egy alkalmazáshoz, tudja, hogy ki férhet hozzá a fiókhoz, és ki fért hozzá a múltban.

Az Azure AD támogatja a megosztott fiókok bármely Enterprise Mobility Suite (EMS) vagy az Azure AD Premium licenccsomag, minden típusú jelszó egyszeri bejelentkezési alkalmazások. Az alkalmazásgalériában megoszthatja a fiókokat az előintegrált alkalmazások bármelyikéhez, és hozzáadhatja saját jelszó-hitelesítő alkalmazását [az egyéni SSO-alkalmazásokkal.](../manage-apps/configure-single-sign-on-non-gallery-applications.md)

A fiókmegosztást lehetővé tevő Azure AD-funkciók a következők:

* [Jelszó egyszeri bejelentkezés](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Jelszó egyszeri bejelentkezési ügynöke
* [Csoportos hozzárendelés](groups-self-service-management.md)
* Egyéni jelszóalkalmazások
* [Alkalmazáshasználati irányítópult/jelentések](../active-directory-passwords-get-insights.md)
* Végfelhasználói hozzáférési portálok
* [Alkalmazásproxy](../manage-apps/application-proxy.md)
* [Active Directory piactér](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Fiók megosztása

Ha az Azure AD-t szeretné használni egy fiók megosztásához, a következőket kell a következőkre használnia:

* Alkalmazásgyűjtemény [app gallery](https://azure.microsoft.com/marketplace/active-directory/) vagy [egyéni alkalmazás](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/) hozzáadása
* Az alkalmazás konfigurálása a jelszó egyszeri bejelentkezéshez (SSO)
* [Csoportalapú hozzárendelés](groups-saasapps.md) használata és megosztott hitelesítő adatok megadásának kiválasztása

A többtényezős hitelesítés (MFA) (az [Azure AD-vel](../authentication/concept-mfa-whichversion.md)való védelemről is többet tud hatni), és delegálhatja azt a lehetőséget, hogy az [Azure AD önkiszolgáló](groups-self-service-management.md) csoportkezelés ével kezelje, hogy ki férhet hozzá az alkalmazáshoz.

## <a name="next-steps"></a>További lépések

* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Alkalmazások védelme feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoportmenedzsment/SSAA](groups-self-service-management.md)
