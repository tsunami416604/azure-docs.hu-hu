---
title: Jelszavas egyszeri Bejelentkezést a katalógusban nem szereplő alkalmazás konfigurálásával kapcsolatos problémák |} A Microsoft Docs
description: Egyéni alkalmazások, amelyek nem az Azure AD alkalmazáskatalógusában a jelszavas egyszeri bejelentkezés (SSO) konfigurálásakor előforduló gyakori problémák.
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440356"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Jelszavas egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálásával kapcsolatos problémák

Ez a cikk ismerteti a konfigurálásakor előforduló gyakori problémák *jelszavas egyszeri bejelentkezés* (SSO) katalógusban nem szereplő alkalmazásokhoz.

## <a name="capture-sign-in-fields-for-an-app"></a>Az alkalmazás bejelentkezési mezők rögzítése

Bejelentkezési mezők rögzítése csak a HTML-kompatibilis bejelentkezési lapok esetében támogatott. Nem támogatott a nem szabványos bejelentkezési lapok, mint az Adobe Flash használó vagy más nem HTML-kompatibilis technológiákat.

Az egyéni alkalmazások bejelentkezési mezők rögzítése két módja van:

- **Az automatikus bejelentkezési mezők rögzítése** nagyszerűen működik a legtöbb HTML-kompatibilis bejelentkezési oldalt, *, ha azok jól ismert DIV azonosítók* a felhasználói név és jelszó mezők. A HTML-kódot az oldalon található DIV azonosítók, amelyek megfelelnek bizonyos feltételeknek van lekaparták. Ezekhez a metaadatokhoz a rendszer menti, így később megismétlését az alkalmazáshoz.

- **Bejelentkezési mezők manuális rögzítési** akkor használatos, ha az alkalmazás gyártójával *nem címkemezők a bejelentkezési bemeneti*. Manuális rögzítési is használatos, ha a szállító *több mező található, nem lehet automatikusan észlelt rendereli*. Az Azure Active Directory (Azure AD) annyi mezők, mert a bejelentkezési lapon megadhatja, hogy ezeknek a mezőknek hol találhatók az oldalon Ha adatait tárolhatja.

Általánosságban elmondható Ha az automatikus bejelentkezési mezők rögzítése nem működik, a manuális beállítást.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatikusan az alkalmazás bejelentkezési mezők rögzítése

Jelszóalapú egyszeri bejelentkezés konfigurálása az automatikus bejelentkezési mezők rögzítése használatával, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. A bal oldali navigációs ablaktábláján válassza **minden szolgáltatás** megnyitásához az Azure AD-bővítményben.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás** listája. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amely az egyszeri bejelentkezéshez konfigurálandó.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** a bal oldali navigációs ablakban.

8. Válassza ki **jelszóalapú bejelentkezés** mód.

9. Adja meg a **bejelentkezési URL-** , azaz az oldal, ahol adja meg a felhasználóknak a felhasználónév és a jelszót, hogy jelentkezzen be az URL-címet. *Győződjön meg arról, hogy a bejelentkezési mezők láthatók az Ön által megadott URL-cím oldalán*.

10. Kattintson a **Mentés** gombra.

    A lap automatikusan lekaparták, a felhasználói nevet és jelszót beviteli mezők esetében. A hozzáférési Panel böngészőbővítményének használatánál használatával küldheti a jelszavakat, amelyet az alkalmazás most már használhatja az Azure ad-ben.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Manuálisan az alkalmazás bejelentkezési mezők rögzítése

Bejelentkezési mezők manuális rögzítéséhez a hozzáférési Panel böngészőbővítményének használatánál telepítve kell rendelkeznie. Is, a böngésző nem fut *inPrivate*, *inkognitó*, vagy *privát* mód.

A bővítmény telepítése: a [a hozzáférési Panel webböngésző-bővítmény telepítése](#install-the-access-panel-browser-extension) című szakaszát.

Manuális bejelentkezési mezők rögzítése a jelszóalapú egyszeri bejelentkezés az alkalmazás konfigurálásához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be egy globális rendszergazdai vagy társadminisztrátorként.

2. A bal oldali navigációs ablaktábláján válassza **minden szolgáltatás** megnyitásához az Azure AD-bővítményben.

3. Típus **Azure Active Directory** a szűrőt a keresési mezőbe, és válassza ki a **Azure Active Directory**.

4. Válassza ki **vállalati alkalmazások** az Azure ad-ben navigációs ablaktáblán.

5. Válassza ki **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   > [!NOTE] 
   > Ha nem látja a kívánt alkalmazást, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás** listája. Állítsa be a **megjelenítése** beállítást az "Összes alkalmazás."

6. Válassza ki az alkalmazást, amely az egyszeri bejelentkezéshez konfigurálandó.

7. Miután betölti az alkalmazást, válassza ki **egyszeri bejelentkezési** a bal oldali navigációs ablakban.

8. Válassza ki **jelszóalapú bejelentkezés** mód.

9. Adja meg a **bejelentkezési URL-** , azaz az oldal, ahol adja meg a felhasználóknak a felhasználónév és a jelszót, hogy jelentkezzen be. *Győződjön meg arról, hogy a bejelentkezési mezők láthatók az Ön által megadott URL-cím oldalán*.

10. Válassza ki **konfigurálása *&lt;appname&gt;* jelszó egyszeri bejelentkezési beállításainak**.

11. Válassza ki **bejelentkezési mezők manuális észlelése**.

14. Kattintson az **OK** gombra.

15. Kattintson a **Mentés** gombra.

16. Kövesse az utasításokat a hozzáférési panelen.

## <a name="troubleshoot-problems"></a>Kapcsolatos problémák elhárítása

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Egy "Minden olyan bejelentkezési mezők megadott URL-címen nem található" hibaüzenet jelenik meg:

Bejelentkezési mezők automatikus észlelése sikertelen lesz. Ha ez a hibaüzenet kap. A probléma megoldásához próbálja meg a bejelentkezési mezők manuális észlelése. Tekintse meg a [manuálisan az alkalmazás bejelentkezési mezők rögzítése](#manually-capture-sign-in-fields-for-an-app) című szakaszát.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Kapok "Nem sikerült menteni az egyszeri bejelentkezés beállításainak" hiba

A ritkán az egyszeri bejelentkezés beállításainak frissítése sikertelen. A probléma megoldásához próbálja meg újra a konfiguráció mentése.

Ha a hiba továbbra is megkapja, nyisson meg egy támogatási esetet. A leírt adatokat tartalmazzák a [portal értesítése a részletek megtekintéséhez](#view-portal-notification-details) és [értesítés küldése egy támogatási szakértőhöz, ha segítséget szeretne kérni](#send-notification-details-to-a-support-engineer-to-get-help) Ez a cikk szakaszainak.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>E nem manuális észlelése bejelentkezési mezők a saját alkalmazás

Az alábbi viselkedés tapasztalható előfordulhat, hogy figyelje meg, ha manuális észlelése nem működik:

- A manuális rögzítési folyamat működéséhez jelent meg, de a rögzített mezők nem megfelelőek.

- A megfelelő mezőket ne első kiemelve a rögzítési folyamat futtatásakor.

- A rögzítési folyamat veszi át, az alkalmazás bejelentkezési oldala a várt módon, de semmi nem történik.

- Manuális rögzítési úgy tűnik, hogy működik, de SSO nem fordulhat elő, amikor a felhasználók megnyitják az alkalmazást a hozzáférési panelen.

Ha bármelyik ezeket a problémákat tapasztal, tegye a következőket:

- Győződjön meg arról, hogy a legújabb verzióját a hozzáférési Panel böngészőbővítményének használatánál *telepítve és engedélyezve van*. Tekintse meg a [telepítse a hozzáférési Panel böngészőbővítményének használatánál](#install-the-access-panel-browser-extension) című szakaszát.

- Győződjön meg arról, hogy a böngésző nem *inkognitó*, *inPrivate*, vagy *privát* mód a rögzítési folyamat során. A hozzáférési Panel bővítmény ezek módban nem támogatott.

- Győződjön meg arról, hogy a felhasználók nem próbál jelentkezzen be az alkalmazásba a hozzáférési panelen közben az *inkognitó*, *inPrivate*, vagy *privát módban*.

- Próbálja meg újra a manuális rögzítési folyamat. Győződjön meg arról, hogy a piros jelölők esnek-e a megfelelő mezőket.

- Ha a manuális rögzítési folyamat úgy tűnik, hogy nem válaszol, vagy a bejelentkezési oldal nem válaszol, próbálkozzon újra a manuális rögzítési folyamat. De ezúttal a folyamat befejezését követően az F12 billentyűt a böngésző fejlesztői konzol megnyitásához. Válassza ki a **konzol** fülre. Típus **window.location= " *&lt;a bejelentkezési URL-címet az alkalmazás konfigurálásakor megadott&gt;* "** , és nyomja le az ENTER billentyűt. Ez kényszeríti, hogy a rögzítési folyamat befejeződik, és tárolja a mezőket, amelyek akkor lettek rögzítve lap átirányítási.

### <a name="contact-support"></a>Forduljon a támogatási szolgálathoz.

Ha a probléma továbbra is fennáll, nyisson meg egy esetet Support. Írja le, mit próbált meg. A részleteket a ismertetett tartalmazza a [portal értesítése a részletek megtekintéséhez](#view-portal-notification-details) és [értesítés küldése egy támogatási szakértőhöz, ha segítséget szeretne kérni](#send-notification-details-to-a-support-engineer-to-get-help) (ha alkalmazható) Ez a cikk szakaszainak.

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési Panel böngészőbővítményének használatánál telepítése

Kövesse az alábbi lépéseket:

1. Nyissa meg [hozzáférési Panel](https://myapps.microsoft.com) egy támogatott böngészővel. Jelentkezzen be az Azure ad-hez mint egy *felhasználói*.

2. Válassza ki **jelszó-SSO alkalmazás** a hozzáférési panelen.

3. Ha a szoftver telepítésére kéri, válassza ki a **telepítés most**.

4. Akkor jut el a letöltési oldalra a böngészőjében. Válassza ki a **Hozzáadás** a bővítményt.

5. Ha az kéri, válassza ki a **engedélyezése** vagy **engedélyezése**.

6. A telepítés után indítsa újra a böngészőt.

7. Jelentkezzen be a hozzáférési panelen. Tekintse meg, ha a jelszó-SSO-kompatibilis alkalmazások megnyitható.

Emellett közvetlenül is letöltheti a webböngésző-bővítmény a Chrome és a Firefox keresztül ezeket a hivatkozásokat:

-   [Chrome-hozzáférési Panel kiterjesztése](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [A Firefox hozzáférési Panel kiterjesztése](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Portál értesítési részleteinek megtekintése

Bármilyen portál értesítési részleteinek megtekintéséhez kövesse az alábbi lépéseket:

1. Válassza ki a **értesítések** ikont (Csengő) az Azure portal jobb felső sarkában.

2. Válassza ki minden olyan értesítés, amely bemutatja egy *hiba* állapota. (A piros rendelkeznek "!".)

   > [!NOTE]
   > Nem választhat ki, amelyek az értesítések a *sikeres* vagy *folyamatban lévő* állapota.

3. A **értesítés részletei** panel nyílik meg. Olvassa el a problémáról további információk.

5. Ha segítségre van szüksége, megoszthatja az információkat a támogatási szakember vagy a csoport. Válassza ki a **másolási** ikont jobb oldalán a **hiba másolása** jelölőnégyzetet, hogy másolja vágólapra az értesítési adatokat megosztani.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Értesítés küldése egy támogatási szakértőhöz, ha segítséget szeretne kérni

Fontos, hogy megosztott *összes* támogatásával, ebben a szakaszban szereplő így meghatározhatja, hogy gyorsan részleteit. Jegyezze fel, a képernyőképek készítésének vagy válasszon **hiba másolása**.

A következő információkat ismerteti, mi minden értesítési elem azt jelenti, hogy, és példákat talál.

### <a name="essential-notification-items"></a>Fontos értesítés elemek

- **Cím**: az értesítés leíró címet.

   Példa: *Alkalmazásproxy-beállítások*

- **Leírás**: Mi történt a művelet eredményeként.

   Példa: *A megadott belső URL-cím már használatban van egy másik alkalmazás.*

- **Értesítés azonosítója**: az értesítés egyedi azonosítója.

    Példa: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Ügyfélkérés azonosítója**: a megadott kérés azonosítója, hogy a böngészőben.

    Példa: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Szolgáltatásblokk UTC idő**: az időbélyeg, ha az értesítés történt, (UTC).

    Példa: *2017-03-23T19:50:43.7583681Z*

- **Belső Tranzakcióazonosító**: a belső azonosítója, amellyel rendszereinkben keressen.

    Példa: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **EGYSZERŰ FELHASZNÁLÓNÉV**: A felhasználó, aki futtatta a műveletet.

    Példa: *tperkins\@f128.info*

- **Bérlőazonosító**:, hogy a művelet futtató felhasználó tagja a bérlő egyedi azonosítója.

    Példa: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Felhasználói objektum azonosítója**: A művelet futtató felhasználó egyedi azonosítója.

    Példa: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

- **Megjelenítendő név**: (üres is lehet) részletesebb megjelenített neve, a hibát.

    Példa: *Alkalmazásproxy-beállítások*

- **Állapot**: az értesítés az adott állapotát.

    Példa: *Nem sikerült*

- **Objektumazonosító:** : (üres is lehet) az Objektumazonosítót, amelyre vonatkozóan a művelet volt futtatva.

   Példa: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Részletek**: Mi történt a művelet eredményeként a részletes leírását.

    Példa: *Belső URL-cím "<https://bing.com/>" értéke érvénytelen, mert már használatban van.*

- **Másolási hiba**: Lehetővé teszi, hogy válassza ki a **másolás ikonra** jobb oldalán a **hiba másolása** szövegmezőben másolja vágólapra az értesítési adatokat a támogatása érdekében.

    Példa:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>További lépések
[Az alkalmazásproxy egyszeri bejelentkezést az alkalmazásokba biztosít](application-proxy-configure-single-sign-on-with-kcd.md)
