---
title: Alkalmazások kezelése az Azure Active Directoryban | Microsoft Docs
description: Ez a cikk ismerteti az Azure Active Directory integrálásának előnyeit a helyszíni, felhőbeli és SaaS-alkalmazásokkal.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063322"
---
# <a name="application-management-with-azure-active-directory"></a>Alkalmazáskezelés az Azure Active Directory használatával

Az Azure Active Directory (Azure AD) leegyszerűsíti az alkalmazások kezelésének módját azáltal, hogy egyetlen identitásrendszert biztosít a felhőbeli és a helyszíni alkalmazásokszámára. A szoftvert szolgáltatásként (SaaS), helyszíni alkalmazásokként és üzletági (LOB) alkalmazásokként is hozzáadhatja az Azure AD-hez. Ezután a felhasználók egyszer jelentkeznek be, hogy biztonságosan és zökkenőmentesen hozzáférjenek ezekhez az alkalmazásokhoz, valamint az Office 365-höz és a Microsoft más üzleti alkalmazásaihoz. A [felhasználók kiépítésének automatizálásával](../app-provisioning/user-provisioning.md)csökkentheti az adminisztratív költségeket. A többtényezős hitelesítési és feltételes hozzáférési házirendek biztonságos alkalmazás-hozzáférést is biztosíthatnak.

![Az Azure AD-n keresztül összevont alkalmazásokat bemutató diagram](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Miért jó az alkalmazásokat felhőalapú megoldással kezelni?

A szervezetekben gyakran több száz alkalmazás is van, amitől a felhasználók munkavégzése függ. A felhasználók sokféle eszközről és helyről érik el ezeket az alkalmazásokat. Minden nap új alkalmazásokat adnak hozzá, fejlesztenek és selejteznek le. A sok alkalmazás és hozzáférési pont miatt minden eddiginél fontosabb, hogy felhőalapú megoldást használjon az összes alkalmazás felhasználói hozzáférésének kezeléséhez.

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Milyen típusú alkalmazásokat integrálhatok az Azure AD-vel?

Négy fő alkalmazástípus létezik, amelyet hozzáadhat a **vállalati alkalmazásokhoz,** és amelyet az Azure AD-vel kezelhet:

- **Azure AD Gallery-alkalmazások** – Az Azure AD egy olyan katalógussal rendelkezik, amely több ezer olyan alkalmazást tartalmaz, amelyek et előre integráltak az Azure AD-vel való egyszeri bejelentkezéshez. A vállalat által használt alkalmazások egy része valószínűleg megtalálható a katalógusban. [Ismerje meg az alkalmazásintegráció megtervezését,](plan-an-application-integration.md)vagy az egyes alkalmazások részletes [integrációs lépéseit a SaaS-alkalmazásoktatóanyagokban.](https://docs.microsoft.com/azure/active-directory/saas-apps/)

- **Helyszíni alkalmazások alkalmazásproxyval** – Az Azure AD alkalmazásproxyval integrálhatja a helyszíni webalkalmazásokat az Azure AD-vel az egyszeri bejelentkezés támogatásához. Ezután a végfelhasználók ugyanúgy érhetik el a helyszíni webalkalmazásokat, mint az Office 365-öt és más SaaS-alkalmazásokat. [További információ az alkalmazásproxy használatáról és működéséről.](what-is-application-proxy.md)

- **Egyéni fejlesztésű alkalmazások** – Saját üzletági alkalmazások létrehozásakor integrálhatja őket az Azure AD-vel az egyszeri bejelentkezés támogatásához. Az alkalmazás regisztrálásával az Azure AD, szabályozhatja az alkalmazás hitelesítési szabályzatát. További információt a [fejlesztőknek szóló útmutatóban](developer-guidance-for-integrating-applications.md)talál.

- **Nem Galéria alkalmazások** - Hozd a saját alkalmazások! Támogatja az egyszeri bejelentkezést más alkalmazásokhoz az Azure AD-hez való hozzáadásával. Bármilyen webes hivatkozást integrálhat, vagy bármely olyan alkalmazást, amely felhasználónév- és jelszómezőt jelenít meg, támogatja az SAML vagy OpenID Connect protokollokat, vagy támogatja az SCIM-et. További információ: [Egyszeri bejelentkezés konfigurálása nem galériaalkalmazásokhoz című témakörben.](configure-single-sign-on-non-gallery-applications.md)

## <a name="manage-risk-with-conditional-access-policies"></a>Kockázatok kezelése feltételes hozzáférési házirendekkel

Az Azure AD egyszeri bejelentkezése feltételes [hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) nagyfokú biztonságot nyújt az alkalmazások eléréséhez. A biztonsági funkciók közé tartozik a felhőszintű identitásvédelem, a kockázatalapú hozzáférés-vezérlés, a natív többtényezős hitelesítés és a feltételes hozzáférési házirendek. E funkciókkal részletesen szabályozható szabályzatok állíthatók be alkalmazások vagy magasabb szintű biztonságot igénylő csoportok alapján.

## <a name="improve-productivity-with-single-sign-on"></a>Termelékenység növelése egyszeri bejelentkezéssel

Az egyszeri bejelentkezés (SSO) a bejelentkezési kérések számának csökkentésével vagy a kérések kiiktatásával kiváló bejelentkezési élményt biztosít a meglévő felhasználók számára a különböző alkalmazások és az Office 365 esetében. A felhasználó egységesebb környezetet érzékel és kevésbé zavarja a sok bejelentkezési kérés és a sokféle jelszó kezelésének szükségessége. Az üzleti csoport az önkiszolgálással és a dinamikus tagsággal kezelni és engedélyezni tudja a hozzáféréseket. Az alkalmazáshoz való hozzáférések kezelésének a vállalaton belül a megfelelő emberek számára történő lehetővé tétele javítja az azonosítási rendszer biztonságát.

Az egyszeri bejelentkezés javítja a biztonságot. Az *egyszeri bejelentkezés nélkül* a rendszergazdáknak minden egyes alkalmazáshoz külön kell felhasználói fiókokat létrehozni és frissíteni, ami időbe kerül. A felhasználóknak is többféle hitelesítő adatot kell nyomon követniük az alkalmazásaik eléréséhez. Ennek eredményeképpen a felhasználók hajlanak arra, hogy leírják a jelszavaikat vagy másféle jelszókezelési megoldást használjanak, ami adatbiztonsági kockázatot jelent. [További információ az egyszeri bejelentkezésről.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Irányítás és megfelelőség

Az Azure AD segítségével az alkalmazás bejelentkezéseket a biztonsági információk és események kezelésének (SIEM) eszközeit felhasználó jelentésekkel követheti nyomon. A jelentéseket a portálról vagy az API-kból érheti el. Programozott módon naplózza, ki fér hozzá az alkalmazásokhoz és megszünteti az inaktív felhasználók hozzáférését a hozzáférési felülvizsgálatokkal.

## <a name="manage-costs"></a>Költségek kezelése

Az Azure AD-re történő átállással költségeket takarít meg és megszabadul a helyszíni infrastruktúra kezelésével járó gondoktól. Az Azure AD önkiszolgáló hozzáférést is nyújt az alkalmazásokhoz, amivel a rendszergazdák és felhasználók számára is időt takarít meg. Az egyszeri bejelentkezés használata esetén nincs szükség alkalmazásspecifikus jelszavakra. Az egyszeri bejelentkezéssel megtakaríthatók az új alkalmazásjelszavak kéréséhez és a jelszavak beszerzése közben kieső termelékenységhez kapcsolódó költségek.

## <a name="next-steps"></a>További lépések

- [Mi az alkalmazásproxy?](what-is-application-proxy.md)
- [Rövid útmutató: Katalógusalkalmazás hozzáadása az Azure AD-bérlőhöz](add-application-portal.md)
