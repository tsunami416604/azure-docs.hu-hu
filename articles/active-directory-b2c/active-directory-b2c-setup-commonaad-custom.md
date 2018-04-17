---
title: Egyéni házirendek – Azure Active Directory B2C használatával több-bérlős az Azure AD identity szolgáltató hozzáadása |} Microsoft Docs
description: Adja hozzá az egyéni házirendekkel – Azure Active Directory B2C egy több-bérlős az Azure AD-identitásszolgáltató
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: e751272222c493f3061eb15b8106f1c13eec960d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-into-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Engedélyezése a felhasználók számára, hogy jelentkezzen be egy egyéni házirendekkel több-bérlős az Azure AD-identitásszolgáltató

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés használatával a közös végpontot az Azure Active Directory (Azure AD) révén a felhasználók engedélyezése [egyéni házirendek](active-directory-b2c-overview-custom.md).

>[!NOTE]
> A szervezeti a használjuk a "contoso.com" Azure AD-bérlő és a "fabrikamb2c.onmicrosoft.com" az Azure AD B2C bérlő az alábbi utasításokat.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

Ezek a lépések az alábbiak:

1. Egy Azure Active Directory B2C létrehozása (Azure AD B2C) bérlő.
2. Az Azure AD B2C-alkalmazás létrehozása.
3. Két házirendmotor alkalmazás regisztrálásakor.
4. Kulcsok beállítása.
5. Az alapszintű csomag beállítása.

## <a name="create-a-multi-tenant-azure-ad-app"></a>Több-bérlős az Azure AD-alkalmazás létrehozása

Bejelentkezés a felhasználók számára a több-bérlős az Azure AD-végpont használatával engedélyezéséhez olyan több-bérlős alkalmazással, sem a bérlők számára regisztrált kell

Ebben a témakörben bemutatjuk, egy több-bérlős az Azure AD-alkalmazás létrehozása az Azure AD B2C-bérlőben.

>[!NOTE]
> Ha azt szeretné, hogy az Azure Active Directory-felhasználók **és a Microsoft-fiókkal rendelkező felhasználók** való bejelentkezéshez, hagyja ki ezt a szakaszt, és ehelyett az alkalmazás regisztrálása a [Microsoft developer portálon](https://apps.dev.microsoft.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A felső sávon válassza ki a fiókját. Az a **Directory** menüben válassza ki az Azure AD B2C-bérlő hol szeretne regisztrálni az alkalmazás (fabrikamb2c.onmicrosoft.com).
1. Válassza ki **további szolgáltatások** a bal oldali ablaktáblán, és keresse meg az "App regisztráció."
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adjon meg egy nevet az alkalmazáshoz (például `Azure AD B2C App`).
1. Válassza a **Webalkalmazás/API** lehetőséget az alkalmazás típusaként.
1. A **bejelentkezési URL-cím**, adja meg a következő URL-cím, ahol `yourtenant` cseréli le az Azure AD B2C bérlő neve (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >A "yourtenant" értékének kell lennie a kisbetűket a **bejelentkezési URL-cím**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Mentse az azonosítót.
1. Válassza ki az újonnan létrehozott alkalmazást.
1. Az a **beállítások** panelen válassza **tulajdonságok**.
1. Állítsa be **Multi-központjaként** való **Igen**.
1. Az a **beállítások** panelen válassza **kulcsok**.
1. Hozzon létre egy új kulcsot, és mentse. A következő szakaszban a lépések még szüksége lesz rájuk.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Az Azure AD-kulcs hozzáadása az Azure AD B2C

Az alkalmazás kulcsot az Azure AD B2C beállítások regisztrálnia kell. Ehhez tegye a következőket:

1. Válassza a beállítások menü az Azure AD B2C-vel
1. Kattintson a **identitás élmény keretrendszer** > **házirend kulcsok**.
1. Válassza ki **+ Hozzáadás**.
1. Válassza ki vagy adja meg ezeket a beállításokat:
   * Válassza ki **manuális**.
   * A **neve**, válasszon egy nevet, amely megfelel az Azure AD-bérlő nevét (például `AADAppSecret`).  Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
   * Illessze be az alkalmazás kulcs a **titkos** mezőbe.
   * Válassza ki **aláírás**.
5. Kattintson a **Létrehozás** gombra.
6. Győződjön meg arról, hogy létrehozta a kulcs `B2C_1A_ContosoAppSecret`.

## <a name="add-a-claims-provider-in-your-base-policy"></a>A jogcím-szolgáltató hozzáadása a kiinduló házirend

Ha azt szeretné, hogy a felhasználók jelentkezhetnek be az Azure AD használatával, kell meghatározni az Azure AD, a jogcím-szolgáltató. Ez azt jelenti meg kell adnia egy végpontot, amely az Azure AD B2C fognak kommunikálni. A végpont jogcímeket, az Azure AD B2C által használt győződjön meg arról, hogy egy adott felhasználó engedélyezést biztosít. 

Meghatározhatja az Azure AD egy jogcímszolgáltatótól, az Azure AD-be való hozzáadásával a `<ClaimsProvider>` csomópont házirend bővítmény fájlban:

1. A bővítményfájl (TrustFrameworkExtensions.xml) megnyitása a munkakönyvtárat.
1. Keresés a `<ClaimsProviders>` szakasz. Ha még nem létezik, adja hozzá a legfelső szintű csomópontja alatt.
1. Adjon hozzá egy új `<ClaimsProvider>` csomópont az alábbiak szerint:

    ```XML
      <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the client ID below to Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below -->
            <Key Id="client_secret" StorageReferenceId="Reference to Client Secret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Az a `<ClaimsProvider>` csomópont, frissítse az értéket a `<Domain>` segítségével különböztetheti meg egymástól az egyéb identitás-szolgáltatóktól származó egyedi értéket.
1. A a `<ClaimsProvider>` csomópont, frissítse az értéket a `<DisplayName>` egy rövid nevet a jogcím-szolgáltató számára. Ez az érték nincs használatban.

### <a name="update-the-technical-profile"></a>A műszaki profil frissítése

Az Azure AD-végpont egy token beszerzéséhez ját definiálja a protokollokat, amelyeket az Azure AD B2C segítségével kommunikálni az Azure AD. Ez a `<TechnicalProfile>` eleme `<ClaimsProvider>`.
1. Frissítés Azonosítóját a `<TechnicalProfile>` csomópont. Az azonosító olyan utal a műszaki profilhoz, a házirend egyéb részeitől.
1. Frissítse az értéket a `<DisplayName>`. Ez az érték jelenik meg a Bejelentkezés gombra, a bejelentkezési képernyőn.
1. Frissítse az értéket a `<Description>`.
1. Az Azure AD az OpenID Connect protokollt használja, ezért ügyeljen arra, hogy az érték `<Protocol>` van `"OpenIdConnect"`.
1. Állítsa be `<Item Key="client_id">` számára az alkalmazás regisztrálása a alkalmazás azonosítója.
1. Győződjön meg arról, hogy `<Item Key="UsePolicyInRedirectUri">` értéke `false`.

Frissítenie kell a érvényes jogkivonat kiállítók listájának. Beszerzése az értékeket, szüksége lesz a metaadatok az adott mindegyikének tekintse meg a bejelentkezés felhasználói kívánt Azure AD-bérlő. Az adatok formátumának a következőhöz hasonló: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, ahol `yourAzureADtenant` a Azure AD-bérlő neve (contoso.com).
1. Nyissa meg a böngészőt, és navigáljon a metaadatainak URL-CÍMÉT.
1. A böngészőben keresse meg a "kiállító" objektum, és másolja az értékét. A következő formában: `https://sts.windows.net/{tenantId}/`.
1. Illessze be az értéket a `ValidTokenIssuerPrefixes` kulcs. Hozzáadhat által seperating több vesszővel válassza el őket. 

> [!NOTE]
> Használatával `https://sts.windows.net` , előtag érték lehetővé teszi minden Azure AD-felhasználó bejelentkezni az alkalmazásba.

Is össze kell kapcsolni az Azure ad az Azure AD B2C bérlő regisztrált az Azure AD alkalmazás titkos kulcs `<ClaimsProvider>` információkat:

* Az a `<CryptographicKeys>` szakasz az előző XML-fájl, frissítse az értéket a `StorageReferenceId` a titkos kulcsot, amelyet megadott, a referencia-azonosítóra (például `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

Mostanra így az Azure AD B2C tudja végponttal való kommunikációhoz az általános az Azure AD konfigurálta a házirendet. Próbálja feltölti a bővítmény a házirend csak győződjön meg arról, hogy nincs probléma merül fel eddig. Ehhez tegye a következőket:

1. Nyissa meg a **házirend** panel az Azure AD B2C-bérlőben.
1. Ellenőrizze a **felülírja a házirendet, ha létezik** mezőbe.
1. A bővítményfájl (TrustFrameworkExtensions.xml) feltöltése, és győződjön meg arról, hogy azt nem az érvényesítés.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Regisztráció az Azure AD jogcímszolgáltató felhasználói út

Most kell az Azure AD identity provider hozzáadása a felhasználói utak egyike. Ezen a ponton az identitásszolgáltató be van állítva, de a sign-Close-Up/sign-in képernyők egyik nem érhető el. Tegye elérhetővé, a rendszer létrehoz egy meglévő sablon felhasználói út duplikált, és módosíthatja azt, hogy az Azure AD identity provider is rendelkezik:

1. Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
1. Keresés a `<UserJourneys>` elemet, és másolja a teljes `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"`.
1. Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keresse a `<UserJourneys>` elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a teljes `<UserJourney>` csomópont gyermekeként másolt a `<UserJourneys>` elemet.
1. Nevezze át az új felhasználói út azonosítója (például a módon nevezze át `SignUpOrSignUsingAzureAD`).

### <a name="display-the-button"></a>A "gomb"

A `<ClaimsProviderSelection>` a elem egy identitás szolgáltató gombjára egy sign-Close-Up/bejelentkezési képernyő hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` elem az Azure AD, egy új gombon, amikor egy felhasználó fájljai az oldalon. Ez az elem hozzáadása:

1. Keresse a `<OrchestrationStep>` tartalmazó csomópont `Order="1"` az újonnan létrehozott felhasználó út található.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Állítsa be `TargetClaimsExchangeId` egy megfelelő értékre. Azt javasoljuk, mint mások azonos egyezmény követően:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy a gomb helyen, hogy egy művelet kapcsolódnia kell. A művelet, ebben az esetben, akkor az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot az Azure AD. A gomb művelet mutató hivatkozás létrehozása az Azure AD jogcímeket szolgáltató létrehozhatja, ha a műszaki profil:

1. Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Frissítés `Id` ugyanarra az értékre, amely `TargetClaimsExchangeId` az előző szakaszban leírt.
1. Frissítés `TechnicalProfileReferenceId` a műszaki profil azonosítója létrehozott korábbi (közös-aad-ben).

### <a name="upload-the-updated-extension-file"></a>A frissített kiterjesztésű fájl feltöltése

Befejezte a bővítmény fájl módosítása. Mentse a fájlt. Majd feltölteni a fájlt, és győződjön meg arról, hogy az összes érvényesítések sikeres.

### <a name="update-the-rp-file"></a>A függő Entitás fájl frissítése

Most frissíteni kell a függő entitásonkénti (RP) fájl, amely indít el az újonnan létrehozott felhasználó út:

1. Készítsen másolatot a SignUpOrSignIn.xml a munkakönyvtárat, és adjon neki (például nevezze át SignUpOrSignInWithAAD.xml).
1. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel (például SignUpOrSignInWithAAD). <br> Ez lesz a házirend nevét (például B2C\_1A\_SignUpOrSignInWithAAD).
1. Módosítsa a `ReferenceId` attribútumnak `<DefaultUserJourney>` megfelelően az új felhasználói út (SignUpOrSignUsingAzureAD) létrehozott azonosítója.
1. A módosítások mentéséhez, és töltse fel a fájlt.

## <a name="troubleshooting"></a>Hibaelhárítás

Tesztelje a egyéni házirendet, majd kattintson a panel megnyitása feltöltött **futtatása most**. Problémák diagnosztizálásához, olvassa el [hibaelhárítási](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>További lépések

Visszajelzés küldése a [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
