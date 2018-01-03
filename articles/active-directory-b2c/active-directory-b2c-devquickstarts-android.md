---
title: "Az Azure Active Directory B2C: Tokenbeolvasás Android alkalmazással |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan, amely Azure Active Directory B2C AppAuth használ a felhasználói identitások kezelésére, és hitelesíti a felhasználókat, Android-alkalmazás létrehozásához."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: mtillman
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/06/2017
ms.author: parakhj
ms.openlocfilehash: dd08c666c09b651e6c0def72a89eda56ba73e34d
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Az Azure AD B2C: Bejelentkezés Android alkalmazás segítségével

A Microsoft identitásplatformja nyílt szabványokat, többek között OAuth2-t és OpenID Connectet használ. Így a fejlesztők bármilyen típusú kódtárat integrálhatnak szolgáltatásainkkal. A fejlesztők támogatási be más könyvtárakkal a platformot, azt korábban írt bemutatják, hogyan lehet kapcsolódni a Microsoft identity platform 3. fél szalagtárak konfigurálása a jelen szoftverhez hasonló néhány forgatókönyvek. Az [RFC6749 OAuth2 specifikációt](https://tools.ietf.org/html/rfc6749) használó legtöbb kódtár képes lesz kapcsolódni a Microsoft identitásplatformjához.

> [!WARNING]
> A Microsoft nem biztosít azoknak a 3. fél szalagtárak, és nem végrehajtva a tárak áttekintése. Ez a minta, amely tesztelték AppAuth nevű alapszintű forgatókönyvekben kompatibilisek-e az Azure AD B2C 3. fél szalagtárat használ. A könyvtár nyílt forráskódú projekt problémákat és funkciókra vonatkozó kérések legyenek irányítva. Ellenőrizze a [Ez a cikk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) további információt.  
>
>

Ha csak most ismerkedik az OAuth2 vagy az OpenID Connect használatával, előfordulhat, hogy nem fogja tökéletesen érteni a konfigurációs lépéseket. Ebben az esetben javasoljuk, hogy olvassa el [a protokoll áttekintését, amelyet itt talál](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Az Azure AD B2C-címtár beszerzése

Az Azure AD B2C használatához létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és más elemeket. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne.

## <a name="create-an-application"></a>Alkalmazás létrehozása

A következő lépésben hozzon létre egy alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. A mobilalkalmazások létrehozásához hajtsa végre az [ezeket az utasításokat](active-directory-b2c-app-registration.md). Ügyeljen arra, hogy:

* Tartalmaznak egy **Native Client** az alkalmazásban.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Ezt később lesz szüksége.
* Állítson be egy natív ügyfél **átirányítási URI-** (pl. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Később erre is szüksége lesz.

## <a name="create-your-policies"></a>Szabályzatok létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Az alkalmazás tartalmaz egy identitás-élmény: egy kombinált bejelentkezési és regisztrációs. Szeretné létrehozni ezt a házirendet, lásd: a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). A szabályzat létrehozásakor ügyeljen arra, hogy:

* Válassza ki a **megjelenített név** a szabályzatot az előfizetési attribútumaként.
* Az összes szabályzatban válassza ki a **Megjelenített név** és az **Objektumazonosító** alkalmazási jogcímet. Kiválaszthat egyéb jogcímeket is.
* Az egyes házirendek létrehozása után másolja a házirend **nevét**. A névnek a következő előtaggal kell rendelkeznie: `b2c_1_`.  A szabályzat nevére később még szüksége lesz.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A szabályzat létrehozását követően készen áll az alkalmazás elkészítésére.

## <a name="download-the-sample-code"></a>A mintakód letöltése

Egy Azure AD B2C AppAuth használó minta adtunk [a Githubon](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Töltse le a kódot, és futtassa. Gyorsan utasításait követve a saját Azure AD B2C konfigurációs használatával saját alkalmazással Ismerkedés a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

A minta rendszer által biztosított mintafájl módosítását [AppAuth](https://openid.github.io/AppAuth-Android/). Látogasson el a AppAuth és funkcióival kapcsolatos további oldalára.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Az alkalmazás az Azure AD B2C használata AppAuth módosítása

> [!NOTE]
> AppAuth támogatja az Android API 16 (Jellybean) vagy újabb verzió. Azt javasoljuk, API 23 vagy újabb verzió.
>

### <a name="configuration"></a>Konfiguráció

Az Azure AD B2C kommunikáció vagy a felderítés URI, vagy az engedélyezési végpont és a token-végpont URI-azonosítók megadásával konfigurálható. Mindkét esetben szüksége lesz a következő információkat:

* Bérlő azonosítója (pl. contoso.onmicrosoft.com)
* Házirend neve (pl. B2C\_1\_SignUpIn)

Ha automatikus észleléséhez, az engedélyezés és a token-végpont URI-azonosítók választja, akkor adatok beolvasása a felderítés URI. A felderítés URI generálhatók azáltal, hogy a bérlő\_azonosítója és a házirend\_az alábbi URL-címben:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Ezután az engedélyezési és a token-végpont URI-k megszerzésére és AuthorizationServiceConfiguration objektumot létrehozni a következő futtatásával:

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

Felderítési helyett a az engedélyezési és a token-végpont URI-k, azt is megadhatja azokat explicit módon történő lecserélésével a bérlő\_azonosítója és a házirend\_neve az URL-címet az alábbi:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Futtassa a következő kódot a AuthorizationServiceConfiguration objektum létrehozásához:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Engedélyezés folyamatban

Konfigurálja, vagy egy engedélyezési szolgáltatás konfigurációjának beolvasása után egy engedélyezési-kérelem lehet létrehozni. A kérelem létrehozásához szüksége lesz a következő információkat:

* Ügyfél-azonosító (pl. 00000000-0000-0000-0000-000000000000)
* Átirányítási URI egy egyéni séma (pl. com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Mindkét elemeket kell mentett Ha Ön volt [regisztrálja az alkalmazást](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Tekintse meg a [AppAuth útmutató](https://openid.github.io/AppAuth-Android/) való fejezze be a folyamatot. Ha egy működő alkalmazást gyorsan megismerkedhet van szüksége, tekintse meg [a minta](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Kövesse a [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) a saját Azure AD B2C konfigurációs megadását.

Azt mindig nyitva a visszajelzések és tanácsok! Ha bármilyen nehézségbe ütközik az ebben a témakörben, vagy az ehhez a tartalomhoz javítására állnak, azt fogadjuk visszajelzéseit az oldal alján. A szolgáltatás kéréseket, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

