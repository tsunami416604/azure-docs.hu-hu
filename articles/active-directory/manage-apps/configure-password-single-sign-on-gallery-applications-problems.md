---
title: Jelszó egyszeri bejelentkezés konfigurálása az Azure AD katalógusából származó alkalmazás probléma |} A Microsoft Docs
description: Megismerheti a gyakori problémák személyek face jelszó egyszeri bejelentkezéshez, amely már szerepel az Azure AD Alkalmazásgyűjteményben alkalmazások konfigurálása során
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: e0571a92f376e0be481a9016f186846fe29b0a37
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168277"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jelszó egyszeri bejelentkezés az Azure AD katalógusából származó alkalmazás konfigurálása során

Ez a cikk segít megérteni a gyakori problémák személyek face, konfigurálásakor **jelszó egyszeri bejelentkezés** együtt az Azure AD katalógusából származó alkalmazásba.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Hitelesítő adatok ki vannak töltve, de a bővítmény nem elküldeni őket

Ez a probléma általában akkor fordul elő, ha az alkalmazás gyártójától a bejelentkezési oldalon adjon hozzá egy mezőt a közelmúltban módosította, a felhasználónév és jelszó mezők észleléséhez használt azonosító megváltozott, vagy hogyan a bejelentkezés során a az alkalmazás működését módosító. Szerencsére a sok esetben a Microsoft együttműködhet tesztjéről alkalmazásszállítók problémák gyors megoldásához.

Míg a Microsoft technológiák automatikusan észlelni, ha Integrációk felosztása, nem feltétlenül azonnal találni a problémákat, vagy a problémák megoldásához hosszabb időt is igénybe. Abban az esetben egyet ezek Integrációk nem működik megfelelően, ha nyissa meg egy támogatási esetet, a lehető leggyorsabban lehet meghatározni.

**Ha Ön az alkalmazás gyártójától, szerepelnek** küldjön el nekünk, a Microsoft is dolgozhat velük, natív módon integrálhatja alkalmazását az Azure Active Directoryval. A gyártó, elküldheti a [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában listázása](../develop/howto-app-gallery-listing.md) , azok elindult.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Hitelesítő adatok kitölti a rendszer, és elküldve, de az oldal azt jelzi, hogy a hitelesítő adatok helytelenek

A probléma megoldásához először próbálkozzon a következőkkel:

-   A felhasználó először próbál **közvetlenül bejelentkezni az alkalmazás webhely** számukra tárolt hitelesítő adatokkal.

  * Bejelentkezési működik, majd van-e a felhasználót, kattintson a **hitelesítő adatainak frissítése** gombot a **alkalmazás csempe** a a **alkalmazások** szakaszában a [alkalmazás-hozzáférés Panel](https://myapps.microsoft.com/) frissíteni őket a legutóbbi ismert működő felhasználónevet és jelszót.

   * Ha Ön vagy egy másik rendszergazda rendelve Ez a felhasználó hitelesítő adatait, keresse meg a felhasználó vagy csoport az alkalmazás-hozzárendelés lépve a **felhasználók és csoportok** az alkalmazást, a hozzárendelés ki, és kattintson a lapján **Hitelesítő adatok frissítése** gombra.

-   Ha a felhasználó a saját hitelesítő adatait rendeli, a felhasználó rendelkezik **győződjön meg arról, hogy a jelszavát nem járt le, az alkalmazás ellenőrzési** , és ha igen, **lejárt jelszófrissítési** jelentkezzen be az alkalmazás közvetlenül.

   * A jelszó frissítése az alkalmazásban, után kérelem a felhasználó válassza az **hitelesítő adatainak frissítése** gombot a **alkalmazás csempe** a a **alkalmazások** szakaszában a [Alkalmazás-hozzáférési Panel](https://myapps.microsoft.com/) frissíteni őket a legutóbbi ismert működő felhasználónevet és jelszót.

   * Ha Ön vagy egy másik rendszergazda rendelve Ez a felhasználó hitelesítő adatait, keresse meg a felhasználó vagy csoport az alkalmazás-hozzárendelés lépve a **felhasználók és csoportok** az alkalmazást, a hozzárendelés ki, és kattintson a lapján **Hitelesítő adatok frissítése** gombra.

-   Frissítse a hozzáférési panel böngészőbővítményének használatánál az alábbi lépéseket követve a felhasználó rendelkezik a [a hozzáférési Panel webböngésző-bővítmény telepítése](#how-to-install-the-access-panel-browser-extension) szakaszban.

-   Győződjön meg arról, hogy a hozzáférési panel böngészőbővítményének használatánál fut, és engedélyezve van a felhasználó böngészőjében.

-   Győződjön meg arról, hogy a felhasználók nem próbál bejelentkezni az alkalmazásba a hozzáférési panelen, miközben a **inkognitó, inPrivate vagy privát módban**. A hozzáférési panel bővítmény ezek módban nem támogatott.

Abban az esetben, ha a fenti javaslatok nem működnek, annak oka az lehet az esetben, a módosítás történt, amely rendelkezik ideiglenesen működésképtelenné az alkalmazás-integráció az Azure AD-alkalmazás oldalán. Például ez akkor fordulhat elő, amikor az alkalmazás gyártójától vezet be egy parancsfájlt, amely manuális vs másképpen viselkedik oldalon automatikus bemeneti, milyen okok automatikus integráció, például a saját, kezdetét. Szerencsére a sok esetben a Microsoft együttműködhet tesztjéről alkalmazásszállítók problémák gyors megoldásához.

Míg a Microsoft technológiák automatikusan észlelni, ha az alkalmazás Integrációk felosztása, nem feltétlenül azonnal találni a problémákat, vagy a problémák elhárításához időbe telhet. -Integrációja nem működik megfelelően, ha megnyithat egy támogatási esetet, minél hamarabb hiba javításához. 

Emellett **szerepelnek a az alkalmazás gyártójával, ha** **küldjön el nekünk** így használhatnánk azokat a natív módon integrálja az alkalmazását az Azure Active Directoryval. A gyártó, elküldheti a [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában listázása](../develop/howto-app-gallery-listing.md) , azok elindult.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A bővítmény működik, a Chrome és a Firefox, de nem az Internet Explorer

Ez a probléma két fő oka is van:

-   Attól függően, a biztonsági beállítások engedélyezve van az Internet Explorerben, ha a webhely nem része egy **megbízható zóna**, egyes esetekben a parancsfájl tudják az alkalmazás végrehajtása.

  *  A probléma megoldásához kérje meg a felhasználót, hogy **adja hozzá az alkalmazás webhelyének** , a **megbízható helyek** belül listában azok **Internet Explorer biztonsági beállításai**. A felhasználók számára is elküldheti a [hely hozzáadása a megbízható helyek listájához](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) cikkben részletes útmutatást.

-   Ritka esetekben az Internet Explorer biztonsági ellenőrzés céljából néha okozhat a lap a parancsfájlja lassabban.

   * Sajnos ez a helyzet eltérőek lehetnek attól függően, a böngésző verziója, a számítógép sebessége vagy a meglátogatott webhely. Ebben az esetben javasoljuk, hogy kapcsolatba lép a támogatással, hogy kijavíthassuk az integrációt, az adott alkalmazás.

Emellett **szerepelnek a az alkalmazás gyártójával, ha** **küldjön el nekünk** így használhatnánk azokat a natív módon integrálja az alkalmazását az Azure Active Directoryval. A gyártó, elküldheti a [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában listázása](../develop/howto-app-gallery-listing.md) , azok elindult.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Ellenőrizze, hogy az alkalmazás bejelentkezési lapján nemrég megváltozott vagy egy további mező szükséges

Ha az alkalmazás bejelentkezési lapján a jelentősen megváltozott, néha ennek hatására az integrációkat érvényteleníteni. Példa erre van, ha az alkalmazás szállítójához hozzáadja a bejelentkezési mező, captcha vagy a multi-factor authentication személyre. Szerencsére a sok esetben a Microsoft együttműködhet tesztjéről alkalmazásszállítók problémák gyors megoldásához.

Míg a Microsoft technológiák automatikusan észlelni, ha az alkalmazás Integrációk felosztása, nem feltétlenül azonnal találni a problémákat, vagy a problémák elhárításához időbe telhet. -Integrációja nem működik megfelelően, ha megnyithat egy támogatási esetet, minél hamarabb hiba javításához. 

Emellett **szerepelnek a az alkalmazás gyártójával, ha** **küldjön el nekünk** így használhatnánk azokat a natív módon integrálja az alkalmazását az Azure Active Directoryval. A gyártó, elküldheti a [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában listázása](../develop/howto-app-gallery-listing.md) , azok elindult.

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel webböngésző-bővítmény telepítése

A hozzáférési Panel webböngésző-bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) az egyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a rendszer kéri a szoftver telepítéséhez, **telepítés most**.

4.  A böngészőtől függően a rendszer átirányítja a letöltési hivatkozás. **Adjon hozzá** a bővítményt a böngészőben.

5.  Ha a böngésző kéri, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Miután telepítette, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha a **indítsa el a** a jelszó-SSO-alkalmazások

Az alábbi közvetlen hivatkozások a Chrome és a Firefox is letöltheti a bővítményt:

-   [Chrome-hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)

