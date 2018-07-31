---
title: A Microsoft Authenticator telefonos bejelentkezés – Azure Active Directory |} A Microsoft Docs
description: Részletes információkat és telefonnal bejelentkezni a munkahelyi és iskolai és személyes Microsoft-fiókok helyett adja meg a jelszót a részletes útmutatást biztosít.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 3303a0fb1ca4c4a8f6695c2e4338c1b0a0fc9f40
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358632"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>Bejelentkezés telefonnal, nem kell jelszót

A Microsoft Authenticator alkalmazást, nem csak azt használni, hogy a fiókok biztonságos végez kétlépéses ellenőrzést, miután segítségével adja meg a jelszót, de azt is cserélje le a Microsoft-fiókja jelszavát teljes egészében.

Ez a funkció iOS és Android-eszközökön futó Android 6.0-s vagy újabb érhető el.

## <a name="how-it-works"></a>Működés

A legtöbb felhasználó használja a Microsoft Authenticator alkalmazást a kétlépéses ellenőrzéshez a Microsoft-fiókkal való bejelentkezéskor. Írja be a jelszavát, és keresse meg az alkalmazást, vagy hagyja jóvá az értesítést vagy egy ellenőrző kódot lekéréséhez. A telefonnal bejelentkezni hagyja ki a jelszót, és hajtsa végre az identitás-ellenőrző a telefonján. Telefonos bejelentkezés is egy kétlépéses ellenőrzést, továbbra is szeretné egy dolog, tudja, és a egy dolog van megadásával igazolhatja a személyazonosságát. A telefon továbbra is a dolog van, de a telefon PIN-kódot vagy biometrikus kulcs most már ismeri a dolog.

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>Jelentkezzen be munkahelyi vagy iskolai fiókja phone bekapcsolása

Ha a szervezete Azure Active Directory használja az identitáskezeléshez és hitelesítéshez, használhatja telefonos bejelentkezés bekapcsolása után, a Microsoft Authenticator alkalmazásban. 

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>Jelentkezzen be a fiók phone bekapcsolása

- **Ha ez az ikon ![telefonos bejelentkezés ikonját](./media/microsoft-authenticator-app-phone-signin-faq/icon.png) a telefonján.** Már beállította telefonnal bejelentkezni. Előfordulhat, hogy megkérdezi, hogy a fiók hozzáadása a leküldéses értesítések, így értesítést kaphat az alkalmazás hitelesítési kérések kapcsolatos frissítését.

- **Ha már használja a Microsoft Authenticator alkalmazást a kétlépéses ellenőrzéshez.** Nyissa meg a **fiókok** az alkalmazást, válassza ki a legördülő listájának nyíl gombját, a munkahelyi vagy iskolai fiók, és válassza ki a képernyő **telefonos bejelentkezés engedélyezése**.

- **Ha a Microsoft Authenticator alkalmazást a személyes fiók, de nem a munkahelyi vagy iskolai fiókjával.** A szervezet kétlépéses ellenőrzési utasítások szerint, és kapcsolja be a telefonos bejelentkezés. További információkért lásd: [a Microsoft Authenticator alkalmazás használatának első lépései](microsoft-authenticator-app-how-to.md)

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>Jelentkezzen be munkahelyi vagy iskolai fiókját a telefonos bejelentkezés használatához

1.  Jelentkezzen be munkahelyi vagy iskolai fiókjával, a szokásos módon.

    Után írja be a felhasználónevét, megjelenik egy oldal a 2-jegyű szám rákérdez arra, hogy a bejelentkezés a Microsoft Authenticator alkalmazásban jóvá. Ha nem szeretné használni a bejelentkezési módszer, választhatja **inkább jelszó**, és jelentkezzen be a jelszót.

    ![Bejelentkezési oldal](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)
 
2. A Microsoft Authentication alkalmazásban rákérdez arra, hogy értesítést kapjon **hagyhatja jóvá a bejelentkezést**. Válasszon **jóváhagyás** Ha ismeri fel a bejelentkezési kísérlet. Ellenkező esetben válasszon **Megtagadás**.

3. Koppintson az azonos számú akkor jelenik meg a **jóváhagyja a bejelentkezést** képernyő. A telefon PIN-kód vagy a biometrikus kulcsot használja a hitelesítéshez.
 
    ![Hagyja jóvá a bejelentkezési oldal](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>Jelentkezzen be a személyes fiókjához phone bekapcsolása

Bekapcsolhatja a telefonos bejelentkezés személyes Microsoft-fiókja, például az Outlook.com, Xbox vagy Skype bejelentkezéshez használt fióknak.

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>Jelentkezzen be a személyes fiókjához phone bekapcsolása

- **Ha nem használja a Microsoft Authenticator alkalmazást.** Töltse le és telepítse a Microsoft Authenticator alkalmazást, és adja hozzá a személyes Microsoft-fiókjával jelentkezzen be a fiók a **fiókok hozzáadása** képernyőjén a Microsoft Authenticator alkalmazást. További információkért lásd: [Ismerkedés a Microsoft Authenticator alkalmazás](microsoft-authenticator-app-how-to.md).
Új fiókokat is automatikusan bekapcsolva, így azonnal megkezdheti a telefonos bejelentkezés.

- **Ha már használja a Microsoft Authenticator alkalmazást a kétlépéses ellenőrzéshez.** Nyissa meg a **fiókok** az alkalmazást, válassza ki a legördülő listájának nyíl gombját, a munkahelyi vagy iskolai fiók, és válassza ki a képernyő **telefonos bejelentkezés engedélyezése**.

>[!NOTE]
>A fiók védelme érdekében, hogy az alkalmazás PIN-kódot vagy biometrikus zárolást az eszközön van szükség. A telefon zárolása feloldva, ha az alkalmazás állíthat be egy biztonsági zárolása előtt ne tudják bekapcsolni a telefonos bejelentkezés szükséges.

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>A személyes fiókjával jelentkezzen be a telefonos bejelentkezés használatához

1.  Nyissa meg a személyes Microsoft fiók bejelentkezési oldalát, és a jelszó beírása helyett válassza a **inkább alkalmazás** hivatkozásra.

    A Microsoft értesítést küld a telefonjára. 

2.  Hagyja jóvá az értesítést.

## <a name="phone-sign-in-faq"></a>Telefonos bejelentkezés – gyakori kérdések

|**Kérdés**|**Megoldás**|
|--------------|-------------|
|**Hogyan biztonságosabb, mint a jelszó beírása telefonom történő bejelentkezés?**|Ma a legtöbb ember bejelentkezés webhelyek vagy alkalmazások felhasználónév és jelszó használatával. Sajnos jelszavakat is lehet elvesztése, ellopása, vagy a támadók által kitalálni.<br><br>Miután beállította a Microsoft Authenticator alkalmazást, a telefonján, hogy feloldja fiókját, a telefon PIN-kódot vagy biometrikus zárolás által védett létrehoz egy kulcsot. Ez a kulcs szolgál majd igazolja az identitását aláírás közben.<br><br>**Fontos**<br>Az adatok csak használatos helyileg a kulcsának védelmére. Ez soha nem küldött, vagy a felhőben tárolt.|
|**Vált, ez a kétlépéses ellenőrzés? Kell azt a kikapcsolása után?**| Bontsa ki a telefonos bejelentkezés hatókörének dolgozunk, de egyelőre továbbra is vannak, amelyek nem támogatják a Microsoft-ökoszisztéma helyén. Ezen a helyen a kétlépéses ellenőrzés még mindig használatban van a biztonságos bejelentkezéshez. Éppen ezért nem, akkor ne kétlépéses ellenőrzés kikapcsolása a fiókjához.|
|**Ha a kétlépéses ellenőrzés bekapcsolva a fiókomhoz megtartásához van két értesítések jóváhagyása?**| Nem. Bejelentkezés a Microsoft-fiókot a telefonon is számít a kétlépéses ellenőrzés, így nem második jóváhagyás szükséges.|
|**Mi történik, ha megszakad a telefonra, vagy még nincs velem? Hogyan férhetek hozzá a-fiókomat?**| Mindig kiválaszthatja a használatát, ehelyett hivatkozás egy jelszót a bejelentkezési lapon váltson vissza az Ön jelszavát. Azonban ha a kétlépéses ellenőrzés továbbra is szüksége egy második módszerrel igazolja személyazonosságát.<br><br>**Fontos**<br>Most már javasoljuk, hogy több, naprakész, a fiókjához társított ellenőrzési módszert.<br><br>Az ellenőrzési módszerek sorát a személyes fiókok segítségével kezelheti a [biztonsági beállítások](https://account.live.com/proofs/manage) lap. Munkahelyi vagy iskolai fiókok esetében nyissa meg a szervezet [további biztonsági ellenőrzés](https://aka.ms/MFASetup) lap vagy az **a fiókja biztonságának megőrzéséhez** lapon, ha a rendszergazda bekapcsolta az biztonsági adatait. További információ a biztonsági adatok: [a biztonsági adatok kezelése](security-info-manage-settings.md).<br><br>Ha Ön tudja felügyelni az ellenőrzési módszerek sorát, meg kell forduljon a rendszergazdához.|
|**Hogyan leállítása ezzel a funkcióval és térjen vissza a jelszó használatával?**|Személyes fiókok esetében válassza a **inkább jelszó** hivatkozásra a bejelentkezés során. A legutóbbi választott megjegyezze őket, és alapértelmezés szerint el, amikor legközelebb bejelentkezik. Ha szeretne lépjen vissza a telefonos bejelentkezés, válassza ki a **inkább alkalmazás** hivatkozásra a bejelentkezés során.<br><br>A munkahelyi vagy iskolai fiókkal, akkor el kell távolítania a nyissa meg a Microsoft Authenticator alkalmazás kiválasztása a **fiókok szerkesztése** menüt, és ezután a fiók törlése.|
|**Miért nem használható a telefonos bejelentkezés egynél több munkahelyi vagy iskolai fiókkal?**| A telefon egyetlen munkahelyi vagy iskolai fiók regisztrálva kell lenniük. Kapcsolja be a telefonos bejelentkezés egy másik munkahelyi vagy iskolai fiókot szeretne, ha a régi fiókot keresztül először kell regisztrációját a **beállítások** lapot.|
|**Is tudok bejelentkezni a számítógépre a telefon használatával?**| A számítógépen javasoljuk, hogy a használatával Windows Hello a Windows 10-es aláírási. Windows Hello lehetővé teszi az arcok, ujjlenyomattal vagy a PIN-kód segítségével jelentkezzen be.|
|**Használható telefonos bejelentkezés a saját Windows Phone?**| Nem. Ez a funkció nem támogatja a Microsoft Authenticator alkalmazást a Windows Phone.|

## <a name="next-steps"></a>További lépések

-   Ha az alkalmazással kapcsolatos további általános kérdése van, tekintse meg [a Microsoft Authenticator – gyakori kérdések](microsoft-authenticator-app-faq.md)

-   Ha azt szeretné, hogy további információ a kétlépéses ellenőrzést, [a kétlépéses ellenőrzéshez a fiók beállítása](multi-factor-authentication-end-user-first-time.md)

-   Ha azt szeretné, hogy további információ a biztonsági adatait, tekintse meg [a biztonsági adatok kezelése](security-info-manage-settings.md)