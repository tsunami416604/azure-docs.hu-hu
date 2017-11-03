---
title: "Az Azure Active Directory v2.0 Android-alkalmazás |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Android-alkalmazás, mely aláírja a felhasználók személyes Microsoft-fiók és a munkahelyi vagy iskolai fiókok és a Graph API hívások harmadik féltől származó könyvtárak használatával."
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mbaldwin
editor: 
ms.assetid: 16294c07-f27d-45c9-833f-7dbb12083794
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: c0a5a818c61f7af7ff04bf890b54e8364f3b21b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-android-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Bejelentkezés hozzáadása egy külső könyvtár használatával Graph API-t használ a v2.0-végpontra Android-alkalmazás
A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. A fejlesztők a függvénytárat, hogy integrálni szeretne a szolgáltatások. Segítségével a fejlesztők a platformot használja a többi könyvtárak, azt korábban írt bemutatják, hogyan lehet kapcsolódni a Microsoft identity platform külső szalagtárak konfigurálása a jelen szoftverhez hasonló néhány forgatókönyvek. A legtöbb tárak, amelyek megvalósítják az [a RFC6749 OAuth2 spec](https://tools.ietf.org/html/rfc6749) csatlakozni tud-e a Microsoft identity platform.

Ez a forgatókönyv hoz létre az alkalmazással felhasználók jelentkezzen be a szervezet és majd keresse meg a magukat a szervezetek a Graph API használatával.

Ha most ismerkedik az OAuth2 vagy az OpenID Connect, ez a minta konfigurálási nem célszerű Önnek. Azt javasoljuk, hogy olvassa el [2.0 protokoll - OAuth 2.0 hitelesítési kód Flow](active-directory-v2-protocols-oauth-code.md) a háttérben.

> [!NOTE]
> Egyes szolgáltatások, amelyek rendelkeznek egy kifejezést a OAuth2 vagy az OpenID Connect szabványok, például a feltételes hozzáférés és a Csoportházirend kezelése Intune-ban, a platform kell használni a Microsoft Azure identitáskezelési szalagtárak nyílt forráskódú.
> 
> 

A v2.0-végpontra nem támogatja az összes Azure Active Directory forgatókönyvek és funkciók.

> [!NOTE]
> Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="download-the-code-from-github"></a>Töltse le a kódot a Githubról
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2).  Követéséhez is [töltse le az alkalmazás vázát egy .zip](https://github.com/Azure-Samples/active-directory-android-native-oidcandroidlib-v2/archive/skeleton.zip) vagy klónozza a vázat:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

A minta csak is tölthetik le, és rögtön használatba:

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Hozzon létre egy új alkalmazást a [alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy részletes kövesse a [egy alkalmazás regisztrálása a v2.0-végponttal](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Másolás a **alkalmazásazonosító** , amely hozzá van rendelve az alkalmazás mivel hamarosan lesz szükség.
* Adja hozzá a **Mobile** platform az alkalmazásra vonatkozóan.

> Megjegyzés: Az alkalmazásregisztrációs portálra biztosít egy **átirányítási URI-** érték. Azonban ez a példa kell használnia az alapértelmezett érték `https://login.microsoftonline.com/common/oauth2/nativeclient`.
> 
> 

## <a name="download-the-nxoauth2-third-party-library-and-create-a-workspace"></a>Töltse le a NXOAuth2 külső könyvtárban, és egy munkaterület létrehozása
A forgatókönyv a Githubból, amelyek alapján a Google OpenID Connect kódját OAuth2 könyvtár OIDCAndroidLib fogja használni. A natív alkalmazásprofil valósítja meg, és a felhasználó az engedélyezési végpont támogatja. Az alábbiakban az összes, a Microsoft identitásplatformmal együttműködve integrálni kell.

A számítógépre a OIDCAndroidLib tárház klónozása.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](../media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## <a name="set-up-your-android-studio-environment"></a>Az Android Studio környezet beállítása
1. Hozzon létre egy új Android Studio-projektet, és fogadja el az alapértelmezett beállításokat a varázslóban.
   
    ![Az Android Studio új projekt létrehozása](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)
   
    ![Cél Android-eszközök](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)
   
    ![Adjon hozzá olyan tevékenységet Mobile](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)
2. A projekt modulok beállításához helyezze át a klónozott tárház a projekt helyére. A projekt is létrehozhat, és majd klónozza a projekt helyére a közvetlenül.
   
    ![Projekt modulok](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)
3. Nyissa meg a projekt modulok beállításait, a helyi menü használatával vagy a Ctrl + Alt + Maj + S parancsikon használatával.
   
    ![Modulok Projektbeállítások](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)
4. Távolítsa el az alapértelmezett app modul, mivel csak a tároló Projektbeállítások.
   
    ![Az alapértelmezett app modul](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)
5. Modulok importálása a klónozott tárház az aktuális projektben.
   
    ![Projekt importálása a gradle-lel](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![új modul lap](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)
6. Ismételje meg ezeket a lépéseket a `oidlib-sample` modul.
7. A oidclib függőségek ellenőrzése a `oidlib-sample` modul.
   
    ![a oidlib-mintavételi modul oidclib függőségek](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)
8. Kattintson a **OK** és várjon, amíg a gradle-szinkronizálás.
   
    A settings.gradle hasonlóan kell kinéznie:
   
    ![Képernyőkép a settings.gradle](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)
9. Hozza létre a minta alkalmazását, és győződjön meg arról, hogy megfelelően fut a minta.
   
    Sem lesz még használhatja az Azure Active Directoryban. Egyes végpontok először konfigurálni kell. Ez azért így az Android Studio problémák nem rendelkezik, először a mintaalkalmazás testreszabása előtt.
10. Létrehozása és futtatása `oidlib-sample` Android Studio céljaként.
    
    ![A minta oidlib build folyamatban](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)
11. Törölje a `app ` címtárhoz, amely hagyták, ha a modul a projektből eltávolította, mert a biztonsági Android Studio nem törli.
    
    ![Az alkalmazás könyvtárát tartalmazó fájlstruktúra](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)
12. Nyissa meg a **szerkesztése konfigurációk** menüből, hogy távolítsa el a futtatási konfigurációját, hogy a modul a projekt való eltávolításakor is balra.
    
    ![Konfigurációk menü szerkesztése](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG)
    ![konfigurációs alkalmazás futtatása](../media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## <a name="configure-the-endpoints-of-the-sample"></a>A minta végpontok konfigurálása
Most, hogy a `oidlib-sample` fut-e., most szerkesztése egyes végpontok a működéséhez az Azure Active Directoryban.

### <a name="configure-your-client-by-editing-the-oidcclientconfxml-file"></a>Az ügyfél konfigurálása a oidc_clientconf.xml fájl szerkesztésével
1. Mivel csak a szolgáltatáshitelesítést egy token és a Graph API hívása OAuth2 adatfolyamok használ, állítsa be az ügyfelet az OAuth2 csak. Egy újabb példa OIDC származnak.
   
    ```xml
        <bool name="oidc_oauth2only">true</bool>
    ```
2. Az ügyfél-azonosító, hogy a regisztrációs portál konfigurálása
   
    ```xml
        <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
        <string name="oidc_clientSecret"></string>
    ```
3. Konfigurálja az átirányítási URI-t a egyet az alábbi.
   
    ```xml
        <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
    ```
4. A hatókörök, amelyekre szüksége van ahhoz, hogy hozzáférhessen a Graph API konfigurálása.
   
    ```xml
        <string-array name="oidc_scopes">
            <item>openid</item>
            <item>https://graph.microsoft.com/User.Read</item>
            <item>offline_access</item>
        </string-array>
    ```

A `User.Read` értéket `oidc_scopes` lehetővé teszi a felhasználó által aláírt az alapvető profil olvasása.
Ön tudhat meg többet a rendelkezésre álló összes hatókör [Microsoft Graph-engedélyhatókörök](https://graph.microsoft.io/docs/authorization/permission_scopes).

Ha azt szeretné magyarázatokat kapcsolatos `openid` vagy `offline_access` , az OpenID Connect hatókörök, lásd: [2.0 protokoll - OAuth 2.0 hitelesítési kód Flow](active-directory-v2-protocols-oauth-code.md).

### <a name="configure-your-client-endpoints-by-editing-the-oidcendpointsxml-file"></a>A oidc_endpoints.xml fájl szerkesztésével ügyfél végpontok konfigurálása
* Nyissa meg a `oidc_endpoints.xml` fájlt, és hajtsa végre a következő módosításokat:
  
    ```xml
    <!-- Stores OpenID Connect provider endpoints. -->
    <resources>
        <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
        <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
        <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
        <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
    </resources>
    ```

Ezeket a végpontokat kell soha ne módosuljanak OAuth2 protokoll használatakor.

> [!NOTE]
> A végpontokat a `userInfoEndpoint` és `revocationEndpoint` jelenleg nem támogatottak az Azure Active Directoryban. Ha nem adja meg ezeket az alapértelmezett example.com értékkel, fog emlékezteti, hogy nincsenek elérhető minta :-)
> 
> 

## <a name="configure-a-graph-api-call"></a>A Graph API-hívás konfigurálása
* Nyissa meg a `HomeActivity.java` fájlt, és hajtsa végre a következő módosításokat:
  
    ```Java
       //TODO: set your protected resource url
        private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
    ```

Egy egyszerű Graph API-hívás itt az adatait adja vissza.

Most már az összes módosítást, végre kell hajtani. Futtassa a `oidlib-sample` alkalmazáshoz, majd kattintson **bejelentkezés**.

Már sikeresen hitelesítése után válassza ki a **védett erőforrás kérelem** gombra kattintva tesztelheti a Graph API hívása.

## <a name="get-security-updates-for-our-product"></a>A termék biztonsági frissítések beszerzése
Javasoljuk, hogy a biztonsági események szóló értesítések lekérése látogasson el a [biztonsági TechCenter](https://technet.microsoft.com/security/dd252948) és fizessen elő a biztonsági tanácsadói riasztásokra.

