---
title: Állítsa be a kétlépéses ellenőrzés – Azure Active Directory |} A Microsoft Docs
description: A vállalat Azure multi-factor Authentication állítja be, amikor a program felkéri iratkozzon fel a kétlépéses ellenőrzéshez. Ismerje meg, hogyan állítsa be.
services: active-directory
keywords: az azure directory, a felhőben, az active directory az oktatóanyag az active directory használata
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.openlocfilehash: d53221a8bed6995231385cae21a8994fce132a30
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175910"
---
# <a name="set-up-my-account-for-two-step-verification"></a>A kétlépéses ellenőrzéshez a fiók beállítása
A kétlépéses ellenőrzés egy kiegészítő biztonsági lépés, amely megnehezíti mások feltörhessék fiókja védelme érdekében. Ha ez a cikk olvasása közben, valószínűleg kapott e-mailt a munkahelyi vagy iskolai rendszergazda multi-factor Authentication hitelesítéssel kapcsolatban. Vagy esetleg próbált meg bejelentkezni, és itt van egy üzenet, hogy további biztonsági ellenőrzés beállítása. Ebben az esetben, ha **nem tud bejelentkezni, amíg el nem végezte az automatikus regisztrációs folyamatot**.

Ez a cikk segít beállítani a **munkahelyi vagy iskolai fiók**. Ha a saját, személyes Microsoft-fiók kétlépéses ellenőrzéshez engedélyezni szeretné, tekintse meg [tudnivalók a kétlépéses ellenőrzés](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Fiók beállítása

A cég informatikai támogatási kell kezdeni használni a kétlépéses ellenőrzést, ha megjelenik egy képernyő, amely szerint **a rendszergazda megköveteli, hogy állítsa be ezt a fiókot a további biztonsági ellenőrzés**:

![Beállítás](./media/multi-factor-authentication-end-user-first-time/first.png)

Első lépésként válassza ki a **hozza létre most.**

Ha nem látja a egy ilyen képernyőt, amikor bejelentkezik, kövesse a megjelenő utasításokat [felügyelheti a kétlépéses ellenőrzés](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) találja a beállítások lapra, ahol kezelheti az ellenőrzési lehetőség.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Annak eldöntése, hogyan szeretné ellenőrizni a bejelentkezések

A regisztrációs folyamat az első kérdést a kívánt kapcsolatba lépni velünk. Tekintse meg a beállításokat a táblázatban, és nyissa meg a telepítési lépéseket az egyes módszerek hivatkozások segítségével.

| Kapcsolattartási mód | Leírás |
| --- | --- |
| [Mobilalkalmazás](#use-a-mobile-app-as-the-contact-method) |- **Értesítések küldése az ellenőrzéshez.** Ez a beállítás leküldéses értesítést az authenticator alkalmazás a okostelefonján vagy táblagépén lévő. Tekintse meg az értesítést, és ha az megbízható, válassza az **hitelesítés** az alkalmazásban. A munkahelyi vagy iskolai szükség lehet PIN-kód megadását, mielőtt Önt a hitelesítéshez.<br>- **Ellenőrzőkód használata.** Ebben a módban az authenticator alkalmazás egy ellenőrző kódot, amely frissíti a 30 másodpercenként hoz létre. A bejelentkezési felületen írja be az aktuális ellenőrzőkódot.<br>A Microsoft Authenticator alkalmazás [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) és [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071) rendszereken érhető el. |
| [Mobiltelefon hívás vagy szöveges üzenet](#use-your-mobile-phone-as-the-contact-method) |- **Telefonhívás** helyezi el az Ön telefonszám automatikus hanghívást indít. A hívás, és nyomja le a hitelesítéshez lenyomja a telefon billentyűzetén a #.<br>- **Szöveges üzenet** egy ellenőrző kódot tartalmazó szöveges üzenetet ér véget. A rendszer a szövegben, a következő válaszoljon az üzenetre, vagy adja meg a bejelentkezési felületen megadott ellenőrző kód. |
| [Irodai telefon hívása](#use-your-office-phone-as-the-contact-method) |Automatikus hanghívást indít az Ön telefonszám helyezi. A hívás és a hitelesítéshez lenyomja a telefon billentyűzetén a # gombot. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>A mobilalkalmazás használata kapcsolattartási módszerként
Ez a módszer használatához, hogy egy hitelesítő alkalmazást telepítenie a telefonját vagy táblagépét. A jelen cikkben ismertetett lépések a Microsoft Authenticator alkalmazást, amely alapuló [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072), és [iOS](https://go.microsoft.com/fwlink/?Linkid=825073).

>[!NOTE]
>Nem kell használni a Microsoft Authenticator alkalmazást. Ha már használja egy másik hitelesítő alkalmazás, továbbra is használhatja.

1. Válassza ki **mobilalkalmazás** a legördülő listából.
2. Ezek közül bármelyikre **értesítések küldése az ellenőrzéshez** vagy **Ellenőrzőkód használata**, majd **beállítása**.

   ![További biztonsági ellenőrzési képernyő](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Telefonján vagy táblagépén, nyissa meg az alkalmazást, és válassza ki **+** egy fiók hozzáadásához. (Az Android-eszközökön, válassza a három pontra, majd **fiók hozzáadása**.)
4. Adja meg, hogy szeretné-e munkahelyi vagy iskolai fiók hozzáadása. Megnyílik a QR-kódolvasót a telefonján. Ha a kamera nem működik megfelelően, válassza ki manuálisan adja meg a vállalati adatok. További információkért lásd: [fiók manuális hozzáadása](#add-an-account-manually).  
5. A QR-kód kép megjelent a képernyőt a mobilalkalmazás konfigurálásához beolvasása.  Válassza ki **kész** gombra kattintva zárja be a QR-kód képernyőn.  

   ![QR-kód képernyőn](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Az aktiválás a telefonon befejezése után válassza **kapcsolatfelvételi**.  Ebben a lépésben vagy egy értesítést, vagy egy ellenőrzőkódot küld a telefonjára. Válassza ki **ellenőrzése**.  
7. Ha a cég egy PIN-kódot a bejelentkezés-ellenőrző jóváhagyása, adja meg.

   ![A PIN-kód megadására szolgáló mező](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. PIN-kód bevitelének befejezése után jelölje ki a **Bezárás**. Ezen a ponton az ellenőrzés sikeres legyen.
9. Azt javasoljuk, hogy meg mobiltelefonszámát arra az esetre, ha megszakadna a mobilalkalmazással. Adja meg a legördülő listából válassza ki az országot, és adja meg mobiltelefonszámát az ország neve melletti jelölőnégyzetbe. Kattintson a **Tovább** gombra.
10. Ezen a ponton kéri alkalmazásjelszókat a böngészőn kívüli alkalmazások, például az Outlook 2010 vagy korábbi, vagy a natív e-mail alkalmazás Apple-eszközök beállításához. Ennek oka az, bizonyos alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **kész** és a további lépéseit kihagyhatja.
11. Ha használja ezeket az alkalmazásokat, a Másolás az alkalmazásjelszót megadott, és illessze be az alkalmazás a szokásos jelszava helyett. Ugyanazt a jelszót több alkalmazáshoz is használhat. További információ [segítség az alkalmazásjelszókhoz].
12. Kattintson a **Done** (Kész) gombra.

### <a name="add-an-account-manually"></a>Fiók manuális hozzáadása
Ha azt szeretné, hogy hozzáadjon egy fiókot a mobilalkalmazásba manuálisan, helyett használja az QR-olvasót, kövesse az alábbi lépéseket.

1. Válassza ki a **manuálisan adja meg a fiók** gombra.  
2. Adja meg a kódot és a vonalkód meg, amely ugyanazon az oldalon lévő URL-CÍMÉT. Ezek az adatok kerül a **kód** és **URL-cím** mezőkbe a mobilalkalmazásban.

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Ha az aktiválás befejeződött, válassza ki a **kapcsolatfelvételi**. Ebben a lépésben vagy egy értesítést, vagy egy ellenőrzőkódot küld a telefonjára. Válassza ki **ellenőrzése**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>A mobiltelefon használata kapcsolattartási módszerként
1. Válassza ki **hitelesítéshez használt telefon** a legördülő listából.  

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. A legördülő listából válassza ki az országot, és adja meg mobiltelefonszámát.
3. Válassza inkább a mobiltelefonjára – szöveges vagy telefonhíváson alapuló használandó módszert.
4. Válassza ki **kapcsolatfelvételi** ellenőrzése a telefonszámát. A kiválasztott módtól függően a Microsoft egy szöveges üzenet elküldéséhez, vagy hívja meg. Kövesse a képernyőn megjelenő utasításokat, majd válassza a **ellenőrizze**.
5. Ezen a ponton kéri alkalmazásjelszókat a böngészőn kívüli alkalmazások, például az Outlook 2010 vagy korábbi, vagy a natív e-mail alkalmazás Apple-eszközök beállításához. Ennek oka az, bizonyos alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **kész** és a további lépéseit kihagyhatja.
6. Ha használja ezeket az alkalmazásokat, a Másolás az alkalmazásjelszót megadott, és illessze be az alkalmazás a szokásos jelszava helyett. Ugyanazt a jelszót több alkalmazáshoz is használhat. További információ [segítség az alkalmazásjelszókhoz].
7. Kattintson a **Done** (Kész) gombra.

## <a name="use-your-office-phone-as-the-contact-method"></a>A kapcsolattartási módszerként az irodai telefonját használja
1. Válassza ki **irodai telefon** a legördülő listából  

    ![Beállítás](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A telefonszám mezőben automatikusan kitölti a kapcsolattartási adatokat. Ha a szám nem megfelelő vagy hiányzó, kérje a rendszergazda módosíthatja.
3. Válassza ki **kapcsolatfelvételi** telefon ellenőrzése számát, valamint hogy hívni fogja a számot. Kövesse a képernyőn megjelenő utasításokat, majd válassza a **ellenőrizze**.
4. Ezen a ponton kéri alkalmazásjelszókat a böngészőn kívüli alkalmazások, például az Outlook 2010 vagy korábbi, vagy a natív e-mail alkalmazás Apple-eszközök beállításához. Ennek oka az, bizonyos alkalmazások nem támogatják a kétlépéses ellenőrzést. Ha nem használja ezeket az alkalmazásokat, kattintson a **kész** és a további lépéseit kihagyhatja.
5. Ha használja ezeket az alkalmazásokat, a Másolás az alkalmazásjelszót megadott, és illessze be az alkalmazás a szokásos jelszava helyett. Ugyanazt a jelszót több alkalmazáshoz is használhat. További információ: [Mik azok az Alkalmazásjelszavak](multi-factor-authentication-end-user-app-passwords.md).
6. Kattintson a **Done** (Kész) gombra.

## <a name="next-steps"></a>További lépések
* Az előnyben részesített beállításainak módosítása és [a kétlépéses ellenőrzés beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)
* Állítsa be a [alkalmazásjelszók](multi-factor-authentication-end-user-app-passwords.md) natív eszköz alkalmazások, amelyek nem támogatják a kétlépéses ellenőrzést.
* Tekintse meg a [Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md) gyors, biztonságos hitelesítést akkor is, ha a cella szolgáltatás nem rendelkezik.
