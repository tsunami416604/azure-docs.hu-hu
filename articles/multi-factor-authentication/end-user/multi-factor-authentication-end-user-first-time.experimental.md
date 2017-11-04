---
title: "Kétlépéses ellenőrzést, a munkahelyi vagy iskolai fiók beállítása |} Microsoft Docs"
description: "Amikor a vállalat konfigurálja az Azure multi-factor Authentication, kérni fogja a kétlépéses ellenőrzéshez regisztrálni. Megtudhatja, hogyan állítsa be. "
services: multi-factor-authentication
keywords: "az azure directory, a felhőben, az active directory-oktatóanyag az active directory használatáról"
documentationcenter: 
author: barlanmsft
manager: angrobe
editor: pblachar
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: a345cacfe53f37f851eebb76b5b8997cb9c0b54b
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="set-up-my-account-for-two-step-verification"></a>A kétlépéses ellenőrzéshez a fiók beállítása
Kétlépéses ellenőrzés egy további biztonsági lépés, amely segít megvédeni a fiókját úgy, hogy elkészíti a nehezebben más feltörhessék. Ha a cikk elolvasása valószínűleg kapott e-mailt a munkahelyi vagy iskolai rendszergazda többtényezős hitelesítéssel kapcsolatos. Vagy próbált bejelentkezni, és lehet, hogy a további biztonsági ellenőrzés beállítása kérő üzenetet kapott. Ebben az esetben, ha **nem tud bejelentkezni, az automatikus igénylés folyamat befejezése után**.

Ez a cikk segít beállítani a **munkahelyi vagy iskolai fiók**. Ha a kétlépéses ellenőrzést a saját, személyes Microsoft-fiók számára engedélyezni kívánja, tekintse meg [tudnivalók a kétlépéses ellenőrzést](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>A fiók beállítása

Ha a vállalat támogatási meg kell indíthatja a kétlépéses ellenőrzést, a képernyő bármelyiket **a rendszergazda előírta, állítsa be az ehhez a fiókhoz további biztonsági ellenőrzés** jelenik meg:

![Beállítás](./media/multi-factor-authentication-end-user-first-time/first.png)

Első lépésként válassza ki a **, állítsa be most.**

Ha egy ehhez hasonló képernyőt bejelentkezéskor nem látja, kövesse az utasításokat a [kezelheti a kétlépéses ellenőrzés beállításait](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) található, ahol kezelheti az ellenőrzési lehetőségek beállításait tartalmazó oldalt. 

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Annak eldöntése, hogyan szeretné ellenőrizni a bejelentkezések

A regisztrációs folyamat az első kérdést, hogyan szeretné kapcsolatba lépni velünk. Tekintse meg a beállításokat a táblázatban, és navigáljon a beállítási lépéseket az egyes módszerek hivatkozások segítségével.

| Kapcsolatfelvétel módja | Leírás |
| --- | --- |
| [Mobilalkalmazás](#use-a-mobile-app-as-the-contact-method) |- **Az ellenőrzési értesítések.** Ez a beállítás leküldéses értesítést küld a hitelesítőalkalmazása a okostelefonján vagy táblagépén lévő. Az értesítés és, ha az megbízható, választhatja ki, **hitelesítés** az alkalmazásban. A munkahelyi vagy iskolai szükség lehet PIN-kód megadása előtt a hitelesítést.<br>- **Ellenőrzőkód használata.** Ebben a módban a állítanak elő egy megerősítési kódot, amely 30 másodpercenként frissíti. Adja meg a legfrissebb a bejelentkezési felületen.<br>A Microsoft Authenticator alkalmazás érhető el [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| [Mobiltelefon hívása vagy szöveg](#use-your-mobile-phone-as-the-contact-method) |- **Telefonhívás** helyezi el a telefonszámot megadnia egy automatizált hang hívása. A hívás, és nyomja meg a # gombot a telefon billentyűzetén.<br>- **Szöveges üzenet** karakterlánccal végződik-e egy megerősítési kódot tartalmazó szöveges üzenetet. A szöveg megjelenő következő válaszoljon az üzenetre, vagy adja meg a bejelentkezési felületén megadott ellenőrzőkódot. |
| [Irodai telefon hívása](#use-your-office-phone-as-the-contact-method) |A telefonszámot megadnia egy automatizált hang hívása helyezi. A hívás és nyomnia a # gombot a telefon billentyűzetén hitelesítéséhez. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>A mobilalkalmazás használata kapcsolattartási módszerként
Ezzel a módszerrel kell a telefonját vagy táblagépét egy hitelesítő alkalmazást telepíteni. A cikkben leírt lépéseket a Microsoft Authenticator alkalmazást, amely alapuló [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Válassza ki **mobilalkalmazás** a legördülő listából.
2. Válassza **értesítéseket az ellenőrzéshez** vagy **Ellenőrzőkód használata**, majd jelölje be **beállítása**.

   ![További biztonsági ellenőrzési képernyő](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. A telefonját vagy táblagépét, nyissa meg az alkalmazást, és válassza  **+**  fiók hozzáadásához. (Az Android-eszközökön, válassza a három pontra, majd **fiók hozzáadása**.)
4. Adja meg, hogy szeretné-e munkahelyi vagy iskolai fiók beállítása. Megnyitja a QR-kód képolvasó a telefonján. Ha a kamera nem működik megfelelően, válassza a vállalat adatainak manuális megadása. További információkért lásd: [fiók manuális hozzáadása](#add-an-account-manually).  
5. A QR-kód kép a mobilalkalmazás konfigurálásához a képernyőn megjelenő beolvasása.  Válassza ki **végzett** a QR-kód képernyő bezárása.  

   ![A QR-kód képernyő](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Aktiválási befejezése után a telefonján, válassza ki a **forduljon me**.  Ez a lépés vagy egy értesítés, vagy egy megerősítési kódot küld a telefonjára. Válassza ki **ellenőrizze**.  
7. Ha a vállalat a PIN-kód bejelentkezés-ellenőrző jóváhagyására van szüksége, adja meg.

   ![Be a PIN-kód megadása](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. PIN-kód bevitelének befejezése után válassza ki a **Bezárás**. Ezen a ponton az ellenőrzés sikeres legyen.
9. Azt javasoljuk, hogy a mobiltelefon számát adja meg, abban az esetben, ha elveszítené hozzáférését a mobilalkalmazáshoz. Válassza ki a legördülő listából válassza ki az országot, és írja be mobiltelefonszámát ország neve melletti jelölőnégyzetbe. Válassza ki **következő**.
10. Ezen a ponton kéri alkalmazásjelszókat a böngészőn kívüli alkalmazások, például az Outlook 2010 vagy korábbi, vagy a natív e-mail alkalmazás Apple-eszközök beállítása. Ez a művelet az oka, hogy egyes alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **végzett** és hagyja ki az alábbi lépéseket.
11. Ha használja ezeket az alkalmazásokat, a Másolás az alkalmazásjelszót megadott, majd illessze be a rendszeres jelszó helyett az alkalmazásra. Több alkalmazás használhatja ugyanazt a jelszót. További információk [Súgó az alkalmazásjelszavak].
12. Kattintson a **Done** (Kész) gombra.

### <a name="add-an-account-manually"></a>Egy fiók manuális hozzáadása
Ha azt szeretné, hogy hozzáadjon egy fiókot a mobilalkalmazásban manuálisan, a QR-olvasó helyett kövesse az alábbi lépéseket.

1. Válassza ki a **manuálisan adja meg a fiók** gombra.  
2. Adja meg a kódot, és bemutatja, hogy a vonalkódot ugyanazon az oldalon lévő URL-CÍMÉT. Ezeket az adatokat kerül a **kód** és **URL-cím** mezőket a mobilalkalmazásban.

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Amikor befejezte az aktiválást, válassza ki a **forduljon me**. Ez a lépés vagy egy értesítés, vagy egy megerősítési kódot küld a telefonjára. Válassza ki **ellenőrizze**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>A mobiltelefon használata kapcsolattartási módszerként
1. Válassza ki **hitelesítéshez megadott telefonját** a legördülő listából.  

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Válassza ki az országot a legördülő listából, és adja meg a mobiltelefonszámát.
3. Válassza ki a módszert szeretné használni a mobiltelefon - szöveges vagy telefonhíváson.
4. Válassza ki **forduljon me** ellenőrizni a telefonszámát. A kiválasztott módtól függően azt szöveget küld, vagy hívja meg. Kövesse a képernyőn megjelenő utasításokat, majd válasszon **ellenőrizze**.
5. Ezen a ponton kéri alkalmazásjelszókat a böngészőn kívüli alkalmazások, például az Outlook 2010 vagy korábbi, vagy a natív e-mail alkalmazás Apple-eszközök beállítása. Ez a művelet az oka, hogy egyes alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **végzett** , és hagyja ki a további lépéseket.
6. Ha használja ezeket az alkalmazásokat, a Másolás az alkalmazásjelszót megadott, majd illessze be a rendszeres jelszó helyett az alkalmazásra. Több alkalmazás használhatja ugyanazt a jelszót. További információk [Súgó az alkalmazásjelszavak].
7. Kattintson a **Done** (Kész) gombra.

## <a name="use-your-office-phone-as-the-contact-method"></a>Az irodai telefon használata kapcsolattartási módszerként
1. Válassza ki **irodai telefon** a legördülő  

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A telefonszám mezőben automatikusan kitölti a vállalat kapcsolattartási adatait. Ha a szám nem megfelelő vagy hiányzó, kérje meg a rendszergazdát, hogy a módosításokat.
3. Válassza ki **forduljon me** ellenőrzése a telefon számát, valamint azt fel fogja hívni a számot. Kövesse a képernyőn megjelenő utasításokat, majd válasszon **ellenőrizze**.
4. Ezen a ponton kéri alkalmazásjelszókat a böngészőn kívüli alkalmazások, például az Outlook 2010 vagy korábbi, vagy a natív e-mail alkalmazás Apple-eszközök beállítása. Ez a művelet az oka, hogy egyes alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **végzett** , és hagyja ki a további lépéseket.
5. Ha használja ezeket az alkalmazásokat, a Másolás az alkalmazásjelszót megadott, majd illessze be a rendszeres jelszó helyett az alkalmazásra. Több alkalmazás használhatja ugyanazt a jelszót. További információk: [Mik az Alkalmazásjelszók](multi-factor-authentication-end-user-app-passwords.md).
6. Kattintson a **Done** (Kész) gombra.

## <a name="next-steps"></a>Következő lépések
* Az előnyben részesített beállításainak módosítására és [a kétlépéses ellenőrzést beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)
* Állítson be [alkalmazásjelszók](multi-factor-authentication-end-user-app-passwords.md) natív eszköz alkalmazások, amelyek nem támogatják a kétlépéses ellenőrzést.
* Tekintse meg a [Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) gyors, biztonságos hitelesítés akkor is, ha a cella szolgáltatás nem rendelkezik.

