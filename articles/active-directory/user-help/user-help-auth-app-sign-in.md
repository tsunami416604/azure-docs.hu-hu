---
title: Bejelentkezés a Microsoft Authenticator alkalmazás használatával – Azure AD
description: A Microsoft Authenticator alkalmazással bejelentkezhet a munkahelyi vagy iskolai fiókjába, illetve a személyes Microsoft-és nem Microsoft-fiókokba a kétfaktoros ellenőrzés vagy a telefonos bejelentkezés használatával.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: abf5d0d4aeeb18a11f50220e982448d41a9ac1b4
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773202"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>Jelentkezzen be a fiókjába a Microsoft Authenticator alkalmazás használatával

A Microsoft Authenticator alkalmazás segít bejelentkezni a fiókjába, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá a fiókokhoz, különösen a bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejthető, ellopott vagy sérült, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít a fiók védelmében azáltal, hogy nehezebbé teszi a más személyek számára a betörést.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- A felhasználónévvel és a jelszóval való bejelentkezés után adjon meg egy második ellenőrzési módszert.

- Jelszó kérése nélkül biztosíthatja a bejelentkezést a felhasználónévvel és a mobileszköz használatával az ujjlenyomattal, a Face vagy a PIN-kóddal.

  >[!Important]
  >Ez a telefonos bejelentkezési módszer csak a munkahelyi vagy iskolai és személyes Microsoft-fiókjaival működik. A nem Microsoft-fiókok esetében a standard kétfaktoros ellenőrzési folyamatot kell használnia.

## <a name="prerequisites"></a>Előfeltételek

A Microsoft Authenticator alkalmazás használata előtt a következőket kell tennie:

 1. Töltse le és telepítse a Microsoft Authenticator alkalmazást. Ha még nem tette meg, tekintse meg [az alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md)című témakört.

 2. Adja hozzá munkahelyi/iskolai, személyes és külső fiókjait a Microsoft Authenticator alkalmazáshoz. A részletes lépésekért tekintse meg a [munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md), [a személyes fiókok](user-help-auth-app-add-personal-ms-account.md)hozzáadása és [a nem Microsoft-fiókok](user-help-auth-app-add-non-ms-account.md)hozzáadása című témakört.

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>A munkahelyi vagy iskolai fiókhoz tartozó telefonos bejelentkezés bekapcsolása és használata

A telefonos bejelentkezés a kétlépéses ellenőrzés típusa. Az identitást továbbra is meg kell győződnie arról, hogy az Ön által ismert dolog és egy dolog van, de a telefonos bejelentkezés lehetővé teszi a fiók jelszavának kihagyása és az összes személyazonosság-ellenőrzés elvégzése a mobileszközön.

A telefonos bejelentkezés bekapcsolásához be kell kapcsolni a kétfaktoros ellenőrzést. További információ a fiók kétfaktoros ellenőrzésének bekapcsolásáról: [munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) és [személyes fiókjainak hozzáadása](user-help-auth-app-add-personal-ms-account.md).

A telefonos bejelentkezés csak az Android 6,0-es vagy újabb verzióját futtató iOS-és Android-eszközökön érhető el.

### <a name="turn-on-phone-sign-in"></a>Telefonos bejelentkezés bekapcsolása

Nyissa meg a Microsoft Authenticator alkalmazást, lépjen a munkahelyi vagy iskolai fiókjába, és kapcsolja be a telefonos bejelentkezést.

Android-eszközön:

- **Ha megjelenik a beállított ikon ![ ikonja ](media/user-help-auth-app-sign-in/icon.png) .** Ha ez az ikon a munkahelyi vagy iskolai fiók neve mellett jelenik meg, az azt jelenti, hogy már beállította a telefonos bejelentkezést a fiókhoz. Előfordulhat, hogy a rendszer leküldéses értesítések hozzáadását kéri a fiókjához, így értesítést kaphat az alkalmazáson kívüli hitelesítési kérelmekről.
- **Ha már használja az alkalmazást a kétfaktoros ellenőrzéshez.** Ha már használta az alkalmazást és a kétfaktoros ellenőrzést, akkor válassza a fiók neve melletti nyilat, majd válassza a **telefonos bejelentkezés engedélyezése**lehetőséget.
- **Ha nem találja a munkahelyi vagy iskolai fiókját.** Ha nem találja a munkahelyi vagy iskolai fiókját az alkalmazás **fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá munkahelyi vagy iskolai fiókját a [munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) című cikkben leírt lépések végrehajtásával.

IOS-eszközön:

- **Ha a fiók csempére koppint**, megnyílik a fiók teljes képernyős nézete. Ha úgy látja, hogy a **telefonos bejelentkezés engedélyezve** van, az azt jelenti, hogy teljes körűen be kell jelentkeznie a jelszó nélkül. Ha a **telefonos bejelentkezés engedélyezése**lehetőséget látja, koppintson rá a telefonos bejelentkezés bekapcsolásához.
- **Ha már használta az alkalmazást a kétfaktoros ellenőrzéshez**, koppintson a fiók csempére, amely ezután a fiók teljes képernyős nézetére bővül. Ezután koppintson a **telefonos bejelentkezés engedélyezése** lehetőségre a telefonos bejelentkezés bekapcsolásához.
- **Ha nem találja a munkahelyi vagy iskolai fiókját** az alkalmazás **fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá munkahelyi vagy iskolai fiókját a munkahelyi vagy iskolai fiók hozzáadása című cikkben leírt lépések végrehajtásával.

> [!NOTE]
> Nem támogatjuk az eszközök regisztrálási és tanúsítványalapú hitelesítésének kombinációját az iOS-ben. A felhasználónak egy megkerülő megoldást kell használnia, ha a bejelentkezés előtt manuálisan regisztrálja az eszközt a hitelesítő beállításain keresztül.

A telefonos bejelentkezés bekapcsolását követően egyszerűen bejelentkezhet a Microsoft Authenticator alkalmazás használatával.

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába.

    Miután beírta a felhasználónevet, megjelenik egy, a **jóváhagyást kérő bejelentkezési** képernyő, amely két számjegyű számot mutat be, és a Microsoft Authenticator alkalmazáson keresztül kéri a bejelentkezést. Ha nem szeretné használni ezt a bejelentkezési módszert, válassza **a jelszó használata helyet**, és jelentkezzen be a jelszavával.

    ![Bejelentkezési mező jóváhagyása a számítógépen](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. Nyissa meg az értesítés vagy a Microsoft Authenticator alkalmazást az eszközön, majd koppintson arra a számra, amely megfelel a számítógép **jóváhagyására szolgáló bejelentkezési** képernyőn megjelenő számnak.

    ![Bejelentkezési mező jóváhagyása az eszközön](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. Ha felismeri a bejelentkezési kísérletet, válassza a **jóváhagyás** lehetőséget. Ellenkező esetben válassza a **Megtagadás**lehetőséget.

4. A hitelesítés befejezéséhez használja a telefon PIN-kódját vagy a biometrikus kulcsát.

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>A személyes Microsoft-fiókok Telefonos bejelentkezésének bekapcsolása és használata

Bekapcsolhatja a telefonos bejelentkezést a személyes Microsoft-fiók, például a Outlook.com, Xbox vagy Skype-ba való bejelentkezéshez használt fiókkal.

>[!NOTE]
>A fiók védelmének biztosításához a Microsoft Authenticator alkalmazásnak PIN-vagy biometrikus zárolásra van szüksége az eszközön. Ha megtartja a telefon zárolását, az alkalmazáshoz be kell állítania egy biztonsági zárolást, mielőtt bekapcsolja a telefonos bejelentkezést.

### <a name="turn-on-phone-sign-in"></a>Telefonos bejelentkezés bekapcsolása 

Nyissa meg a Microsoft Authenticator alkalmazást, lépjen a munkahelyi vagy iskolai fiókjába, és kapcsolja be a telefonos bejelentkezést.

Android-eszközön:

- **Ha megjelenik a beállított ikon ![ ikonja ](media/user-help-auth-app-sign-in/icon.png) .** Ha ez az ikon a személyes Microsoft-fiók neve mellett jelenik meg, az azt jelenti, hogy már beállította a telefonos bejelentkezést a fiókhoz. Előfordulhat, hogy a rendszer leküldéses értesítések hozzáadását kéri a fiókjához, így értesítést kaphat az alkalmazáson kívüli hitelesítési kérelmekről.
- **Ha az alkalmazást kétfaktoros ellenőrzésre használta.** Ha már használta az alkalmazást és a kétfaktoros ellenőrzést, akkor válassza a fiók neve melletti nyilat, majd válassza a **telefonos bejelentkezés engedélyezése**lehetőséget.
- **Ha nem találja a munkahelyi vagy iskolai fiókját.** Ha nem találja a fiókját az alkalmazás **fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá a fiókját a [személyes Microsoft-fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md) cikk lépéseit követve.

IOS-eszközön:

- **Ha a fiók csempére koppint**, megnyílik a fiók teljes képernyős nézete. Ha úgy látja, hogy a **telefonos bejelentkezés engedélyezve** van, az azt jelenti, hogy teljes körűen be kell jelentkeznie a jelszó nélkül. Ha a **telefonos bejelentkezés engedélyezése**lehetőséget látja, koppintson rá a telefonos bejelentkezés bekapcsolásához.
- **Ha már használja az alkalmazást a kétfaktoros ellenőrzéshez**, koppintson a fiók csempére, amely ezután a fiók teljes képernyős nézetére bővül. Ezután koppintson a **telefonos bejelentkezés engedélyezése** lehetőségre a telefonos bejelentkezés bekapcsolásához.
- **Ha nem találja a fiókját** az alkalmazás **fiókok** képernyőjén, az azt jelenti, hogy még nem adta hozzá az alkalmazáshoz. Adja hozzá személyes Microsoft-fiók a [személyes Microsoft-fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md) című cikk lépéseit követve.

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>Bejelentkezés a fiókba a telefonos bejelentkezés használatával

1. Nyissa meg a személyes Microsoft-fiók bejelentkezési oldalát, majd a jelszó beírása helyett válassza a **Microsoft Authenticator alkalmazás használata helyet** .

    A Microsoft értesítést küld a telefonjára.

2. Hagyja jóvá az értesítést.

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>Bejelentkezés a fiók kétfaktoros ellenőrzésének használatával

A standard kétfaktoros ellenőrzési módszerhez meg kell adnia a felhasználónevét és a jelszavát arra az eszközre, amelyhez be van jelentkezve. Ezután nyissa meg a Microsoft Authenticator alkalmazást, és írja be a fiókja véletlenszerűen generált kódját a **kód megadása** mezőbe. Android-eszközön ezek az ellenőrző kódok a **fiókok** képernyőjén találhatók. IOS-eszközökön ezek az ellenőrző kódok a **fiókok** képernyőjén vagy egy fiók teljes képernyős nézetében találhatók a fiók típusától függően. A fiók a Microsoft Authenticator alkalmazásba való felvételének folyamata során bekapcsolja a fiók kétfaktoros ellenőrzését.

>[!Note]
>Ha nem látja munkahelyi vagy iskolai fiókját vagy személyes fiókját a Microsoft Authenticator alkalmazás **fiókok** képernyőjén, az azt jelenti, hogy nem adta hozzá a fiókot a Microsoft Authenticator alkalmazáshoz. A fiók hozzáadásához tekintse meg [a munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) vagy [a személyes fiókok](user-help-auth-app-add-personal-ms-account.md)hozzáadása című témakört.

A munkahelyi vagy iskolai vagy személyes fiókba való bejelentkezéshez szükséges lépéseket a kétfaktoros ellenőrzés különböző módszereit követve tekintse meg a [Bejelentkezés kétlépéses ellenőrzés vagy biztonsági információ használatával](user-help-sign-in.md)című témakört.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

| Kérdés | Megoldás |
| -------- | -------- |
| Hogyan történik a telefonos bejelentkezés biztonságosabb, mint a jelszó beírása? | Manapság a legtöbb felhasználó felhasználónevet és jelszót használ a webhelyekre vagy alkalmazásokba való bejelentkezéshez. A jelszavakat sajnos a hackerek elveszik, ellopják vagy kitalálják.<br><br>Miután beállította a Microsoft Authenticator alkalmazást, egy kulcsot hoz létre a telefonján a telefon PIN-kódjának vagy biometrikus zárolásának védelme alatt álló fiók zárolásának feloldásához. Ezt követően a rendszer a bejelentkezéskor a személyazonosság igazolására használja a kulcsot.<br><br>**Fontos**<br>Az adatait a rendszer kizárólag a kulcs helyi védelme érdekében használja. A felhőben soha nem érkezik, vagy nem tárolódik. |
| A telefonos bejelentkezés lecseréli a kétlépéses ellenőrzést? Ki kell kapcsolni? | A telefonos bejelentkezés a kétlépéses ellenőrzés típusa, ahol két lépés zajlik a mobileszközön. Ha további biztonságra van szüksége a fiók számára, tartsa meg a két lépésből álló ellenőrzést. |
| Ha a fiókomban a kétlépéses ellenőrzés bekapcsolva maradok, két értesítést kell jóváhagyni? | Nem. Ha telefonján bejelentkezik a Microsoft-fiókba, a két lépésből álló ellenőrzésnek számít, így nincs szükség második jóváhagyásra. |
| Mi a teendő, ha elveszítem a telefont, vagy nem vagyok velem? Hogyan hozzáférni a fiókomhoz? | A bejelentkezési oldalon bármikor bejelölheti a jelszó használata helyet, hogy visszaváltson a jelszó használatára. Ha azonban kétlépéses ellenőrzést használ, továbbra is egy második módszert kell használnia a személyazonosságának ellenőrzéséhez.<br><br>**Fontos**<br>Javasoljuk, hogy győződjön meg arról, hogy a fiókjához tartozó több, naprakész, ellenőrzési módszert használ.<br><br>A [biztonsági beállítások](https://account.live.com/proofs/manage) lapról kezelheti a személyes fiókok ellenőrzési módszereit. Munkahelyi vagy iskolai fiókok esetében lépjen a szervezet [további biztonsági ellenőrzés](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) lapjára, vagy a **fiók biztonságos megőrzése** lapra, ha a rendszergazda bekapcsolta a biztonsági adatokat. További információ a biztonsági adatokról: [biztonsági adatok (előzetes verzió) – áttekintés](user-help-security-info-overview.md).<br><br>Ha nem tudja kezelni az ellenőrzési módszereket, forduljon a rendszergazdához. |
| Hogyan a funkció használatát, és térjen vissza a saját jelszó használatára? | Személyes fiókok esetén a bejelentkezéskor válassza a **jelszó használata hivatkozás helyett** lehetőséget. A legutóbbi bejelentkezés alkalmával a rendszer alapértelmezés szerint megjegyezi és felkínálja a legutóbbi választást. Ha bármikor vissza szeretne térni a telefonos bejelentkezés használatára, jelölje be az **alkalmazás használata hivatkozás helyett** a bejelentkezéskor lehetőséget.<br><br>Munkahelyi vagy iskolai fiókok esetében szüntesse meg az eszköz regisztrációját a Microsoft Authenticator alkalmazás **Beállítások** lapján, vagy tiltsa le az eszközt a profiljának **eszközök & tevékenység** területéről. További információ az eszköz profilból való letiltásáról: a [profil és a fiókadatok frissítése a saját alkalmazások portálról](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information). |
| Miért nem használhatok több munkahelyi vagy iskolai fiókot a telefonos bejelentkezéshez? | A telefont egyetlen munkahelyi vagy iskolai fiókhoz kell regisztrálni. Ha egy másik munkahelyi vagy iskolai fiókhoz szeretné bekapcsolni a telefonos bejelentkezést, akkor a **Beállítások** lapon törölni kell a fiókját az eszközről. |
| Be tudok jelentkezni a számítógépre a telefonom használatával? | A számítógép esetében javasoljuk, hogy jelentkezzen be a Windows Hello használatával a Windows 10 rendszeren. A Windows Hello lehetővé teszi az arc, az ujjlenyomat vagy a PIN-kód használatát a bejelentkezéshez. |

## <a name="next-steps"></a>További lépések

- Ha nem sikerül beszereznie a személyes Microsoft-fiók ellenőrzési kódját, tekintse meg az **ellenőrző kód problémáinak elhárítása** című szakaszt a [Microsoft-fiók biztonsági adatok & ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikkben.

- Ha általánosabb kérdései vannak az alkalmazással kapcsolatban, tekintse meg a [Microsoft Authenticator gyakori](user-help-auth-app-faq.md) kérdések című témakört.

- Ha további információra van szüksége a kétlépéses ellenőrzésről, tekintse [meg a fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című témakört.

- Ha további információra van szüksége a biztonsági adatokról, tekintse meg a [biztonsági adatok (előzetes verzió) áttekintése](user-help-security-info-overview.md) című témakört.
