---
title: Jelentkezzen be a fiókok a Microsoft Authenticator alkalmazással – Azure Active Directory |} A Microsoft Docs
description: A Microsoft Authenticator alkalmazás használatával jelentkezzen be a munkahelyi vagy iskolai fiókkal, vagy a személyes Microsoft és nem Microsoft-fiókok, kétfaktoros hitelesítési vagy telefonos bejelentkezés használatával.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80fd458356cf5b375e0b3d922d9bdca3f3a30503
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961669"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Jelentkezzen be a fiókok a Microsoft Authenticator alkalmazással
A Microsoft Authenticator alkalmazás segítségével jelentkezzen be a fiókok kétfaktoros ellenőrzési használatakor. A kétfaktoros hitelesítési segítségével a fiókok biztonságosabb, hozzáférhet a különösen bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejtett is, amelyeket elloptak vagy feltört, kéttényezős hitelesítés végrehajtása egy további biztonsági lépés, amely megnehezíti mások feltörhessék fiókja védelme érdekében.

Többféle módon, beleértve a Microsoft Authenticator alkalmazást is használhatja:

- Kérdés egy második ellenőrzési módszert biztosítása után jelentkezik be a felhasználónevét és jelszavát.

- Jelentkezzen be lehetővé anélkül, hogy a jelszó, a felhasználónév és a mobil eszköz használata az ujjlenyomatot, arc vagy PIN-kód.

 >[!Important]
 >A telefonos bejelentkezési módszer csak munkahelyi vagy iskolai és személyes Microsoft-fiókok működik. A nem Microsoft-fiókok is a standard szintű kétfaktoros hitelesítési eljárás használnia kell.

## <a name="prerequisites"></a>Előfeltételek
A Microsoft Authenticator alkalmazás használata előtt a következőket kell tennie:

 1. Töltse le és telepítse a Microsoft Authenticator alkalmazást. Ha még nem tette ez, [töltse le és telepítse az alkalmazást](user-help-auth-app-download-install.md).

 2. Adja hozzá a munkahelyi vagy iskolai, a személyes és a külső fiókok a Microsoft Authenticator alkalmazást. A részletes lépéseiért lásd: [adja hozzá munkahelyi vagy iskolai fiókját](user-help-auth-app-add-work-school-account.md), [adja hozzá a személyes fiókokat](user-help-auth-app-add-personal-ms-account.md), és [hozzáadása a nem Microsoft-fiókok](user-help-auth-app-add-non-ms-account.md).

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>Kapcsolja be, és a telefonos bejelentkezés a munkahelyi vagy iskolai fiók használata
Telefonos bejelentkezés olyan kétlépéses ellenőrzési. Továbbra is ellenőriznie kell a személyazonosságát azáltal, hogy egy dolog, tudja, és a egy dolog van, de a telefonos bejelentkezés lehetővé teszi, hogy hagyja ki a fiók jelszavának megadása és az összes az identitás-ellenőrzést hajt végre a mobil eszközére.

Ahhoz, hogy a telefonos bejelentkezés, be kell kapcsolnia a kétfaktoros ellenőrzése. Egy olyan fiók kétfaktoros ellenőrzés kapcsolatos további információkért lásd: [adja hozzá munkahelyi vagy iskolai fiókját](user-help-auth-app-add-work-school-account.md) és [adja hozzá a személyes fiókokat](user-help-auth-app-add-personal-ms-account.md).

Telefonos bejelentkezés csak akkor használható iOS és Android-eszközökön futó Android 6.0-s vagy újabb.

### <a name="turn-on-phone-sign-in"></a>Kapcsolja be a telefonos bejelentkezés 

- Nyissa meg a Microsoft Authenticator alkalmazást, nyissa meg a munkahelyi vagy iskolai fiókjával, és kapcsolja be a telefonos bejelentkezés:

    - **Ha ez az ikon látható ![ikon látható, hogy beállítottuk](media/user-help-auth-app-sign-in/icon.png).** Ha ez az ikon jelenik meg mellett a munkahelyi vagy iskolai fiók neve, az azt jelenti, hogy már beállította telefonnal bejelentkezni a fiók. Adja hozzá a fiókjához, leküldéses értesítéseket, hogy értesítést kaphat az alkalmazás hitelesítési kérések kapcsolatos kérheti.

    - **Ha már használja az alkalmazást a kétfaktoros ellenőrzéshez.** Ha Ön már használja az alkalmazást és a kétfaktoros ellenőrzési, is válassza ki a fiók neve melletti legördülő nyílra, és válassza ki **telefonos bejelentkezés engedélyezése**.
    
    - **Ha nem találja a munkahelyi vagy iskolai fiókjával.** Ha nem találja a munkahelyi vagy iskolai fiókját a **fiókok** képernyőn az alkalmazás azt jelenti, hogy még nem vett, az alkalmazás még. A lépéseit követve adja hozzá a munkahelyi vagy iskolai fiókjával az [adja hozzá munkahelyi vagy iskolai fiókját](user-help-auth-app-add-work-school-account.md) cikk.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Bejelentkezés telefonnal bejelentkezni a fiókjába
Ha bekapcsolja a telefonos bejelentkezés, bejelentkezhet csak a Microsoft Authenticator alkalmazás használatával.

1. Jelentkezzen be munkahelyi vagy iskolai fiókjával.

    Írja be a felhasználónevét, miután egy **jóváhagyja a bejelentkezést** képernyő jelenik meg, egy kétjegyű számmal jeleníti meg, és rákérdez arra, hogy jelentkezzen be a Microsoft Authenticator alkalmazás használatával. Ha nem szeretné használni a bejelentkezési módszer, választhatja **inkább jelszó**, és jelentkezzen be a jelszót.

    ![Jelentkezzen be a számítógépre jóváhagyása](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Nyissa meg az értesítést, vagy a Microsoft Authenticator alkalmazást az eszközön, és koppintson a száma, amely megfelel látható számra a számítógép **hagyhatja jóvá a bejelentkezést** képernyő.

    ![Jelentkezzen be az eszközön jóváhagyása](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Válasszon **jóváhagyás** Ha ismeri fel a bejelentkezési kísérlet. Ellenkező esetben válasszon **Megtagadás**.

4. A telefon PIN-kód vagy a biometrikus kulcsot használja a hitelesítéshez.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>Kapcsolja be, és a személyes Microsoft-fiókok a telefonos bejelentkezés használata
Bekapcsolhatja a telefonos bejelentkezés személyes Microsoft-fiókja, például az Outlook.com, Xbox vagy Skype bejelentkezéshez használt fióknak.

>[!NOTE]
>Fiókja védelme érdekében a Microsoft Authenticator alkalmazást szükséges, PIN-kódot vagy biometrikus zárolást az eszközön. A telefon zárolása feloldva, ha az alkalmazás állíthat be egy biztonsági zárolása előtt ne tudják bekapcsolni a telefonos bejelentkezés szükséges.

### <a name="turn-on-phone-sign-in"></a>Kapcsolja be a telefonos bejelentkezés 

- Nyissa meg a Microsoft Authenticator alkalmazást, nyissa meg a személyes Microsoft-fiókjával, és kapcsolja be a telefonos bejelentkezés:

    - **Ha ez az ikon látható ![ikon látható, hogy beállítottuk](media/user-help-auth-app-sign-in/icon.png).** Ha ez az ikon jelenik meg, a fiók neve mellett, az azt jelenti, hogy már beállította telefonnal bejelentkezni a fiók. Adja hozzá a fiókjához, leküldéses értesítéseket, hogy értesítést kaphat az alkalmazás hitelesítési kérések kapcsolatos kérheti.

    - **Ha már használja az alkalmazást a kétfaktoros ellenőrzéshez.** Ha Ön már használja az alkalmazást és a kétfaktoros ellenőrzési, is válassza ki a fiók neve melletti legördülő nyílra, és válassza ki **telefonos bejelentkezés engedélyezése**.
    
    - **Ha a fiók nem található.** Ha a fiókja nem található a **fiókok** képernyőn az alkalmazás azt jelenti, hogy még nem vett, az alkalmazás még. A személyes Microsoft-fiókjával lépéseit követve adja hozzá a [személyes Microsoft-fiók beállítása](user-help-auth-app-add-personal-ms-account.md#add-your-personal-microsoft-account) cikk.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Bejelentkezés telefonnal bejelentkezni a fiókjába

1. Nyissa meg a személyes Microsoft fiók bejelentkezési oldalát, és a jelszó beírása helyett válassza a **használja helyette a Microsoft Authenticator alkalmazás** hivatkozásra. 

    A Microsoft értesítést küld a telefonjára. 

2. Hagyja jóvá az értesítést. 

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Jelentkezzen be a kétfaktoros hitelesítési fiókja
A standard szintű kétfaktoros ellenőrzési módszert kell adnia a felhasználónevét és jelszavát jelentkeztethetik az eszközre éppen bejelentkezett, és válassza-e a Microsoft Authenticator alkalmazás értesítést küld, vagy ha szeretne-e másolni a társított ellenőrzése a kód a **fiókok** képernyőjén a Microsoft Authenticator alkalmazást. Kapcsolja be, kétfaktoros ellenőrzési fiókjához adja hozzá a fiókot a Microsoft Authenticator alkalmazást a folyamat részeként.

>[!Note]
>Ha a munkahelyi vagy iskolai fiókkal vagy személyes fiókja nem jelenik meg a **fiókok** képernyő, a Microsoft Authenticator alkalmazást, azt jelenti, hogy a fiók még nem vett a Microsoft Authenticator alkalmazásnak. Tekintse meg a fiók hozzáadásához [adja hozzá munkahelyi vagy iskolai fiókját](user-help-auth-app-add-work-school-account.md) vagy [adja hozzá a személyes fiókokat](user-help-auth-app-add-personal-ms-account.md).

Jelentkezzen be a munkahelyi vagy iskolai vagy személyes fiókjához szükséges lépéseket, kétfaktoros hitelesítés, a különböző módszerekkel lásd [jelentkezzen be a kétlépéses ellenőrzés vagy a biztonsági adatok](user-help-sign-in.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések
|**Kérdés**|**Megoldás**|
|--------------|-------------|
|**Hogyan biztonságosabb, mint a jelszó beírása telefonom történő bejelentkezés?**|Ma a legtöbb ember bejelentkezés webhelyek vagy alkalmazások felhasználónév és jelszó használatával. Sajnos jelszavakat is lehet elvesztése, ellopása, vagy a támadók által kitalálni.<br><br>Miután beállította a Microsoft Authenticator alkalmazást, a telefonján, hogy feloldja fiókját, a telefon PIN-kódot vagy biometrikus zárolás által védett létrehoz egy kulcsot. Ez a kulcs szolgál majd igazolja az identitását aláírás közben.<br><br>**Fontos**<br>Az adatok csak használatos helyileg a kulcsának védelmére. Ez soha nem küldött, vagy a felhőben tárolt.|
|**Telefonos bejelentkezés vált a kétlépéses ellenőrzés? Kell azt a kikapcsolása után?**|Telefonos bejelentkezés egy kétlépéses ellenőrzést olyan típusú, ahol a két lépést mindkét fordulhat elő, a mobileszközön. Két lépés ellenőrzés bekapcsolva a fiókjához további biztonsági biztosítása érdekében érdemes megtartani.|
|**Ha a kétlépéses ellenőrzés bekapcsolva a fiókomhoz megtartásához van két értesítések jóváhagyása?**| Nem. Bejelentkezés a Microsoft-fiókot a telefonon is számít a kétlépéses ellenőrzés, így nem második jóváhagyás szükséges.|
|**Mi történik, ha megszakad a telefonra, vagy még nincs velem? Hogyan férhetek hozzá a-fiókomat?**| Mindig kiválaszthatja a használatát, ehelyett hivatkozás egy jelszót a bejelentkezési lapon váltson vissza az Ön jelszavát. Azonban ha a kétlépéses ellenőrzés továbbra is szüksége egy második módszerrel igazolja személyazonosságát.<br><br>**Fontos**<br>Határozottan javasoljuk, hogy ellenőrizze, hogy egynél több, naprakész, a fiókjához társított ellenőrzési módszert.<br><br>Az ellenőrzési módszerek sorát a személyes fiókok segítségével kezelheti a [biztonsági beállítások](https://account.live.com/proofs/manage) lap. Munkahelyi vagy iskolai fiókok esetében nyissa meg a szervezet [további biztonsági ellenőrzés](https://aka.ms/MFASetup) lap vagy az **a fiókja biztonságának megőrzéséhez** lapon, ha a rendszergazda bekapcsolta az biztonsági adatait. További információ a biztonsági adatok: [Security info (előzetes verzió) – áttekintés](user-help-security-info-overview.md).<br><br>Ha Ön tudja felügyelni az ellenőrzési módszerek sorát, meg kell forduljon a rendszergazdához.|
|**Hogyan leállítása ezzel a funkcióval és térjen vissza a jelszó használatával?**|Személyes fiókok esetében válassza a **inkább jelszó** hivatkozásra a bejelentkezés során. A legutóbbi választott megjegyezze őket, és alapértelmezés szerint el, amikor legközelebb bejelentkezik. Ha szeretne lépjen vissza a telefonos bejelentkezés, válassza ki a **inkább alkalmazás** hivatkozásra a bejelentkezés során.<br><br>A további biztonsági ellenőrzési lapot kell munkahelyi vagy iskolai fiókok esetében (például https://aka.ms/MFASetup) , és távolítsa el a Microsoft Authenticator alkalmazást.|
|**Miért nem használható a telefonos bejelentkezés egynél több munkahelyi vagy iskolai fiókkal?**| A telefon egyetlen munkahelyi vagy iskolai fiók regisztrálva kell lenniük. Kapcsolja be a telefonos bejelentkezés egy másik munkahelyi vagy iskolai fiókot szeretne, ha meg kell először a régi törli az eszköz regisztrációját keresztül a **beállítások** lapot.|
|**Is tudok bejelentkezni a számítógépre a telefonom használatával?**| A számítógépen javasoljuk, hogy a használatával Windows Hello a Windows 10-es aláírási. Windows Hello lehetővé teszi az arcok, ujjlenyomattal vagy a PIN-kód segítségével jelentkezzen be.|

## <a name="next-steps"></a>További lépések

- Ha problémába ütközik az ellenőrző kód beolvasása a személyes Microsoft-fiókkal, tekintse meg a **ellenőrző kód kapcsolatos hibák elhárítása** szakaszában a [Microsoft-fiók biztonsági adatai & k ellenőrzése kódok](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) cikk.

- Ha az alkalmazással kapcsolatos további általános kérdése van, tekintse meg a [a Microsoft Authenticator – gyakori kérdések](user-help-auth-app-faq.md)

- Ha azt szeretné, hogy további információ a kétlépéses ellenőrzést, [a kétlépéses ellenőrzéshez a fiók beállítása](multi-factor-authentication-end-user-first-time.md)

- Ha azt szeretné, hogy további információ a biztonsági adatait, tekintse meg [Security info (előzetes verzió) – áttekintés](user-help-security-info-overview.md)
