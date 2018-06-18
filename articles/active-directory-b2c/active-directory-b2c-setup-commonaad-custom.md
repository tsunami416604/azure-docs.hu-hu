---
title: Egyéni házirendek az Azure Active Directory B2C segítségével több-bérlős az Azure AD identity szolgáltató hozzáadása |} Microsoft Docs
description: Egyéni házirendek – Azure Active Directory B2C használatával több-bérlős az Azure AD identity szolgáltató hozzáadása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 83a2ce5d885a446713470c92fc3a638d37d4517d
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709223"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Engedélyezése a felhasználók számára, hogy jelentkezzen be egy egyéni házirendekkel több-bérlős az Azure AD-identitásszolgáltató

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezhet a több-bérlős végpont használatával az Azure Active Directory (Azure AD) segítségével a felhasználók engedélyezése [egyéni házirendek](active-directory-b2c-overview-custom.md). Így a felhasználók több Azure AD-bérlő bejelentkezni az Azure AD B2C az egyes bérlők műszaki szolgáltató konfigurálása nélkül. Azonban Vendég tagok a bérlők valamelyikében **sem fog** tudnak bejelentkezni. Az adott, akkor kell [külön-külön konfigurálása mindegyik bérlő](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> A szervezeti a használjuk a "contoso.com" Azure AD-bérlő és a "fabrikamb2c.onmicrosoft.com" az Azure AD B2C bérlő az alábbi utasításokat.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) cikk.

Ezek a lépések az alábbiak:
     
1. Egy Azure Active Directory B2C létrehozása (Azure AD B2C) bérlő.
1. Az Azure AD B2C-alkalmazás létrehozása.    
1. Két házirendmotor alkalmazás regisztrálásakor.  
1. Kulcsok beállítása. 
1. Az alapszintű csomag beállítása.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>1. lépés Több-bérlős az Azure AD-alkalmazás létrehozása

Bejelentkezés a felhasználók számára a több-bérlős az Azure AD-végpont használatával engedélyezéséhez kell regisztrálni az egyik az Azure AD-bérlő több-bérlős alkalmazás. Ebben a témakörben bemutatjuk, egy több-bérlős az Azure AD-alkalmazás létrehozása az Azure AD B2C-bérlőben. Bejelentkezés a felhasználók számára, hogy több-bérlős az Azure AD alkalmazás segítségével engedélyezze.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A felső sávon válassza ki a fiókját. Az a **Directory** menüben válassza ki az Azure AD B2C-bérlő regisztrálása az Azure AD-alkalmazás (fabrikamb2c.onmicrosoft.com).
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

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>2. lépés Az Azure AD-kulcs hozzáadása az Azure AD B2C

Az alkalmazás kulcsot az Azure AD B2C beállítások regisztrálnia kell. Ehhez tegye a következőket:

1. Válassza a beállítások menü az Azure AD B2C-vel
1. Kattintson a **identitás élmény keretrendszer** > **házirend kulcsok**.
1. Válassza ki **+ Hozzáadás**.
1. Válassza ki vagy adja meg ezeket a beállításokat:
   * Válassza ki **manuális**.
   * A **neve**, válasszon egy nevet, amely megfelel az Azure AD-bérlő nevét (például `AADAppSecret`).  Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
   * Illessze be az alkalmazás kulcs a **titkos** mezőbe.
   * Válassza ki **aláírás**.
1. Kattintson a **Létrehozás** gombra.
1. Győződjön meg arról, hogy létrehozta a kulcs `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>3. lépés A jogcím-szolgáltató hozzáadása a kiinduló házirend

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
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
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
1. Az a `<TechnicalProfile>` csomópont, frissítse az értéket a `<DisplayName>`. Ez az érték jelenik meg a Bejelentkezés gombra, a bejelentkezési képernyőn.
1. Frissítse az értéket a `<Description>`.
1. Állítsa be `<Item Key="client_id">` az alkalmazás azonosító az Azure AD bérlői mulity app vételét.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>3.1 lépés a hozzáférés korlátozása az Azure AD-bérlő adott listájára

> [!NOTE]
> Használatával `https://sts.windows.net` értéke **ValidTokenIssuerPrefixes** lehetővé teszi, hogy minden Azure AD-felhasználó bejelentkezni az alkalmazásba.

A jogkivonat érvényes kiállítók listájának frissítése és a hozzáférés korlátozása adott listára a felhasználók is bejelentkezhet az Azure AD-bérlő kell. Beszerzése az értékeket, szüksége lesz a metaadatok az adott mindegyikének tekintse meg a bejelentkezés felhasználói kívánt Azure AD-bérlő. Az adatok formátumának a következőhöz hasonló: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, ahol `yourAzureADtenant` a Azure AD-bérlő neve (contoso.com vagy bármely más Azure AD-bérlő).
1. Nyissa meg a böngészőt, és navigáljon a metaadatainak URL-CÍMÉT.
1. A böngészőben keresse meg a "kiállító" objektum, és másolja az értékét. A következő formában: `https://sts.windows.net/{tenantId}/`.
1. Illessze be az értéket a `ValidTokenIssuerPrefixes` kulcs. Több, vesszővel elválasztva adhat hozzá. Ilyen például a fenti XML minta megjegyzésként.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>4. lépés Az Azure AD fiók jogcím-szolgáltató regisztrálása

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1 lépés másolatot készít a felhasználó út

Most kell az Azure AD identity provider hozzáadása a felhasználói utak egyike. Ezen a ponton az identitásszolgáltató be van állítva, de a sign-Close-Up/sign-in képernyők egyik nem érhető el.

Tegye elérhetővé, a rendszer létrehoz egy meglévő sablon felhasználói út duplikált, és módosíthatja azt, hogy az Azure AD identity provider is rendelkezik:

1. Nyissa meg a házirend (például TrustFrameworkBase.xml) Alap fájlt.
1. Keresés a `<UserJourneys>` elemet, és másolja a teljes `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"`.
1. Nyissa meg a bővítmény (például TrustFrameworkExtensions.xml) fájlt, és keresse a `<UserJourneys>` elemet. Ha az elem nem létezik, vegyen fel egyet.
1. Illessze be a teljes `<UserJourney>` csomópont gyermekeként másolt a `<UserJourneys>` elemet.
1. Nevezze át az új felhasználói út azonosítója (például a módon nevezze át `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>A "button" lépés 4.2 megjelenítése

A `<ClaimsProviderSelection>` a elem egy identitás szolgáltató gombjára egy sign-Close-Up/bejelentkezési képernyő hasonló. Ha ad hozzá egy `<ClaimsProviderSelection>` elem az Azure AD, egy új gombon, amikor egy felhasználó fájljai az oldalon. Ez az elem hozzáadása:

1. Keresés a `<OrchestrationStep>` tartalmazó csomópont `Order="1"` a létrehozott felhasználói utazás.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Állítsa be `TargetClaimsExchangeId` egy megfelelő értékre. Azt javasoljuk, mint mások azonos egyezmény követően:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>4.3 lépés hivatkozás a gombra kattintva egy műveletet

Most, hogy a gomb helyen, hogy egy művelet kapcsolódnia kell. A művelet, ebben az esetben, akkor az Azure AD B2C-vel való kommunikációhoz fogadhatnak jogkivonatot az Azure AD. A gomb művelet mutató hivatkozás létrehozása az Azure AD jogcímeket szolgáltató létrehozhatja, ha a műszaki profil:

1. Keresés a `<OrchestrationStep>` is tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Frissítés `Id` ugyanarra az értékre, amely `TargetClaimsExchangeId` az előző szakaszban leírt.
1. Frissítés `TechnicalProfileReferenceId` a műszaki profil azonosítója létrehozott korábbi (közös-aad-ben).

## <a name="step-5-create-a-new-rp-policy"></a>5. lépés: Új RP házirend létrehozása

Most frissíteni kell a függő entitásonkénti (RP) fájl, amely indít el az újonnan létrehozott felhasználó út:
 
1. Készítsen másolatot a SignUpOrSignIn.xml a munkakönyvtárat, és adjon neki (például nevezze át SignUpOrSignInWithAAD.xml).  
1. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel (például SignUpOrSignInWithAAD). Ez lesz a házirend nevét (például B2C\_1A\_SignUpOrSignInWithAAD). 
1. Módosítsa a `ReferenceId` attribútumnak `<DefaultUserJourney>` megfelelően az új felhasználói út (SignUpOrSignUsingAzureAD) létrehozott azonosítója. 
1. A módosítások mentéséhez, és töltse fel a fájlt. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6. lépés: Töltse fel a házirend a bérlő

1. A a [Azure-portálon](https://portal.azure.com), váltson a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **az Azure AD B2C**.
1. Válassza ki **identitás élmény keretrendszer**.
1. Válassza ki **házirend**.
1. Válassza ki **házirend feltöltése**.
1. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.
1. Töltse fel a `TrustFrameworkExtensions.xml` és a függő Entitás fájl (pl. `SignUpOrSignInWithAAD.xml`), és győződjön meg arról, hogy teljesíti az ellenőrző.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7. lépés: Az egyéni házirend tesztelése Futtatás most használatával

1. Válassza ki **az Azure AD B2C beállítások**, majd válassza ki **identitás élmény keretrendszer**.
    > [!NOTE]
    > Futtatás most a tenant preregistered kell legalább egy alkalmazás szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.

1. Nyissa meg a függő entitásonkénti (RP) egyéni házirend-feltöltött (*B2C\_1A\_SignUpOrSignInWithAAD*), majd válassza ki **futtatása most**.
1. Most kell az Azure AD-fiókot használva jelentkezhet be.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Választható) 8. lépés: A profil szerkesztheti a felhasználói út az Azure AD fiók jogcímszolgáltató regisztrálása

Érdemes is az Azure AD fiók identitásszolgáltató hozzáadása a `ProfileEdit` felhasználói út. A felhasználó út elérhetővé, ismételje meg az 4 – 6. Most, jelölje be a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"`. Mentse, töltse fel, és tesztelje a házirendet.

## <a name="troubleshooting"></a>Hibaelhárítás

Problémák diagnosztizálásához, olvassa el [hibaelhárítási](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>További lépések

Visszajelzés küldése a [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
