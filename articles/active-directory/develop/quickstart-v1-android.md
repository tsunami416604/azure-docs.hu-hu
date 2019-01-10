---
title: Felhasználók bejelentkeztetése és a Microsoft Graph API meghívása egy Android-alkalmazásból | Microsoft Docs
description: Ismerje meg, hogyan jelentkeztethet be felhasználókat és hívhatja meg a Microsoft Graph API-t saját Android-alkalmazásából.
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
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: d5d284e8f7756e3e9bff5b08d28a8ed911a60572
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158873"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Gyors útmutató: A felhasználók és a Microsoft Graph API hívása Androidos alkalmazásokból

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Ha Android-alkalmazást fejleszt, a Microsoft egyszerűvé és egyértelművé teszi a felhasználók bejelentkeztetését az Azure Active Directory (Azure AD) szolgáltatásba. Az Azure AD lehetővé teszi, hogy alkalmazása hozzáférjen a felhasználók adataihoz a Microsoft Graph vagy az Ön saját védett webes API-ja használatával.

Az Azure AD Authentication Library (ADAL) Androidos függvénytár lehetővé teszi az alkalmazás használatának megkezdéséhez a [Microsoft Azure Felhőszolgáltatásbeli](https://cloud.microsoft.com) és [Microsoft Graph API](https://developer.microsoft.com/graph) támogatásával [Microsoft Azure Active Directory-fiókok](https://azure.microsoft.com/services/active-directory/) használatával az iparági szabványos OAuth 2.0 és OpenID Connect.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* Jogkivonat lekérése a Microsoft Graph-hoz
* Jogkivonat frissítése
* A Microsoft Graph meghívása
* Felhasználó kijelentkeztetése

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége lesz egy Azure AD-bérlőre, ahol felhasználókat hozhat létre és regisztrálhat egy alkalmazást. Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Forgatókönyv: A felhasználók, és hívja a Microsoft Graph

![Topológia](./media/quickstart-v1-android/active-directory-android-topology.png)

Az alkalmazást minden Azure AD-fiókhoz használhatja. Az egybérlős és a több-bérlős forgatókönyveket is támogatja (az egyes lépésekben részletezzük). A cikk bemutatja, hogyan hozhat létre egy olyan alkalmazást, amely csatlakoztatja a vállalati felhasználókat, és hozzáfér az Azure- és O365-adataikhoz a Microsoft Graph-on keresztül. A hitelesítési folyamat során a végfelhasználónak be kell majd jelentkeznie, és hozzájárulását kell adnia az alkalmazás engedélyeihez, néhány esetben pedig szükség lehet egy rendszergazdára is a hozzájárulás megadásához. A minta logikájának nagy része megmutatja, hogyan hitelesíthet egy végfelhasználót, illetve hogyan kezdeményezhet egy alapszintű hívást a Microsoft Graph-ba.

## <a name="sample-code"></a>Mintakód

A minta teljes kódját megtalálja [a Githubon](https://github.com/Azure-Samples/active-directory-android).

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

## <a name="step-1-register-and-configure-your-app"></a>1. lépés: Regisztráljon, és az alkalmazás konfigurálása

Szüksége lesz egy natív ügyfélalkalmazásra, amely az [Azure Portalon](https://portal.azure.com) keresztül regisztrálva lett a Microsoftnál.

1. Az alkalmazásregisztráció elkezdése
    - Lépjen az [Azure Portalra](https://aad.portal.azure.com).
    - Válassza az ***Azure Active Directory*** > ***Appok regisztrálása*** elemet.

2. Az alkalmazás létrehozása
    - Válassza az **Új alkalmazás regisztrálása** elemet.
    - A **Név** mezőbe írja be az alkalmazás nevét.
    - Az **Alkalmazás típusa** elemnél válassza a **Natív** lehetőséget.
    - Az **Átirányítási URI** elemnél írja be a következőt: `http://localhost`.

3. A Microsoft Graph konfigurálása
    - Válassza a **Beállítások > Szükséges engedélyek** elemet.
    - Válassza a **Hozzáadás** elemet, majd az **API kiválasztása** elemen belül válassza a ***Microsoft Graph*** lehetőséget.
    - Válassza ki a **Beléptetés és felhasználói profil olvasása** elemet, majd kattintson a **Kiválasztás** gombra a mentéshez.
        - Ez az engedély a következő hatókörre lesz leképezve: `User.Read`.
    - Nem kötelező: Belül **szükséges engedélyek > Windows Azure Active Directory**, távolítsa el a kiválasztott engedélyt **jelentkezzen be a felhasználói profil olvasása és**. Így elkerülhető, hogy a felhasználói hozzájárulások oldala kétszer listázza az engedélyt.

4. Gratulálunk! Az alkalmazás konfigurálása kész. A következő szakaszban a következőre lesz szüksége:
    - `Application ID`
    - `Redirect URI`

## <a name="step-2-get-the-sample-code"></a>2. lépés: A mintakód letöltése

1. Klónozza a kódot.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Nyissa meg a mintát az Android Studióban.
    - Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

## <a name="step-3-configure-your-code"></a>3. lépés: A kód konfigurálása

A mintakódhoz tartozó összes konfigurációt megtalálja az ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** fájlban.

1. Cserélje le a `CLIENT_ID` állandót az `ApplicationID` értékére.
2. Cserélje le a `REDIRECT URI` állandót az előbb konfigurált `Redirect URI`-értékre (`http://localhost`).

## <a name="step-4-run-the-sample"></a>4. lépés: Minta futtatása

1. Válassza a **Build > Clean Project** (Létrehozás > Új projekt) lehetőséget.
2. Válassza a **Run > Run app** (Futtatás > Alkalmazás futtatása) lehetőséget.
3. Az alkalmazásnak létre kell jönnie, és egy alapszintű felhasználói felületet kell megjelenítenie. A `Call Graph API` gomb megnyomása után egy bejelentkezést fog kérni Öntől, majd csendben meghívja a Microsoft Graph API-t az új jogkivonattal.

## <a name="next-steps"></a>További lépések

1. Látogasson el az [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) webhelyre a kódtárak működésével, illetve az új forgatókönyvek és képességek konfigurálásával kapcsolatos további információkért.
2. Natív forgatókönyvekben az alkalmazás egy beágyazott webes nézetet fog használni, és nem lép ki az alkalmazásból. A `Redirect URI` tetszőlegesen állítható be.
3. Problémába ütközött, vagy kérése lenne? Probléma létrehozása, illetve közzététele a Stack overflow-n a címkével ellátott `azure-active-directory`.

### <a name="cross-app-sso"></a>Alkalmazások közötti SSO

Ismerje meg, [hogyan engedélyezheti Androidon az alkalmazások közötti SSO-t az ADAL segítségével](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Hitelesítési telemetria

Az ADAL-kódtár elérhetővé teszi a hitelesítési telemetriát annak érdekében, hogy segítsen az alkalmazásfejlesztőknek alkalmazásuk működésének megértésében és jobb felhasználói élmények létrehozásában. Ez lehetővé teszi a sikeres bejelentkezések, aktív felhasználók és számos más érdekes elemzés rögzítését. A hitelesítési telemetria használatához az alkalmazásfejlesztőknek létre kell hozniuk egy telemetriaszolgáltatást, amely összesíti és tárolja az eseményeket.

A hitelesítési telemetriához kapcsolódó további információkért keresse fel az [ADAL Android hitelesítési telemetria](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry) oldalát.
