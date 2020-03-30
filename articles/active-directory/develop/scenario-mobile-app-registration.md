---
title: Webes API-kat hívó mobilalkalmazások regisztrálása | Azure
titleSuffix: Microsoft identity platform
description: További információ a webes API-kat meghívjaó mobilalkalmazás létrehozásáról (az alkalmazás kódkonfigurációja)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132441"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Webes API-kat hívó mobilalkalmazások regisztrálása

Ez a cikk a létrehozandó mobilalkalmazások regisztrálásához segítséget nyújt.

## <a name="supported-account-types"></a>Támogatott fióktípusok

A mobilalkalmazások által támogatott fióktípusok az engedélyezni kívánt élménytől és a használni kívánt folyamatoktól függenek.

### <a name="audience-for-interactive-token-acquisition"></a>Interaktív tokenek megszerzésének célközönsége

A legtöbb mobilalkalmazás interaktív hitelesítést használ. Ha az alkalmazás ezt a hitelesítési formát használja, bármilyen [fióktípusból](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)bejelentkezhet a felhasználókhoz.

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Az integrált Windows-hitelesítés, a felhasználónév-jelszó és a B2C közönsége

Ha univerzális Windows-platform (UWP) alkalmazással rendelkezik, integrált Windows-hitelesítéssel jelentkezhet be a felhasználókhoz. Az integrált Windows-hitelesítés vagy a felhasználónév-jelszó hitelesítés használatához az alkalmazásnak be kell jelentkeznie a felhasználókat a saját üzletági (LOB) fejlesztői bérlőjében. Független szoftverszállítói (ISV) forgatókönyv esetén az alkalmazás bejelentkezhet az Azure Active Directory-szervezetekfelhasználóiba. Ezek a hitelesítési folyamatok nem támogatottak a Microsoft személyes fiókok.

A felhasználókat a B2C-jogosultságnak és házirendnek megfelelő közösségi identitások használatával is bejelentkezhet. A módszer használatához csak interaktív hitelesítést és felhasználónév-jelszó hitelesítést használhat. A felhasználónév-jelszó hitelesítés jelenleg csak a Xamarin.iOS, a Xamarin.Android és az UWP rendszeren támogatott.

További információt a [Forgatókönyvek és a támogatott hitelesítési folyamatok,](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) valamint a támogatott platformok és nyelvek című [témakörben talál.](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Platform konfigurációja és átirányítása URI-k  

### <a name="interactive-authentication"></a>Interaktív hitelesítés

Amikor interaktív hitelesítést használó mobilalkalmazást hoz létre, a legkritikusabb regisztrációs lépés az átirányítási URI. Az interaktív hitelesítést a [Hitelesítés panel platformkonfigurációján keresztül **állíthatja** ](https://aka.ms/MobileAppReg)be.

Ez a felhasználói élmény lehetővé teszi, hogy az alkalmazás egyszeri bejelentkezést (SSO) kapjon a Microsoft Authenticator (és az Android-alapú Intune vállalati portál) segítségével. Támogatja az eszközkezelési házirendeket is.

Az alkalmazásregisztrációs portál előnézeti élményt nyújt az iOS- és Android-alkalmazások közvetített válaszURI-jának kiszámításához:

1. Az alkalmazásregisztrációs portálon válassza a **Hitelesítés** > **lehetőséget, próbálja ki az új felületet.**

   ![A Hitelesítés panel, ahol új felületet választ](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Válassza **a Platform hozzáadása**lehetőséget.

   ![Platform hozzáadása](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Ha a platformok listája támogatott, válassza az **iOS**lehetőséget.

   ![Mobilalkalmazás kiválasztása](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Adja meg a csomagazonosítóját, majd válassza a **Regisztráció**lehetőséget.

   ![Adja meg a csomagazonosítóját](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

A lépések befejezésekor az átirányítási URI kiszámításra kerül, mint az alábbi képen.

![Az eredményül kapott átirányítási URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Ha manuálisan szeretné konfigurálni az átirányítási URI-t, ezt az alkalmazásjegyzéken keresztül teheti meg. Itt van az ajánlott formátum a jegyzékfájlhoz:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Írja be például a`msauth.com.yourcompany.appName://auth`
- **Android**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Az Android-aláíráskivonatot a kiadási kulccsal vagy a hibakeresési kulccsal hozhatja létre a KeyTool paranccsal.

### <a name="username-password-authentication"></a>Felhasználónév-jelszó hitelesítés

Ha az alkalmazás csak felhasználónév-jelszó hitelesítést használ, nem kell újrairányított URI-t regisztrálnia az alkalmazáshoz. Ez a folyamat a Microsoft identity platform 2.0-s verziójának végpontjára irányuló oda-vissza utat tesz meg. Az alkalmazás nem lesz visszahívva egy adott URI-n. 

Azonban meg kell azonosítania az alkalmazást, mint egy nyilvános ügyfél alkalmazás. Ehhez indítsa el az alkalmazás **Hitelesítés** szakaszában. A **Speciális beállítások** alszakasz **Alapértelmezett ügyféltípus** bekezdésében, a **Treat alkalmazás nyilvános ügyfélként**való kezelése kérdésnél válassza az **Igen**lehetőséget.

## <a name="api-permissions"></a>API-engedélyek

A mobilalkalmazások a bejelentkezett felhasználó nevében api-kat hívnak fel. Az alkalmazásnak delegált engedélyeket kell kérnie. Ezeket az engedélyeket hatókörnek is nevezik. A kívánt élménytől függően statikusan kérhet delegált engedélyeket az Azure Portalon keresztül. Vagy kérheti őket dinamikusan futásidőben. 

Az engedélyek statikus regisztrálásával lehetővé teszi a rendszergazdák számára az alkalmazás egyszerű jóváhagyását. Statikus regisztráció ajánlott.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kódkonfiguráció](scenario-mobile-app-configuration.md)
