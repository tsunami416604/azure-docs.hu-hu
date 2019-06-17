---
title: Alkalmazások kezelése az Azure Active Directoryban | Microsoft Docs
description: Ez a cikk ismerteti az Azure Active Directory integrálása a helyszíni, felhőbeli és a SaaS-alkalmazások előnyeit.
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
ms.openlocfilehash: 9246d7bd48579def171986606e88c09593029aa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108150"
---
# <a name="application-management-with-azure-active-directory"></a>Alkalmazáskezelés az Azure Active Directory használatával

Az Azure Active Directory (Azure AD) leegyszerűsíti a kezelhet alkalmazásokat a felhőbeli és helyszíni alkalmazások egyetlen identitás rendszert biztosítanak. A szoftverfrissítési szolgáltatásként (SaaS) is hozzáadhat alkalmazásokat, a helyszíni alkalmazások és üzletági (LOB) alkalmazásokat az Azure ad-hez. Majd bejelentkeznek egyszer biztonságosan és akadálytalanul hozzáférni ezekhez az alkalmazásokhoz és az Office 365 és a Microsoft más üzleti alkalmazásokban. Felhasználók létrehozásának automatizálása is csökkenthetők az adminisztratív költségek. A multi-factor authentication és a feltételes hozzáférési szabályzatok segítségével is biztonságos hozzáférést biztosítanak.

![Az Azure AD-n keresztül összevont alkalmazások](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Minden nap új alkalmazásokat adnak hozzá, fejlesztenek és selejteznek le. Sok alkalmazásokkal és hozzáférési pontok rendkívül fontos több használata minden eddiginél egy felhőalapú megoldás felhasználói hozzáférést az összes alkalmazás kezeléséhez.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Milyen típusú alkalmazások integrálása az Azure ad-ben?
Az alkalmazásokat, amelyek adhat hozzá négy fő típusa van a **vállalati alkalmazások** és kezelése az Azure ad-vel:

-   **Az Azure AD katalógusából származó alkalmazások** – az Azure AD rendelkezik egy katalógus, amely több ezer előre integrált, az egyszeri bejelentkezés az Azure AD-alkalmazásokat. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. [Ismerje meg az alkalmazás-integráció megtervezése](plan-an-application-integration.md), vagy szerezze be a részletes integrációs lépések az egyes alkalmazások a [SaaS-alkalmazások oktatóanyagai](https://docs.microsoft.com/azure/active-directory/saas-apps/). 

-   **A helyszíni alkalmazásokat az alkalmazásproxy használatával** – az Azure AD-alkalmazásproxyval, integrálható a helyszíni webalkalmazásokhoz az Azure AD egyszeri bejelentkezés támogatására. Ezután a végfelhasználók elérhetik a helyszíni webalkalmazásokhoz férnek hozzá az Office 365 és más SaaS-alkalmazások azonos módon. [Ismerje meg, ezért Application Proxy használatára és annak működéséről](what-is-application-proxy.md).

-   **Saját fejlesztésű alkalmazásokat** – a saját üzleti alkalmazások létrehozását, integrálhatja őket az Azure AD egyszeri bejelentkezés támogatására. Regisztrálja az alkalmazást az Azure ad-vel, a hitelesítési szabályzatot az alkalmazáshoz hozzáféréssel rendelkeznek. További információkért lásd: [fejlesztői útmutató olvasható](developer-guidance-for-integrating-applications.md).

-   **Katalógusban nem szereplő alkalmazások** – saját alkalmazások importálása! Támogatja az egyszeri bejelentkezés más alkalmazások adja hozzá őket az Azure AD. Minden olyan webes hivatkozás azt szeretné, vagy bármilyen alkalmazás, amely egy felhasználónevet és jelszót a mezőben jelenik meg, támogatja az SAML- vagy OpenID Connect protokollok vagy támogatja az SCIM integrálható. További információkért lásd: [egyszeri bejelentkezés nem katalógusból származó alkalmazások esetében konfigurálhatja](configure-single-sign-on-non-gallery-applications.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatkezelés feltételes hozzáférési szabályzatok
Az Azure AD egyszeri bejelentkezés (SSO) kapcsoló [feltételes hozzáférési](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) magas szintű biztonságot biztosít az alkalmazásokhoz való hozzáférésüket. Biztonsági funkciók között szerepel a felhőméretű identity protection, a kockázat-alapú hozzáférés-vezérlés, a natív a multi-factor authentication és a feltételes hozzáférési szabályzatok. E funkciókkal részletesen szabályozható szabályzatok állíthatók be alkalmazások vagy magasabb szintű biztonságot igénylő csoportok alapján.

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel
Az egyszeri bejelentkezés (SSO) a bejelentkezési kérések számának csökkentésével vagy a kérések kiiktatásával kiváló bejelentkezési élményt biztosít a meglévő felhasználók számára a különböző alkalmazások és az Office 365 esetében. A felhasználó egységesebb környezetet érzékel és kevésbé zavarja a sok bejelentkezési kérés és a sokféle jelszó kezelésének szükségessége. Az üzleti csoport az önkiszolgálással és a dinamikus tagsággal kezelni és engedélyezni tudja a hozzáféréseket. Az alkalmazáshoz való hozzáférések kezelésének a vállalaton belül a megfelelő emberek számára történő lehetővé tétele javítja az azonosítási rendszer biztonságát.

Az egyszeri bejelentkezés javítja a biztonságot. Az *egyszeri bejelentkezés nélkül* a rendszergazdáknak minden egyes alkalmazáshoz külön kell felhasználói fiókokat létrehozni és frissíteni, ami időbe kerül. A felhasználóknak is többféle hitelesítő adatot kell nyomon követniük az alkalmazásaik eléréséhez. Ennek eredményeképpen a felhasználók hajlanak arra, hogy leírják a jelszavaikat vagy másféle jelszókezelési megoldást használjanak, ami adatbiztonsági kockázatot jelent. [További információt az egyszeri bejelentkezés](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség
Az Azure AD segítségével az alkalmazás bejelentkezéseket a biztonsági információk és események kezelésének (SIEM) eszközeit felhasználó jelentésekkel követheti nyomon. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése
Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

## <a name="next-steps"></a>További lépések

- [Mi az Application Proxy?](what-is-application-proxy.md)
- [Rövid útmutató: Az Azure AD-bérlő katalógus alkalmazás hozzáadása](add-application-portal.md)
