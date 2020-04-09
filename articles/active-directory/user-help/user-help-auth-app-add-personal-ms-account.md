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
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984749"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Személyes Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazáshoz

Adja hozzá személyes Microsoft-fiókjait, például Outlook.com és Xbox LIVE-fiókjait a Microsoft Authenticator alkalmazáshoz a szabványos kétfaktoros ellenőrzési folyamathoz és a jelszó nélküli bejelentkezéshez.

- **Standard kétfaktoros ellenőrzési módszer.** Írja be felhasználónevét és jelszavát abba az eszközbe, amelybe bejelentkezik, majd válassza ki, hogy a Microsoft Authenticator alkalmazás küld-e értesítést, vagy szeretné átmásolni a kapcsolódó ellenőrző kódot a Microsoft Authenticator alkalmazás **Fiókok** képernyőjén.

- **Jelszó nélküli bejelentkezési módszer.** Írja be a felhasználónevét abba az eszközbe, amelybe bejelentkezik a személyes Microsoft-fiókjával, majd a mobileszközével ellenőrizze, hogy ön-e az ujjlenyomata, az arca vagy a PIN-kódja. Ehhez a módszerhez nem kell megadnia a jelszavát.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikkben leírt lépéseket.

Személyes Microsoft-fiókját a kétfaktoros ellenőrzés bekapcsolásával, majd a fiók alkalmazáshoz való hozzáadásával veheti fel. Nem kell bekapcsolnia a kétfaktoros ellenőrzést, ha csak jelszó nélküli bejelentkezést szeretne használni a fiókjához, de javasoljuk, hogy a fiók biztonsága érdekében kapcsolja be a kétfaktoros ellenőrzést.

## <a name="turn-on-two-factor-verification"></a>A kétfaktoros ellenőrzés bekapcsolása

1. A számítógépen nyissa meg a [Biztonsági alapok](https://account.microsoft.com/security) lapot, és jelentkezzen be személyes Microsoft-fiókjával. Például: alain@outlook.com.

2. A **Biztonság alapjai** lap alján válassza a **További biztonsági beállítások** hivatkozást.

    ![A biztonság alapjai lap kiemelt "További biztonsági beállítások" hivatkozással](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Nyissa meg a **Kétlépéses ellenőrzés** szakaszt, és kapcsolja be a **funkciót**. Itt is kikapcsolhatja, ha már nem szeretné használni a személyes fiókjával.

## <a name="add-your-microsoft-account-to-the-app"></a>Microsoft-fiók hozzáadása az alkalmazáshoz

1. Nyissa meg a Microsoft Authenticator alkalmazást mobileszközén.

1. Android on válassza a **Fiók hozzáadása lehetőséget** a jobb felső sarokban található Testreszabás és **vezérlés** ikonon.

    ![Android-fiókválasztó lapok](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    IOS rendszeren válassza a plusz ikont a jobb felső sarokban.

    ![a fiókkiválasztási élmény iOS-verziója](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. A **Fiók hozzáadása** lapon válassza a **Személyes fiók**lehetőséget.

1. A fiók hozzáadásához válassza a **Bejelentkezés a Microsofttal** lehetőséget. A QR-kód is használható, ha rendelkezésre áll, de a felhasználónévvel és jelszóval bejelentkezve bármikor hozzáadhatja fiókját.

    ![Válasszon microsoftos fiókot, vagy ha elérhető, beszkéselje a QR-kódot](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Jelentkezzen be személyes fiókjába a megfelelő e-mail címmel (például alain@outlook.com) , majd válassza a **Tovább**gombot.

    >[!Note]
    >Ha nem rendelkezik személyes Microsoft-fiókkal, [létrehozhat egyet.](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)

1. Írja be a jelszavát, majd válassza **a Bejelentkezés**lehetőséget. Az Ön személyes fiókja hozzáadódik a Microsoft Authenticator alkalmazáshoz.

## <a name="next-steps"></a>További lépések

- Miután hozzáadja fiókjait az alkalmazáshoz, bejelentkezhet az eszközön lévő Authenticator alkalmazással. További információt a [Bejelentkezés az alkalmazás használatával című témakörben](user-help-auth-app-sign-in.md)talál.

- Ha nem sikerül beszereznie a személyes Microsoft-fiókjához szükséges ellenőrző kódot, olvassa el a [Microsoft-fiók biztonsági adatai& ellenőrző kódok](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) című cikk **Hibaelhárítási kódokkal kapcsolatos hibáinak elhárítása** című részét.

- Az iOS rendszert futtató eszközökön a fiók hitelesítő adatairól és a kapcsolódó alkalmazásbeállításokról, például a fiókok sorrendjéről is biztonsági másolatot tarthat a felhőbe. További információt a [Biztonsági másolat és helyreállítás a Microsoft Authenticator alkalmazással című témakörben talál.](user-help-auth-app-backup-recovery.md)
