---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az Azure AD B2C használatával kapcsolatos konkrét szempontokat a Microsoft Android hitelesítési könyvtárával (MSAL). Android)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696521"
---
# <a name="use-msal-for-android-with-b2c"></a>Az MSAL használata Android-ra B2C-vel

A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazásfejlesztők számára, hogy az [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)használatával hitelesítsék a felhasználókat a közösségi és helyi identitással. Az Azure AD B2C egy identitáskezelési szolgáltatás. Segítségével testre szabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztráljanak, jelentkezzenek be és kezeljék a profiljukat az alkalmazások használata során.

## <a name="configure-known-authorities-and-redirect-uri"></a>Ismert hatóságok konfigurálása és URI-átirányítás

Az Android-alapú MSAL-ban a B2C-házirendek (felhasználói utak) egyéni hatóságokként vannak konfigurálva.

Adott egy B2C alkalmazás, amely két házirend:
- Regisztráció / Bejelentkezés
    * Úgynevezett`B2C_1_SISOPolicy`
- Profil szerkesztése
    * Úgynevezett`B2C_1_EditProfile`

Az alkalmazás konfigurációs fájlja `authorities`két deklarál. Minden politikához egy. Az `type` egyes hatóságok `B2C`tulajdona .

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

Az `redirect_uri` engedélyezési [kód engedélyezési folyamata](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)során `AndroidManifest.xml` regisztrálni kell az alkalmazás konfigurációjában, valamint az átirányítás támogatásához.

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication inicializálása

`IPublicClientApplication`gyári módszerrel készült, hogy lehetővé tegye az alkalmazás konfigurációjának aszinkron elemzését.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Interaktív antomot szerezni

Token interaktív beszerzéséhez az MSAL-lal hozzon létre egy példányt, `AcquireTokenParameters` és adja meg a `acquireToken` metódusnak. Az alábbi jogkivonat-kérelem a `default` jogosultságot használja.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Zábban jogkivonat csendes megújítása

Token csendes beszerzéséhez az MSAL-lal, hozzon létre egy példányt, `AcquireTokenSilentParameters` és adja meg a `acquireTokenSilentAsync` metódusnak. A `acquireToken` metódussal `authority` ellentétben a token csendes megszerzéséhez meg kell adni.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Házirend megadása

Mivel a B2C házirendjei külön hatóságokként jelennek meg, az alapértelmezetttől eltérő `fromAuthority` házirend meghívása egy záradék megadásával érhető el a létrehozáskor `acquireToken` vagy `acquireTokenSilent` a paraméterekben.  Példa:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Jelszómódosítási házirendek kezelése

A helyi fiókra való feliratkozási vagy bejelentkezési felhasználói folyamat ontja**az elfelejtett jelszót?** Link. Erre a hivatkozásra kattintva nem indítja el automatikusan a jelszó-visszaállítási felhasználói folyamatot.

Ehelyett a hibakódot `AADB2C90118` visszaküldi az alkalmazásnak. Az alkalmazásnak kezelnie kell ezt a hibakódot egy adott felhasználói folyamat futtatásával, amely alaphelyzetbe állítja a jelszót.

A jelszó-visszaállítási hibakód elfogásához a `AuthenticationCallback`következő implementáció használható a következőkben:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Az IAuthenticationResult használata

A sikeres token-beszerzés egy objektumot `IAuthenticationResult` eredményez. A hozzáférési jogkivonatot, a felhasználói jogcímeket és a metaadatokat tartalmazza.

### <a name="get-the-access-token-and-related-properties"></a>A hozzáférési jogkivonat és a kapcsolódó tulajdonságok beszereznie

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Az engedélyezett fiók beszerezése

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken jogcímek

Az IdToken ben visszaadott jogcímeket a biztonsági jogkivonat-szolgáltatás (STS) tölti fel, nem pedig az MSAL. A használt identitásszolgáltatótól (IdP) függően előfordulhat, hogy egyes jogcímek hiányoznak. Egyes idp-k jelenleg `preferred_username` nem biztosítják a jogcímet. Mivel ezt a jogcímet az MSAL gyorsítótárazásra `MISSING FROM THE TOKEN RESPONSE`használja, a helyén egy helyőrző érték van használatban. A B2C IdToken jogcímekről az [Azure Active Directory B2C tokeneinek áttekintése](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)című témakörben olvashat bővebben.

## <a name="managing-accounts-and-policies"></a>Fiókok és házirendek kezelése

A B2C minden egyes politikát külön hatóságként kezel. Így a hozzáférési jogkivonatok, a frissítési jogkivonatok és az egyes házirendekből visszaadott azonosító jogkivonatok nem felcserélhetők. Ez azt jelenti, `IAccount` hogy minden házirend egy külön objektumot ad vissza, amelynek jogkivonatai nem használhatók más házirendek meghívására.

Minden házirend `IAccount` minden felhasználó számára hozzáad egy értéket a gyorsítótárhoz. Ha egy felhasználó bejelentkezik egy alkalmazásba, és két `IAccount`szabályzatot hív meg, akkor két s lesz. Ha el szeretné távolítani ezt a `removeAccount()` felhasználót a gyorsítótárból, minden házirendet meg kell hívnia.

Amikor megújítja a jogkivonatokat a rendszerhez, `acquireTokenSilent`adja meg ugyanazt, `IAccount` amelyet a házirend korábbi meghívásaiból a. `AcquireTokenSilentParameters` Egy másik házirend által visszaadott fiók megadása hibát eredményez.

## <a name="next-steps"></a>További lépések

További információ az Azure Active Directory B2C -ről (Azure AD B2C) a [Mi az Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
