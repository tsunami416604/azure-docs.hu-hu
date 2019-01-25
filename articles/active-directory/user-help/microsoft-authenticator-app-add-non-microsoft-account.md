---
title: A személyes-Microsoft fiókok hozzáadása a Microsoft Authenticator alkalmazás – az Azure Active Directory |} A Microsoft Docs
description: Hogyan lehet hozzáadni a személyes-Microsoft fiókok, például Google, Facebook vagy a GitHub kétfaktoros ellenőrzéshez a Microsoft Authenticator alkalmazást.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 62ab28c484575a59d25d9601b92b9b89fad71d6d
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904853"
---
# <a name="add-your-personal-non-microsoft-accounts"></a>A személyes-Microsoft fiókok hozzáadása
Hozzáadása a személyes-Microsoft fiókok, például Google, Facebook vagy a GitHub kétfaktoros ellenőrzéshez a Microsoft Authenticator alkalmazást.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítse a Microsoft Authenticator alkalmazást. Ha még nem tette, hogy, kövesse a [töltse le és telepítse az alkalmazást](microsoft-authenticator-app-how-to.md) cikk.

## <a name="add-personal-accounts"></a>Személyes fiókok hozzáadása
Általában az összes személyes fiók, tegye a következőket:

1. Jelentkezzen be a fiókjába, és kapcsolja be az eszköz vagy a számítógép kétfaktoros hitelesítési.

2. Adja hozzá a fiókot a Microsoft Authenticator alkalmazást.

A Facebook, Google, GitHub és az Amazon fiókok Itt a folyamat tudjuk biztosítani, de ez a folyamat megegyezik más alkalmazás, például az Instagramon, a Netflix vagy az Adobe.

## <a name="add-your-google-account"></a>A Google-fiók hozzáadása
Adja hozzá a Google-fiók kétfaktoros ellenőrzési bekapcsolásával, és ezután hozzáadná a fiókot az alkalmazást.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés

1. Lépjen a számítógépen, https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome, jelölje be **Ismerkedés**, és majd a személyazonosságát.

2. Kövesse az oldalon lépéseket a személyes Google-fiókját a kétlépéses ellenőrzés bekapcsolása.

### <a name="add-your-google-account-to-the-app"></a>A Google-fiók hozzáadása az alkalmazáshoz

1. A Google lapon a számítógépen nyissa meg a **állítsa be a második lépésben alternatív** válassza **beállítása** származó a **hitelesítő alkalmazás** szakaszban.

2. Az a **kódok beolvasása az Authenticator alkalmazásból** lapra, jelölje be vagy **Android** vagy **iPhone** a telefontípus alapján, és adja meg **következő**.

    QR-kód, amely segítségével automatikusan társítsa a fiókját a Microsoft Authenticator alkalmazással felhőszolgáltatására. Ne zárja be ezt az ablakot.

3. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be **fiók hozzáadása** származó a **testreszabása és vezérlési** ikonra a jobb oldali, és adja meg a felső **egyéb fiók (Google, Facebook stb.)** .

4. A QR-kód az eszköz kamerájának használata a **állítsa be az Authenticator** lap a számítógépen.

    >[!Note]
    >Ha a kamera nem működik megfelelően, akkor [írja be a QR-kódot és URL-cím manuálisan](#add-an-account-to-the-app-manually).

5. Tekintse át a **fiókok** lapja a Microsoft Authenticator alkalmazást az eszközön, győződjön meg arról, hogy a fiók adatait el a jobb oldalon, és hogy van-e egy kapcsolódó 6 jegyű ellenőrző kódot.

    A fokozott biztonság érdekében az ellenőrző kód módosítása megakadályozza, hogy valaki a kód többször használatával 30 másodpercenként.

6. Válassza ki **tovább** a a **állítsa be a hitelesítő** rendszerű számítógép lapon írja be a 6 jegyű ellenőrző kódot, a Google-fiókját az alkalmazásban megadott, és válassza ki **ellenőrizze**.

7. Fiókját visszaigazolta, és kiválaszthatja **kész** gombra kattintva zárja be a **állítsa be az Authenticator** lapot.

    >[!NOTE]
    >További információ kétfaktoros hitelesítési és a Google-fiók: [2. lépés – ellenőrzés](https://support.google.com/accounts/answer/185839) és [további információ a 2. lépés ellenőrzési](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Adja hozzá a Facebook-fiókban
Adja hozzá a Facebook-fiókban kétfaktoros ellenőrzési bekapcsolásával, és ezután hozzáadná a fiókot az alkalmazást.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés

1. A számítógépen, nyissa meg a Facebook, a legördülő menüben válassza a jobb felső sarokban, és folytassa a **beállítások** > **biztonsági és a bejelentkezési**.

    A **biztonsági és a bejelentkezési** lap jelenik meg.

2. Menjen lejjebb a **kétfaktoros hitelesítés használata** beállítást a **Kétfaktoros hitelesítés** szakaszt, és válassza ki **szerkesztése**.

    A **Kétfaktoros hitelesítés** lap jelenik meg.

3. Válassza ki **bekapcsolása**.

### <a name="add-your-facebook-account-to-the-app"></a>A Facebook-fiókban hozzáadása az alkalmazáshoz

1. A Facebook-oldalon a számítógépen, nyissa meg a **adja hozzá a biztonsági másolat** szakaszt, és válassza a **telepítő** származó a **Authentication alkalmazás** területen.

    QR-kód, amely segítségével automatikusan társítsa a fiókját a Microsoft Authenticator alkalmazással felhőszolgáltatására. Ne zárja be ezt az ablakot.

2. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be **fiók hozzáadása** származó a **testreszabása és vezérlési** ikonra a jobb oldali, és adja meg a felső **egyéb fiók (Google, Facebook stb.)** .

3. A QR-kód az eszköz kamerájának használata a **kéttényezős hitelesítéssel a két** lap a számítógépen.

    >[!Note]
    >Ha a kamera nem működik megfelelően, akkor [írja be a QR-kódot és URL-cím manuálisan](#add-an-account-to-the-app-manually).

4. Tekintse át a **fiókok** lapja a Microsoft Authenticator alkalmazást az eszközön, győződjön meg arról, hogy a fiók adatait el a jobb oldalon, és hogy van-e egy kapcsolódó 6 jegyű ellenőrző kódot.

    A fokozott biztonság érdekében az ellenőrző kód módosítása megakadályozza, hogy valaki a kód többször használatával 30 másodpercenként.

5. Válassza ki **tovább** a a **kéttényezős hitelesítéssel a két** lapon a számítógépen, és írja be a Facebook-fiókját az alkalmazásban megadott 6 jegyű ellenőrző kódot.

    Fiókját visszaigazolta, és a fiók mostantól használhatja az alkalmazást.

    >[!NOTE]
    >További információ kétfaktoros hitelesítési és a Facebook-fiókban, lásd: [mi kéttényezős hitelesítést, és hogyan működik?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>A GitHub-fiók hozzáadása
Adja hozzá a GitHub-fiók kétfaktoros ellenőrzési bekapcsolásával, és ezután hozzáadná a fiókot az alkalmazást.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés

1. A számítógépen, nyissa meg a GitHub, a rendszerkép kiválasztása a jobb felső sarokban, és válassza **beállítások**.

    A **kétfaktoros hitelesítés** lap jelenik meg.

2. Válassza ki **biztonsági** származó a **személyes beállítások** oldalsáv, és válassza ki **kétfaktoros hitelesítés engedélyezése** a a **kéttényezős hitelesítés**  területen.

### <a name="add-your-github-account-to-the-app"></a>A GitHub-fiók hozzáadása az alkalmazáshoz

1. Az a **kétfaktoros hitelesítés** a számítógépen, válassza a lap **beállítása egy adott alkalmazás használatát**.

2. A helyreállítási kódokat mentéséhez, így vissza a fiókja visszaszerzéséhez Ha elveszíti a hozzáférést, és válassza ki **tovább**. 

    Az eszköz letöltésével, egy nyomtatott nyomtatási, vagy másolja őket egy jelszó manager eszközbe, mentheti a kódokat.

3. Az a **kétfaktoros hitelesítés** lapon jelölje be **beállítása egy adott alkalmazás használatát**.

    A lap vált, QR-kódot. Ne zárja be ezt oldal.

4. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be **fiók hozzáadása** származó a **testreszabása és vezérlési** ikonra a jobb oldali felső sarokban válassza **egyéb fiók (Google, Facebook stb.)** , majd válassza ki **adja meg a szöveg kódot** a szöveg, amely a lap tetején.

    A Microsoft Authenticator alkalmazás akkor tudja beolvasni a QR-kódot, így manuálisan adja meg a kódot.

5. Adjon meg egy **fióknév** (például a GitHub), és írja be a **titkos kulcs** 4. lépés, és válassza ki a **Befejezés**.

4. Az a **kéttényezős hitelesítő** rendszerű számítógép lapon írja be a GitHub-fiókját az alkalmazásban megadott 6 jegyű ellenőrző kódot, és válassza ki **engedélyezése**.

    A **fiókok** az oldal az alkalmazás megjeleníti a fióknevet és a egy 6 jegyű ellenőrző kódot. A fokozott biztonság érdekében az ellenőrző kód módosítása megakadályozza, hogy valaki a kód többször használatával 30 másodpercenként.

    >[!NOTE]
    >További információ kétfaktoros hitelesítési és a GitHub-fiók: [készül a kétfaktoros hitelesítés](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Az Amazon-fiók hozzáadása
Adja hozzá az Amazon-fiókkal kétfaktoros ellenőrzési bekapcsolásával, és ezután hozzáadná a fiókot az alkalmazást.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés

1. A számítógépen, nyissa meg az Amazon, jelölje be a **fiók & listák** legördülő menüből, és válassza ki **fiók**.

2. Válassza ki **bejelentkezési és a biztonság**, jelentkezzen be az Amazon-fiókkal, és válassza **szerkesztése** a a **speciális biztonsági beállítások** területen.

    A **speciális biztonsági beállítások** lap jelenik meg.

3. Válassza ki **Ismerkedés**.

4. Válassza ki **hitelesítő alkalmazás** származó a **válassza ki, hogyan fog kapni a kódok** lapot.

    A lap vált, QR-kódot. Ne zárja be ezt oldal.

5. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be **fiók hozzáadása** származó a **testreszabása és vezérlési** ikonra a jobb oldali, és adja meg a felső **egyéb fiók (Google, Facebook stb.)** .

6. A QR-kód az eszköz kamerájának használata a **válassza ki, hogyan fog kapni a kódok** lap a számítógépen.

    >[!Note]
    >Ha a kamera nem működik megfelelően, akkor [írja be a QR-kódot és URL-cím manuálisan](#add-an-account-to-the-app-manually).

5. Tekintse át a **fiókok** lapja a Microsoft Authenticator alkalmazást az eszközön, győződjön meg arról, hogy a fiók adatait el a jobb oldalon, és hogy van-e egy kapcsolódó 6 jegyű ellenőrző kódot.

    A fokozott biztonság érdekében az ellenőrző kód módosítása megakadályozza, hogy valaki a kód többször használatával 30 másodpercenként.

6. Az a **válassza ki, hogyan fog kapni a kódok** rendszerű számítógép lapon írja be az Amazon-fiókját az alkalmazásban megadott 6 jegyű ellenőrző kódot, és válassza ki **kód ellenőrzése, és továbbra is**.

7. Fejezze be a regisztrációs folyamat, beleértve a szöveges üzenetben, például a biztonsági másolat ellenőrzésének metódus hozzáadása, majd **kód küldése**.

8. Az a **adjon hozzá egy biztonsági mentési ellenőrzési módszert** rendszerű számítógép lapon írja be a biztonsági mentési ellenőrzési módszert az Amazon fiók által biztosított hat számjegyű ellenőrzőkódot, és válassza ki **kód ellenőrzése, és továbbra is**.

9. A Almost lapon, győződjön meg a számítógép megbízható eszköz, és válassza ki kell-e **rendben. Kétlépéses ellenőrzés**.

    A **speciális biztonsági beállítások** megjelenítő lap jelenik meg, a frissített kétfaktoros hitelesítési adatait.

    >[!NOTE]
    >További információ kétfaktoros hitelesítési és az Amazon-fiókkal, tekintse meg [kétlépéses ellenőrzés](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) és [bejelentkezik egy kétlépéses ellenőrzés](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>További lépések

- Miután hozzáadta a fiókok az alkalmazáshoz, bejelentkezhet az Authenticator alkalmazás használatával az eszközén. További információkért lásd: [jelentkezzen be az alkalmazás](microsoft-authenticator-app-phone-signin-faq.md).

- IOS rendszerű eszközökhöz is készíthető a fiók hitelesítő adatait, és a felhőhöz kapcsolódó alkalmazás beállításait, például a fiókok sorrendjét. További információkért lásd: [biztonsági mentés és helyreállítás a Microsoft Authenticator alkalmazás](microsoft-authenticator-app-backup-and-recovery.md).