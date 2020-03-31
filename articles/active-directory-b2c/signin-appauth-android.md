---
title: Token beszerzése Android-alkalmazásban
titleSuffix: Azure AD B2C
description: Hogyan hozzon létre egy Android-alkalmazást, amely az AppAuth és az Azure Active Directory B2C használatával kezeli a felhasználói identitásokat és hitelesíti a felhasználókat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31ad373b1544fc601a9c37e05e324a9c1dfb3f73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183779"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Bejelentkezés Android-alkalmazás használatával az Azure Active Directory B2C-ben

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. Ezek a szabványok lehetővé teszik, hogy kihasználhassa az Azure Active Directory B2C-vel integrálni kívánt tárat. Más tárak használatához az ehhez hasonló forgatókönyv segítségével bemutathatja, hogyan konfigurálhatja a külső könyvtárakat a Microsoft identity platformhoz való csatlakozáshoz. Az [RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) megvalósító legtöbb könyvtár csatlakozhat a Microsoft Identity platformhoz.

> [!WARNING]
> A Microsoft nem biztosít javításokat a külső könyvtárakhoz, és nem vizsgálta felül ezeket a könyvtárakat. Ez a minta egy külső féltől származó, AppAuth nevű függvénytárat használ, amelyet az Azure AD B2C-vel az alapvető forgatókönyvekben való kompatibilitás szempontjából teszteltek. A problémákat és a szolgáltatáskérelmeket a tár nyílt forráskódú projektjéhez kell irányítani. További információt ebben a [cikkben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) talál.
>
>

Ha csak most ismerkedik az OAuth2 vagy az OpenID Connect használatával, előfordulhat, hogy nem fogja tökéletesen érteni a konfigurációs lépéseket. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](tutorial-create-tenant.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ezután regisztráljon egy alkalmazást az Azure AD B2C-bérlőben. Ez biztosítja az Azure AD az alkalmazással való biztonságos kommunikációhoz szükséges információkat.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Is rögzítse az egyéni átirányítási URI-hoz használ egy későbbi lépésben. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Felhasználói folyamatok létrehozása

Az Azure AD B2C-ben minden felhasználói élményt egy [felhasználói folyamat](user-flow-overview.md)határoz meg, amely az Azure AD viselkedését szabályozó szabályzatok készlete. Ez az alkalmazás bejelentkezési és regisztrációs felhasználói folyamatot igényel. A felhasználói folyamat létrehozásakor ügyeljen arra, hogy:

* Válassza ki a **Megjelenítendő nevet** regisztrációs attribútumként a felhasználói folyamatban.
* Minden felhasználói folyamatban válassza **a Megjelenítendő név** és **az Objektumazonosító** alkalmazásjogcímeit. Ezenfelül más jogcímeket is használhat.
* A létrehozás után másolja az egyes felhasználói folyamatok **nevét.** A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  Később szüksége lesz a felhasználói folyamat nevére.

Miután létrehozta a felhasználói folyamatokat, készen áll az alkalmazás létrehozásához.

## <a name="download-the-sample-code"></a>A mintakód letöltése

A [GitHubon](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)az Azure AD B2C használatával működő mintát biztosítottunk. Letöltheti a kódot, és futtathatja. A saját alkalmazással gyorsan elkezdheti a saját Azure AD B2C-konfigurációját a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)utasításainak követésével.

A minta az [AppAuth](https://openid.github.io/AppAuth-Android/)által biztosított minta módosítása. Kérjük, látogasson el az oldalra, hogy többet tudjon AppAuth és annak jellemzőit.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás módosítása az Azure AD B2C és az AppAuth használatához

> [!NOTE]
> Az AppAuth támogatja az Android API 16 (Jellybean) és újabb verziókat. Az API 23 és újabb eszközök használatát javasoljuk.
>

### <a name="configuration"></a>Konfiguráció

Az Azure AD B2C-vel való kommunikációkonfigurálása a felderítési URI megadásával, vagy az engedélyezési végpont és a tokenvégpont URI-k megadásával konfigurálható. Mindkét esetben a következő információkra lesz szüksége:

* Bérlőazonosítója (pl. contoso.onmicrosoft.com)
* Felhasználóforgalom neve (pl.\_B2C\_1 SignUpIn)

Ha úgy dönt, hogy automatikusan felderíti az engedélyezési és a tokenvégpont URI-k, adatokat kell lekérnie a felderítésuri.If you choose to automatically discover the authorization and token endpoint URI, you will need to fetch information from the discovery URI. A felderítési URI a bérlői\_azonosító és\_a házirend neve cseréjével jön létre a következő URL-címen:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Ezután beszerezheti az engedélyezési és tokenvégpont-URI-kat, és létrehozhat egy AuthorizationServiceConfiguration objektumot a következő futtatásával:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Ahelyett, hogy felderítés használatával szerezné be az engedélyezési és tokenvégpont-URI-kat, explicit módon is megadhatja őket a bérlői\_azonosító és a házirend\_neve lecserélésével az URL-cím alatt:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Futtassa a következő kódot az AuthorizationServiceConfiguration objektum létrehozásához:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Engedélyezése

Az engedélyezési szolgáltatás konfigurációjának konfigurálása vagy beolvasása után engedélyezési kérelem állítható létre. A kérelem létrehozásához a következő adatokra lesz szüksége:

* A korábban rögzített ügyfélazonosító (APPLICATION ID). Például: `00000000-0000-0000-0000-000000000000`.
* Egyéni átirányítási URI, amely korábban rögzített. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Az [alkalmazás regisztrálásakor](#create-an-application)mindkét elemet menteni kellett volna.

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Kérjük, olvassa el az [AppAuth útmutatót](https://openid.github.io/AppAuth-Android/) a folyamat további befejezéséről. Ha gyorsan el kell kezdenie egy működő alkalmazást, tekintse meg [a mintát.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Kövesse a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) saját Azure AD B2C-konfigurációjának megadásához kövesse a lépéseket.
