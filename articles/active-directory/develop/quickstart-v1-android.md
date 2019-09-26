---
title: Felhasználók bejelentkeztetése és a Microsoft Graph API meghívása egy Android-alkalmazásból | Microsoft Docs
description: Megtudhatja, hogyan jelentkezhet be a felhasználókba, és hogyan hívhatja meg a Microsoft Graph API-t Android-alkalmazásból.
services: active-directory
documentationcenter: android
author: rwike77
manager: CelesteDG
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac4007cc9379aa98d88099df13ba303063f9831f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268526"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Gyors útmutató: Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása Android-alkalmazásból

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Ha Android-alkalmazást fejleszt, a Microsoft egyszerűvé és egyértelművé teszi a felhasználók bejelentkeztetését az Azure Active Directory (Azure AD) szolgáltatásba. Az Azure AD lehetővé teszi, hogy alkalmazása hozzáférjen a felhasználók adataihoz a Microsoft Graph vagy az Ön saját védett webes API-ja használatával.

Az Azure AD Authentication Library (ADAL) Android Library lehetővé teszi az alkalmazásnak, hogy az iparági szabványnak megfelelő [Microsoft Azure Active Directory fiókok](https://azure.microsoft.com/services/active-directory/) támogatásával megkezdhesse a [Microsoft Azure Cloud](https://azure.microsoft.com/free/cloud-services/) és [Microsoft Graph API](https://developer.microsoft.com/graph) használatát. OAuth 2,0 és OpenID Connect.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* Jogkivonat lekérése a Microsoft Graph-hoz
* Jogkivonat frissítése
* A Microsoft Graph meghívása
* Felhasználó kijelentkeztetése

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége lesz egy Azure AD-bérlőre, ahol felhasználókat hozhat létre és regisztrálhat egy alkalmazást. Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Forgatókönyv: Jelentkezzen be a felhasználókba, és hívja meg a Microsoft Graph

![Az Azure AD-és Android-topológiát jeleníti meg](./media/quickstart-v1-android/active-directory-android-topology.png)

Az alkalmazást minden Azure AD-fiókhoz használhatja. Az egybérlős és a több-bérlős forgatókönyveket is támogatja (az egyes lépésekben részletezzük). A cikk bemutatja, hogyan hozhat létre egy olyan alkalmazást, amely csatlakoztatja a vállalati felhasználókat, és hozzáfér az Azure- és O365-adataikhoz a Microsoft Graph-on keresztül. A hitelesítési folyamat során a végfelhasználónak be kell majd jelentkeznie, és hozzájárulását kell adnia az alkalmazás engedélyeihez, néhány esetben pedig szükség lehet egy rendszergazdára is a hozzájárulás megadásához. A minta logikájának nagy része megmutatja, hogyan hitelesíthet egy végfelhasználót, illetve hogyan kezdeményezhet egy alapszintű hívást a Microsoft Graph-ba.

## <a name="sample-code"></a>Mintakód

A teljes mintakód megtalálható a [githubon](https://github.com/Azure-Samples/active-directory-android).

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

## <a name="step-1-register-and-configure-your-app"></a>1\. lépés: Az alkalmazás regisztrálása és konfigurálása

Szüksége lesz egy natív ügyfélalkalmazásra, amely az [Azure Portalon](https://portal.azure.com) keresztül regisztrálva lett a Microsoftnál.

1. Az alkalmazásregisztráció elkezdése
    - Lépjen az [Azure Portalra](https://aad.portal.azure.com).
    - Válassza az ***Azure Active Directory*** > ***Appok regisztrálása*** elemet.

2. Az alkalmazás létrehozása
    - Válassza az **új regisztráció**lehetőséget.
    - A **Név** mezőbe írja be az alkalmazás nevét.
    - A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
    - Az **átirányítási URI**-ban válassza a **nyilvános ügyfél (mobil és asztali)** lehetőséget a `http://localhost`legördülő listából, és írja be a következőt:.
    - Kattintson a **regisztrálás**gombra.

3. A Microsoft Graph konfigurálása
    - Válassza ki **API-engedélyek**.
    - Válassza az **engedély hozzáadása**lehetőséget, és válasszon ki **egy API** -t, majd válassza a ***Microsoft Graph***lehetőséget.
    - A **delegált engedélyek**területen válassza ki az engedélyt **felhasználó. Read**parancsot, majd nyomja meg a **Hozzáadás** a mentéshez lehetőséget.        
    
4. Gratulálunk! Az alkalmazás konfigurálása kész. A következő szakaszban a következőre lesz szüksége:
    - `Application ID`
    - `Redirect URI`

## <a name="step-2-get-the-sample-code"></a>2\. lépés: A mintakód letöltése

1. Klónozza a kódot.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Nyissa meg a mintát az Android Studióban.
    - Válassza az **Open an existing Android Studio project** (Létező Android Studio-projekt megnyitása) lehetőséget.

## <a name="step-3-configure-your-code"></a>3\. lépés: A kód konfigurálása

A mintakódhoz tartozó összes konfigurációt megtalálja az ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** fájlban.

1. Cserélje le a `CLIENT_ID` állandót az `ApplicationID` értékére.
2. Cserélje le a `REDIRECT URI` állandót az előbb konfigurált `Redirect URI`-értékre (`http://localhost`).

## <a name="step-4-run-the-sample"></a>4\. lépés: Minta futtatása

1. Válassza a **Build > Clean Project** (Létrehozás > Új projekt) lehetőséget.
2. Válassza a **Run > Run app** (Futtatás > Alkalmazás futtatása) lehetőséget.
3. Az alkalmazásnak létre kell jönnie, és egy alapszintű felhasználói felületet kell megjelenítenie. A `Call Graph API` gomb megnyomása után egy bejelentkezést fog kérni Öntől, majd csendben meghívja a Microsoft Graph API-t az új jogkivonattal.

## <a name="next-steps"></a>További lépések

1. Látogasson el az [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) webhelyre a kódtárak működésével, illetve az új forgatókönyvek és képességek konfigurálásával kapcsolatos további információkért.
2. Natív forgatókönyvekben az alkalmazás egy beágyazott webes nézetet fog használni, és nem lép ki az alkalmazásból. A `Redirect URI` tetszőlegesen állítható be.
3. Problémába ütközött, vagy kérése lenne? Létrehozhat egy problémát, vagy közzéteheti Stack Overflow a címkével `azure-active-directory`.

### <a name="cross-app-sso"></a>Alkalmazások közötti SSO

Ismerje meg, [hogyan engedélyezheti Androidon az alkalmazások közötti SSO-t az ADAL segítségével](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Hitelesítési telemetria

Az ADAL-kódtár elérhetővé teszi a hitelesítési telemetriát annak érdekében, hogy segítsen az alkalmazásfejlesztőknek alkalmazásuk működésének megértésében és jobb felhasználói élmények létrehozásában. Ez lehetővé teszi a sikeres bejelentkezések, aktív felhasználók és számos más érdekes elemzés rögzítését. A hitelesítési telemetria használatához az alkalmazásfejlesztőknek létre kell hozniuk egy telemetriaszolgáltatást, amely összesíti és tárolja az eseményeket.

A hitelesítési telemetriához kapcsolódó további információkért keresse fel az [ADAL Android hitelesítési telemetria](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry) oldalát.
