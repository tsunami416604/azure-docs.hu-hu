---
title: Webes API-kat meghívó Mobile apps konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást (az alkalmazás kódjának konfigurációja)
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
ms.openlocfilehash: 2c5160d88e390f9bfe905dfee0684983a7a49365
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919885"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Webes API-kat meghívó mobil alkalmazás – alkalmazás regisztrálása

Ez a cikk az alkalmazás regisztrációs utasításait tartalmazza a mobileszközök létrehozásához.

## <a name="supported-accounts-types"></a>Támogatott fiókok típusai

A mobileszközök által támogatott fióktípus az engedélyezni kívánt élménytől és a használni kívánt folyamattól függ.

### <a name="audience-for-interactive-token-acquisition"></a>Célközönség az interaktív jogkivonat-beszerzéshez

A legtöbb Mobile-alkalmazás interaktív hitelesítést használ. Ha ez a helyzet, akkor bármely [fióktípus](quickstart-register-app.md#register-a-new-application-using-the-azure-portal) bejelentkezhet a felhasználókba

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Az integrált hitelesítés, a Felhasználónév/jelszó és a B2C célközönsége

- Ha integrált Windows-hitelesítést (UWP-alkalmazásokat) vagy felhasználónevet vagy jelszót szeretne használni, az alkalmazásnak be kell jelentkeznie a saját bérlő (LOB-fejlesztő) vagy az Azure Active Directory-szervezetek (ISV-forgatókönyv) felhasználói számára. A Microsoft személyes fiókjai nem támogatják ezeket a hitelesítési folyamatokat
- Ha a felhasználók a B2C-szolgáltatót és a szabályzatot áthaladó közösségi identitásokkal jelentkeznek be, akkor csak az interaktív és a username-Password hitelesítést használhatja. Felhasználónév – a jelszó jelenleg csak a Xamarin. iOS, a Xamarin. Android és a UWP esetében támogatott.

A Big Picture esetében lásd: [forgatókönyvek és támogatott hitelesítési folyamatok](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) és [forgatókönyvek, valamint támogatott platformok és nyelvek](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Platform konfigurálása és URI-k átirányítása  

### <a name="interactive-authentication"></a>Interaktív hitelesítés

A mobileszköz interaktív hitelesítéssel való létrehozásakor a legkritikusabb regisztrációs lépés az átirányítási URI. Ez a hitelesítés panelen állítható be a [platform-konfiguráción](https://aka.ms/MobileAppReg)keresztül.

Ez a felhasználói élmény lehetővé teszi az alkalmazás számára, hogy egyszeri bejelentkezést (SSO) kapjon a Microsoft Authenticator (és az Androidon Intune Céges portál), valamint az eszköz-felügyeleti házirendek támogatásával.

Vegye figyelembe, hogy az alkalmazás regisztrációs portálján elérhető egy előzetes verzió, amely segítséget nyújt az iOS-és Android-alkalmazások felügyelt válasz URI-azonosítójának kiszámításához:

1. Az alkalmazás regisztrálása beállításban válassza a **hitelesítés** és kiválasztás **kipróbálom az új felhasználói élményt**
   ![rendszerképet](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Válassza a **platform hozzáadása**
   ![rendszerkép lehetőséget](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Ha a platformok listája támogatott, válassza az **iOS**
   ![a rendszerképet](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Adja meg a köteg AZONOSÍTÓját a kért módon, majd nyomja meg
   ![-rendszerkép **regisztrálása**](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Az átirányítási URI-t az Ön számára számítjuk ki.
   ![kép](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Ha manuálisan szeretné konfigurálni az átirányítási URI-t, ezt az alkalmazás Jegyzékfájlján keresztül teheti meg. Az ajánlott formátum a következő:

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (például "msauth. com. yourcompany. appName://auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Az androidos aláírás kivonata a Key Tool parancs használatával hozható létre a kiadási vagy a hibakeresési kulcsokkal.

### <a name="username-password"></a>Felhasználónév jelszava

Ha az alkalmazás csak a username/Password nevet használja, nem kell regisztrálnia az alkalmazás átirányítási URI-JÁT. Valójában ez a folyamat a Microsoft Identity platform v 2.0-végpontját is átveszi, és az alkalmazás nem hívható vissza semmilyen konkrét URI-ra. Azonban meg kell adnia, hogy az alkalmazás egy nyilvános ügyfélalkalmazás. Ezt a konfigurációt az alkalmazás **hitelesítési** szakasza fogja megvalósítani, a **Speciális beállítások** alszakaszban pedig válassza az **Igen**lehetőséget az **alkalmazás nyilvános ügyfélként való kezelésére** szolgáló kérdésnél (az **alapértelmezett ügyfél típusa** bekezdésben).

## <a name="api-permissions"></a>API-engedélyek

A Mobile Applications a bejelentkezett felhasználó nevében hívja meg az API-kat. Az alkalmazásnak delegált engedélyeket kell igényelnie, más néven hatóköröknek kell lennie. A kívánt élménytől függően ez statikusan végezhető el a Azure Portalon vagy dinamikusan, futásidőben. Az engedélyek statikus regisztrálása lehetővé teszi, hogy a rendszergazdák egyszerűen jóváhagyják az alkalmazást, és ajánlottak legyenek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kód konfigurálása](scenario-mobile-app-configuration.md)
