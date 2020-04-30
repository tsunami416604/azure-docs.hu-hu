---
title: Alkalmazások kezelése az Azure Active Directoryban | Microsoft Docs
description: Ez a cikk a Azure Active Directory helyszíni, felhőalapú és SaaS-alkalmazásokkal való integrálásának előnyeit ismerteti.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 06/05/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca73fac06649f801461e53130a67aa9ec0ad0d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77063322"
---
# <a name="application-management-with-azure-active-directory"></a>Alkalmazásfelügyelet az Azure Active Directoryval

A Azure Active Directory (Azure AD) leegyszerűsíti az alkalmazások kezelését úgy, hogy egyetlen identitást biztosít a Felhőbeli és a helyszíni alkalmazások számára. A szolgáltatott szoftverek (SaaS), helyszíni alkalmazások és üzletági (LOB) alkalmazások is hozzáadhatók az Azure AD-hez. Ezután a felhasználók egyszer bejelentkeznek az alkalmazások biztonságos és zökkenőmentes eléréséhez, valamint az Office 365 és a Microsoft egyéb üzleti alkalmazásaihoz. A [felhasználók üzembe helyezésének automatizálásával](../app-provisioning/user-provisioning.md)csökkentheti az adminisztrációs költségeket. A többtényezős hitelesítést és a feltételes hozzáférési szabályzatokat is használhatja a biztonságos alkalmazás-hozzáférés biztosításához.

![Az Azure AD-n keresztül összevont alkalmazásokat bemutató diagram](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Minden nap új alkalmazásokat adnak hozzá, fejlesztenek és selejteznek le. A sok alkalmazással és hozzáférési ponttal minden eddiginél nagyobb jelentőséggel bír a felhőalapú megoldás használata az összes alkalmazás felhasználói hozzáférésének kezeléséhez.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Milyen típusú alkalmazásokat lehet integrálni az Azure AD-vel?

A **nagyvállalati alkalmazásokhoz** és az Azure ad-vel való felügyelethez négy fő típusú alkalmazás adható meg:

- **Azure ad Gallery-alkalmazások** – az Azure ad tartalmaz egy katalógust, amely az Azure ad-vel való egyszeri bejelentkezéshez előre integrált, több ezer alkalmazást tartalmaz. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. [Ismerje meg az alkalmazások integrálásának megtervezését](plan-an-application-integration.md), vagy a [SaaS-alkalmazás oktatóanyagokban](https://docs.microsoft.com/azure/active-directory/saas-apps/)részletes integrációs lépéseket kaphat az egyes alkalmazásokhoz.

- Helyszíni **alkalmazások alkalmazásproxy** használatával – az Azure ad Application proxy segítségével a helyszíni webalkalmazásokat az Azure ad-vel integrálva támogathatja az egyszeri bejelentkezést. Ezután a végfelhasználók ugyanúgy férhetnek hozzá a helyszíni webalkalmazásokhoz, mint az Office 365 és más SaaS-alkalmazások. [Tudnivalók az alkalmazásproxy használatáról és működéséről](what-is-application-proxy.md).

- **Testreszabott alkalmazások** – a saját üzletági alkalmazásai kiépítésekor az egyszeri bejelentkezés támogatásához integrálhatja őket az Azure ad-vel. Az alkalmazás Azure AD-vel való regisztrálásával szabályozhatja az alkalmazás hitelesítési házirendjét. További információ: [útmutató a fejlesztőknek](developer-guidance-for-integrating-applications.md).

- **Galérián kívüli alkalmazások** – saját alkalmazások használata Az Azure AD-be való felvételsel más alkalmazásokhoz is támogathatja az egyszeri bejelentkezést. Integrálhat bármilyen kívánt webhivatkozást, vagy bármely olyan alkalmazást, amely a Felhasználónév és a jelszó mezőt jeleníti meg, támogatja az SAML vagy OpenID Connect protokollokat, vagy támogatja a SCIM. További információ: [az egyszeri bejelentkezés konfigurálása nem katalógusbeli alkalmazásokhoz](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatkezelés feltételes hozzáférési szabályzatokkal

A [feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) rendelkező Azure ad egyszeri bejelentkezés (SSO) összekapcsolása magas szintű biztonságot nyújt az alkalmazások eléréséhez. A biztonsági képességek közé tartozik a felhőalapú Identitáskezelés, a kockázatalapú hozzáférés-vezérlés, a natív többtényezős hitelesítés és a feltételes hozzáférési szabályzatok. E funkciókkal részletesen szabályozható szabályzatok állíthatók be alkalmazások vagy magasabb szintű biztonságot igénylő csoportok alapján.

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel

Az egyszeri bejelentkezés (SSO) a bejelentkezési kérések számának csökkentésével vagy a kérések kiiktatásával kiváló bejelentkezési élményt biztosít a meglévő felhasználók számára a különböző alkalmazások és az Office 365 esetében. A felhasználó egységesebb környezetet érzékel és kevésbé zavarja a sok bejelentkezési kérés és a sokféle jelszó kezelésének szükségessége. Az üzleti csoport az önkiszolgálással és a dinamikus tagsággal kezelni és engedélyezni tudja a hozzáféréseket. Az alkalmazáshoz való hozzáférések kezelésének a vállalaton belül a megfelelő emberek számára történő lehetővé tétele javítja az azonosítási rendszer biztonságát.

Az egyszeri bejelentkezés javítja a biztonságot. Az *egyszeri bejelentkezés nélkül* a rendszergazdáknak minden egyes alkalmazáshoz külön kell felhasználói fiókokat létrehozni és frissíteni, ami időbe kerül. A felhasználóknak is többféle hitelesítő adatot kell nyomon követniük az alkalmazásaik eléréséhez. Ennek eredményeképpen a felhasználók hajlanak arra, hogy leírják a jelszavaikat vagy másféle jelszókezelési megoldást használjanak, ami adatbiztonsági kockázatot jelent. [További információ az egyszeri bejelentkezésről](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség

Az Azure AD segítségével az alkalmazás bejelentkezéseket a biztonsági információk és események kezelésének (SIEM) eszközeit felhasználó jelentésekkel követheti nyomon. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése

Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

## <a name="next-steps"></a>További lépések

- [Mi az alkalmazásproxy?](what-is-application-proxy.md)
- [Gyors útmutató: gyűjteménybeli alkalmazás hozzáadása az Azure AD-bérlőhöz](add-application-portal.md)
