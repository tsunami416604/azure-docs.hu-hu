---
title: Problémák a jelszó egyszeri bejelentkezés konfigurálása nem galéria alkalmazásokhoz
description: Gyakori problémák, amelyek akkor fordulnak elő, ha a jelszó egyszeri bejelentkezés (SSO) az egyéni alkalmazások, amelyek nem az Azure AD-alkalmazás katalógusban.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274132"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problémák a jelszó egyszeri bejelentkezésének konfigurálásával egy nem katalógusalkalmazáshoz

Ez a cikk azokat a gyakori problémákat ismerteti, amelyek akkor fordulhatnak elő, ha egy nem katalógusalkalmazáshoz egyetlen bejelentkezést (Egyszeri *bejelentkezés)* konfigurál.

## <a name="capture-sign-in-fields-for-an-app"></a>Bejelentkezési mezők rögzítése egy alkalmazáshoz

A bejelentkezési mező rögzítése csak a HTML-kompatibilis bejelentkezési oldalakesetében támogatott. Nem támogatott a nem szabványos bejelentkezési oldalak, például az Adobe Flash vagy más, nem HTML-kompatibilis technológiák használatát.

Az egyéni alkalmazások bejelentkezési mezőinek rögzítésére kétféleképpen rögzítheti a bejelentkezési mezőket:

- **Az automatikus bejelentkezési mezőrögzítés** jól működik a legtöbb HTML-kompatibilis bejelentkezési oldalon, *ha jól ismert DIV-azonosítókat használnak* a felhasználónév és a jelszó mezőkhöz. Az oldalon lévő HTML-t lekaparták, hogy olyan DIV azonosítókat találjanak, amelyek megfelelnek bizonyos feltételeknek. A rendszer menti a metaadatokat, hogy később visszalehessen játszani az alkalmazásba.

- **A kézi bejelentkezési mezőrögzítés** akkor használatos, ha az alkalmazás szállítója *nem címkézi a bejelentkezési beviteli mezőket.* A kézi rögzítés akkor is használatos, ha a szállító *több olyan mezőt jelenít meg, amelyeknem észlelhetők automatikusan.* Az Azure Active Directory (Azure AD) a bejelentkezési lapon található annyi mező adatait tárolhatja, ha megmondja, hogy hol vannak ezek a mezők az oldalon.

Általában, ha az automatikus bejelentkezési mező rögzítése nem működik, próbálkozzon a kézi beállítással.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek automatikus rögzítése

A jelszóalapú egyszeri bejelentkezés automatikus bejelentkezési mezőrögzítéssel történő konfigurálásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társadminisztrátorként.

2. A bal oldali navigációs ablakban válassza a **Minden szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás** lista tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet az SSO-hoz szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablakban.

8. Válassza **a Jelszóalapú bejelentkezési** mód lehetőséget.

9. Adja meg a **bejelentkezési URL-címet**, amely annak a lapnak az URL-címe, ahol a felhasználók beírják a felhasználónevüket és jelszavukat a bejelentkezéshez. *Győződjön meg arról, hogy a bejelentkezési mezők láthatók a megadott URL-cím oldalán.*

10. Kattintson a **Mentés** gombra.

    A rendszer automatikusan lekaparta az oldalt a felhasználónév és a jelszó beviteli mezőihez. Most már használhatja az Azure AD biztonságosan továbbíthatja a jelszavakat, hogy az alkalmazás segítségével az Access Panel böngészőbővítmény.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek manuális rögzítése

A bejelentkezési mezők manuális rögzítéséhez telepítve kell lennie az Access Panel böngészőbővítményének. Emellett a böngésző nem futtatható *inPrivate,* *inkognitóban*vagy *privát* módban.

A bővítmény telepítéséhez olvassa el a cikk [A hozzáférési panel böngészőbővítményének telepítése](#install-the-access-panel-browser-extension) című szakaszát.

Ha egy alkalmazáshoz jelszóalapú egyszeri bejelentkezést szeretne beállítani manuális bejelentkezési mezőrögzítéssel, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társadminisztrátorként.

2. A bal oldali navigációs ablakban válassza a **Minden szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.

3. Írja be az **Azure Active Directory** kifejezést a szűrő keresőmezőjébe, majd válassza az Azure Active **Directory**lehetőséget.

4. Válassza ki a **vállalati alkalmazások** az Azure AD navigációs ablaktáblán.

5. Válassza **a Minden alkalmazás lehetőséget** az alkalmazások listájának megtekintéséhez.

   > [!NOTE] 
   > Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás** lista tetején. Állítsa a **Megjelenítés** beállítást "Minden alkalmazás" beállításra.

6. Válassza ki azt az alkalmazást, amelyet az SSO-hoz szeretne konfigurálni.

7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablakban.

8. Válassza **a Jelszóalapú bejelentkezési** mód lehetőséget.

9. Adja meg a **bejelentkezési URL-címet**, amely az a lap, ahol a felhasználók beírják a felhasználónevüket és jelszavukat a bejelentkezéshez. *Győződjön meg arról, hogy a bejelentkezési mezők láthatók a megadott URL-cím oldalán.*

10. Válassza **az * &lt;Alkalmazásnév&gt; * jelszó konfigurálása egyszeri bejelentkezési beállítások lehetőséget.**

11. Jelölje **be a Bejelentkezési mezők manuális észlelése**jelölőnégyzetet.

14. Válassza az **Ok gombot.**

15. Kattintson a **Mentés** gombra.

16. A Hozzáférési panel használatához kövesse az utasításokat.

## <a name="troubleshoot-problems"></a>Problémák elhárítása

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"Nem találtunk bejelentkezési mezőket az adott URL-címen" hibaüzenet jelenik meg

Ez a hibaüzenet akkor jelenik meg, ha a bejelentkezési mezők automatikus észlelése sikertelen. A probléma megoldásához próbálkozzon a manuális bejelentkezési mező észlelésével. Tekintse meg a [cikk alkalmazásszakaszának bejelentkezési mezőinek kézi rögzítése](#manually-capture-sign-in-fields-for-an-app) című szakaszát.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Nem sikerült menteni az egyszeri bejelentkezési konfigurációt" hibaüzenet jelenik meg

Ritkán az SSO-konfiguráció frissítése sikertelen. A probléma megoldásához próbálja meg újra menteni a konfigurációt.

Ha továbbra is megjelenik a hiba, nyisson meg egy támogatási esetet. Adja meg a [Portál értesítési részleteinek megtekintése](#view-portal-notification-details) és az [Értesítés részleteinek küldése a támogatási szakembernek](#send-notification-details-to-a-support-engineer-to-get-help) a cikk súgószakaszaiban ismertetett információkat.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nem tudom manuálisan észlelni az alkalmazásbejelentkezési mezőket

Ha a manuális észlelés nem működik, a következő viselkedéseket észlelheti:

- A kézi rögzítési folyamat működött, de a rögzített mezők nem helyesek.

- A rögzítési folyamat futtatásakor a megfelelő mezők nem jelennek meg.

- A rögzítési folyamat az alkalmazás bejelentkezési oldalára vezet, ahogy az várható volt, de semmi sem történik.

- Úgy tűnik, hogy a manuális rögzítés működik, de az SSO nem történik meg, amikor a felhasználók a Hozzáférési panelről navigálnak az alkalmazásra.

Ha a következő problémák bármelyikét tapasztalja, tegye a következőket:

- Győződjön meg arról, hogy az Access Panel böngészőbővítmény legújabb verziója *telepítve van és engedélyezve van.* Lásd a cikk [Access Panel böngészőbővítményének telepítése](#install-the-access-panel-browser-extension) című részét.

- Győződjön meg arról, hogy a böngésző nem *inkognitóban*, *inPrivate*, vagy *privát* módban van a rögzítési folyamat során. Ezekben a módokban a Hozzáférési panel bővítmény nem támogatott.

- Győződjön meg arról, hogy a felhasználók nem próbálnak meg bejelentkezni az alkalmazásba a Hozzáférési panelről *inkognitóban*, *inPrivate*vagy *Privát módban.*

- Próbálkozzon újra a kézi rögzítési folyamattal. Győződjön meg arról, hogy a piros jelölők a megfelelő mezők felett vannak.

- Ha úgy tűnik, hogy a manuális rögzítési folyamat nem válaszol, vagy a bejelentkezési oldal nem válaszol, próbálkozzon újra a kézi rögzítési folyamattal. De ezúttal a folyamat befejezése után nyomja meg az F12 billentyűt a böngésző fejlesztői konzoljának megnyitásához. Válassza a **konzol** fülre. Írja be **az ablakot.location="*&lt;az&gt;alkalmazás konfigurálásakor megadott bejelentkezési URL-címet"***, majd nyomja le az Enter billentyűt. Ez kényszeríti az oldalátirányítást, amely befejezi a rögzítési folyamatot, és tárolja a rögzített mezőket.

### <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is problémái vannak, nyisson meg egy esetet a Microsoft támogatási szolgálatával. Írja le, mit próbált. Adja meg a portál [értesítési részleteinek megtekintése](#view-portal-notification-details) és az [értesítés részleteinek küldése a támogatási szakembernek](#send-notification-details-to-a-support-engineer-to-get-help) a cikk súgószakaszaiban leírt részleteket (ha van ilyen).

## <a name="install-the-access-panel-browser-extension"></a>Az Access Panel böngészőbővítményének telepítése

Kövesse az alábbi lépéseket:

1. Nyissa meg a [Hozzáférési panelt](https://myapps.microsoft.com) egy támogatott böngészőben. Jelentkezzen be az Azure *AD-be felhasználóként.*

2. Válassza ki **a jelszó-Egyszeri bejelentkezés alkalmazást** a Hozzáférési panelen.

3. Amikor a rendszer a szoftver telepítését kéri, válassza a **Telepítés most**lehetőséget.

4. A böngésző letöltési oldalára irányítjuk. Válassza a bővítmény **hozzáadását.**

5. Ha a rendszer kéri, válassza az **Engedélyezés** vagy **az Engedélyezés**lehetőséget.

6. A telepítés után indítsa újra a böngészőt.

7. Jelentkezzen be a hozzáférési panelre. Nézze meg, hogy meg tudja-e nyitni a jelszó-SSO-kompatibilis alkalmazásokat.

Ön is közvetlenül letöltheti a böngésző kiterjesztése chrome és a Firefox ezeken a linkeken keresztül:

-   [A Chrome access panel bővítménye](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Portálértesítés részleteinek megtekintése

A portálértesítések részleteinek megtekintéséhez kövesse az alábbi lépéseket:

1. Válassza az **Értesítések** ikont (a csengőt) az Azure Portal jobb felső sarkában.

2. Jelöljön ki minden olyan értesítést, amely *hibaállapotot jelenít meg.* (Van egy piros "!".)

   > [!NOTE]
   > Nem választhatja ki a *Sikeres* vagy folyamatban *lévő* értesítéseket.

3. Megnyílik **az Értesítés részletei** ablaktábla. A probléma megismeréséhez olvassa el az információkat.

5. Ha továbbra is segítségre van szüksége, ossza meg az információkat egy támogatási szakemberrel vagy a termékcsoporttal. A **copy** megosztásra, a másolási ikonra a **Másolás hibamező** jobb oldalán.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Értesítésrészleteinek elküldése a támogatási szakembernek a segítséghez

Fontos, hogy az ebben a szakaszban felsorolt *összes* részletet megossza támogatással, hogy azok gyorsan segíthessenek. A felvételhez képernyőképet készíthet, vagy **kiválaszthatja a Másolási hibát**.

Az alábbi információk ismertetik, hogy mit jelentenek az egyes értesítési elemek, és példákat tartalmaz.

### <a name="essential-notification-items"></a>Alapvető értesítési tételek

- **Megnevezése**: az értesítés leíró címe.

   Példa: *Alkalmazásproxy-beállítások*

- **Leírás**: a művelet eredményeként történt.

   Példa: *A megadott belső URL-t már egy másik alkalmazás használja.*

- **Értesítésazonosító:** az értesítés egyedi azonosítója.

    Példa: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Ügyfélkérelem-azonosító:** a böngésző által készített konkrét kérelemazonosító.

    Példa: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Időbélyegző UTC:** az értesítés megtörténtének időbélyegzője UTC-ben.

    Példa: *2017-03-23T19:50:43.7583681Z*

- **Belső tranzakcióazonosító:** a belső azonosító, amely a rendszereinkben a hiba kinézetére szolgál.

    Példa: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UpN**: A műveletet futtató felhasználó.

    Példa: *tperkins\@f128.info*

- **Bérlőazonosító:** annak a bérlőnek az egyedi azonosítója, amelynek a műveletet futtató felhasználó tagja.

    Példa: *7918d4b5-0442-4a97-be2d-36f9962ece*

- **Felhasználói objektum azonosítója**: A műveletet futtató felhasználó egyedi azonosítója.

    Példa: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Részletes értesítési tételek

- **Megjelenítendő név**: (lehet üres) a hiba részletesebb megjelenítendő neve.

    Példa: *Alkalmazásproxy-beállítások*

- **Állapot**: az értesítés adott állapota.

    Példa: *Sikertelen*

- **Objektumazonosító**: (lehet üres) az az objektumazonosító, amelyre a műveletet futtatták.

   Példa: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Részletek**: a művelet eredményeként történt adatok részletes leírása.

    Példa: *A<https://bing.com/>belső url ' ' érvénytelen, mivel már használatban van.*

- **Másolási hiba:** Lehetővé teszi, hogy a Másolás hiba szövegdoboztól **jobbra** lévő **másolási ikont** válassza az értesítés részleteinek másolásához, hogy segítséget nyújtson a támogatáshoz.

    Példa:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés az alkalmazásokba alkalmazásproxyval](application-proxy-configure-single-sign-on-with-kcd.md)
