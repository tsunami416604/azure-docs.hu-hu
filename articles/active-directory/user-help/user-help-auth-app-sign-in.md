---
title: Bejelentkezés a Microsoft Authenticator alkalmazással – Azure AD
description: A Microsoft Authenticator alkalmazással kétfaktoros ellenőrzéssel vagy telefonos bejelentkezéssel jelentkezz be munkahelyi vagy iskolai fiókjába, illetve személyes Microsoft- és nem Microsoft-fiókjába.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9b8c44f99953d4518f0bc3f558f396250657c632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138943"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Bejelentkezés a fiókokba a Microsoft Authenticator alkalmazással

A Microsoft Authenticator alkalmazás segít a fiókokba való bejelentkezésben, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá fiókjaihoz, különösen a bizalmas adatok megtekintése közben. Mivel a jelszavak elfelejthetők, ellophatók vagy feltörhetők, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít megvédeni fiókját azáltal, hogy megnehezíti mások számára a betörést.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- A felhasználónévvel és jelszóval történő bejelentkezés után egy második ellenőrzési módszer megadására vonatkozó kérdés megadása.

- Bejelentkezés jelszó nélkül történő megadása, a felhasználónév és a mobileszköz használata ujjlenyomattal, arccal vagy PIN-kóddal.

  >[!Important]
  >Ez a telefonos bejelentkezési módszer csak a munkahelyi vagy iskolai és személyes Microsoft-fiókokkal működik. A nem Microsoft-fiókok megkövetelik a szabványos kétfaktoros ellenőrzési folyamat használatát.

## <a name="prerequisites"></a>Előfeltételek

A Microsoft Authenticator alkalmazás használata előtt a következőket kell tennie:

 1. Töltse le és telepítse a Microsoft Authenticator alkalmazást. Ha még nem tette meg, olvassa el Az alkalmazás letöltése és telepítése című [témakört.](user-help-auth-app-download-install.md)

 2. Adja hozzá munkahelyi/iskolai, személyes és külső fiókjait a Microsoft Authenticator alkalmazáshoz. A részletes lépéseket a [Munkahelyi vagy iskolai fiók hozzáadása,](user-help-auth-app-add-work-school-account.md)A személyes fiókok [hozzáadása](user-help-auth-app-add-personal-ms-account.md)és a [Nem Microsoft-fiókok hozzáadása](user-help-auth-app-add-non-ms-account.md)című témakörben t.

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>A munkahelyi vagy iskolai fiókba való telefonos bejelentkezés bekapcsolása és használata

A telefonos bejelentkezés a kétlépéses ellenőrzés egyik típusa. Továbbra is igazolnia kell személyazonosságát azáltal, hogy megad egy ismert és egy olyan dolgot, amellyel rendelkezik, de a telefonos bejelentkezés lehetővé teszi, hogy kihagyja a fiók jelszavának megadását, és elvégzi az összes személyazonosság-ellenőrzést a mobileszközén.

A telefonos bejelentkezés bekapcsolása előtt be kell kapcsolnia a kétfaktoros ellenőrzést. Ha többet szeretne tudni arról, hogy miként kapcsolhatja be egy fiók kétfaktoros ellenőrzését, olvassa el [a Munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) és a Személyes fiókok hozzáadása című [témakört.](user-help-auth-app-add-personal-ms-account.md)

A telefonos bejelentkezés csak iOS és Android 6.0 vagy újabb rendszert futtató androidos eszközökön érhető el.

### <a name="turn-on-phone-sign-in"></a>A telefonos bejelentkezés bekapcsolása

Nyissa meg a Microsoft Authenticator alkalmazást, nyissa meg munkahelyi vagy iskolai fiókját, és kapcsolja be a telefonos bejelentkezést.

Android-eszközön:

- **Ha megjelenik ![ez az ikon,](media/user-help-auth-app-sign-in/icon.png)amely a beállított a .** Ha ez az ikon a munkahelyi vagy iskolai fiók neve mellett jelenik meg, az azt jelenti, hogy már beállította a telefonos bejelentkezést a fiókhoz. Előfordulhat, hogy a rendszer leküldéses értesítéseket ad meg a fiókjához, így értesítést kaphat az alkalmazáson kívüli hitelesítési kérelmekről.
- **Ha már használja az alkalmazást a kétfaktoros ellenőrzéshez.** Ha már használta az alkalmazást és a kétfaktoros ellenőrzést, kiválaszthatja a fióknév melletti nyilat, majd a **Telefonos bejelentkezés engedélyezése**lehetőséget.
- **Ha nem találod a munkahelyi vagy iskolai fiókodat.** Ha nem találja munkahelyi vagy iskolai fiókját az alkalmazás **Fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá munkahelyi vagy iskolai fiókját a [Munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) című cikkben leírt lépések végrehajtásával.

iOS-eszközön:

- **Amikor a fiókcsempére koppint,** a fiók a fiók teljes képernyős nézetében nyílik meg. Ha a **Telefon bejelentkezés engedélyezve van,** az azt jelenti, hogy teljes mértékben be van állítva a jelszó nélküli bejelentkezéshez. Ha a **Telefonos bejelentkezés engedélyezése**felirat ot látja, koppintson rá a telefonos bejelentkezés bekapcsolására.
- **Ha már használta az alkalmazást a kétfaktoros ellenőrzéshez,** koppintson a fiókcsempére, amely ezután a fiók teljes képernyős nézetébe bővül. Ezután koppintson **a Telefonos bejelentkezés engedélyezése** elemre a telefonos bejelentkezés bekapcsolásához.
- **Ha nem találja munkahelyi vagy iskolai fiókját** az alkalmazás **Fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá munkahelyi vagy iskolai fiókját a Munkahelyi vagy iskolai fiók hozzáadása című cikkben leírt lépések végrehajtásával.

A telefonos bejelentkezés bekapcsolása után csak a Microsoft Authenticator alkalmazással jelentkezhet be.

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába.

    A felhasználónév beírása után megjelenik egy **Bejelentkezés jóváhagyása** képernyő, amely egy kétjegyű számot jelenít meg, és arra kéri, hogy jelentkezzen be a Microsoft Authenticator alkalmazáson keresztül. Ha nem szeretné használni ezt a bejelentkezési módszert, válassza **a Jelszó használata helyett**lehetőséget, és jelentkezzen be a jelszavával.

    ![Bejelentkezési mező jóváhagyása a számítógépen](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Nyissa meg az értesítést vagy a Microsoft Authenticator alkalmazást az eszközén, majd koppintson a számítógép **Bejelentkezés jóváhagyása képernyőn** látható számra.

    ![Bejelentkezési mező jóváhagyása az eszközön](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Válassza **a Jóváhagyás** lehetőséget, ha felismeri a bejelentkezési kísérletet. Ellenkező esetben válassza **a Megtagadás**lehetőséget.

4. A hitelesítés befejezéséhez használja a telefon PIN-kódját vagy biometrikus kulcsát.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>A telefonos bejelentkezés bekapcsolása és használata személyes Microsoft-fiókjaihoz

Bekapcsolhatja a telefonos bejelentkezést személyes Microsoft-fiókjához, például azt a fiókot, amelyet a Outlook.com, az Xbox ba vagy a Skype-ba való bejelentkezéshez használ.

>[!NOTE]
>A fiók védelme érdekében a Microsoft Authenticator alkalmazáspin-kódot vagy biometrikus zárolást igényel az eszközön. Ha a telefon zárolása feloldva marad, az alkalmazás megköveteli, hogy a telefonbejelentkezés bekapcsolása előtt biztonsági zárat állítson be.

### <a name="turn-on-phone-sign-in"></a>A telefonos bejelentkezés bekapcsolása 

Nyissa meg a Microsoft Authenticator alkalmazást, nyissa meg munkahelyi vagy iskolai fiókját, és kapcsolja be a telefonos bejelentkezést.

Android-eszközön:

- **Ha megjelenik ![ez az ikon,](media/user-help-auth-app-sign-in/icon.png)amely a beállított a .** Ha ez az ikon a személyes Microsoft-fiók neve mellett jelenik meg, az azt jelenti, hogy már beállította a telefonos bejelentkezést a fiókhoz. Előfordulhat, hogy a rendszer leküldéses értesítéseket ad meg a fiókjához, így értesítést kaphat az alkalmazáson kívüli hitelesítési kérelmekről.
- **Ha az alkalmazást kétfaktoros ellenőrzésre használja.** Ha már használta az alkalmazást és a kétfaktoros ellenőrzést, kiválaszthatja a fióknév melletti nyilat, majd a **Telefonos bejelentkezés engedélyezése**lehetőséget.
- **Ha nem találod a munkahelyi vagy iskolai fiókodat.** Ha nem találja fiókját az alkalmazás **Fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá fiókját a [Személyes Microsoft-fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md) című cikkben leírt lépéseket követve.

iOS-eszközön:

- **Amikor a fiókcsempére koppint,** a fiók a fiók teljes képernyős nézetében nyílik meg. Ha a **Telefon bejelentkezés engedélyezve van,** az azt jelenti, hogy teljes mértékben be van állítva a jelszó nélküli bejelentkezéshez. Ha a **Telefonos bejelentkezés engedélyezése**felirat ot látja, koppintson rá a telefonos bejelentkezés bekapcsolására.
- **Ha már használja az alkalmazást a kétfaktoros ellenőrzéshez,** koppintson a fiókcsempére, amely ezután a fiók teljes képernyős nézetébe bővül. Ezután koppintson **a Telefonos bejelentkezés engedélyezése** elemre a telefonos bejelentkezés bekapcsolásához.
- **Ha nem találja fiókját** az alkalmazás **Fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Személyes Microsoft-fiókját a Személyes [Microsoft-fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md) című cikkben ismertetett lépések végrehajtásával adja hozzá.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Bejelentkezés a fiókba telefonos bejelentkezéssel

1. Nyissa meg a személyes Microsoft-fiók bejelentkezési lapját, majd a jelszó beírása helyett válassza **a Microsoft Authenticator alkalmazás használata hivatkozást.**

    A Microsoft értesítést küld a telefonjára.

2. Az értesítés jóváhagyása.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Bejelentkezés a fiókjához kétfaktoros ellenőrzéssel

A szabványos kétfaktoros ellenőrzési módszer megköveteli, hogy adja meg a felhasználónevét és jelszavát az eszköz, akkor bejelentkezik. Ezután megnyitja a Microsoft Authenticator alkalmazást, és beírja a fiókjához véletlenszerűen generált kódot az **Enter kód** mezőbe. Android-eszközön ezek az ellenőrző kódok a **Fiókok** képernyőn találhatók. IOS-eszközön ezek az ellenőrző kódok a fiók típusától függően a **Fiókok** képernyőn vagy a fiók teljes képernyős nézetében találhatók. A fiók Microsoft Authenticator alkalmazáshoz való hozzáadásának folyamatának részeként bekapcsolja a kétfaktoros ellenőrzést a fiókjában.

>[!Note]
>Ha nem látja munkahelyi vagy iskolai fiókját vagy személyes fiókját a Microsoft Authenticator alkalmazás **Fiókok** képernyőjén, az azt jelenti, hogy nem adta hozzá a fiókot a Microsoft Authenticator alkalmazáshoz. A fiók hozzáadásához olvassa el [a Munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) vagy a Személyes fiókok [hozzáadása.](user-help-auth-app-add-personal-ms-account.md)

A munkahelyi, iskolai vagy személyes fiókjába való bejelentkezéshez szükséges lépéseket a kétlépéses ellenőrzés különböző módszereinek használatával a [Bejelentkezés a kétlépéses ellenőrzés vagy](user-help-sign-in.md)a biztonsági adatok használatával című témakörben talál.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

| Kérdés | Megoldás |
| -------- | -------- |
| Hogyan biztonságosabb a telefonnal való bejelentkezés, mint a jelszó beírása? | Ma a legtöbb ember felhasználónévvel és jelszóval jelentkezik be webhelyekre vagy alkalmazásokba. Sajnos a jelszavak elveszhetnek, ellophatók vagy kitalálhatják a hackerek.<br><br>A Microsoft Authenticator alkalmazás beállítása után létrehoz egy kulcsot a telefonján, amely feloldja a telefon PIN-kódjával vagy biometrikus zárolásával védett fiók zárolását. Ez a kulcs ezután a bejelentkezés során a személyazonosság ának igazolására szolgál.<br><br>**Fontos**<br>Az adatokat csak helyileg használjuk fel a kulcs védelmére. Soha nem küldik el, és nem tárolják a felhőben. |
| A telefonos bejelentkezés helyettesíti a kétlépéses ellenőrzést? Kapcsoljam ki? | A telefonos bejelentkezés a kétlépcsős ellenőrzés egy típusa, ahol a két lépés a mobileszközön történik. A kétlépcsős ellenőrzést be kell kapcsolnia, hogy további biztonságot nyújthasson fiókjának. |
| Ha bekapcsolva tartom a kétlépéses ellenőrzést a fiókomban, két értesítést kell jóváhagynom? | Nem. A Microsoft-fiókba a telefonon történő bejelentkezés szintén kétlépéses ellenőrzésnek számít, ezért nincs szükség második jóváhagyásra. |
| Mi van, ha elveszítem a telefonomat, vagy nincs nálam? Hogyan érhetem el a fiókomat? | A bejelentkezési lapon bármikor kiválaszthatja a Jelszó használata hivatkozást a jelszó használatára való visszaváltáshoz. Ha azonban kétlépéses ellenőrzést használ, akkor is egy második módszert kell használnia a személyazonosságának igazolásához.<br><br>**Fontos**<br>Javasoljuk, hogy győződjön meg arról, hogy egynél több, naprakész ellenőrzési módszerrel rendelkezik a fiókjához.<br><br>A személyes fiókok ellenőrzési módszereit a [Biztonsági beállítások](https://account.live.com/proofs/manage) oldalon kezelheti. Munkahelyi vagy iskolai fiókok esetén nyissa meg a szervezet [További biztonsági ellenőrzés](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) lapját, vagy a Fiók **biztonságának megőrzését** tartalmazó lapot, ha a rendszergazda bekapcsolta a biztonsági adatokat. A biztonsági adatokról a [Biztonsági adatok (előzetes verzió) című témakörben olvashat bővebben.](user-help-security-info-overview.md)<br><br>Ha nem tudja kezelni az ellenőrzési módszereket, forduljon a rendszergazdához. |
| Hogyan állíthatom le a funkció használatát, és hogyan visszatérhetek a jelszavam használatához? | Személyes fiókok esetén válassza a **Jelszó használata hivatkozást** bejelentkezéskor. A rendszer a következő bejelentkezéskor a rendszer nem jegyzi meg a legutóbbi választási lehetőséget, és alapértelmezés szerint felajánlja. Ha bármikor vissza szeretne lépni a telefonos bejelentkezéshez, válassza az **Alkalmazás használata inkább** hivatkozást a bejelentkezés során.<br><br>Munkahelyi vagy iskolai fiókok esetén vagy törölnie kell az eszköz regisztrációját a Microsoft Authenticator alkalmazás **Beállítások** lapjáról, vagy le kell tiltania az eszközt a profil **Eszközök & tevékenységi** területéről. Az eszköz profilból való letiltásáról a [Profil- és fiókadatok frissítése a Saját alkalmazások portálon](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information)című témakörben talál további információt. |
| Miért nem tudok egynél több munkahelyi vagy iskolai fiókot használni a telefonos bejelentkezéshez? | A telefont egyetlen munkahelyi vagy iskolai fiókhoz kell regisztrálni. Ha egy másik munkahelyi vagy iskolai fiók esetében szeretné bekapcsolni a telefonos bejelentkezést, a **Beállítások** lapon le kell vennie a fiók regisztrációját erről az eszközről. |
| Bejelentkezhetek a gépemre a telefonommal? | Számítógépe esetében azt javasoljuk, hogy windows 10-es rendszeren a Windows Hello használatával jelentkezzen be. A Windows Hello lehetővé teszi, hogy arcával, ujjlenyomatával vagy PIN-kódjával jelentkezzen be. |

## <a name="next-steps"></a>További lépések

- Ha nem sikerül beszereznie a személyes Microsoft-fiókjához szükséges ellenőrző kódot, olvassa el a [Microsoft-fiók biztonsági adatai& ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikk **Hibaelhárítási kódokkal kapcsolatos hibáinak elhárítása** című részét.

- Ha általánosabb kérdései vannak az alkalmazással kapcsolatban, olvassa el a [Microsoft Hitelesítő gyakori kérdéseit](user-help-auth-app-faq.md)

- Ha további információra van szüksége a kétlépéses ellenőrzésről, olvassa el [a Fiók beállítása kétlépéses ellenőrzéshez című témakört.](multi-factor-authentication-end-user-first-time.md)

- Ha további információra van szüksége a biztonsági adatokról, olvassa el [a Biztonsági adatok (előzetes verzió) áttekintése című témakört.](user-help-security-info-overview.md)
