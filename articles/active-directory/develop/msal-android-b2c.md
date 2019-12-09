---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy milyen szempontokat kell figyelembe venni, amikor az Androidhoz készült Microsoft Authentication Library (MSAL) használatával Azure AD B2Ct használ. Android
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98a31ea2daffba19242e73362af5a44e3a392342
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917114"
---
# <a name="use-msal-for-android-with-b2c"></a>Az Android rendszerhez készült MSAL használata B2C-vel

A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára, hogy [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)használatával hitelesítsék a felhasználókat a közösségi és a helyi identitásokkal. Azure AD B2C egy Identitáskezelés szolgáltatás. Ezzel testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat, amikor az alkalmazásaikat használják.

## <a name="configure-known-authorities-and-redirect-uri"></a>Ismert hatóságok és átirányítási URI konfigurálása

Az Android rendszerhez készült MSAL-ben a B2C-szabályzatok (felhasználói útvonalak) önálló hatóságokként vannak konfigurálva.

Olyan B2C-alkalmazás, amely két házirenddel rendelkezik:
- Regisztráció/bejelentkezés
    * Hívott `B2C_1_SISOPolicy`
- Profil szerkesztése
    * Hívott `B2C_1_EditProfile`

Az alkalmazás konfigurációs fájlja két `authorities`deklarál. Egyet az egyes szabályzatokhoz. Az egyes hatóságok `type` tulajdonsága `B2C`.

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

A `redirect_uri` regisztrálni kell az alkalmazás konfigurációjában, valamint a `AndroidManifest.xml` az átirányítás támogatásához az [engedélyezési kód engedélyezése folyamat](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)során.

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication inicializálása

a `IPublicClientApplication` egy gyári módszerrel állítható be, hogy az alkalmazás konfigurációját aszinkron módon lehessen elemezni.

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

## <a name="interactively-acquire-a-token"></a>Token interaktív beszerzése

Ha a tokent interaktív módon szeretné beszerezni a MSAL, hozzon létre egy `AcquireTokenParameters` példányt, és adja meg a `acquireToken` metódusnak. Az alábbi jogkivonat-kérelem az `default`-szolgáltatót használja.

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

## <a name="silently-renew-a-token"></a>Token csendes megújítása

Ha a tokent a MSAL-mel csendben szeretné beszerezni, hozzon létre egy `AcquireTokenSilentParameters` példányt, és adja meg a `acquireTokenSilentAsync` metódusnak. A `acquireToken` módszertől eltérően a `authority`t meg kell adni a token csendes beszerzéséhez.

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

## <a name="specify-a-policy"></a>Házirend meghatározása

Mivel a B2C-szabályzatok külön hatóságokként jelennek meg, az alapértelmezetttől eltérő házirend meghívásával a `fromAuthority` záradék megadásával `acquireToken` vagy `acquireTokenSilent` paraméterek kiépítésekor.  Példa:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Jelszó-módosítási házirendek kezelése

A helyi fiók regisztrálása vagy bejelentkezési felhasználói folyamata egy "**elfelejtett jelszót**" mutat be hivatkozás. Ha erre a hivatkozásra kattint, nem indítja el automatikusan a jelszó-visszaállítás felhasználói folyamatát.

Ehelyett a `AADB2C90118` hibakódot adja vissza az alkalmazásnak. Az alkalmazásnak ezt a hibakódot egy adott felhasználói folyamat futtatásával kell kezelnie, amely alaphelyzetbe állítja a jelszót.

A jelszó-visszaállítási hibakód elfogásához a következő implementáció használható a `AuthenticationCallback`belül:

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

## <a name="use-iauthenticationresult"></a>IAuthenticationResult használata

A jogkivonat sikeres beszerzése egy `IAuthenticationResult` objektumot eredményez. A hozzáférési jogkivonatot, a felhasználói jogcímeket és a metaadatokat tartalmazza.

### <a name="get-the-access-token-and-related-properties"></a>Hozzáférési jogkivonat és kapcsolódó tulajdonságok beolvasása

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

### <a name="get-the-authorized-account"></a>A hitelesítő fiók beszerzése

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

A IdToken visszaadott jogcímeket a biztonsági jogkivonat szolgáltatás (STS) tölti fel, nem pedig a MSAL. A használt identitásszolgáltató függően előfordulhat, hogy egyes jogcímek hiányoznak. Néhány IDP jelenleg nem adja meg a `preferred_username` jogcímet. Mivel ezt a jogcímet a MSAL használja a gyorsítótárazáshoz, a rendszer helyőrző értéket (`MISSING FROM THE TOKEN RESPONSE`) használ a helyén. A B2C-IdToken jogcímeivel kapcsolatos további információkért lásd: [a jogkivonatok áttekintése Azure Active Directory B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Fiókok és házirendek kezelése

A B2C az egyes szabályzatokat külön szolgáltatóként kezeli. Így a hozzáférési tokenek, a frissítési tokenek és az egyes szabályzatok által visszaadott azonosító tokenek nem módosíthatók. Ez azt jelenti, hogy minden házirend egy külön `IAccount` objektumot ad vissza, amelynek tokenjét nem lehet más szabályzatok meghívására használni.

Minden házirend egy `IAccount` hoz létre az egyes felhasználók gyorsítótárához. Ha egy felhasználó bejelentkezik egy alkalmazásba, és két házirendet hív meg, két `IAccount`s lesz. Ha el szeretné távolítani ezt a felhasználót a gyorsítótárból, minden szabályzathoz meg kell hívnia `removeAccount()`.

Ha `acquireTokenSilent`-házirenddel megújítja a szabályzat jogkivonatait, adja meg ugyanazt a `IAccount`, amelyet a szabályzat korábbi meghívásai visszaadottak `AcquireTokenSilentParameters`re. Egy másik házirend által visszaadott fiók megadása hibát eredményez.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Active Directory B2Cról (Azure AD B2C) a [mi Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
