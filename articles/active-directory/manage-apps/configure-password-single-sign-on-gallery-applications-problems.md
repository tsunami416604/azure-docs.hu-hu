---
title: Problémák az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálásakor
description: Ismerje meg az Azure AD Application Galleryben már felsorolt alkalmazások jelszavas egyszeri bejelentkezésének konfigurálásakor felmerülő gyakori problémákat.
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
ms.openlocfilehash: 96e729bee0ac9beac447893ed8dbf1bba633e412
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274193"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Probléma az Azure AD Gallery-alkalmazás jelszavas egyszeri bejelentkezésének konfigurálásakor

Ebből a cikkből megtudhatja, hogyan tekintheti meg a gyakran előforduló problémákat a **jelszó egyszeri bejelentkezés** Azure ad Gallery-alkalmazással való konfigurálásakor.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>A hitelesítő adatok ki vannak töltve, de a bővítmény nem küldi el őket

Ez a probléma általában akkor fordul elő, ha az alkalmazás gyártója nemrég módosította a bejelentkezési lapját egy mező hozzáadásához, módosította a Felhasználónév és jelszó mezők észleléséhez használt azonosítót, vagy módosította, hogyan működik a bejelentkezési élmény az alkalmazáshoz. Szerencsére a Microsoft számos példányban képes együttműködni az alkalmazások forgalmazójával, hogy gyorsan megoldja ezeket a problémákat.

Habár a Microsoft olyan technológiákkal rendelkezik, amelyekkel automatikusan észlelhetők az integrációs folyamatok, előfordulhat, hogy a problémák azonnal nem találhatók meg, vagy ha a problémák kijavítása hosszabb időt vesz igénybe. Abban az esetben, ha az ilyen integrációk egyike nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban meg lehessen oldani.

**Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** küldje el őket, így a Microsoft dolgozhat velük, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>A hitelesítő adatokat a rendszer kitölti és elküldte, de az oldal jelzi, hogy a hitelesítő adatok helytelenek.

A probléma megoldásához először próbálja meg ezeket a dolgokat:

- Először próbáljon meg **közvetlenül bejelentkezni az alkalmazás webhelyére** a felhasználók által tárolt hitelesítő adatokkal.

  * Ha a bejelentkezés működik, akkor a felhasználó az [alkalmazás-hozzáférési panel](https://myapps.microsoft.com/) **alkalmazások** **területén a** **hitelesítő adatok frissítése** gombra kattintva frissítheti azokat a legutóbbi ismert munkafelhasználónévre és jelszóra.

  * Ha Ön, vagy egy másik rendszergazda hozzárendelte a hitelesítő adatokat a felhasználóhoz, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését az alkalmazás **felhasználók & csoportok** lapjára kattintva, válassza ki a hozzárendelést, majd kattintson a **hitelesítő adatok frissítése** gombra.

- Ha a felhasználó hozzárendelte a saját hitelesítő adatait, a felhasználónak ellenőriznie kell, hogy a **jelszavuk nem járt-e le az alkalmazásban** , és ha igen, **frissítse a lejárt jelszót** úgy, hogy közvetlenül az alkalmazásba jelentkezik be.

  * Miután a jelszó frissítve lett az alkalmazásban, kérje meg a felhasználót, hogy az [alkalmazás-hozzáférési panel](https://myapps.microsoft.com/) **alkalmazások** területén **a** **hitelesítő adatok frissítése** gombra kattintva frissítse azokat a legutóbbi ismert munkafelhasználónévre és jelszóra.

  * Ha Ön, vagy egy másik rendszergazda hozzárendelte a hitelesítő adatokat a felhasználóhoz, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését az alkalmazás **felhasználók & csoportok** lapjára kattintva, válassza ki a hozzárendelést, majd kattintson a **hitelesítő adatok frissítése** gombra.

- A felhasználó a hozzáférési panel [böngésző bővítményének telepítése](#how-to-install-the-access-panel-browser-extension) című szakaszban ismertetett lépéseket követve frissítse a hozzáférési panel böngésző bővítményét.

- Győződjön meg arról, hogy a hozzáférési panel böngésző-bővítménye fut, és engedélyezve van a felhasználó böngészőjében.

- Győződjön meg arról, hogy a felhasználók nem próbálnak bejelentkezni az alkalmazásba a hozzáférési panelen az **Incognito, InPrivate vagy Private módban**. A hozzáférési panel kiterjesztése ezekben a módokban nem támogatott.

Abban az esetben, ha az előző javaslatok nem működnek, lehet, hogy változás történt az alkalmazás oldalán, amely átmenetileg megszakította az alkalmazás Azure AD-integrációját. Ez például akkor fordulhat elő, ha az alkalmazás gyártója olyan parancsfájlt vezet be a lapon, amely másképp viselkedik a kézi és az automatikus bevitel esetében, ami az automatikus integrációt eredményezi, mint a saját, a törés. Szerencsére a Microsoft számos példányban képes együttműködni az alkalmazások forgalmazójával, hogy gyorsan megoldja ezeket a problémákat.

Habár a Microsoft olyan technológiákkal rendelkezik, amelyekkel automatikusan észlelhetők az alkalmazások integrációja, előfordulhat, hogy nem lehet azonnal megkeresni a problémákat, vagy ha a problémák egy ideig eltartanak. Ha egy integráció nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban rögzítse. 

Ezen kívül, **Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** **küldje** el a módját, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>A bővítmény a Chrome-ban és a Firefoxban is működik, az Internet Explorerben nem

Ennek a hibának két fő oka van:

- Az Internet Explorerben engedélyezett biztonsági beállításoktól függően, ha a webhely nem egy **megbízható zónába**tartozik, akkor a szkriptet nem lehet letiltani az alkalmazás végrehajtásához.

  *  A probléma megoldásához kérje meg a felhasználót, hogy az **Internet Explorer biztonsági beállításai**között **adja hozzá az alkalmazás webhelyét** a **megbízható helyek** listájához. Részletes utasításokért küldje el a felhasználókat a [hely hozzáadása a megbízható helyek listájához](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) című cikkben.

- Ritkán előfordul, hogy az Internet Explorer biztonsági érvényesítése időnként azt eredményezheti, hogy a lap lassabban töltődik be a parancsfájl végrehajtásakor.

  * Sajnos ez a helyzet a böngésző verziószáma, a számítógép sebessége vagy a meglátogatott hely függvényében változhat. Ebben az esetben javasoljuk, hogy vegye fel a kapcsolatot az ügyfélszolgálattal, hogy javítsa az adott alkalmazás integrálását.

Ezen kívül, **Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** **küldje** el a módját, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Ellenőrizze, hogy az alkalmazás bejelentkezési lapja nemrég módosult-e, vagy további mezőt igényel

Ha az alkalmazás bejelentkezési lapja drasztikusan módosult, időnként az integrációnk megszakad. Ilyen lehet például, ha egy alkalmazás gyártója felvesz egy bejelentkezési mezőt, egy CAPTCHA-t vagy egy többtényezős hitelesítést a saját tapasztalataiba. Szerencsére a Microsoft számos példányban képes együttműködni az alkalmazások forgalmazójával, hogy gyorsan megoldja ezeket a problémákat.

Habár a Microsoft olyan technológiákkal rendelkezik, amelyekkel automatikusan észlelhetők az alkalmazások integrációja, előfordulhat, hogy nem lehet azonnal megkeresni a problémákat, vagy ha a problémák egy ideig eltartanak. Ha egy integráció nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban rögzítse. 

Ezen kívül, **Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** **küldje** el a módját, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

A hozzáférési panel böngésző bővítményének telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) az egyik támogatott böngészőben, és jelentkezzen be **felhasználóként** az Azure ad-ben.

2.  Kattintson egy **jelszó-SSO-alkalmazásra** a hozzáférési panelen.

3.  A szoftver telepítését kérő üzenetben válassza a **Telepítés most**lehetőséget.

4.  A böngésző alapján a letöltési hivatkozásra kerül. **Adja hozzá** a bővítményt a böngészőjéhez.

5.  Ha a böngésző kéri, válassza ki a bővítmény **engedélyezése** vagy **engedélyezése** lehetőséget.

6.  A telepítés után **indítsa újra** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és ellenőrizze, hogy **elindíthatja** -e a jelszó-SSO-alkalmazásokat

A Chrome és a Firefox bővítményét az alábbi közvetlen hivatkozásokból is letöltheti:

-   [Chrome hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox hozzáférési panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés biztosítása az alkalmazásokba az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)

