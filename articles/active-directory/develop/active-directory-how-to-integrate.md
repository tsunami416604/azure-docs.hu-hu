---
title: Integrálás az Azure Active Directoryval | Microsoft dokumentumok
description: Ismerje meg az alkalmazás Azure Active Directoryval való integrálásának előnyeit, és szerezzen be erőforrásokat olyan funkciókhoz, mint az egyszerűsített bejelentkezés, az identitáskezelés, a többtényezős hitelesítés és a hozzáférés-vezérlés.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 5e4c6fb91b93b1035c68350a5f77a3c16db7323f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300112"
---
# <a name="integrating-with-azure-active-directory"></a>Integráció az Azure Active Directoryval

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ebben a cikkben megismerheti az alkalmazás azure Active Directoryval (Azure AD) való integrálásának előnyeit, és erőforrásokat kaphat az integrációhoz. Az Azure AD nagyvállalati szintű identitáskezelést biztosít a szervezetek számára a felhőalapú alkalmazásokszámára. Az Azure AD-integráció leegyszerűsíti a felhasználók bejelentkezési élményét, és segít az alkalmazásnak az informatikai szabályzatnak való megfelelésben.

## <a name="how-to-integrate"></a>Az integrálás menete

Az alkalmazás számos módon integrálható az Azure AD-vel. Használja ki a lehető legtöbb vagy néhány ilyen forgatókönyvek, mint az alkalmazás megfelelő.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Az Azure AD támogatása az alkalmazásba való bejelentkezés hez

**Csökkentse a bejelentkezési súrlódást és csökkentse a támogatási költségeket.** Az Azure AD használatával jelentkezzen be az alkalmazásba, a felhasználók nem lesz több nevet és jelszót megjegyezni. Fejlesztőként eggyel kevesebb jelszót kell tárolnia és védenie. Nem kell kezelni elfelejtett jelszó alaphelyzetbe lehet jelentős megtakarítást egyedül. Az Azure AD a világ legnépszerűbb felhőalkalmazásaihoz, köztük az Office 365-höz és a Microsoft Azure-hoz jelentkezik be. Több millió szervezet több százmillió felhasználójával valószínű, hogy a felhasználó már be van jelentkezve az Azure AD-be. További információ az [Azure AD-bejelentkezés támogatásának hozzáadásáról.](authentication-scenarios.md)

**Egyszerűsítse a regisztrációt a jelentkezésére.**  Az alkalmazásra való feliratkozás során az Azure AD alapvető információkat küldhet egy felhasználóról, így előre kitöltheti a regisztrációs űrlapot, vagy teljesen megszüntetheti azt. A felhasználók az Azure AD-fiókjuk használatával regisztrálhatnak az alkalmazásra a közösségi médiában és a mobilalkalmazásokban találhatóakhoz hasonló ismerős hozzájárulási élményen keresztül. Bármely felhasználó regisztrálhat, és bejelentkezhet egy olyan alkalmazásba, amely integrálva van az Azure AD-vel informatikai beavatkozás nélkül. További információ [az alkalmazás Azure AD-fiókra való regisztrációjáról.](../../app-service/configure-authentication-provider-aad.md)

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Felhasználók keresése, a felhasználók kiépítésének kezelése és az alkalmazáshoz való hozzáférés szabályozása

**Keresse meg a felhasználókat a címtárban.**  A Microsoft Graph API segítségével a felhasználók a szervezetmás személyek et kereshetnek és kereshetnek, amikor másokat hívnak meg vagy hozzáférést adnak, ahelyett, hogy e-mail-címek megadását kellene megkövetelniük tőlük. A felhasználók egy ismerős címjegyzék-stílus felülethasználatával böngészhetnek, beleértve a szervezeti hierarchia részleteinek megtekintését is. További információ a [Microsoft Graph API-ról.](https://docs.microsoft.com/graph/overview)

**Használja újra az Active Directory-csoportokat és terjesztési listákat, amelyeket az ügyfél már kezel.**  Az Azure AD tartalmazza azokat a csoportokat, amelyeket az ügyfél már használ az e-mailek terjesztéséhez és a hozzáférés kezeléséhez. A Microsoft Graph API használatával használja újra ezeket a csoportokat ahelyett, hogy az ügyfélnek külön csoportokat kellene létrehoznia és kezelnie az alkalmazásban. A csoportadatok is elküldhetők az alkalmazás bejelentkezési jogkivonatai. További információ a [Microsoft Graph API-ról.](https://docs.microsoft.com/graph/overview)

**Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá az alkalmazáshoz.**  Az Azure AD rendszergazdái és alkalmazástulajdonosai hozzáférést rendelhetnek az alkalmazásokhoz adott felhasználókhoz és csoportokhoz. A Microsoft Graph API használatával elolvashatja ezt a listát, és használhatja az erőforrások kiépítésének és az alkalmazáson belüli hozzáférésnek a szabályozására.

**Használja az Azure AD szerepköralapú hozzáférés-vezérlés.**  A rendszergazdák és az alkalmazástulajdonosok felhasználókat és csoportokat rendelhetnek az Azure AD-ben az alkalmazás regisztrálásakor megadott szerepkörökhöz. A szerepköradatokat a rendszer elküldi az alkalmazás bejelentkezési jogkivonatainak, és a Microsoft Graph API-val is olvasható. További információ az [Azure AD engedélyezési használatáról.](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Hozzáférés a felhasználói profilhoz, a naptárhoz, az e-mailhez, a névjegyekhez, a fájlokhoz és egyebekhez

**Az Azure AD az Office 365 és más Microsoft-üzleti szolgáltatások engedélyezési kiszolgálója.**  Ha támogatja az Azure AD-t az alkalmazásba való bejelentkezéshez, vagy támogatja az aktuális felhasználói fiókok összekapcsolását az Azure AD felhasználói fiókjaival az OAuth 2.0 használatával, olvasási és írási hozzáférést kérhet egy felhasználó profiljához, naptárához, e-mailhez, névjegyekhez, fájlokhoz és egyéb információkhoz. Zökkenőmentesen írhat eseményeket a felhasználó naptárába, és fájlokat írhat vagy írhat a OneDrive-ra. További információ [az Office 365 API-k eléréséről.](https://msdn.microsoft.com/office/office365/howto/platform-development-overview)

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Az alkalmazás népszerűsítése az Azure-ban és az Office 365 piactereken

**Népszerűsítse alkalmazását az Azure AD-t már használó szervezetek milliói számára.**  Azok a felhasználók, akik ezekben a piactereken keresik és böngészik, már egy vagy több felhőszolgáltatást használnak, így minősített felhőszolgáltatási ügyfelek. További információ az alkalmazás [azure-piactéren](https://azure.microsoft.com/marketplace/partner-program/)való népszerűsítéséről.

**Amikor a felhasználók regisztrálnak az alkalmazásra, az megjelenik az Azure AD hozzáférési panelen és az Office 365 alkalmazásindítójában.**  A felhasználók később gyorsan és egyszerűen visszatérhetnek az alkalmazáshoz, javítva a felhasználói aktivitást. További információ az [Azure AD hozzáférési panelről.](../user-help/active-directory-saas-access-panel-introduction.md)

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Biztonságos eszköz-szolgáltatás és szolgáltatás-szolgáltatás kommunikáció

**Az Azure AD használatával a szolgáltatások és eszközök identitáskezelése csökkenti a kódot kell írni, és lehetővé teszi az informatikai hozzáférés kezeléséhez.**  A szolgáltatások és az eszközök az OAuth használatával kaphatnak jogkivonatokat az Azure AD-ből, és ezeket a jogkivonatokat használhatják a webes API-k eléréséhez. Az Azure AD használatával elkerülheti az összetett hitelesítési kód írását. Mivel a szolgáltatások és eszközök identitásai az Azure AD-ben tárolódnak, az informatikai szolgáltatások egy helyen kezelhetik a kulcsokat és a visszavonást ahelyett, hogy ezt külön kellene megtennie az alkalmazásban.

## <a name="benefits-of-integration"></a>Az integráció előnyei

Az Azure AD-vel való integráció olyan előnyökkel jár, amelyek nem igényelnek további kódot.

### <a name="integration-with-enterprise-identity-management"></a>Integráció a vállalati identitáskezeléssel

**Segítse az alkalmazást az informatikai irányelveknek való megfelelésben.**  A szervezetek integrálják a vállalati identitáskezelő rendszereket az Azure AD-vel, így amikor egy személy elhagyja a szervezetet, automatikusan elveszíti az alkalmazáshoz való hozzáférést anélkül, hogy az informatikai informatikai szükséges további lépéseket kellene tennie. Az informatikai rendszer kezelheti, hogy ki férhet hozzá az alkalmazáshoz, és meghatározhatja, hogy milyen hozzáférési szabályzatok szükségesek – például a többtényezős hitelesítés –, így csökkenteni kell a kód írásának szükségességét az összetett vállalati házirendeknek való megfelelés érdekében. Az Azure AD részletes naplót biztosít a rendszergazdák számára, hogy ki jelentkezett be az alkalmazásba, így az informatikai környezet nyomon követheti a használatot.

**Az Azure AD kiterjeszti az Active Directoryt a felhőre, hogy az alkalmazás integrálható az AD-vel.**  Világszerte számos szervezet használja az Active Directoryt fő bejelentkezési és identitáskezelő rendszerként, és megköveteli, hogy alkalmazásaik együttműködjenek az AD-vel. Az Azure AD-vel való integráció integrálja az alkalmazást az Active Directoryval.

### <a name="advanced-security-features"></a>Speciális biztonsági funkciók

**Többtényezős hitelesítés.**  Az Azure AD natív többtényezős hitelesítést biztosít. A rendszergazdák nak többtényezős hitelesítésre lehet szükségük az alkalmazás eléréséhez, így nem kell saját magának kódolnia ezt a támogatást. További információ a [többtényezős hitelesítésről.](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)

**Rendellenes bejelentkezés észlelés.**  Az Azure AD naponta több mint egymilliárd bejelentkezést dolgoz fel, miközben gépi tanulási algoritmusokat használ a gyanús tevékenységek észlelésére és a rendszergazdák értesítésére a lehetséges problémákról. Az Azure AD-bejelentkezés támogatásával az alkalmazás a védelem előnyeit élvezi. További információ az [Azure Active Directory hozzáférési jelentés megtekintéséről.](../active-directory-view-access-usage-reports.md)

**Feltételes hozzáférés.**  A többtényezős hitelesítés mellett a rendszergazdák megkövetelhetik, hogy bizonyos feltételek teljesüljenek, mielőtt a felhasználók bejelentkezhetnek az alkalmazásba. A beállítható feltételek közé tartozik az ügyféleszközök IP-címtartománya, a megadott csoportokban való tagság és a hozzáféréshez használt eszköz állapota. További információ az [Azure Active Directory feltételes hozzáféréséről.](../active-directory-conditional-access-azure-portal.md)

### <a name="easy-development"></a>Egyszerű fejlesztés

**Iparági szabványprotokollok.**  A Microsoft elkötelezett az iparági szabványok támogatása mellett. A Microsoft identity platform támogatja az iparági szabványnak megfelelő OAuth 2.0 és OpenID Connect 1.0 protokollokat. További információ a [Microsoft identity platform hitelesítési protokolljairól.](active-directory-v2-protocols.md)

**Nyílt forráskódú könyvtárak.**  A Microsoft teljes mértékben támogatott nyílt forráskódú könyvtárakat biztosít a népszerű nyelvekhez és platformokhoz a fejlesztés felgyorsítása érdekében. A forráskód az Apache 2.0 alatt van licencelve, és ön szabadon elágazhat, és hozzájárulhat a projektekhez. További információ a [Microsoft authentication library (MSAL)](reference-v2-libraries.md)könyvtárról.

### <a name="worldwide-presence-and-high-availability"></a>Világszerte jelenlét és magas rendelkezésre állás

**Az Azure AD világszerte adatközpontokban van telepítve, és éjjel-nappal felügyelet alatt áll.**  Az Azure AD a Microsoft Azure és az Office 365 identitáskezelő rendszere, amely világszerte 28 adatközpontban van telepítve. A címtáradatok replikálása garantáltan legalább három adatközpontba történik. A globális terheléselosztók biztosítják, hogy a felhasználók hozzáférjenek az Azure AD az adataikat tartalmazó legközelebbi példányához, és probléma észlelése esetén automatikusan átirányítsák a kérelmeket más adatközpontokba.

## <a name="next-steps"></a>További lépések

[Kezdje el írni a kódot](v2-overview.md#getting-started).

[Felhasználók bejelentkezése a Microsoft identity platform használatával](authentication-scenarios.md)

