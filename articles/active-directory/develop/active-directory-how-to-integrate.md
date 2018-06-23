---
title: Az Azure Active Directory integrálása |} Microsoft Docs
description: Előnyei és a forrás, amely Azure Active Directory integrációja a útmutatóját.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev
ms.openlocfilehash: b59d86ddb8422108ece66eb1a7bbea808620d063
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319542"
---
# <a name="integrating-with-azure-active-directory"></a>Az Azure Active Directory integrálása
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Az Azure Active Directory biztosít a vállalati szintű felhőalapú alkalmazások Identitáskezelés szervezetek számára. Az Azure AD-integrációs a felhasználók egy zökkenőmentes bejelentkezési élményt biztosít, és lehetővé teszi a IT házirend felel meg az alkalmazás.

## <a name="how-to-integrate"></a>Integrálása
Az alkalmazás az Azure AD integrálása számos módja van. Számos, vagy kevés ezek a forgatókönyvek az alkalmazás megfelelő előnyeinek kihasználása.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Jelentkezzen be az alkalmazás egyik módja az Azure AD támogatja
**Csökkentse a súrlódás bejelentkezés, és csökkentheti a támogatási költségeket.** Jelentkezzen be az alkalmazás használatával az Azure AD, a felhasználóknak nem kell egy további neve, és jegyezze meg jelszót. Fejlesztői tárolhatják és megvédhetik egy kisebb jelszót kell. Ne kelljen elfelejtett jelszó-átállításra kezelni lehet önmagában egy jelentős megtakarítást. Az Azure AD bejelentkezés a világ legnépszerűbb felhőalapú alkalmazások, beleértve az Office 365 és a Microsoft Azure részénél megoldásaira épül. A száz millió felhasználók szervezetek több millió, valószínűleg a felhasználó már bejelentkezett az Azure ad Szolgáltatásba. További információ [az Azure AD bejelentkezés támogatását](active-directory-authentication-scenarios.md).

**Egyszerűbbé teszik a jelentkezzen be az alkalmazáshoz.**  Alatt a regisztrációt az alkalmazás az Azure AD, hogy előre töltse ki a regisztrációs űrlap vagy kiküszöbölheti a teljesen küldhet egy felhasználó alapvető adatait. Felhasználók regisztrálhatnak az alkalmazás használatával az Azure AD-fiókot, egy hasonló a közösségi média és mobilalkalmazások található ismerős hozzájárulási élmény keresztül. Bármely felhasználó regisztráljon, és jelentkezzen be olyan alkalmazás, amely integrálva van az Azure AD informatikai beavatkozás nélkül. További információ [aláíró létrehozása az Azure AD-fiók bejelentkezési alkalmazását](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Keresse meg a felhasználók számára, kezelheti a felhasználók átadása, és az alkalmazáshoz való hozzáférés szabályozása
**Keresse meg a felhasználók számára a címtárban.**  Segítség a felhasználóknak keresni, majd keresse meg a szervezet más munkatársainak, ha mások meghívása a Graph API használatával, vagy a hozzáférést, ahelyett, hogy azok írja be az e-mail címek. Felhasználók tallózhassanak a megszokott cím könyv stílus felületen keresztül, beleértve a szervezeti struktúra részleteinek megtekintése. További információ a [Graph API](active-directory-graph-api.md).

**Active Directory-csoportok és az ügyfél már kezel terjesztési listák újra felhasználhatja.**  Az Azure AD a csoportot, hogy az ügyfél már e-mailek terjesztése használja, és hozzáférés-kezelés tartalmaz. A Graph API-t használ, ezek a csoportok ahelyett, hogy az ügyfél létrehozása és kezelése az alkalmazás csoportok külön készletét újrafelhasználását. Felügyeleticsoport-információkat is lehet küldeni jogkivonatok bejelentkezés az alkalmazást. További információ a [Graph API](active-directory-graph-api.md).

**Használja az Azure AD, hogy ki jogosult az alkalmazás elérésére.**  A rendszergazdák és alkalmazástulajdonosok az Azure AD hozzáférési jogosultságot rendelhet alkalmazások meghatározott felhasználókhoz és csoportokhoz. A Graph API használatával, olvassa el ezen a listán, és segítségével szabályozhatja az üzembe helyezést és megszüntetést erőforrások és az alkalmazáson belül hozzáférni.

**Az Azure AD-szerepkörök alapuló hozzáférés-vezérlést.**  A rendszergazdák és alkalmazástulajdonosok rendelhet felhasználók és csoportok szerepkörök regisztrálnia az alkalmazást az Azure AD meg. Szerepköri információkat az alkalmazás a jogkivonatok bejelentkezés küldött, és is olvashatók a Graph API segítségével. További információ [Azure AD használata a hitelesítéshez](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Szerezze be a profil, naptár, e-mailek, névjegyek, fájlok, és több hozzáférési
**Az Azure AD a hitelesítési kiszolgáló Office 365 és az egyéb Microsoft üzleti szolgáltatások.**  Jelentkezzen be az alkalmazás vagy a támogatási az aktuális felhasználói fiókok összekapcsolása az OAuth 2.0 verziót használja Azure AD-felhasználói fiókok Azure AD támogatása, ha olvasási és írási hozzáférést a felhasználói profil, naptár, e-mailekhez, névjegyek, fájlok és más információkat kérhet. Akkor is zökkenőmentesen felhasználó naptár beírni az eseményeket és olvasására vagy írására fájlokat a onedrive vállalati verzió. További információ [való hozzáférés az Office 365 API-k](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Az alkalmazás az Azure és az Office 365 piacterek előléptetése
**Az alkalmazás már használja az Azure AD szervezetekhez millióit lépteti elő.**  Felhasználók keresése, és keresse meg a piactér már használja egy vagy több felhőszolgáltatások, így azok minősített felhőalapú szolgáltatás felhasználói. További információkat az alkalmazás [az Azure piactéren](https://azure.microsoft.com/marketplace/partner-program/).

**Amikor a felhasználók az alkalmazáshoz, az Azure AD hozzáférési panel és Office 365 alkalmazás indító fog megjelenni.**  Felhasználók fog tudni gyorsan és egyszerűen térjen vissza később, az alkalmazás felhasználói javítása. További információ a [Azure AD hozzáférési panel](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Eszköz-to-Service és a szolgáltatások közötti kommunikáció védelméhez
**Az Azure AD, az Identitáskezelés, a szolgáltatások és az eszközök csökkenti a kódot kell írnia, és lehetővé teszi, hogy informatikai való hozzáférést kezelheti.**  Szolgáltatások és az eszköz jogkivonatok lekérni az OAuth protokollt használó Azure AD és a jogkivonatok webes API-k elérésére használhat. Az Azure AD használatával elkerülheti a programozás összetett hitelesítést. Mivel a szolgáltatások és eszközök az identitások az Azure AD-tárolási informatikai kulcsok és a visszavont tanúsítványok ehhez külön-külön az alkalmazás helyett egy helyen kezelhetők.

## <a name="benefits-of-integration"></a>Integráció előnyei
Az Azure Active Directoryval való integrációhoz tartalmaz, amelyek nem igényelnek további kód írása előnyeit.

### <a name="integration-with-enterprise-identity-management"></a>Integráció a vállalati identitás kezelése
**Az IT-házirendeknek megfelelő alkalmazás segítségével.**  A szervezetek a vállalati identitás felügyeleti rendszerek integrálása az Azure AD, ha egy személy elhagyja a szervezet, azok automatikusan megszűnik a hozzáférése az alkalmazáshoz nem kell további lépések informatikai. Informatikai kezelheti az alkalmazás elérését és határozni, hogy milyen hozzáférési házirendek szükségesek – például többtényezős hitelesítés – ami csökkenti a vállalati házirendek összetett ahhoz, hogy a kód írása. Az Azure AD segítségével a rendszergazdák, akik bejelentkezett az alkalmazás így részletes naplózást informatikai használat nyomon követheti.

**Az Azure AD, hogy az alkalmazás integrálható az Active kiterjeszti a felhőbe az Active Directory.**  A világ számos szervezet Active Directory használata a fő be- és identitás felügyeleti rendszer, és igényelnek az alkalmazások működéséhez az ad-val. Az alkalmazás az Azure AD integrálása integrálható az Active Directory.

### <a name="advanced-security-features"></a>A speciális biztonsági szolgáltatások
**Többtényezős hitelesítés.**  Az Azure AD natív többtényezős hitelesítést biztosít. A rendszergazdák hozzáférni az alkalmazáshoz a multi-factor authentication megkövetelheti, így nem kell saját kezűleg a támogatás kód. További információ [multi-factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Rendellenes bejelentkezési észlelése.**  Az Azure AD dolgozza fel a több mint egymilliárd bejelentkezések napi gyanús tevékenységek észlelése és értesíthetők a informatikai rendszergazdák lehetséges problémák gépi tanulási algoritmusok használata során. Az Azure AD-bejelentkezés támogatásával az alkalmazás lekérdezi az előnye, hogy ez a védelem. További információ [Azure Active Directory hozzáférési jelentés megtekintése](../active-directory-view-access-usage-reports.md).

**Feltételes hozzáférés.**  Többtényezős hitelesítés mellett a rendszergazdák megkövetelheti bizonyos feltételeknek teljesülniük előtt felhasználók is bejelentkezhet az alkalmazásba. Feltételeket, amelyek állíthat be az IP-címtartomány ügyféleszközök, megadott csoportok tagságát és az eszköz eléréshez használt állapotát tartalmazza. További információ [Azure Active Directory feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Könnyű fejlesztés
**Iparági szabványos protokollt használják.**  A Microsoft elkötelezett az ipari szabványok támogatása. Az Azure AD a SAML 2.0, az OpenID Connect 1.0, az OAuth 2.0 és a WS-Federation 1.2-es hitelesítési protokollt támogat. A Graph API OData 4.0-s megfelelő. Az alkalmazás már támogatja az SAML 2.0-s vagy OpenID Connect 1.0 protokoll jelentkezhetnek be összevont módon, ha az Azure AD támogatása egyszerű is lehet. További információ [az Azure AD hitelesítési protokollok támogatott](active-directory-authentication-protocols.md).

**Nyílt forráskódú szalagtárak.**  A Microsoft teljes mértékben támogatott nyílt forráskódú szalagtárak népszerű nyelvekhez és platformokhoz sebesség fejlesztés biztosít. A forráskód Apache 2.0 licencbe, és szabadon elágaztatásáról és hozzájárulnak a projektek. További információ [az Azure AD-hitelesítési kódtárakkal](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Globális jelenlét és magas rendelkezésre állás
**Az Azure AD a világ különböző adatközpontokban van telepítve van és kezelhető és figyelhető éjjel is.**  Az Azure AD a Microsoft Azure és az Office 365 identity management rendszert, és telepítve van az világszerte 28 adatközpontokban. Címtáradatok garantáltan legalább három adatközpontokhoz replikálni. Globális terheléselosztók biztosítása a felhasználók hozzáférést a legközelebbi másolatát tartalmazó az adatokat az Azure AD és az automatikusan újból útvonal-kérelmek más adatközpontokhoz Ha probléma merül fel.

## <a name="next-steps"></a>További lépések
[Első lépések programozás](active-directory-developers-guide.md#get-started).

[Az Azure AD használatával a felhasználók](active-directory-authentication-scenarios.md)

