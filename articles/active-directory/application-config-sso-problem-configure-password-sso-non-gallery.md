---
title: Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása során |} A Microsoft Docs
description: A gyakori problémák személyek face értenie, amikor jelszó egyszeri bejelentkezés konfigurálása egyéni katalógusban nem szereplő alkalmazásokhoz, amelyek nem szerepelnek az Azure AD Alkalmazáskatalógusában
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 03cb425649326ca5313e177df29bfb18e55c14c1
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364871"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása során

Ez a cikk segít megérteni a gyakori problémák személyek face konfigurálásakor **jelszavas egyszeri bejelentkezés** az katalógusban nem szereplő alkalmazást.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Az alkalmazás bejelentkezési mezők rögzítése

Bejelentkezési mezők rögzítése csak a HTML-kompatibilis bejelentkezési lapok esetében támogatott, és **nem támogatja a nem szabványos bejelentkezési lapok**, Flash vagy egyéb nem HTML-kompatibilis technológiákat használó hurokutasítások, például.

Az egyéni alkalmazások bejelentkezési mezők rögzítése két módja van:

-   Az automatikus bejelentkezési mezők rögzítése

-   Manuális bejelentkezési mezők rögzítése

**Az automatikus bejelentkezési mezők rögzítése** nagyszerűen működik a legtöbb HTML-kompatibilis bejelentkezési oldalt, ha azok **felhasználónevet és jelszót adjon meg jól ismert DIV azonosítóinak** mező. Ez a módszer módja automatizované získávání dat a HTML-kódot az oldalon található DIV azonosítók, amelyek megfelelnek bizonyos feltételeknek és majd a jelszavak, azt később visszajátszani az alkalmazás a metaadatok mentése.

**Bejelentkezési mezők manuális rögzítési** abban az esetben is használható, amely az alkalmazás **szállítói nem címke** az adatbeviteli mezők a bejelentkezéshez használt. Bejelentkezési mezők manuális rögzítési is használható abban az esetben amikor a **szállítói rendereli több mező** , amely nem lehet automatikusan észleli. Azure ad-ben tárolhatja az adatokat tetszőleges számú mezőt a bejelentkezési lapon a mindaddig, mondja el, hogy ezeknek a mezőknek hol találhatók az oldalon.

Általánosságban véve **Ha automatikus bejelentkezési mezők rögzítése nem működik, próbálja ki a manuális beállítást.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Automatikusan az alkalmazás bejelentkezési mezők rögzítése

Konfigurálása **jelszóalapú egyszeri bejelentkezés** számára egy alkalmazást a **automatikus bejelentkezési mezők rögzítése**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

  * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-**, az URL-címet, amelyen a felhasználók megadják felhasználónevüket és jelszavukat való bejelentkezéshez. **Győződjön meg arról, a bejelentkezési mezők láthatók-e adnia URL-címen**.

10. Kattintson a **Mentés** gombra.

11. Miután ezzel megvagyunk... URL-cím automatikusan lekaparták van a felhasználónevet és jelszót beviteli mező, és lehetővé teszik, hogy az Azure AD a jelszavakat az alkalmazást, a hozzáférési panel böngészőbővítményének használatánál használatával küldheti.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Manuálisan az alkalmazás bejelentkezési mezők rögzítése

Bejelentkezési mezők manuális rögzítéséhez, először szüksége van a hozzáférési Panel webböngésző-bővítmény telepítve és **nem fut inPrivate, inkognitó vagy privát módban.** A webböngésző-bővítmény telepítéséhez kövesse a lépéseket a [a hozzáférési Panel webböngésző-bővítmény telepítése](#i-cannot-manually-detect-sign-in-fields-for-my-application) szakaszban.

Konfigurálása **jelszóalapú egyszeri bejelentkezés** számára egy alkalmazást a **manuális bejelentkezési mezők rögzítése**, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezéshez konfigurálandó.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Válassza ki a módot **jelszóalapú bejelentkezés.**

9.  Adja meg a **bejelentkezési URL-**, az URL-címet, amelyen a felhasználók megadják felhasználónevüket és jelszavukat való bejelentkezéshez. **Győződjön meg arról, a bejelentkezési mezők láthatók-e adnia URL-címen**.

10. Kattintson a **Mentés** gombra.

11. Miután ezzel megvagyunk... URL-cím automatikusan lekaparták van a felhasználónevet és jelszót beviteli mező, és lehetővé teszik, hogy az Azure AD a jelszavakat az alkalmazást, a hozzáférési panel böngészőbővítményének használatánál használatával küldheti. Sikertelenség esetén, is **manuális bejelentkezési mezők rögzítése használt bejelentkezési mód módosítása** továbbra is 12. lépésben leírtak szerint.

12. Kattintson a **konfigurálása &lt;appname&gt; jelszó egyszeri bejelentkezési beállításainak**.

13. Válassza ki a **bejelentkezési mezők manuális észlelése** konfigurációs beállítást.

14. Kattintson az **OK** gombra.

15. Kattintson a **Save** (Mentés) gombra.

16. Kövesse a képernyőn használata a hozzáférési panelen.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Egy "Minden olyan bejelentkezési mezők megadott URL-címen nem található" hibát

Ezt a hibaüzenetet, ha a bejelentkezési mezők automatikus észlelése sikertelen. A probléma megoldásához próbálja meg bejelentkezési mezők manuális észlelése a lépéseket követve a [manuálisan az alkalmazás bejelentkezési mezők rögzítése](#how-to-manually-capture-sign-in-fields-for-an-application) szakaszban.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>"Nem sikerült menteni az egyszeri bejelentkezés konfigurálása" látok hiba

Egyes ritka esetekben az egyszeri bejelentkezés beállításainak frissítése sikertelen lehet. Elhárításához próbálja meg az egyszeri bejelentkezési konfigurációjának mentése újra.

Ha továbbra sem sikerül egységesen, támogatási eset nyitása, és adja meg az összegyűjtött adatokat a [a portál értesítései részleteinek megtekintése](#i-cannot-manually-detect-sign-in-fields-for-my-application) és [segítségkérés támogatási elküldésével értesítés részletei a visszafejtés](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) szakaszokat.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>E nem manuális észlelése bejelentkezési mezők alkalmazás

Néhány viselkedésmódját láthatja, ha manuális észlelése nem működik a következők:

-   A manuális rögzítési folyamat működéséhez jelent meg, de a rögzített mezők nem volt megfelelő

-   A rögzítési folyamat végrehajtásakor megfelelő mezőihez nem kiemelt beolvasása

-   A rögzítési folyamat veszi át kérek az alkalmazás bejelentkezési oldal várt módon, de semmi nem történik

-   Manuális rögzítési úgy tűnik, hogy működik, de SSO nem fordulhat elő, ha az alkalmazáshoz a hozzáférési panelen keresse meg a felhasználók.

Ellenőrizze az alábbiakat, ha ezeket a problémákat észlel:

-   Gondoskodjon arról, hogy a legújabb verzióját a hozzáférési panel böngészőbővítményének használatánál **telepített** és **engedélyezve** lépéseit követve a [a hozzáférési Panel böngészőbővítménytelepítése](#how-to-install-the-access-panel-browser-extension) szakaszban.

-   Győződjön meg arról, hogy nem próbált a rögzítési folyamat során a böngészőben **inkognitó, inPrivate vagy privát módban**. A hozzáférési panel bővítmény ezek módban nem támogatott.

-   Győződjön meg arról, hogy a felhasználók nem próbál bejelentkezni az alkalmazásba a hozzáférési panelen, miközben a **inkognitó, inPrivate vagy privát módban**. A hozzáférési panel bővítmény ezek módban nem támogatott.

-   Próbálja ki a manuális rögzítési folyamat ismét, biztosítva a piros jelölők esnek-e a megfelelő mezőket.

-   Ha a manuális rögzítési folyamat úgy tűnik, hogy lefagy, vagy a bejelentkezési oldal nem csinál semmit (a fenti 3. eset), próbálkozzon a manuális rögzítési folyamat újra. De ezúttal a folyamat befejezése után nyomja le az **F12** gombra kattintva nyissa meg a böngésző fejlesztői konzolján. Egyszer, nyissa meg a **konzol** , és írja be **window.location= "&lt;adja meg a bejelentkezési URL-címet az alkalmazás konfigurálásakor megadott&gt;"** , és nyomja le az **Enter** . Ez kényszeríti, hogy a rögzítési folyamat befejeződik, és tárolja a mezőket, amelyek rendelkeznek rögzített lap átirányítási.

Ezek a módszerek egyike sem működik, ha a támogatási segítségével. Nyisson meg egy támogatási esetet mit próbált meg, valamint összegyűjtött információk részleteit a [a portál értesítései részleteinek megtekintése](#i-cannot-manually-detect-sign-in-fields-for-my-application) és [segítség kérése, ha a támogatási szakember küld értesítés részletei ](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) szakaszok (ha van).

## <a name="how-to-install-the-access-panel-browser-extension"></a>A hozzáférési Panel webböngésző-bővítmény telepítése

A hozzáférési Panel webböngésző-bővítmény telepítéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [hozzáférési Panel](https://myapps.microsoft.com) az egyik támogatott böngészők és való bejelentkezést egy **felhasználói** az Azure AD-ben.

2.  Kattintson egy **jelszó-SSO alkalmazás** a hozzáférési panelen.

3.  Válassza ki a rendszer kéri a szoftver telepítéséhez, **telepítés most**.

4.  A böngésző alapján kell irányítani a letöltési hivatkozás. **Adjon hozzá** a bővítményt a böngészőben.

5.  Ha a böngésző kéri, válassza ki vagy **engedélyezése** vagy **engedélyezése** a bővítményt.

6.  Miután telepítette, **indítsa újra a** a böngésző-munkamenetet.

7.  Jelentkezzen be a hozzáférési panelre, és tekintse meg, ha a **indítsa el a** jelszó-SSO-alkalmazásait.

Az alábbi közvetlen hivatkozások a Chrome és a Firefox is letöltheti a bővítményt:

-   [Chrome-hozzáférési Panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>A portál értesítései részleteinek megtekintése

Bármilyen portál értesítési részleteit az alábbi lépéseket követve tekintheti meg:

1.  Kattintson a **értesítések** ikonra (harang) a jobb felső sarkában az Azure Portalon

2.  Válassza ki az értesítésekhez egy **hiba** állapota (amelyek mellettük a piros (!)).

  >! Megjegyzés:], nem kattintson az értesítések egy **sikeres** vagy **folyamatban lévő** állapota.
  >
  >

3.  A **értesítés részletei** panel nyílik meg.

4.  Információk saját maga a hiba részleteinek megismeréséhez.

5.  Ha segítségre van szüksége, a az adatokat megosztani a támogatási szakember vagy a termékcsoport kaphat segítséget a probléma megoldásában.

6.  Kattintson a **másolási** **ikon** jobb oldalán a **hiba másolása** megosztani egy támogatási vagy a termékverzió csoport szakértővel értesítési részleteinek másolása szövegmezőben.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Segítség kérése, ha a támogatási szakember küld értesítés részletei

Nagyon fontos, hogy megosztott **az alábbiakban a részletekről** a támogatási szakértővel, ha segítségre van szüksége, így meghatározhatja, hogy gyorsan. Is **képernyőképek készítésének,** , vagy kattintson a **másolási hibajelző ikon**, jobb oldalán található a **hiba másolása** szövegmezőbe.

## <a name="notification-details-explained"></a>Értesítés részletei ismertetése

Az alábbiakban azt ismerteti, több milyen az értesítés azt jelenti, hogy elemeket, és azok példákat mutat be.

### <a name="essential-notification-items"></a>Fontos értesítés elemek

-   **Cím** – a leíró címet az értesítés

    -   Példa – **alkalmazásproxy-beállítások**

-   **Leírás** – Mi történt a művelet leírása

    -   Példa – **megadott belső URL-címet már használja egy másik alkalmazás**

-   **Értesítés azonosítója** – az értesítés egyedi azonosítója

    -   Példa – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Ügyfélkérés azonosítója** – a megadott kérés azonosítója, a böngésző által készített

    -   Példa – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Szolgáltatásblokk UTC idő** – az időbélyeg, amely során az értesítés történt (UTC)

    -   Példa – **2017-03-23T19:50:43.7583681Z**

-   **Belső Tranzakcióazonosító** – rendszereinkben keresse ki a hibát használt belső azonosítója

    -   Example – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Egyszerű felhasználónév** – a műveletet végrehajtó felhasználó

    -   – Példa **tperkins@f128.info**

-   **Bérlőazonosító** –, amelyek a műveletet végrehajtó felhasználó tagja volt. a bérlő egyedi azonosítója

    -   Example – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Felhasználói objektum azonosítója** – a műveletet végrehajtó felhasználó egyedi azonosítója

    -   Példa – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

-   **Megjelenítendő név** – **(üres is lehet)** a hiba részletes megjelenített neve

    -   Példa * – **alkalmazásproxy-beállítások**

-   **Állapot** – az értesítésre adott állapota

    -   Példa * – **nem sikerült**

-   **Objektumazonosító:** – **(üres is lehet)** az Objektumazonosító, amelyek hajtottak végre a műveletet

    -   Példa – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Részletek** – a részletes Mi történt a művelet leírása

    -   Példa – **belső URL-cím "http://bing.com/" értéke érvénytelen, mert már használatban van**

-   **Másolási hiba** – kattintson a **másolás ikonra** jobb oldalán a **hiba másolása** szövegmező másolása egy támogatási vagy a termékverzió csoport mérnök megosztása az összes értesítés részletei

    -   – Példa ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

