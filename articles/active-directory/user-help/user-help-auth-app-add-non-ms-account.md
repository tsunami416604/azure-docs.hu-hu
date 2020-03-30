---
title: Nem Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz – Azure AD
description: Adjon hozzá nem Microsoft-fiókokat, például a Google vagy a Facebook számára a Microsoft Authenticator alkalmazáshoz, hogy ellenőrizze személyazonosságát a kétfaktoros ellenőrzés használata közben.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8650d0170e8ff910140e2b432dd1c998d19e72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063951"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Nem Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz

Adja hozzá nem Microsoft-fiókjait, például a Google, a Facebook vagy a GitHub esetében a Microsoft Authenticator alkalmazáshoz a kétfaktoros ellenőrzéshez. A Microsoft Authenticator alkalmazás minden olyan alkalmazással működik, amely kétfaktoros ellenőrzést használ, és minden olyan fiókkal, amely támogatja az időalapú egyszeri jelszó (TOTP) szabványokat.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben leírt lépéseket.

## <a name="add-personal-accounts"></a>Személyes fiókok hozzáadása

Általában minden személyes fiókjához a következőket kell tennie:

1. Jelentkezzen be a fiókjába, majd kapcsolja be a kétfaktoros ellenőrzést az eszközével vagy a számítógépével.

2. Adja hozzá a fiókot a Microsoft Authenticator alkalmazáshoz. Előfordulhat, hogy a rendszer a folyamat részeként qr-kód beszkéselésre kéri.

    >[!Note]
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók hozzáadása az alkalmazáshoz című témakörben](user-help-auth-app-add-account-manual.md)talál további információt.

Itt biztosítjuk a folyamatot a Facebook-, Google-, GitHub- és Amazon-fiókjaiszámára, de ez a folyamat ugyanaz minden más alkalmazásesetében, például az Instagram, a Netflix vagy az Adobe esetében.

## <a name="add-your-google-account"></a>Google-fiók hozzáadása

Adja hozzá Google-fiókját a kétfaktoros ellenőrzés bekapcsolásával, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen válassza https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcomea , válassza az **Első lépések**lehetőséget, majd ellenőrizze személyazonosságát.

2. Az oldalon található lépéseket követve bekapcsolhatja a kétlépéses ellenőrzést személyes Google-fiókjában.

### <a name="add-your-google-account-to-the-app"></a>Google-fiók hozzáadása az alkalmazáshoz

1. A számítógép Google lapján nyissa meg az **Alternatív második lépés beállítása szakaszt,** és válassza a **Beállítás** lehetőséget a **Hitelesítő alkalmazásból** szakaszban.

2. A **Kódok beírása a Hitelesítő alkalmazásból** lapon válassza az **Android** vagy **az iPhone** lehetőséget a telefon típusától függően, majd válassza a **Tovább**gombot.

    Kap egy QR-kódot, amelysegítségével automatikusan társíthatja fiókját a Microsoft Authenticator alkalmazáshoz. Ne zárja be ezt az ablakot.

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **Fiók hozzáadása** lehetőséget a jobb felső sarokban lévő **Testreszabás és vezérlés** ikonon, majd az Egyéb fiók **(Google, Facebook stb.)** lehetőséget.

4. Az eszköz kamerájával beszkaporolhatja a QR-kódot a **Számítógép Hitelesítő beállítása** lapján.

    >[!Note]
    >Ha a kamera nem működik megfelelően, manuálisan is megadhatja a QR-kódot és az URL-címet.

5. Tekintse át az eszközön található Microsoft Authenticator alkalmazás **Fiókok** lapját, és győződjön meg arról, hogy a fiókadatai helyesek, és hogy van-e társított hatjegyű ellenőrző kód.

    A nagyobb biztonság érdekében az ellenőrző kód 30 másodpercenként változik, megakadályozva, hogy valaki többször is használja a kódot.

6. Válassza a **Tovább** gombot a Számítógép **Hitelesítő beállítása** lapján, írja be a Google-fiókjához tartozó alkalmazásban megadott hatjegyű ellenőrző kódot, majd válassza az **Ellenőrzés lehetőséget.**

7. A rendszer ellenőrzi a fiókját, és a **Kész** lehetőséget választva bezárhatja a **Hitelesítő beállítása** lapot.

    >[!NOTE]
    >A kétfaktoros ellenőrzésről és a Google-fiókjáról a [Kétlépcsős azonosítás bekapcsolása](https://support.google.com/accounts/answer/185839) és [a További információ a kétlépcsős azonosításról](https://www.google.com/landing/2step/help.html)című témakörben olvashat bővebben.

## <a name="add-your-facebook-account"></a>Facebook-fiók hozzáadása

Add hozzá a Facebook-fiókodat a kétfaktoros ellenőrzés bekapcsolásával, majd a fiók alkalmazáshoz való hozzáadásával.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen nyissa meg a Facebookot, válassza a jobb felső sarokban található legördülő menüt, majd válassza a **Beállítások** > **biztonsága és a Bejelentkezés**lehetőséget.

    Megjelenik **a Biztonság és bejelentkezés** lap.

2. Lépjen le a **Kétfaktoros hitelesítés** használata csoport **Kétfaktoros hitelesítése** beállításra, és válassza a **Szerkesztés**lehetőséget.

    Megjelenik **a Kétfaktoros hitelesítés** lap.

3. Válassza **a Bekapcsolás**lehetőséget.

### <a name="add-your-facebook-account-to-the-app"></a>Facebook-fiók hozzáadása az alkalmazáshoz

1. A számítógép Facebook-lapján lépjen a **Biztonsági másolat hozzáadása** szakaszra, és válassza a **Telepítés** lehetőséget a **Hitelesítés alkalmazás** területen.

    Kap egy QR-kódot, amelysegítségével automatikusan társíthatja fiókját a Microsoft Authenticator alkalmazáshoz. Ne zárja be ezt az ablakot.

2. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **Fiók hozzáadása** lehetőséget a jobb felső sarokban lévő **Testreszabás és vezérlés** ikonon, majd az Egyéb fiók **(Google, Facebook stb.)** lehetőséget.

3. A készülék kamerájával beszkacezheti a QR-kódot a számítógép **kétfaktoros hitelesítési** lapjáról.

    >[!Note]
    >Ha a kamera nem működik megfelelően, manuálisan is megadhatja a QR-kódot és az URL-címet.

4. Tekintse át az eszközön található Microsoft Authenticator alkalmazás **Fiókok** lapját, és győződjön meg arról, hogy a fiókadatai helyesek, és hogy van-e társított hatjegyű ellenőrző kód.

    A nagyobb biztonság érdekében az ellenőrző kód 30 másodpercenként változik, megakadályozva, hogy valaki többször is használja a kódot.

5. Válaszd a **Tovább** lehetőséget a számítógép **kétfaktoros hitelesítési** oldalán, majd írd be a Facebook-fiókod alkalmazásában megadott hatjegyű ellenőrző kódot.

    A rendszer ellenőrzi a fiókját, és most már használhatja az alkalmazást a fiók igazolására.

    >[!NOTE]
    >A kétfaktoros ellenőrzésről és a Facebook-fiókról a [Mi a kétfaktoros hitelesítés, és hogyan működik?](https://www.facebook.com/help/148233965247823)

## <a name="add-your-github-account"></a>GitHub-fiók hozzáadása

Adja hozzá GitHub-fiókját a kétfaktoros ellenőrzés bekapcsolásával, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen nyissa meg a GitHubot, jelölje ki a képet a jobb felső sarokban, majd válassza a **Beállítások lehetőséget.**

    Megjelenik **a kétfaktoros hitelesítés** i.

2. Válassza **Security** a **Biztonság** lehetőséget a Személyes beállítások oldalsávon, majd a **Kétfaktoros hitelesítés engedélyezése** a **kétfaktoros hitelesítési** területen lehetőséget.

### <a name="add-your-github-account-to-the-app"></a>GitHub-fiók hozzáadása az alkalmazáshoz

1. A számítógép **Kétfaktoros hitelesítés** lapján válassza a **Beállítás alkalmazás használatával**lehetőséget.

2. Mentse a helyreállítási kódokat, hogy a hozzáférés elvesztése esetén visszajuthass a fiókjába, majd válassza a **Tovább**gombot. 

    A kódokat úgy mentheti, hogy letölti őket az eszközre, nyomtatott példányt nyomtat, vagy bemásolja őket egy jelszókezelő eszközbe.

3. A **Kétfaktoros hitelesítés** lapon válassza a **Beállítás alkalmazással**lehetőséget.

    Az oldal megváltozik, hogy qr-kódot jelenítsen meg. Ne zárja be ezt a lapot.

4. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **Fiók hozzáadása** lehetőséget a jobb felső sarokban lévő **Testreszabás és vezérlés** ikonon, válassza az Egyéb fiók **(Google, Facebook stb.) lehetőséget,** majd válassza a **szövegkód megadása** lehetőséget a lap tetején lévő szövegből.

    A Microsoft Authenticator alkalmazás nem tudja beszkaparani a QR-kódot, ezért manuálisan kell megadnia a kódot.

5. Írjon be egy **fióknevet** (például GitHub), írja be a **Titkos kulcsot** a **4.**

6. A számítógép **kétfaktoros hitelesítőlapján** írja be a GitHub-fiók alkalmazásában megadott hatjegyű ellenőrző kódot, majd válassza az **Engedélyezés lehetőséget.**

    Az alkalmazás **Fiókok** lapján megjelenik a fiók neve és egy hatjegyű ellenőrző kód. A nagyobb biztonság érdekében az ellenőrző kód 30 másodpercenként változik, megakadályozva, hogy valaki többször is használja a kódot.

    >[!NOTE]
    >A kétfaktoros ellenőrzésről és a GitHub-fiókról a [Kétfaktoros hitelesítés – további információért.](https://help.github.com/articles/about-two-factor-authentication/)

## <a name="add-your-amazon-account"></a>Adja hozzá Amazon-fiókját

Adja hozzá Amazon-fiókját a kétfaktoros ellenőrzés bekapcsolásával, majd adja hozzá a fiókot az alkalmazáshoz.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen nyissa meg az Amazont, válassza a **Fiók & listák** legördülő menüt, majd a Saját fiók **lehetőséget**.

2. Válassza **a Bejelentkezés & biztonság**, bejelentkezés amazon fiókjába, majd a Szerkesztés lehetőséget a Speciális biztonsági **beállítások** területen. **Edit**

    Megjelenik **a Speciális biztonsági beállítások** lap.

3. Válassza **az Első lépések lehetőséget.**

4. Válassza a **Hitelesítő alkalmazás** lehetőséget a **Kódok fogadásának módjáról** lapon.

    Az oldal megváltozik, hogy qr-kódot jelenítsen meg. Ne zárja be ezt a lapot.

5. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **Fiók hozzáadása** lehetőséget a jobb felső sarokban lévő **Testreszabás és vezérlés** ikonon, majd az Egyéb fiók **(Google, Facebook stb.)** lehetőséget.

6. A készülék kamerájával beszkacezheti a QR-kódot a **Válassza ki, hogyan kapja meg** a kódokat a számítógépen.

    >[!Note]
    >Ha a kamera nem működik megfelelően, manuálisan is megadhatja a QR-kódot és az URL-címet.

7. Tekintse át az eszközön található Microsoft Authenticator alkalmazás **Fiókok** lapját, és győződjön meg arról, hogy a fiókadatai helyesek, és hogy van-e társított hatjegyű ellenőrző kód.

    A nagyobb biztonság érdekében az ellenőrző kód 30 másodpercenként változik, megakadályozva, hogy valaki többször is használja a kódot.

8. A **Válassza ki, hogyan fogja kapni** a kódokat a számítógépen, írja be az Amazon-fiókjához az alkalmazásban megadott hatjegyű ellenőrző kódot, majd válassza a Kód ellenőrzése lehetőséget, és folytassa a **folytatást.**

9. Végezze el a regisztrációs folyamat további részét, beleértve egy biztonsági mentés-ellenőrzési módszer, például egy szöveges üzenet hozzáadását, majd válassza a **Kód küldése**lehetőséget.

10. A **Biztonsági mentés ellenőrzési módszerének hozzáadása** lapon írja be az Amazon-fiókjához a biztonsági mentés ellenőrzési módszerében megadott hatjegyű ellenőrző kódot, majd válassza **a Kód ellenőrzése lehetőséget, és folytassa**a munkát.

11. A **Majdnem kész** lapon döntse el, hogy a számítógépet megbízható eszközzé teszi-e, majd válassza **a Megvan lehetőséget. Kapcsolja be a kétlépcsős azonosítást**.

    Megjelenik **a Speciális biztonsági beállítások** lap, amely a frissített kétfaktoros ellenőrzési adatokat jeleníti meg.

    >[!NOTE]
    >A kétfaktoros ellenőrzésről és az Amazon-fiókjáról a [Kétlépéses ellenőrzés](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) és [a Kétlépéses ellenőrzéssel való bejelentkezés](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- Miután hozzáadja fiókjait az alkalmazáshoz, bejelentkezhet az eszközön lévő Authenticator alkalmazással. További információt a [Bejelentkezés az alkalmazás használatával című témakörben](user-help-auth-app-sign-in.md)talál.

- Az iOS rendszert futtató eszközökön a fiók hitelesítő adatairól és a kapcsolódó alkalmazásbeállításokról, például a fiókok sorrendjéről is biztonsági másolatot tarthat a felhőbe. További információt a [Biztonsági másolat és helyreállítás a Microsoft Authenticator alkalmazással című témakörben talál.](user-help-auth-app-backup-recovery.md)
