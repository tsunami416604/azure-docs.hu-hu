---
title: Az Azure Active Directory integrálása |} A Microsoft Docs
description: Egy útmutató, amellyel a kedvezményes szolgáltatások és erőforrások az Azure Active Directory-integráció.
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
ms.openlocfilehash: cde2706db255746293ddc29c6d5bdd31d970e535
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901549"
---
# <a name="integrating-with-azure-active-directory"></a>Az Azure Active Directory integrálása

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Az Azure Active Directory nagyvállalati szintű Identitáskezelés a felhőalapú alkalmazások a szervezetek számára nyújt. Az Azure AD-integráció lehetővé teszi a felhasználók számára zökkenőmentes bejelentkezési élményt, és segít az informatikai szabályzatnak megfelel az alkalmazás.

## <a name="how-to-integrate"></a>Hálófrissítések integrálása
Többféleképpen is, hogy az alkalmazás integrálása az Azure ad-ben. Sok vagy néhány ezekben a forgatókönyvekben, mivel az alkalmazás megfelelő előnyeinek kihasználása.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Az Azure AD arra, hogy jelentkezzen be az alkalmazás támogatja
**Fennakadások nélkül használható bejelentkezési csökkentheti, és csökkentheti a támogatási költségeket.** Az Azure AD használatával jelentkezzen be az alkalmazást, a felhasználóknak nem kell egy további nevét és jelszavát, ne felejtse el. A fejlesztők akkor tárolja és védi egy rövidebb jelszót. Elfelejtett jelszó-visszaállítási kezelésére nem rendelkezik, előfordulhat, hogy egy önálló jelentős megtakarítást. Az Azure AD bejelentkezési néhány, a világ legnépszerűbb felhőalapú alkalmazások, beleértve az Office 365 és a Microsoft Azure működteti. Több százmilliós felhasználói a szervezetek több millió felhasználóját, valószínűleg a felhasználó már bejelentkezett az Azure ad-hez. Tudjon meg többet [támogatása az Azure AD-bejelentkezés](authentication-scenarios.md).

**Egyszerűsítése érdekében jelentkezzen be az alkalmazás.**  Jelentkezzen be az alkalmazás, során az Azure AD küldhet felhasználókkal kapcsolatos alapvető információk, hogy előre töltse ki a regisztrációs űrlapot, vagy teljesen ki azt. Felhasználók az alkalmazás használatával az Azure AD-fiókja egy hasonló a közösségimédia- és mobilalkalmazások található ismerős jóváhagyási felületen keresztül regisztrálhat. Bármely felhasználó is regisztráljon, és jelentkezzen be egy alkalmazás, amely integrálva van az Azure AD informatikai bevonása nélkül. Tudjon meg többet [regisztrál az alkalmazás Azure AD-fiók bejelentkezési](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Keresse meg a felhasználók számára, kezelheti a Felhasználókiépítés és az alkalmazásokhoz való hozzáférés szabályozása
**Tallózás a címtárban lévő felhasználók számára.**  Segítség a felhasználóknak a keresése, és keresse meg a szervezet más tagjai, amikor mások meghívása a Graph API használatával, vagy hozzáférést helyett írja be az e-mail-címek. Felhasználók böngészhetnek ismerős cím könyv stílus felületen, beleértve a szervezeti hierarchia részleteinek megtekintése. Tudjon meg többet a [Graph API](active-directory-graph-api.md).

**Újra felhasználhatja az Active Directory-csoportokkal és terjesztési listákat az ügyfél már kezel.**  Azure ad-ben a csoportokat, hogy az ügyfél már használja az e-mailek terjesztése és hozzáférés-kezelés tartalmazza. A Graph API-val, ezeket a csoportokat hozhat létre és kezelhet a csoportok az alkalmazásban külön készlete ügyfele átjárószolgáltatásnak újrafelhasználását. Csoport adatait is lehet küldeni jogkivonatok bejelentkezési az alkalmazását. Tudjon meg többet a [Graph API](active-directory-graph-api.md).

**Az Azure AD, hogy ki férhet hozzá az alkalmazás használja.**  Rendszergazdák és az Azure ad-ben alkalmazástulajdonosok rendelhet adott felhasználók és csoportok alkalmazásokhoz való hozzáférés. A Graph API-val, olvassa el ezt a listát, és ezzel üzembe helyezést és megszüntetést erőforrások vezérlőelemet, és az alkalmazáson belül eléréséhez.

**Az Azure AD szerepkör alapú hozzáférés-vezérlés.**  A rendszergazdák és az alkalmazástulajdonosok rendelhet felhasználókat és csoportokat is regisztrálhatja, az alkalmazás Azure AD-ben megadhat szerepköröket. Szerepköradatok jogkivonatok bejelentkezési az alkalmazását a érkezik, és is olvashatja el a Graph API használatával. Tudjon meg többet [az Azure AD engedélyezési](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Hozzáférhet a felhasználó profilját, naptár, e-mailt, névjegyek, fájlok és több
**Azure AD egy Office 365-höz és más Microsoft üzleti szolgáltatásokat az engedélyezési kiszolgálón.**  Jelentkezzen be az alkalmazás vagy a jelenlegi felhasználói fiókok összekapcsolása az OAuth 2.0-val az Azure AD-felhasználói fiókok támogatása támogatja, az Azure ad-ben, ha az olvasási és írási hozzáférés egy felhasználó profilját, naptár, e-mail, névjegyek, fájlok és egyéb információkat kérhető. Akkor is zökkenőmentesen beírni az eseményeket a felhasználó naptár, és olvasási vagy írási fájlok a onedrive vállalati verzió. Tudjon meg többet [az Office 365 API-k elérése](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Népszerűsítse alkalmazását az Azure és az Office 365-piactér
**Alkalmazásának reklámozása az Azure ad-ben már használó szervezetek millió.**  Felhasználók, akik keressen, és keresse meg a piactér már használja egy vagy több felhőszolgáltatások, így azok minősített felhőalapú szolgáltatás felhasználói. További információ az alkalmazását előléptetése [az Azure Marketplace-en](https://azure.microsoft.com/marketplace/partner-program/).

**Amikor a felhasználók a regisztrációt az alkalmazás meg fog jelenni az Azure AD hozzáférési panel és Office 365 appindítóban.**  Felhasználók tudják, gyorsan és egyszerűen térjen vissza később, az alkalmazás fejlesztése a felhasználói érdeklődést. Tudjon meg többet a [Azure AD hozzáférési panel](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Eszköz-to-Service és a szolgáltatások közötti kommunikáció védelméhez
**Az Azure AD, az Identitáskezelés, a szolgáltatások és az eszközök csökkenti a kódot kell írnia, és lehetővé teszi, hogy informatikai való hozzáférés kezelésére.**  Szolgáltatások és eszközök az Azure AD OAuth protokollt használó tokenekhez és webes API-k elérésére a jogkivonatok használatával. Az Azure AD használatával elkerülheti a összetett hitelesítő kód írása. Mivel a szolgáltatások és eszközök identitása Azure AD-ben vannak tárolva informatikai kulcsokat és a visszavont tanúsítványok ahelyett, hogy ehhez külön-külön az alkalmazásban egy helyen kezelhető.

## <a name="benefits-of-integration"></a>Integráció előnyei
Az Azure AD-integrációs származnak, olyan előnyöket kínál, nincs szükség további kódot kellene írnia.

### <a name="integration-with-enterprise-identity-management"></a>Integráció a vállalati identitás kezelése
**Az IT-házirendeknek eleget alkalmazás segítségével.**  Szervezetek számára a vállalati identitáskezelő-rendszer integrálása az Azure ad-vel, így ha egy személy kilép a szervezetből, azok automatikusan megszűnik a hozzáférése nélkül az alkalmazás további lépések végrehajtására kellene informatikai. Informatikai kezelheti, kik érhetik el az alkalmazást, és határozza meg, milyen hozzáférési házirendek szükségesek – például a multi-factor authentication - csökkenti az kell, hogy megfeleljenek a vállalati szabályzatoknak összetett kódot írni. Az Azure AD segítségével a rendszergazdák, akik jelentkezett be az alkalmazás így egy részletes napló informatikai használat nyomon követheti.

**Az Azure AD kiterjeszti a felhőbe, hogy az alkalmazás integrálva az ad-vel.**  A világ számos szervezet Active Directory használja az egyszerű bejelentkezés és az identitáskezelési rendszerekkel, és alkalmazásaikat, az ad-vel munkahelyi megkövetelése. Az alkalmazás integrálása az Azure ad-ben együttműködik az Active Directoryval.

### <a name="advanced-security-features"></a>A speciális biztonsági funkciók
**A multi-factor authentication.**  Az Azure AD natív multi-factor Authentication hitelesítést biztosít. A rendszergazdák kötelezhetik a multi-factor authentication szolgáltatást az alkalmazás eléréséhez, így nem kell a támogatás kódolásával. Tudjon meg többet [multi-factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Rendellenes bejelentkezési észlelése.**  Azure ad-ben több mint egymilliárd bejelentkezések a gépi tanulási algoritmusok használata a gyanús tevékenységek észlelése és a lehetséges problémákat a rendszergazdák értesítése során a naponta dolgozza fel. Támogatásával az Azure AD-be, az alkalmazás lekéri az előnye, hogy ez a védelem. Tudjon meg többet [Azure Active Directory hozzáférési jelentést megtekintő](../active-directory-view-access-usage-reports.md).

**Feltételes hozzáférés.**  A multi-factor authentication, mellett a rendszergazdák megkövetelhetik meghatározott feltételek teljesülnek megelőzően is bejelentkeznek az alkalmazásba. Feltételeket, amelyek akkor állítható be az IP-címtartományt, ügyféloldali eszközök, adott csoportok tagjai és az eszköz eléréshez használt állapotát tartalmazza. Tudjon meg többet [Azure Active Directory feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Egyszerű fejlesztését
**Az iparági szabványos protokollok.**  A Microsoft elkötelezett az iparági szabványok támogatása. Azure ad-ben az SAML 2.0, OpenID Connect 1.0, OAuth 2.0 és WS-Federation 1.2-es hitelesítési protokollt támogat. A Graph API egy OData 4.0-s szabványnak megfelelő. Ha az alkalmazás már támogatja az SAML 2.0 vagy OpenID Connect 1.0 protokoll az összevont bejelentkezési, támogatása az Azure AD lehetnek könnyen érthető megjegyzésblokkok írására. Tudjon meg többet [Azure ad-ben támogatott hitelesítési protokollok](active-directory-authentication-protocols.md).

**Nyílt forráskódtárak.**  A Microsoft teljes körűen támogatott nyílt forráskódtárak biztosít olyan népszerű programozási nyelvek és platformok gyorsítsa fel a fejlesztést. A forráskód licencelése a Apache 2.0, és szabadon ágaztathatja el és hozzájárulnak a projekteket. Tudjon meg többet [az Azure AD hitelesítési kódtárai](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Globális jelenlétét és magas rendelkezésre állás
**Az Azure AD a világ különböző pontjain található adatközpontokban van üzembe helyezve és a felügyelt és figyelt éjjel-nappal.**  Azure ad-ben az identitáskezelési rendszerekkel a Microsoft Azure és Office 365 és a világ különböző pontjain 28 adatközpontokban van üzembe helyezve. Címtáradatok garantáltan legalább három adatközpontokkal replikálása. Globális terheléselosztók felhasználók hozzáférésének biztosítása a legközelebbi tartalmazó adataikat az Azure AD példányát, és automatikusan újra irányíthatja a kérelmeket más adatközpontokban, ha probléma merül fel.

## <a name="next-steps"></a>További lépések
[Ismerkedés a kódírás](azure-ad-developers-guide.md#get-started).

[Az Azure AD-felhasználók beléptetése](authentication-scenarios.md)

