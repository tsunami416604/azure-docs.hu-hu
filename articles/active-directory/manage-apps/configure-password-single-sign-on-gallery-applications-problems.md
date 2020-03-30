---
title: Problémák a jelszó-sSO konfigurálása az Azure AD Gallery alkalmazáshoz
description: Ismerje meg, milyen gyakori problémákkal szembesülnek az emberek az Azure AD alkalmazásgalériában már felsorolt alkalmazások egyszeri bejelentkezésének konfigurálásakor
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159046"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Probléma az Azure AD Gallery-alkalmazások jelszavának egyszeri bejelentkezése során

Ez a cikk segít megérteni, hogy az emberek milyen gyakori problémákkal szembesülnek az Azure AD **Gallery-alkalmazással** történő egyszeri bejelentkezés konfigurálásakor.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>A hitelesítő adatok ki töltésre kerülnek, de a bővítmény nem küldi el őket

Ez a probléma általában akkor fordul elő, ha az alkalmazás szállítója a közelmúltban módosította a bejelentkezési lapját egy mező hozzáadásához, módosította a felhasználónév és jelszó mezők észlelésére használt azonosítót, vagy módosította a bejelentkezési élmény működését az alkalmazásukhoz. Szerencsére a Microsoft sok esetben együttműködik az alkalmazásgyártókkal a problémák gyors megoldásában.

Bár a Microsoft olyan technológiákkal rendelkezik, amelyek automatikusan észlelik az integrációk megszakítását, előfordulhat, hogy nem lehet azonnal megtalálni a problémákat, vagy a problémák megoldása némi időt vesz igénybe. Abban az esetben, ha az ilyen integrációk egyike nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban kijavítható legyen.

**Ha kapcsolatba lép az alkalmazás szállítójával,** küldje el őket a mi utunkba, hogy a Microsoft együtt dolgozhasson velük az alkalmazás natív módon integrálása az Azure Active Directoryval. A bevezetés elküldheti a szállítót az [alkalmazás listázása az Azure Active Directory alkalmazáskatalógusban](../azuread-dev/howto-app-gallery-listing.md) a kezdéshez.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>A hitelesítő adatok kitöltése és elküldése, de a lap azt jelzi, hogy a hitelesítő adatok helytelenek

A probléma megoldásához először próbálkozzon az alábbi megoldásokkal:

- Először próbálja meg a felhasználó **közvetlenül az alkalmazás webhelyére való bejelentkezést** a számukra tárolt hitelesítő adatokkal.

  * Ha a bejelentkezés működik, akkor a felhasználó kattintson a **Hitelesítő adatok frissítése** gombra az Alkalmazás-hozzáférési panel Alkalmazások szakaszában az **Application Tile** [Alkalmazás-hozzáférési panel](https://myapps.microsoft.com/) **Alkalmazások** szakaszában, hogy frissítse őket a legújabb ismert működő felhasználónévre és jelszóra.

  * Ha ön vagy egy másik rendszergazda rendelte hozzá a felhasználó hitelesítő adatait, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését úgy, hogy az alkalmazás **Felhasználók & csoportok** lapjára navigál, kiválasztja a hozzárendelést, és a Hitelesítő adatok **frissítése** gombra kattint.

- Ha a felhasználó saját hitelesítő adatokat rendelt hozzá, ellenőrizze a felhasználóval, hogy a **jelszó nem járt-e le az alkalmazásban,** és ha igen, **frissítse lejárt jelszavát** közvetlenül az alkalmazásba bejelentkezve.

  * Miután frissítette a jelszót az alkalmazásban, kérje meg a felhasználót, hogy kattintson a **Hitelesítő adatok frissítése** gombra az [Alkalmazás-hozzáférési panel](https://myapps.microsoft.com/) **Application Tile** **Alkalmazások** szakaszában, hogy frissítse azokat a legújabb ismert működő felhasználónévre és jelszóra.

  * Ha ön vagy egy másik rendszergazda rendelte hozzá a felhasználó hitelesítő adatait, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését úgy, hogy az alkalmazás **Felhasználók & csoportok** lapjára navigál, kiválasztja a hozzárendelést, és a Hitelesítő adatok **frissítése** gombra kattint.

- A felhasználó frissítse a hozzáférési panel böngészőbővítményét az [Access Panel böngészőbővítményének telepítése](#how-to-install-the-access-panel-browser-extension) című szakaszban található alábbi lépések végrehajtásával.

- Győződjön meg arról, hogy a hozzáférési panel böngészőbővítménye fut és engedélyezve van a felhasználó böngészőjében.

- Győződjön meg arról, hogy a felhasználók **inkognitóban, inPrivate vagy privát módban**nem próbálnak bejelentkezni az alkalmazásba a hozzáférési panelről. Ezekben a módokban a hozzáférési panel kiterjesztése nem támogatott.

Abban az esetben, ha a korábbi javaslatok nem működnek, lehet, hogy az alkalmazás oldalon történt egy változás, amely ideiglenesen megszakította az alkalmazás integrációját az Azure AD-vel. Ez például akkor fordulhat elő, ha az alkalmazás szállítója bevezet egy parancsfájlt az oldalon, amely eltérően viselkedik a manuális és az automatikus bemenet, ami miatt az automatikus integráció, mint a miénk, megtörni. Szerencsére a Microsoft sok esetben együttműködik az alkalmazásgyártókkal a problémák gyors megoldásában.

Bár a Microsoft olyan technológiákkal rendelkezik, amelyek automatikusan észlelik az alkalmazásintegrációk megszakítását, előfordulhat, hogy nem lehet azonnal megtalálni a problémákat, vagy a problémák megoldása eltarthat egy ideig. Ha az integráció nem működik megfelelően, megnyithat egy támogatási esetet, hogy a lehető leggyorsabban kijavíthassa. 

Ezen kívül, **ha a kapcsolatot az alkalmazás szállítójával,** **küldje el őket az utat,** így együtt tudunk velük, hogy natívmódon integrálja az alkalmazást az Azure Active Directoryval. A bevezetés elküldheti a szállítót az [alkalmazás listázása az Azure Active Directory alkalmazáskatalógusban](../azuread-dev/howto-app-gallery-listing.md) a kezdéshez.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A bővítmény működik a Chrome és a Firefox, de nem az Internet Explorer

Ennek a problémának két fő oka van:

- Az Internet Explorer ben engedélyezett biztonsági beállításoktól függően, ha a webhely nem része **a Megbízható zónának**, néha a parancsfájlunk nem hajtható végre az alkalmazásszámára.

  *  A probléma megoldásához utasítsa a felhasználót, hogy az Internet **Explorer biztonsági beállításai**között **adja hozzá az alkalmazás webhelyét** a **Megbízható helyek** listához. A felhasználókat elküldheti a Webhely hozzáadása a [megbízható helyek listájához](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) cikkhez részletes útmutatásért.

- Ritka esetekben az Internet Explorer biztonsági ellenőrzése néha a lap lassabb betöltését okozhatja, mint a parancsfájl végrehajtása.

  * Sajnos ez a helyzet a böngésző verziójától, a számítógép sebességétől vagy a meglátogatott webhelytől függően változhat. Ebben az esetben azt javasoljuk, hogy lépjen kapcsolatba az ügyfélszolgálattal, hogy kijavíthassuk az adott alkalmazás integrációját.

Ezen kívül, **ha a kapcsolatot az alkalmazás szállítójával,** **küldje el őket az utat,** így együtt tudunk velük, hogy natívmódon integrálja az alkalmazást az Azure Active Directoryval. A bevezetés elküldheti a szállítót az [alkalmazás listázása az Azure Active Directory alkalmazáskatalógusban](../azuread-dev/howto-app-gallery-listing.md) a kezdéshez.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Annak ellenőrzése, hogy az alkalmazás bejelentkezési oldala nemrég megváltozott-e, vagy további mezőre van szüksége

Ha az alkalmazás bejelentkezési oldala drasztikusan megváltozott, néha ez okozza az integrációk megszakadását. Erre példa, ha egy alkalmazás szállítója bejelentkezési mezőt, captcha-t vagy többtényezős hitelesítést ad hozzá a felhasználói élményéhez. Szerencsére a Microsoft sok esetben együttműködik az alkalmazásgyártókkal a problémák gyors megoldásában.

Bár a Microsoft olyan technológiákkal rendelkezik, amelyek automatikusan észlelik az alkalmazásintegrációk megszakítását, előfordulhat, hogy nem lehet azonnal megtalálni a problémákat, vagy a problémák megoldása eltarthat egy ideig. Ha az integráció nem működik megfelelően, megnyithat egy támogatási esetet, hogy a lehető leggyorsabban kijavíthassa. 

Ezen kívül, **ha a kapcsolatot az alkalmazás szállítójával,** **küldje el őket az utat,** így együtt tudunk velük, hogy natívmódon integrálja az alkalmazást az Azure Active Directoryval. A bevezetés elküldheti a szállítót az [alkalmazás listázása az Azure Active Directory alkalmazáskatalógusban](../azuread-dev/howto-app-gallery-listing.md) a kezdéshez.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Az Access Panel böngészőbővítményének telepítése

Az Access Panel browser bővítményének telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen be **felhasználóként** az Azure AD-ben.

2.  kattintson egy **jelszó-Egyszeri bejelentkezés alkalmazásra** a Hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngésző alapján a letöltési linkre irányítja a készüléket. **Adja hozzá** a bővítményt a böngészőhöz.

5.  Ha a böngésző kérdezi, válassza a **bővítmény engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngészőmunkamenetet.

7.  Jelentkezzen be a hozzáférési panelen, és nézze meg, hogy **el tudja-e indítani** a jelszó-Egyszeri bejelentkezés alkalmazásokat

Ön is letöltheti a kiterjesztést a Chrome és a Firefox a közvetlen alábbi linkekre:

-   [Chrome-hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox access panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)

