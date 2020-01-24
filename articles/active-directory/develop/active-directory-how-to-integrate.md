---
title: Integráció a Azure Active Directory használatával | Microsoft Docs
description: Ismerje meg az alkalmazások Azure Active Directoryokkal való integrálásának előnyeit, és szerezzen be erőforrásokat olyan funkciókhoz, mint például az egyszerűsített bejelentkezés, az Identitáskezelés, a többtényezős hitelesítés és a hozzáférés-vezérlés.
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
ms.openlocfilehash: c5b8c506398af27ced284405dd75e48fb19a17af
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698849"
---
# <a name="integrating-with-azure-active-directory"></a>Integráció a Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ebből a cikkből megtudhatja, milyen előnyökkel jár az alkalmazás integrálása Azure Active Directory (Azure AD) és erőforrások beszerzése az integrációhoz. Az Azure AD nagyvállalati szintű Identitáskezelés-kezelést kínál a felhőalapú alkalmazásokhoz. Az Azure AD-integráció lehetővé teszi a felhasználók számára, hogy zökkenőmentes bejelentkezési élményt biztosítson, és segít az alkalmazásnak megfelelni az informatikai szabályzatoknak.

## <a name="how-to-integrate"></a>Az integrálás menete

Az alkalmazás az Azure AD-vel való integrálásának számos módja van. Használja ki az alkalmazásának megfelelő számos vagy több ilyen forgatókönyvet.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Az Azure AD támogatása az alkalmazásba való bejelentkezéshez

**Csökkentse a bejelentkezés súrlódását, és csökkentse a támogatási költségeket.** Ha az Azure AD-vel bejelentkezik az alkalmazásba, a felhasználók nem fognak tudni még egy nevet és jelszót használni. Fejlesztőként egy kevesebb jelszót fog tárolni és védelemmel ellátni. Az elfelejtett jelszó-visszaállítások kezelésének mellőzése önmagában jelentős megtakarítás lehet. Az Azure AD-ben a világ legnépszerűbb felhőalapú alkalmazásai, például az Office 365 és a Microsoft Azure is bejelentkezhetnek. A felhasználók milliói több száz millió felhasználót érintenek, így a felhasználó már be van jelentkezve az Azure AD-be. További információ [Az Azure ad-bejelentkezés támogatásának hozzáadásáról](v1-authentication-scenarios.md).

**Egyszerűsítse az alkalmazás regisztrálását.**  Az alkalmazásra való regisztráció során az Azure AD alapvető információkat küldhet a felhasználóról, így előre kitöltheti a regisztrációs űrlapot, vagy teljesen megszüntetheti a regisztrációt. A felhasználók az Azure AD-fiókjával regisztrálhatnak az alkalmazásra, mint a közösségi médiában és a mobil alkalmazásokban található, ismerős hozzájárulással. Bármely felhasználó regisztrálhat, és bejelentkezhet az Azure AD-be integrált alkalmazásba anélkül, hogy ez bevonást igényel. További információ az [alkalmazás Azure ad-fiókba való bejelentkezésének regisztrálásáról](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Felhasználók tallózása, felhasználók üzembe helyezésének kezelése és az alkalmazáshoz való hozzáférés szabályozása

**Tallózással keresse meg a címtárban lévő felhasználókat.**  A Graph API segítségével a felhasználók megkereshetik és böngészhetik a szervezet más személyeit, amikor mások meghívását vagy hozzáférést biztosítanak, és nem igénylik e-mail-címek beírását. A felhasználók megkereshetik az ismerős címjegyzék stílusának felületét, beleértve a szervezeti hierarchia részletes adatainak megtekintését is. További információ a [Graph APIról](active-directory-graph-api.md).

**Active Directory csoportok és terjesztési listák újbóli használata az ügyfél már felügyelet alatt áll.**  Az Azure AD azokat a csoportokat tartalmazza, amelyeket az ügyfél már használ az e-mailek terjesztéséhez és a hozzáférések kezeléséhez. A Graph API használatával újra felhasználhatja ezeket a csoportokat ahelyett, hogy az ügyfélnek külön csoportokat kellene létrehoznia és kezelnie az alkalmazásban. A csoport adatai a bejelentkezési jogkivonatokban is elküldhetők az alkalmazásnak. További információ a [Graph APIról](active-directory-graph-api.md).

**Az Azure AD használatával szabályozhatja, hogy ki férhet hozzá az alkalmazáshoz.**  Az Azure AD-rendszergazdák és az alkalmazások tulajdonosai az adott felhasználókhoz és csoportokhoz is hozzárendelhet hozzáférést az alkalmazásokhoz. A Graph API segítségével elolvashatja ezt a listát, és felhasználhatja az erőforrások kiépítés, valamint az alkalmazáson belüli hozzáférés szabályozására.

**Az Azure AD használata szerepkörökön alapuló Access Control.**  A rendszergazdák és az alkalmazások tulajdonosai felhasználókat és csoportokat rendelhetnek hozzá az alkalmazás Azure AD-ben való regisztrálásakor meghatározott szerepkörökhöz. A szerepkör-információkat a rendszer a bejelentkezési jogkivonatokban elküldi az alkalmazásnak, és a Graph API használatával is olvasható. További információ az [Azure ad engedélyezésének használatáról](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Hozzáférés a felhasználói profilhoz, a naptárhoz, az e-mailekhez, a névjegyekhez és a fájlokhoz

**Az Azure AD az Office 365 és más Microsoft üzleti szolgáltatások engedélyezési kiszolgálója.**  Ha támogatja az Azure AD-t, hogy bejelentkezzen az alkalmazásba, vagy támogassa a jelenlegi felhasználói fiókjainak az Azure AD felhasználói fiókjaihoz való összekapcsolását a OAuth 2,0 használatával, olvasási és írási hozzáférést kérhet a felhasználó profiljához, naptárához, e-mailekhez, névjegyekhez, fájlokhoz és egyéb információkhoz. Az eseményeket zökkenőmentesen is megírhatja a felhasználó naptárába, és fájlokat olvashat vagy írhat a OneDrive. További információ [az Office 365 API-k eléréséről](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Népszerűsítse alkalmazását az Azure-ban és az Office 365-piactéren

**Népszerűsítse alkalmazását az Azure AD-t már használó több millió szervezet számára.**  Azok a felhasználók, akik ezeket a piactéreket keresik és böngészhetik, már egy vagy több felhőalapú szolgáltatást használnak, így azok a felhőalapú szolgáltatás ügyfelei számára is elérhetővé válnak. További információ az alkalmazás előléptetéséről [Az Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)-en.

**Amikor a felhasználók regisztrálnak az alkalmazásra, az az Azure AD hozzáférési paneljén és az Office 365 app launcherben fog megjelenni.**  A felhasználók a későbbiekben gyorsan és egyszerűen visszatérhetnek az alkalmazáshoz, ami javítja a felhasználói szerepvállalást. További információ az [Azure ad hozzáférési paneljéről](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Biztonságos eszköz-szolgáltatás és szolgáltatások közötti kommunikáció

**Az Azure AD használata a szolgáltatások és eszközök személyazonosságának kezeléséhez csökkenti az íráshoz szükséges kódot, és lehetővé teszi a hozzáférés kezelését.**  A szolgáltatások és eszközök megkapják az Azure AD-jogkivonatokat az OAuth használatával, és ezeket a jogkivonatokat használhatják a webes API-k eléréséhez. Az Azure AD használatával elkerülhető az összetett hitelesítési kód írása. Mivel a szolgáltatások és az eszközök identitása az Azure AD-ben van tárolva, a kulcsokat és a visszavonást egy helyen kezelheti ahelyett, hogy ezt külön kellene elvégeznie az alkalmazásban.

## <a name="benefits-of-integration"></a>Az integráció előnyei

Az Azure AD-val való integráció olyan előnyökkel jár, amelyek nem igénylik további kódok megírását.

### <a name="integration-with-enterprise-identity-management"></a>Integráció a vállalati identitások kezelésével

**Segítsen az alkalmazásnak az informatikai szabályzatoknak való megfelelésben.**  A szervezetek az Azure AD-vel integrálják vállalati Identitáskezelés-felügyeleti rendszerüket, így amikor egy személy elhagyja a szervezetet, automatikusan elveszíti az alkalmazáshoz való hozzáférést anélkül, hogy további lépéseket kellene elvégeznie. Felügyelheti, hogy ki férhet hozzá az alkalmazáshoz, és meghatározhatja, hogy milyen hozzáférési házirendekre van szükség – például a többtényezős hitelesítésnél –, amely csökkenti az összetett vállalati házirendeknek való megfeleléshez szükséges kód írását. Az Azure AD részletes naplót biztosít a rendszergazdáknak, hogy kik bejelentkezett az alkalmazásba, hogy nyomon kövessék a használatot.

**Az Azure AD kiterjeszti Active Directory a felhőbe, hogy az alkalmazás integrálható legyen az AD-vel.**  A világ számos szervezete a Active Directory elsődleges bejelentkezési és Identitáskezelés-felügyeleti rendszereként használja, és megköveteli, hogy alkalmazásaikat az AD-vel működjenek. Az Azure AD-val való integráció az alkalmazást Active Directorytel integrálja.

### <a name="advanced-security-features"></a>Speciális biztonsági funkciók

**Multi-Factor Authentication.**  Az Azure AD natív multi-Factor Authentication szolgáltatást nyújt. A rendszergazdák megkövetelhetik a többtényezős hitelesítés használatát az alkalmazáshoz való hozzáféréshez, így Önnek nem kell ezt a támogatást leírnia. További információ a [multi-Factor Authenticationról](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Rendellenes bejelentkezési észlelés.**  Az Azure AD naponta több mint egy milliárd bejelentkezést dolgoz fel, a gépi tanulási algoritmusok használatával pedig észlelheti a gyanús tevékenységeket, és értesíti a rendszergazdákat a lehetséges problémákról. Az Azure AD-bejelentkezés támogatásával az alkalmazás megkapja a védelem előnyeit. További információ a [Azure Active Directory Access-jelentés megtekintéséről](../active-directory-view-access-usage-reports.md).

**Feltételes hozzáférés.**  A többtényezős hitelesítésen kívül a rendszergazdáknak bizonyos feltételek teljesülése szükséges ahhoz, hogy a felhasználók bejelentkezhetnek az alkalmazásba. A beállítható feltételek közé tartozik az ügyféleszközök IP-címtartomány, a megadott csoportok tagsága és a hozzáféréshez használt eszköz állapota. További információ a [Azure Active Directory feltételes hozzáférésről](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Könnyű fejlesztés

**Iparági szabványnak megfelelő protokollok.**  A Microsoft elkötelezett az iparági szabványok támogatásában. A Microsoft Identity platform támogatja az iparági szabványnak megfelelő OAuth 2,0 és az OpenID Connect 1,0 protokollokat. További információ a [Microsoft Identity platform hitelesítési protokolljairól](active-directory-v2-protocols.md).

**Nyílt forráskódú kódtárak.**  A Microsoft teljes körűen támogatott nyílt forráskódú kódtárakat biztosít népszerű nyelvekhez és platformokhoz a gyorsabb fejlesztés érdekében. A forráskód az Apache 2,0 alatt van, és Ön szabadon elvégezheti az elágazást, és hozzájárulhat a projektekhez. További információ a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md)szolgáltatásról.

### <a name="worldwide-presence-and-high-availability"></a>Globális jelenlét és magas rendelkezésre állás

**Az Azure AD a világ különböző pontjain üzemelő adatközpontokban van üzembe helyezve, és az óra körül felügyelhető és figyelhető.**  Az Azure AD a Microsoft Azure és az Office 365 Identity Management rendszer, amely a világ 28 adatközpontjában van üzembe helyezve. A címtáradatok legalább három adatközpontba való replikálásra vannak garantálva. A globális Load Balancer biztosítja, hogy a felhasználók hozzáférjenek az Azure AD legközelebbi példányához, amely tartalmazza az adatmennyiséget, és ha problémát észlel, automatikusan átirányítja a kéréseket más adatközpontokhoz.

## <a name="next-steps"></a>Következő lépések

A [kód írásának megkezdése](v2-overview.md#getting-started).

[Felhasználók aláírása a Microsoft Identity platform használatával](v1-authentication-scenarios.md)

