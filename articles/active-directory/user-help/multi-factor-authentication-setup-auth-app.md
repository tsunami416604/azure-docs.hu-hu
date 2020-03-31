---
title: Hitelesítő alkalmazás beállítása kétfaktoros ellenőrzési módszerként – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be a Microsoft Authenticator alkalmazást kétfaktoros ellenőrzési módszerként.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 6ab1f7c97173021cc112a5f117469abd74ac954d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062574"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Hitelesítő alkalmazás beállítása kétfaktoros ellenőrzési módszerként

Beállíthat egy hitelesítő alkalmazást, hogy értesítést küldjön a mobileszközére, vagy hogy biztonsági ellenőrzési módszerként ellenőrző kódot küldjön Önnek. Nem kell használnia a Microsoft Authenticator alkalmazást, és a beállítási folyamat során másik alkalmazást is kiválaszthat. Ez a cikk azonban a Microsoft Authenticator alkalmazást használja.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben leírt lépéseket.

>[!Note]
> Ha a Mobilalkalmazás beállítás szürkén jelenik meg, lehetséges, hogy a szervezet nem engedélyezi a hitelesítési alkalmazás használatát az ellenőrzéshez. Ebben az esetben másik módszert kell választania, vagy további segítségért forduljon a rendszergazdához.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>A Microsoft Authenticator alkalmazás beállítása értesítések küldéséhez

1. A **További biztonsági ellenőrzés** lapon válassza a **Mobilalkalmazás** lehetőséget az **1.**

2. Válassza az **Értesítések fogadása ellenőrzésre** lehetőséget a **Hogyan szeretné használni a mobilalkalmazás-területet,** majd válassza a Beállítás **lehetőséget.**

    ![További biztonsági ellenőrző oldal a mobilalkalmazás és az értesítések lehetőséggel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Megjelenik **a Mobilalkalmazás konfigurálása** lap.

    ![A QR-kódot tartalmazó képernyő](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a Fiók **hozzáadása** elemet a jobb felső sarokban a **Testreszabás és vezérlés** ikonon, majd válassza a Munkahelyi vagy iskolai fiók **lehetőséget.**

    >[!Note]
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók hozzáadása az alkalmazáshoz című témakörben](user-help-auth-app-add-account-manual.md)talál további információt.

4. Az eszköz kamerájával szidhatja a QR-kódot a **Mobilalkalmazás konfigurálása** képernyőn a számítógépen, majd válassza a **Tovább**gombot.

5. Térjen vissza a számítógéphez és a **További biztonsági ellenőrzés** lapra, győződjön meg arról, hogy a konfiguráció sikeres volt, és válassza a **Tovább**gombot.

    ![További biztonsági ellenőrző lap, sikeres üzenettel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    A hitelesítő alkalmazás tesztként értesítést küld a mobileszközére.

6. Mobileszközén válassza a **Jóváhagyás**lehetőséget.

7. A számítógépen adja hozzá a mobileszköz telefonszámát a **3.** **Next**

    Javasoljuk, hogy adja meg mobiltelefonszámát, hogy biztonsági másolatként működjön, ha bármilyen okból nem tudja elérni vagy használni a mobilalkalmazást.

8. **4. lépés: Továbbra is használja a meglévő alkalmazásterületet,** másolja a megadott alkalmazásjelszót, és illessze be biztonságos helyre.

    ![A További biztonsági ellenőrzés lap alkalmazásjelszavak területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Az alkalmazásjelszó régebbi alkalmazásokkal való használatáról az [Alkalmazásjelszavak kezelése című](multi-factor-authentication-end-user-app-passwords.md)témakörben talál további információt. Csak akkor kell alkalmazásjelszavakat használnia, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

9. Válassza a **Done** (Kész) lehetőséget.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>A Microsoft Authenticator alkalmazás beállítása ellenőrző kódok használatára

1. A **További biztonsági ellenőrzés** lapon válassza a **Mobilalkalmazás** lehetőséget az **1.**

2. Válassza az **Ellenőrző kód használata lehetőséget** a Hogyan szeretné használni a **mobilalkalmazás-területet,** majd válassza a **Beállítás lehetőséget.**

    ![További biztonsági ellenőrző oldal a mobilalkalmazás és az értesítések lehetőséggel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Megjelenik **a Mobilalkalmazás konfigurálása** lap.

    ![A QR-kódot tartalmazó képernyő](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a Fiók **hozzáadása** elemet a jobb felső sarokban a **Testreszabás és vezérlés** ikonon, majd válassza a Munkahelyi vagy iskolai fiók **lehetőséget.**

    >[!Note]
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók hozzáadása az alkalmazáshoz című témakörben](user-help-auth-app-add-account-manual.md)talál további információt.

4. Az eszköz kamerájával szidhatja a QR-kódot a **Mobilalkalmazás konfigurálása** képernyőn a számítógépen, majd válassza a **Tovább**gombot.

5. Térjen vissza a számítógéphez és a **További biztonsági ellenőrzés** lapra, győződjön meg arról, hogy a konfiguráció sikeres volt, és válassza a **Tovább**gombot.

    ![További biztonsági ellenőrző lap, sikeres üzenettel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    A hitelesítő alkalmazás tesztként ellenőrző kódot kér.

6. A Microsoft Authenticator alkalmazásban görgessen le a munkahelyi vagy iskolai fiókjához, másolja és illessze be a 6 jegyű kódot az alkalmazásból a **2.** **Verify**

    ![További biztonsági ellenőrző lap, ellenőrzőkód-vizsgálattal](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. A számítógépen adja hozzá a mobileszköz telefonszámát a **3.** **Next**

    Javasoljuk, hogy adja meg mobiltelefonszámát, hogy biztonsági másolatként működjön, ha bármilyen okból nem tudja elérni vagy használni a mobilalkalmazást.

8. **4. lépés: Továbbra is használja a meglévő alkalmazásterületet,** másolja a megadott alkalmazásjelszót, és illessze be biztonságos helyre.

    ![A További biztonsági ellenőrzés lap alkalmazásjelszavak területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Az alkalmazásjelszó régebbi alkalmazásokkal való használatáról az [Alkalmazásjelszavak kezelése című](multi-factor-authentication-end-user-app-passwords.md)témakörben talál további információt. Csak akkor kell alkalmazásjelszavakat használnia, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

9. Válassza a **Done** (Kész) lehetőséget.

## <a name="next-steps"></a>További lépések

A kétfaktoros ellenőrzési módszer beállítása után további módszereket adhat hozzá, kezelheti a beállításokat és az alkalmazásjelszavakat, bejelentkezhet, vagy segítséget kaphat néhány gyakori, kéttényezős ellenőrzéssel kapcsolatos problémához.

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-signin.md)

- [Segítség a kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-troubleshoot.md)
