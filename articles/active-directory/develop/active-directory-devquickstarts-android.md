---
title: Ismerkedés az Azure AD Android |} Microsoft Docs
description: Hogyan hozhat létre egy Android-alkalmazás, amely az Azure AD bejelentkezési és a hívások Azure AD számára az API-k használatával OAuth2.0 védett.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155694"
---
# <a name="azure-ad-android-getting-started"></a>Ismerkedés az Azure AD Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Ha az Android-alkalmazás, Microsoft teszi egyszerű és magától értetődő, Azure Active Directory (Azure AD) felhasználó bejelentkezni. Az Azure AD lehetővé teszi, hogy az alkalmazás hozzáférési felhasználói adatokat a Microsoft Graph vagy a saját védett webes API-t. 

Az Azure AD Authentication Library (ADAL) Android library lehetővé teszi az alkalmazás használatának megkezdéséhez a [Microsoft Azure felhőbe](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io) támogatásával [ A Microsoft Azure Active Directory-fiókok](https://azure.microsoft.com/services/active-directory/) az iparági szabványoknak szabványos OAuth2 és OpenID Connect. Az alkalmazás tapasztalnak, beleértve a normál életciklusának mutatja be:

* A Microsoft Graph jogkivonat lekérése
* A token frissítése
* A Microsoft diagramhoz hívható
* A felhasználó kijelentkeztetése

A kezdéshez, ahol felhasználók létrehozása és egy alkalmazás regisztrálása az Azure AD-bérlő lesz szüksége. Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Forgatókönyv: A felhasználók bejelentkezés, és hívja meg a Microsoft Graph

![Topológia](./media/active-directory-android-topology.png)

Ez az alkalmazás minden Azure AD-fiókok használható. Egy- és több szervezeti forgatókönyvek (lépések bemutatása) támogatja. Bemutatja, hogyan a fejlesztők olyan alkalmazásokat hozhatnak létre a vállalati felhasználók csatlakozzanak, és hozzáférés az Azure + az Office 365 a Microsoft Graph keresztül. A hitelesítési folyamat során a végfelhasználók kell majd jelentkezzen be, és hozzájárul az engedélyeket, az alkalmazás, és bizonyos esetekben előfordulhat, hogy egy rendszergazdát, hogy az alkalmazásba hozzájárulás. A legtöbb, az ebben a mintában szereplő logikai bemutatja, hogyan hitelesítés a felhasználó, és ellenőrizze a basic hívása a Microsoft Graph.

## <a name="example-code"></a>Mintakód

A teljes mintakód található [a Githubon](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Lépéseket futtatásához

### <a name="register-and-configure-your-app"></a>Regisztrálja, és állítsa be alkalmazását 
Microsoft használatával regisztrált egy natív ügyfélalkalmazás rendelkeznie kell a [Azure-portálon](https://portal.azure.com). 

1. Alkalmazás regisztrálása elérése
    - Lépjen az [Azure Portalra](https://aad.portal.azure.com). 
    - Válassza ki ***az Azure Active Directory*** > ***App regisztrációk***. 

2. Az alkalmazás létrehozása
    - Válassza az **Új alkalmazás regisztrálása** elemet. 
    - Adja meg az alkalmazás nevét, a a **neve** mező. 
    - A **alkalmazástípus** válasszon **natív**. 
    - A **átirányítási URI-**, adja meg `http://localhost`. 

3. A Microsoft Graph konfigurálása
    - Válassza ki **beállítások > szükséges engedélyek**.
    - Válassza ki **Hozzáadás**, belső **API kiválasztása** válasszon ***Microsoft Graph***. 
    - Válassza ki az engedély **jelentkezzen be a felhasználói profil olvasása és**, majd kattintson a **kiválasztása** mentéséhez. 
        - Ez az engedély van leképezve a `User.Read` hatókör. 
    - Választható lehetőség: Belső **szükséges engedélyek > Windows Azure Active Directory**, távolítsa el a kijelölt engedélyt **jelentkezzen be a felhasználói profil olvasása és**. Így elkerülhető, a felhasználói hozzájárulás lap kétszer engedély listázása. 

4. Gratulálunk! Az alkalmazás konfigurálása sikeresen megtörtént. A következő szakaszban lesz szüksége:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>A mintakód letöltése

1. Klónozza a kódot.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Nyissa meg a minta az Android Studio.
    - Válassza ki **Android Studio projekt megnyitása**.

### <a name="configure-your-code"></a>A kód konfigurálása

Az ebben a kódmintában a konfiguráció található a ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** fájlt. 

1. Cserélje le a állandó `CLIENT_ID` rendelkező a `ApplicationID`.
2. Cserélje le a állandó `REDIRECT URI` rendelkező a `Redirect URI` korábban konfigurált (`http://localhost`). 

### <a name="run-the-sample"></a>Minta futtatása

1. Válassza ki **Build > Projekt tiszta**. 
2. Válassza ki **futtatása > futtatási app**. 
3. Az alkalmazás összeállítása, és meg kell néhány alapvető UX megjelenítése Amikor rákattint az `Call Graph API` gomb, emellett meg kell adni a bejelentkezéshez, és csendes hívja a Microsoft Graph API-t az új jogkivonatot. 

## <a name="important-info"></a>Fontos információ

1. Kivételt a [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) könyvtár beállítás és az új forgatókönyveiről és képességeiről konfigurálásáról további információt. 
2. A natív esetben az alkalmazás egy beágyazott webes nézet fogja használni, és nem hagynak az alkalmazás. A `Redirect URI` tetszőleges lehet. 
3. Talált problémákat, vagy a kérelmek rendelkezik? Hozzon létre egy probléma, vagy írjon a címkével ellátott Stackoverflow `azure-active-directory`. 

### <a name="cross-app-sso"></a>Alkalmazások közötti SSO
Ismerje meg, [az Android alkalmazások közötti SSO engedélyezése az ADAL használatával](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Hitelesítési telemetriai adat
az ADAL-könyvtár auth telemetria bekapcsolásával segítheti alkalmazásfejlesztők alkalmazások működésének megértése és jobb élmény build mutatja. Ez lehetővé teszi, hogy a bejelentkezés sikeres, az aktív felhasználók és a számos érdekes insights rögzítése. Használata hitelesítési telemetriai adatok is szükségesek alkalmazásfejlesztők telemetriai adatok összesítéséhez és eseményeket tároló szolgáltatáshoz.

További információt a hitelesítési telemetriai kivételt [ADAL Android hitelesítési telemetriai](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
