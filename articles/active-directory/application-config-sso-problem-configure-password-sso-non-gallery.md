---
title: "A probléma jelszó egyszeri bejelentkezés nem galéria alkalmazások konfigurálása |} Microsoft Docs"
description: "A gyakori problémák személyek arcfelismerési áttekinteni jelszó egyszeri bejelentkezéshez, amelyek nem szerepelnek az Azure AD Application Gallery egyéni nem galéria-alkalmazások konfigurálása"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 265d58ce4098ea924318dfe2959397d60a0721d6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>A probléma jelszó egyszeri bejelentkezés nem galéria alkalmazások konfigurálása

Ez a cikk segítenek megérteni a gyakori problémák személyek arcfelismerési konfigurálásakor **jelszó egyszeri bejelentkezés** nem galéria alkalmazással.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Az alkalmazás bejelentkezési mezők rögzítése

Bejelentkezési mező rögzítési csak a támogatott bejelentkezési lap HTML-kompatibilis, és **nem támogatott a nem szabványos bejelentkezési lapok**, például a Flash, vagy egyéb nem HTML-kompatibilis technológiákat használó.

Az egyéni alkalmazások rögzítheti a bejelentkezési mező két módja van:

-   Automatikus bejelentkezés mező rögzítése

-   Manuális bejelentkezési mező rögzítése

**Automatikus bejelentkezés mező rögzítési** jól működik a legtöbb HTML-kompatibilis bejelentkezési lapok, ha azok **jól ismert DIV azonosítóit a felhasználónevet és jelszót adjon meg** mező. A működés módja a HTML-KÓDBAN a lapon található a feltételeknek megfelelő DIV azonosítók lekaparást és azt a jelszavakat, később is visszajátszásos, majd mentse a, hogy az alkalmazás metaadatai.

**Manuális bejelentkezési mező rögzítési** abban az esetben is használható, amely az alkalmazás **szállító nem címke** a bejelentkezéshez használt beviteli mezők. Manuális bejelentkezési mező rögzítési is használható abban az esetben, ha a **szállító Renderelés több mező** lehet, amely automatikusan észleli. Az Azure AD tárolhatja az adatokat, a bejelentkezési oldal, a lehető legtöbb mezők mindaddig, amíg Ön mondja el, ha ezen mezők szerepelnek a lap.

Általában **automatikus bejelentkezési mező rögzítése nem működik, ha mindig javasoljuk, hogy a manuális beállítás közben.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Az alkalmazás bejelentkezési mezők automatikusan rögzítése

Konfigurálása **jelszó-alapú egyszeri bejelentkezést** egy alkalmazást, amely a **automatikus bejelentkezési mező rögzítési**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-cím**. Ez az URL-CÍMÉT ahol adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. **Biztosítsa, hogy a bejelentkezési mező látható, akkor adja meg a megadott URL-címen**.

10. Kattintson a **Mentés** gombra.

11. Ha így tesz, azt fogja automatikusan scrape a felhasználónévhez URL-címet, és jelszó beviteli mező, és biztonságos átvitelére a jelszavakat, hogy a hozzáférési panel bővítmény használó alkalmazások az Azure AD használatára.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Manuálisan rögzítése az alkalmazás bejelentkezési mezők

Bejelentkezési mezőket manuálisan rögzítéséhez kell a hozzáférési Panel bővítmény telepítve és **nem fut az InPrivate-, incognito vagy privát módban.** A bővítmény telepítéséhez kövesse a [a hozzáférési Panel bővítmény telepítése](#i-cannot-manually-detect-sign-in-fields-for-my-application) szakasz.

Konfigurálása **jelszó-alapú egyszeri bejelentkezést** egy alkalmazást, amely a **manuális bejelentkezési mező rögzítési**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure Portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazda** vagy **Co-rendszergazda segítségét.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **további szolgáltatások** a fő bal oldali navigációs menü alján.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt megjelennek az alkalmazás nem látja, használja a **szűrő** vezérlő tetején a **összes alkalmazások listáját** és állítsa be a **megjelenítése** lehetőséggel **összes alkalmazást.**

6.  Válassza ki az egyszeri bejelentkezés konfigurálni kívánt alkalmazást.

7.  Ha az alkalmazás betölt, kattintson a **egyszeri bejelentkezés** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-cím**. Ez az URL-CÍMÉT ahol adja meg a felhasználók a felhasználónevével és jelszavával bejelentkezni. **Biztosítsa, hogy a bejelentkezési mező látható, akkor adja meg a megadott URL-címen**.

10. Kattintson a **Mentés** gombra.

11. Ha így tesz, azt fogja automatikusan scrape a felhasználónévhez URL-címet, és jelszó beviteli mező, és biztonságos átvitelére a jelszavakat, hogy a hozzáférési panel bővítmény használó alkalmazások az Azure AD használatára. Abban az esetben, ez nem sikerül, akkor **állítsa át a bejelentkezési módot manuális bejelentkezési mező rögzíti** által folytatni szeretné a 12. lépés.

12. Kattintson a **konfigurálása &lt;appname&gt; jelszó egyszeri bejelentkezési beállítások**.

13. Válassza ki a **manuálisan észleli a bejelentkezési mező** konfigurációs beállítást.

14. Kattintson az **OK** gombra.

15. Kattintson a **Save** (Mentés) gombra.

16. Kövesse a képernyőn a hozzáférési Panel használatára.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>A "Minden bejelentkezés mezőt, hogy az URL-címen nem található" hiba

Ezt a hibaüzenetet látja, ha a bejelentkezési mezők automatikus észlelése nem sikerült. A probléma megoldásához próbálja meg manuális bejelentkezési mező észlelési lépéseit követve a [manuálisan rögzítése az alkalmazás bejelentkezési mezők](#how-to-manually-capture-sign-in-fields-for-an-application) szakasz.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>"Egyszeri bejelentkezést a konfiguráció mentéséhez, nem" látható hiba

Ritka esetekben az egyszeri bejelentkezés beállításainak frissítése sikertelen lehet. Hárítsa el a próbálja az egyszeri bejelentkezés konfigurációjának mentése újra.

Ha ez továbbra is egységesen sikertelen, nyissa meg a támogatási esetet, és adja meg az összegyűjtött adatokat a [a portál értesítései részleteinek megtekintése](#i-cannot-manually-detect-sign-in-fields-for-my-application) és [segítség kérése értesítési részletek küld egy támogatási szakértőhöz](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) szakaszok.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>I nem manuálisan észlel bejelentkezési mezőket az alkalmazáshoz

Láthatja, ha manuális észlelése nem működik az viselkedésmódok közé:

-   A manuális rögzítési folyamat működéséhez jelent meg, de a rögzített mezők nem volt megfelelő

-   A jobb oldali mezők nem beolvasni a kijelölt a rögzítési folyamat végrehajtása során

-   A rögzítési folyamat veszi át me az alkalmazás bejelentkezési oldalt, a várt, de semmi nem történik

-   Kézi rögzítési úgy tűnik, hogy működik, de az SSO nem fordulhat elő, ha a hozzáférési panelen igényelheti az alkalmazás a felhasználók keresse meg.

Ellenőrizze az alábbiakat, ha ezeket a problémákat tapasztal:

-   Győződjön meg arról, hogy rendelkezik-e a hozzáférési panel bővítmény legújabb verziója **telepítve** és **engedélyezett** a lépések a [a hozzáférési Panel bővítmény telepítése](#how-to-install-the-access-panel-browser-extension) szakasz.

-   Győződjön meg arról, hogy nem kívánt a rögzítési folyamat közben a böngésző a **incognito, inPrivate vagy privát mód**. A hozzáférési panel bővítmény e mód nem támogatott.

-   Győződjön meg arról, hogy a felhasználók nem próbál bejelentkezni az alkalmazás a hozzáférési panelen igényelheti közben a **incognito, inPrivate vagy privát mód**. A hozzáférési panel bővítmény e mód nem támogatott.

-   Próbálja meg a manuális rögzítési folyamat ismét, annak biztosítása, a vörös jelölőket keresztül a megfelelő mezőket.

-   Ha a kézi rögzítési folyamat úgy tűnik, hogy leállását, vagy a bejelentkezési oldal nem semmit (a fenti 3. eset), ismételje meg a manuális rögzítési folyamat. De ezúttal a folyamat befejezése után nyomja le az **F12** gombra kattintva nyissa meg a böngésző fejlesztői konzolján. Egyszer, nyissa meg a **konzol** és írja be **window.location= "&lt;a az alkalmazás konfigurálásakor megadott URL-címet adja meg a bejelentkezési&gt;"** , és nyomja le az **Enter**. A kényszerített egy lap átirányítási, amely a rögzítési folyamat befejezéséhez, és tárolja a mezőket, amelyeknek már rögzített.

Ha ezek egyike sem működik, meg, segítünk. Nyisson meg egy támogatási esetet mi próbált, valamint begyűjtött információ részleteit a [a portál értesítései részleteinek megtekintése](#i-cannot-manually-detect-sign-in-fields-for-my-application) és [segítség kérése értesítési részletek küld egy támogatási szakértőhöz](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) részek (ha van ilyen).

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel bővítmény telepítése

A hozzáférési Panel bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) valamelyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a szoftver telepítéséhez megkérdezi **telepítés**.

4.  A böngésző alapján kell irányítani a letöltési hivatkozás. **Adja hozzá** az bővítményt, hogy a böngészőben.

5.  Ha a böngésző, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Korábban telepítve, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha **indítása** a jelszó-egyszeri bejelentkezés alkalmazásokhoz.

Az alábbi közvetlen hivatkozások közül a Chrome és Firefox is letöltheti a bővítmény:

-   [Chrome hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>A portál értesítései részleteinek megtekintése

A portál értesítései részleteit láthatja az alábbi lépéseket követve:

1.  Kattintson a **értesítések** (a harang) ikonra a képernyő jobb felső sarkában az Azure portálon

2.  Válassza ki az értesítésekhez egy **hiba** állapota (amelyek a piros (!) mellett).

  >! Vegye figyelembe], nem kattintson az értesítések egy **sikeres** vagy **folyamatban lévő** állapotát.
  >
  >

3.  A Megnyitás a **értesítési részletek** panelen.

4.  Ez a témakör a problémával kapcsolatos további részletekért megértéséhez.

5.  Ha további segítségre van, a ezek az információk megosztása a támogatási szakember vagy a csoport segítség a probléma megoldásában.

6.  Kattintson a **másolási** **ikon** jobb oldalán a **hiba másolása** szövegmező megosztani a támogatási szolgálathoz vagy a termék csoport mérnöke értesítés részleteinek másolása.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Segítség kérése értesítési részletek küld egy támogatási szakértőhöz

Nagyon fontos, hogy megosztott **alább felsorolt összes részletes** , ha segítségre van szüksége, így gyorsan segít a támogatási szakértőhöz. Ehhez az egyszerű **elkészíti a képernyőfelvételt** vagy kattintson a **másolási hibaikon**, míg a talált jobb oldalán a **hiba másolása** szövegmező.

## <a name="notification-details-explained"></a>Értesítési részletek alapján

Az alábbiakban azt ismerteti, több milyen az értesítés azt jelenti, hogy elemeket, és azok példákat.

### <a name="essential-notification-items"></a>Alapvető értesítési elemek

-   **Cím** – az értesítés informatív címet

    -   Példa – **Application proxy beállításai**

-   **Leírás** – Mi történt a művelet leírása

    -   Példa – **megadott belső URL-cím már használatban van egy másik alkalmazás.**

-   **Értesítés azonosítója** – az értesítés egyedi azonosítója

    -   Példa – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Ügyfélkérelem-azonosító** – a böngésző által végrehajtott egyedi azonosítója

    -   Példa – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Stamp UTC-idő** – a timestamp, amely során az értesítés történt UTC szerint

    -   Példa – **2017-03-23T19:50:43.7583681Z**

-   **Belső tranzakció azonosítója** – belső azonosítója a Microsoft segítségével, a rendszer keresse meg a hiba

    -   Példa – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Egyszerű felhasználónév** – a műveletet végző felhasználó

    -   – Példa**tperkins@f128.info**

-   **A bérlői azonosító** – az egyedi azonosító a bérlő által, hogy a művelet a felhasználó tagja volt.

    -   Példa – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Felhasználói objektum azonosítója** – a művelet a felhasználó egyedi azonosítója

    -   Példa – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

-   **Megjelenítendő név** – **(üres is lehet)** a hiba részletes megjelenítendő neve

    -   Példa * – **Application proxy beállításai**

-   **Állapot** – az értesítésre adott állapota

    -   Példa * – **nem sikerült**

-   **Objektumazonosító:** – **(üres is lehet)** az objektum azonosítója, amely alapján a művelet végrehajtása

    -   Példa – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Részletek** – a részletes Mi történt a művelet leírása

    -   Példa – **belső URL-cím "http://bing.com/" érvénytelen, mert már használatban van**

-   **Másolja át a hiba** – kattintson a **másolás ikon** jobb oldalán a **hiba másolása** szövegmező megosztani a támogatási szolgálathoz vagy a termék csoport mérnöke értesítés részleteinek másolása

    -   – Példa```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Következő lépések
[Adja meg az egyszeri bejelentkezés az alkalmazásokba a Proxy](active-directory-application-proxy-sso-using-kcd.md)

