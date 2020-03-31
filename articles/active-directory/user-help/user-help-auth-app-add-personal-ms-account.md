---
title: Személyes Microsoft-fiók hozzáadása a Microsoft Authenticator alkalmazáshoz – Azure AD
description: A kétfaktoros ellenőrzés során személyes Microsoft-fiókokat, például Outlook.com vagy Xbox LIVE-fiókot adhat hozzá a Microsoft Authenticator alkalmazáshoz, hogy ellenőrizze személyazonosságát.
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
ms.openlocfilehash: 289c00e28fd82b78b321732628ff85d22c64cabf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062302"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Személyes Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz

Adja hozzá személyes Microsoft-fiókjait, például a Outlook.com és az Xbox LIVE-fiókját a Microsoft Authenticator alkalmazáshoz a szabványos kétfaktoros ellenőrzési folyamathoz és a jelszó nélküli telefonbejelentkezési módszerhez.

- **Standard kétfaktoros ellenőrzési módszer.** Írja be felhasználónevét és jelszavát abba az eszközbe, amelybe bejelentkezik, majd válassza ki, hogy a Microsoft Authenticator alkalmazás küld-e értesítést, vagy szeretné átmásolni a kapcsolódó ellenőrző kódot a Microsoft Authenticator alkalmazás **Fiókok** képernyőjén.

- **Jelszó nélküli bejelentkezési módszer.** Írja be a felhasználónevét abba az eszközbe, amelybe bejelentkezik a személyes Microsoft-fiókjához, majd a mobileszközével ellenőrizze, hogy Ön az ujjlenyomata, az arca vagy a PIN-kódja segítségével. Ehhez a módszerhez nem kell megadnia a jelszavát.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben leírt lépéseket.

## <a name="add-your-personal-microsoft-account"></a>Személyes Microsoft-fiók hozzáadása

Személyes Microsoft-fiókját a kétfaktoros ellenőrzés bekapcsolásával, majd a fiók alkalmazáshoz való hozzáadásával veheti fel.

>[!Note]
>ha csak jelszó nélküli bejelentkezést tervez személyes Microsoft-fiókjához, nem kell bekapcsolnia a kétfaktoros ellenőrzést. A fiók biztonsága érdekében azonban azt javasoljuk, hogy kapcsolja be a kétfaktoros ellenőrzést.

### <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen nyissa meg a [Biztonsági alapok](https://account.microsoft.com/security) lapot, és jelentkezzen be személyes Microsoft-fiókjával. Például: alain@outlook.com.

2. A **Biztonság alapjai** lap alján válassza a **További biztonsági beállítások** hivatkozást.

    ![A biztonság alapjai lap kiemelt "További biztonsági beállítások" hivatkozással](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Nyissa meg a **Kétlépéses ellenőrzés** szakaszt, és kapcsolja be a **funkciót**. Itt is kikapcsolhatja, ha már nem szeretné használni a személyes fiókjával.

### <a name="add-your-microsoft-account-to-the-app"></a>Microsoft-fiók hozzáadása az alkalmazáshoz

1. Nyissa meg a Microsoft Authenticator alkalmazást mobileszközén.

2. Válassza a **Fiók hozzáadása elemet** a jobb felső sarokban lévő Testreszabás és **vezérlés** ikonon.

    ![Fiókok lap, kiemelve a Testreszabás és vezérlés ikont](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. A **Fiók hozzáadása** lapon válassza a **Személyes fiók**lehetőséget.

4. Jelentkezzen be személyes fiókjába a megfelelő e-mail címmel (például alain@outlook.com) , majd válassza a **Tovább**gombot.

    >[!Note]
    >Ha nem rendelkezik személyes Microsoft-fiókkal, itt is létrehozhat egyet.

5. Írja be a jelszavát, majd válassza **a Bejelentkezés**lehetőséget.

    Az Ön személyes fiókja hozzáadódik a Microsoft Authenticator alkalmazáshoz.

## <a name="next-steps"></a>További lépések

- Miután hozzáadja fiókjait az alkalmazáshoz, bejelentkezhet az eszközön lévő Authenticator alkalmazással. További információt a [Bejelentkezés az alkalmazás használatával című témakörben](user-help-auth-app-sign-in.md)talál.

- Ha nem sikerül beszereznie a személyes Microsoft-fiókjához szükséges ellenőrző kódot, olvassa el a [Microsoft-fiók biztonsági adatai& ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikk **Hibaelhárítási kódokkal kapcsolatos hibáinak elhárítása** című részét.

- Az iOS rendszert futtató eszközökön a fiók hitelesítő adatairól és a kapcsolódó alkalmazásbeállításokról, például a fiókok sorrendjéről is biztonsági másolatot tarthat a felhőbe. További információt a [Biztonsági másolat és helyreállítás a Microsoft Authenticator alkalmazással című témakörben talál.](user-help-auth-app-backup-recovery.md)
