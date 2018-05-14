---
title: A probléma jelszó egyszeri bejelentkezés az Azure AD-katalógusában alkalmazás konfigurálása |} Microsoft Docs
description: A gyakori problémák személyek arcfelismerési áttekinteni jelszó egyszeri bejelentkezés az Azure AD Application Gallery már szereplő alkalmazások konfigurálása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: f19b684a6c7426134844a2657b886280af2f061c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>A probléma jelszó egyszeri bejelentkezés az Azure AD-katalógusában alkalmazás konfigurálása

Ez a cikk segít megérteni a gyakori problémák személyek oldallal, konfigurálásakor **jelszó egyszeri bejelentkezés** Azure AD-katalógusában alkalmazással.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Kitölti a hitelesítő adatokat, de a bővítmény elküldeni őket

Ez a probléma általában akkor fordul elő, ha az alkalmazás gyártójának a bejelentkezési oldal nemrég mező felvétele a megváltozott, az észlelése a felhasználónév és jelszó megadására használt azonosító módosította, vagy módosítani, hogyan a bejelentkezés tapasztalhat az alkalmazásokban működik. Szerencsére sok esetben Microsoft együttműködhet alkalmazásszállítók gyorsan a problémák megoldásához.

Míg Microsoft technológiák Integrációk törés automatikus észleléséhez, hogy előfordulhat, hogy nem lehet a problémák azonnal található, vagy a problémák megoldásához egy kis idő múlva. Abban az esetben egy ezek integrációja nem működik megfelelően, ha nyissa meg a támogatási esetet, a lehető leggyorsabban lehet meghatározni.

**Ha az alkalmazás gyártójával, szerepelnek** küldje el a módon, a Microsoft is dolgozhat velük a natív módon integrálja az alkalmazásokban az Azure Active Directoryban. A forgalmazójával küldhet a [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](./develop/active-directory-app-gallery-listing.md) azokat elindult.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Hitelesítő adatok kitöltötte és elküldve, de az oldal azt jelzi, hogy a hitelesítő adatok helytelenek

A probléma megoldásához először próbálkozzon a következőkkel:

-   A felhasználó először próbál **jelentkezzen be az alkalmazás webhelyre közvetlenül** számukra tárolt hitelesítő adatokkal.

  * Ha bejelentkezési működik, akkor meg kell a felhasználót, kattintson a **hitelesítő adatainak frissítése** gombra a **alkalmazás csempe** a a **alkalmazások** szakasza a [alkalmazás-hozzáférés Panel](https://myapps.microsoft.com/) frissíteni a legutóbbi ismert működő felhasználónevet és jelszót.

   * Ha Ön vagy egy másik rendszergazda hozzárendelve a felhasználó hitelesítő adatainak, található a felhasználó vagy csoport alkalmazás hozzárendelése navigáljon a **felhasználók és csoportok** lapon jelölje ki a hozzárendelés, kattintson az alkalmazás a **frissítéséhez szükséges hitelesítő adatokat** gombra.

-   Ha a felhasználó a saját hitelesítő adataikat, kérheti a felhasználótól **ellenőrizze, hogy ne feledje, hogy a jelszavát nem járt le az alkalmazás** , és ha igen, **lejárt jelszófrissítési** történő bejelentkezéssel az alkalmazás közvetlenül.

   * A jelszó frissítése az alkalmazásban, után kérjen a felhasználótól az a **hitelesítő adatainak frissítése** gombra a **alkalmazás csempe** a a **alkalmazások** szakasza a [alkalmazás hozzáférési Panel](https://myapps.microsoft.com/) frissíteni a legutóbbi ismert működő felhasználónevet és jelszót.

   * Ha Ön vagy egy másik rendszergazda hozzárendelve a felhasználó hitelesítő adatainak, található a felhasználó vagy csoport alkalmazás hozzárendelése navigáljon a **felhasználók és csoportok** lapon jelölje ki a hozzárendelés, kattintson az alkalmazás a **frissítéséhez szükséges hitelesítő adatokat** gombra.

-   A felhasználó a hozzáférési panel bővítmény frissítése az alábbi lépéseket követve a [a hozzáférési Panel bővítmény telepítése](#how-to-install-the-access-panel-browser-extension) szakasz.

-   Győződjön meg arról, hogy a hozzáférési panel bővítmény fut, és a felhasználó böngészőben engedélyezve-e.

-   Győződjön meg arról, hogy a felhasználók nem próbál bejelentkezni az alkalmazás a hozzáférési panelen igényelheti közben a **incognito, inPrivate vagy privát mód**. A hozzáférési panel bővítmény e mód nem támogatott.

Abban az esetben, ha az előző javaslatok nem működnek, annak oka lehet az eset, hogy olyan módosítás történt tartozó ideiglenesen az alkalmazások integrálása az Azure AD alkalmazás oldalán. Például ez akkor fordulhat elő, amikor az alkalmazás gyártójának vezet be a lapon, amely manuális vs másképpen viselkedik parancsfájl automatikus bemeneti, aminek következtében automatikus integrációs, például a saját, hibájához. Szerencsére sok esetben Microsoft együttműködhet alkalmazásszállítók gyorsan a problémák megoldásához.

Míg a Microsoft automatikus észlelés, ha alkalmazás Integrációk törés technológiákat, nem feltétlenül azonnal találni a problémákat, vagy a problémák megoldásához időbe telhet. Integrációs nem működik megfelelően, ha nyithatja meg a legyen a lehető leggyorsabban rögzített támogatási esetet. 

Továbbá a **szerepelnek a az alkalmazás gyártójával, ha** **küldje el a módon** , azt is elérheti őket a natív módon integrálja az alkalmazásokban az Azure Active Directoryban. A forgalmazójával küldhet a [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](./develop/active-directory-app-gallery-listing.md) azokat elindult.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A bővítmény a Chrome és a Firefox, de az Internet Explorer nem működik

A probléma két fő oka is van:

-   Attól függően, hogy a biztonsági beállítások engedélyezve van az Internet Explorerben, ha a webhely nem része egy **megbízható zóna**, egyes esetekben a parancsfájl letiltja a végrehajtása az alkalmazáshoz.

  *  A probléma megoldásához, kérje meg a felhasználót, hogy **az alkalmazás-webhely hozzáadása** való a **megbízható helyek** listán belül a **Internet Explorer biztonsági beállításai**. A felhasználók számára is elküldheti a [a webhely felvétele a megbízható helyek listájához](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) cikk részletes utasításokat.

-   Ritka esetekben előfordulhat, hogy az Internet Explorer biztonsági ellenőrzés néha okozhat a lapon, mint a parancsfájl végrehajtása lassan töltődik be.

   * Sajnos ez a helyzet eltérőek lehetnek attól függően, hogy a böngésző verziója, a számítógép sebességétől, vagy a meglátogatott webhely. Ebben az esetben javasoljuk, hogy kijavíthassuk a integráció az adott alkalmazás támogatási szolgálatához.

Továbbá a **szerepelnek a az alkalmazás gyártójával, ha** **küldje el a módon** , azt is elérheti őket a natív módon integrálja az alkalmazásokban az Azure Active Directoryban. A forgalmazójával küldhet a [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](./develop/active-directory-app-gallery-listing.md) azokat elindult.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Ha az alkalmazás bejelentkezési lapján mostanában megváltozott, vagy egy további mező szükséges ellenőrzése

Ha az alkalmazás bejelentkezési oldalt a jelentősen megváltozott, néha ennek hatására a integrációja megszüntetése. Példa erre akkor, ha az alkalmazás gyártójának ad hozzá egy bejelentkezési mező, captcha vagy a multi-factor authentication tapasztalataikat. Szerencsére sok esetben Microsoft együttműködhet alkalmazásszállítók gyorsan a problémák megoldásához.

Míg a Microsoft automatikus észlelés, ha alkalmazás Integrációk törés technológiákat, nem feltétlenül azonnal találni a problémákat, vagy a problémák megoldásához időbe telhet. Integrációs nem működik megfelelően, ha nyithatja meg a legyen a lehető leggyorsabban rögzített támogatási esetet. 

Továbbá a **szerepelnek a az alkalmazás gyártójával, ha** **küldje el a módon** , azt is elérheti őket a natív módon integrálja az alkalmazásokban az Azure Active Directoryban. A forgalmazójával küldhet a [listázása az alkalmazást az Azure Active Directory alkalmazáskatalógusában](./develop/active-directory-app-gallery-listing.md) azokat elindult.

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel bővítmény telepítése

A hozzáférési Panel bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) valamelyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a szoftver telepítéséhez megkérdezi **telepítés**.

4.  A böngésző alapján, hogy van irányítva a letöltési hivatkozás. **Adja hozzá** az bővítményt, hogy a böngészőben.

5.  Ha a böngésző, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Korábban telepítve, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha **indítása** a jelszó-egyszeri bejelentkezés alkalmazásokhoz

Az alábbi közvetlen hivatkozások közül a Chrome és Firefox is letöltheti a bővítmény:

-   [Chrome hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>További lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

