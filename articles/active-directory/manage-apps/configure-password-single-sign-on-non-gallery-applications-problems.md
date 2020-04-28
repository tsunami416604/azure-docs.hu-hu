---
title: Problémák a nem katalógusbeli alkalmazások jelszavas BEJELENTKEZÉSének konfigurálásakor
description: Az Azure AD-alkalmazás-katalógusban nem szereplő egyéni alkalmazások jelszavas egyszeri bejelentkezés (SSO) beállításakor előforduló gyakori problémák.
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274132"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problémák a nem katalógusbeli alkalmazások jelszavas egyszeri bejelentkezésének konfigurálásakor

Ez a cikk azokat a gyakori problémákat ismerteti, amelyek akkor fordulhatnak elő, ha a *jelszó egyszeri bejelentkezést* (SSO) konfigurálja egy nem katalógusbeli alkalmazáshoz.

## <a name="capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek rögzítése

A bejelentkezési mezők rögzítése csak HTML-kompatibilis bejelentkezési lapok esetén támogatott. A nem szabványos bejelentkezési lapok esetében nem támogatottak, például az Adobe Flash vagy más, nem HTML-kompatibilis technológiák használatát.

Az egyéni alkalmazások bejelentkezési mezőinek rögzítése kétféleképpen lehetséges:

- *Ha jól ismert div-azonosítókat használ* a Felhasználónév és a jelszó mezőkhöz, az **automatikus bejelentkezési mező rögzítése** a legtöbb HTML-kompatibilis bejelentkezési oldallal jól működik. Az oldalon található HTML-fájl a megadott feltételeknek megfelelő DIV-azonosítók megkeresésére van lekaparva. A rendszer menti a metaadatokat, hogy később újra lehessen játszani az alkalmazásban.

- A **bejelentkezési mező manuális rögzítése** akkor használatos, ha az alkalmazás gyártója *nem címkézi a bejelentkezési beviteli mezőket*. A manuális rögzítés akkor is használatos, ha a szállító *több olyan mezőt jelenít meg, amelyek nem észlelhetők automatikusan*. A Azure Active Directory (Azure AD) annyi mezőre képes tárolni az adattárolást, mint a bejelentkezési oldalon, ha azt mondja, hogy ezek a mezők az oldalon találhatók.

Általánosságban elmondható, hogy ha az automatikus bejelentkezési mező rögzítése nem működik, próbálkozzon a manuális beállítással.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek automatikus rögzítése

Ha a jelszó-alapú egyszeri bejelentkezést az automatikus bejelentkezési mező rögzítése használatával szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. A bal oldali navigációs ablaktáblán a **minden szolgáltatás** elemre kattintva nyissa meg az Azure ad bővítményt.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás** lista tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablaktáblán.

8. Válassza a **jelszó alapú bejelentkezési** mód lehetőséget.

9. Adja meg a **bejelentkezési URL-címet**, amely annak az oldalnak az URL-címe, ahol a felhasználók megadják a felhasználónevét és jelszavát a bejelentkezéshez. Győződjön *meg arról, hogy a bejelentkezési mezők láthatók az Ön által megadott URL-címen a lapon*.

10. Kattintson a **Mentés** gombra.

    A rendszer automatikusan lekaparja a lapot a Felhasználónév és a jelszó beviteli mezőihez. Mostantól az Azure AD használatával biztonságosan továbbíthatja a jelszavakat az alkalmazáshoz a hozzáférési panel böngésző bővítményének használatával.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Egy alkalmazás bejelentkezési mezőinek manuális rögzítése

A bejelentkezési mezők manuális rögzítéséhez telepíteni kell a hozzáférési panel böngésző bővítményét. Emellett a böngésző nem futhat *InPrivate*, *inkognitóban*vagy *privát* módban.

A bővítmény telepítéséhez tekintse meg a jelen cikk [hozzáférési panel böngésző bővítményének telepítése](#install-the-access-panel-browser-extension) című szakaszát.

Ha a jelszó-alapú egyszeri bejelentkezést szeretné beállítani egy alkalmazáshoz a manuális bejelentkezési mező rögzítése segítségével, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.

2. A bal oldali navigációs ablaktáblán a **minden szolgáltatás** elemre kattintva nyissa meg az Azure ad bővítményt.

3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory**lehetőséget.

4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.

5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.

   > [!NOTE] 
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás** lista tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.

6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.

7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablaktáblán.

8. Válassza a **jelszó alapú bejelentkezési** mód lehetőséget.

9. Adja meg a **bejelentkezési URL-címet**, amely a felhasználók felhasználónevének és jelszavának megadására szolgáló lap. Győződjön *meg arról, hogy a bejelentkezési mezők láthatók az Ön által megadott URL-címen a lapon*.

10. Válassza **a * &lt;AppName&gt; * jelszavának egyszeri bejelentkezési beállításainak konfigurálása**lehetőséget.

11. Jelölje be **a bejelentkezési mezők manuális észlelése**jelölőnégyzetet.

14. Kattintson **az OK gombra**.

15. Kattintson a **Mentés** gombra.

16. A hozzáférési panel használatához kövesse az utasításokat.

## <a name="troubleshoot-problems"></a>Problémák elhárítása

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"Nem találtunk a bejelentkezési mezőket ezen az URL-címen" hibaüzenet jelenik meg.

Ez a hibaüzenet akkor jelenik meg, ha a bejelentkezési mezők automatikus észlelése sikertelen. A probléma megoldásához próbálkozzon a bejelentkezési mezők manuális észlelésével. Tekintse meg a jelen cikk [alkalmazás-és bejelentkezési mezőinek kézi rögzítése](#manually-capture-sign-in-fields-for-an-app) című szakaszát.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Nem sikerült menteni az egyszeri bejelentkezési konfigurációt" hibaüzenet jelenik meg

Ritkán, az SSO-konfiguráció frissítése sikertelen. A probléma megoldásához próbálja meg újból menteni a konfigurációt.

Ha megtartja a hibát, nyisson meg egy támogatási esetet. A jelen cikk Súgó szakaszának beszerzéséhez adja meg a [portál értesítési adatainak megtekintése](#view-portal-notification-details) és az [értesítési adatok küldése támogatási szakembernek](#send-notification-details-to-a-support-engineer-to-get-help) című témakörben leírt információkat.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nem tudom manuálisan megállapítani a bejelentkezési mezőket az alkalmazáshoz

Ha a manuális észlelés nem működik, a következő viselkedéseket érdemes figyelembe venni:

- A manuális rögzítési folyamat úgy tűnt, hogy működik, de a rögzített mezők nem megfelelőek.

- A rögzítési folyamat futtatásakor a megfelelő mezők nem jelennek meg a kiemelve.

- A rögzítési folyamat végigvezeti az alkalmazás bejelentkezési oldalának a várt módon, de semmi nem történik.

- A manuális rögzítés úgy tűnik, hogy működik, az egyszeri bejelentkezés azonban nem történik meg, amikor a felhasználók a hozzáférési panelen navigálnak az alkalmazáshoz.

Ha ezeket a problémákat tapasztalja, tegye a következőket:

- Győződjön meg arról, hogy a hozzáférési panel böngésző bővítményének legújabb verziója van *telepítve és engedélyezve*. Tekintse meg a jelen cikk [hozzáférési panel böngésző bővítményének telepítése](#install-the-access-panel-browser-extension) című szakaszát.

- A rögzítési folyamat során győződjön meg arról, hogy a böngésző nem az *inkognitóban*, *InPrivate*vagy *Private* módban van. A hozzáférési panel kiterjesztése ezekben a módokban nem támogatott.

- Győződjön meg arról, hogy a felhasználók nem próbálnak bejelentkezni az alkalmazásba a hozzáférési panelről az *Incognito*, *InPrivate*vagy *Private módban*.

- Próbálkozzon újra a manuális rögzítési folyamattal. Győződjön meg arról, hogy a piros jelölők a megfelelő mezőkön vannak.

- Ha a manuális rögzítési folyamat úgy tűnik, hogy nem válaszol, vagy a bejelentkezési oldal nem válaszol, próbálkozzon újra a manuális rögzítési folyamattal. Ezúttal azonban a folyamat befejezése után nyomja le az F12 billentyűt a böngésző fejlesztői konzoljának megnyitásához. Válassza a **konzol** fület. írja be az **ablakot. location = "*&lt;az alkalmazás&gt;konfigurálásakor megadott bejelentkezési URL-címet***, majd nyomja le az ENTER billentyűt. Ez kényszeríti az átirányítást, amely befejezi a rögzítési folyamatot, és a rögzített mezőket tárolja.

### <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is problémákba ütközne, nyisson meg egy esetet Microsoft ügyfélszolgálata. Írja le, hogy mit próbált meg. Adja meg a [portálon](#view-portal-notification-details) megjelenő értesítés részletei között ismertetett részleteket, és [küldjön értesítéseket a támogatási szakembernek a cikk súgójának](#send-notification-details-to-a-support-engineer-to-get-help) megtekintéséhez (ha van ilyen).

## <a name="install-the-access-panel-browser-extension"></a>A hozzáférési panel böngésző bővítményének telepítése

Kövesse az alábbi lépéseket:

1. Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com) egy támogatott böngészőben. Jelentkezzen be az Azure AD-be *felhasználóként*.

2. Válassza a **jelszó-SSO-alkalmazás** lehetőséget a hozzáférési panelen.

3. Amikor a rendszer felszólítja a szoftver telepítésére, válassza a **Telepítés most**lehetőséget.

4. A böngésző letöltési lapjára lesz irányítva. Válassza a bővítmény **hozzáadását** .

5. Ha a rendszer kéri, válassza az **Engedélyezés** vagy az **Engedélyezés**lehetőséget.

6. A telepítés után indítsa újra a böngészőt.

7. Jelentkezzen be a hozzáférési panelre. Megtudhatja, hogy megnyithatja-e a jelszó-SSO-kompatibilis alkalmazásokat.

Az alábbi hivatkozásokon keresztül közvetlenül is letöltheti a Chrome és a Firefox böngésző-bővítményét:

-   [Chrome hozzáférési panel bővítmény](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox hozzáférési panel bővítmény](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>A portál értesítési adatainak megtekintése

A portálon megjelenő értesítések részleteinek megtekintéséhez kövesse az alábbi lépéseket:

1. Válassza ki az **értesítések** ikont (a harangot) a Azure Portal jobb felső sarkában.

2. Válassza ki a *hibás* állapotot megjelenítő értesítéseket. (Piros "!".)

   > [!NOTE]
   > Nem választhat olyan értesítéseket, amelyek *sikeres* vagy *folyamatban* lévő állapotban vannak.

3. Megnyílik az **értesítés részletei** ablaktábla. A problémával kapcsolatos további információkért olvassa el a következő témakört:.

5. Ha továbbra is segítségre van szüksége, ossza meg az információkat egy támogatási szakemberrel vagy a termék csoporttal. Válassza a másolási **hiba** mező jobb oldalán található **Másolás** ikont az értesítési adatok megosztásra másolásához.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Értesítési adatok küldése támogatási szakembernek segítség kéréséhez

Fontos, hogy az ebben a szakaszban felsorolt *összes* részletet a támogatással ossza meg, így gyorsan segíthet. A rögzítéshez képernyőfelvételt készíthet, vagy kiválaszthatja a **másolási hibát**.

Az alábbi információk ismertetik, hogy az egyes értesítési elemek mit jelentenek és példákat biztosítanak.

### <a name="essential-notification-items"></a>Alapvető értesítési elemek

- **Title**: az értesítés leíró címe.

   Példa: *alkalmazásproxy-beállítások*

- **Leírás**: mi történt a művelet eredményeként.

   Példa: *a megadott belső URL-címet már egy másik alkalmazás használja.*

- **Értesítés azonosítója**: az értesítés egyedi azonosítója.

    Példa: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Ügyfél-kérelem azonosítója**: a böngésző által megadott kérelem-azonosító.

    Példa: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Időbélyeg UTC**: az értesítés időbélyegzője, UTC szerint.

    Példa: *2017-03-23T19:50:43.7583681 z*

- **Belső tranzakció azonosítója**: az a belső azonosító, amelyet a rendszer a hibájának keresésére használ.

    Példa: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: az a felhasználó, aki futtatta a műveletet.

    Példa: *tperkins\@f128.info*

- **Bérlő azonosítója**: annak a bérlőnek az egyedi azonosítója, amely a műveletet futtató felhasználó tagja.

    Példa: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Felhasználói objektum azonosítója**: a műveletet futtató felhasználó egyedi azonosítója.

    Példa: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

- **Megjelenített név**: (üres is lehet) a hiba részletesebb megjelenítendő neve.

    Példa: *alkalmazásproxy-beállítások*

- **Állapot**: az értesítés konkrét állapota.

    Példa: *sikertelen*

- **Objektumazonosító**: (üres is lehet) a művelet futtatására szolgáló objektumazonosító.

   Példa: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Részletek**: a művelet eredményének részletes leírása.

    Példa: a *belső URL<https://bing.com/>-cím érvénytelen, mert már használatban van.*

- **Másolási hiba**: lehetővé teszi, hogy a másolási **hiba** szövegmező jobb oldalán válassza ki a **Másolás ikont** az értesítési adatok másolásához, hogy segítsen a támogatásban.

    Például```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>További lépések
[Egyszeri bejelentkezés biztosítása az alkalmazásokba az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md)
