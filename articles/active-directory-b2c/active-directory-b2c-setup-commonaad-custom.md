---
title: Adjon hozzá egy több-bérlős Azure ad-ben identitásszolgáltatótól az egyéni szabályzatok használatával az Azure Active Directory B2C |} A Microsoft Docs
description: Adjon hozzá egy több-bérlős Azure ad-ben identitásszolgáltatótól az egyéni szabályzatok – Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2a8a23245a17c9a80c70860588a8312dbbb5e926
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446072"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Az Azure Active Directory B2C: Engedélyezése a felhasználók számára, hogy jelentkezzen be egy több-bérlős Azure ad-ben identitásszolgáltatótól az egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a több-bérlős végpont használata az Azure Active Directory (Azure AD) használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md). Így a felhasználók több Azure AD-bérlő minden bérlő műszaki szolgáltató konfigurálása nélkül bejelentkezni az Azure AD B2C-vel. Azonban a tagjai bármely ezeket a bérlők vendég **nem lesz** tud bejelentkezni. Ehhez el [külön-külön konfigurálása mindegyik bérlő](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> A szervezeti használjuk a "contoso.com" Azure AD-bérlő és a "fabrikamb2c.onmicrosoft.com" a következő utasítások az Azure AD B2C-bérlő.

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

Ezeket a lépéseket tartalmazza:
     
1. Az Azure Active Directory B2C létrehozása (Azure AD B2C) bérlő.
1. Az Azure AD B2C-alkalmazás létrehozása.    
1. Két csoportházirend-motor alkalmazás regisztrálásakor.  
1. Kulcsok beállításához. 
1. Az alapszintű csomag beállítása.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>1. lépés Egy több-bérlős Azure AD-alkalmazás létrehozása

Bejelentkezés engedélyezése a felhasználók számára a több-bérlős Azure AD-végpont használatával, meg kell egy regisztrált az Azure AD-bérlőt egy több-bérlős alkalmazást. Ebben a cikkben bemutatjuk, hogyan lehet egy több-bérlős Azure AD-alkalmazás létrehozása az Azure AD B2C-bérlőben. Ezután bejelentkezés engedélyezése a felhasználók számára, hogy több-bérlős Azure AD-alkalmazás használatával.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az a felső menüsávon válassza a fiókját. Az a **Directory** menüben válassza ki az Azure AD B2C-bérlő regisztrálásához az Azure AD-alkalmazás (fabrikamb2c.onmicrosoft.com).
1. Válassza ki **további szolgáltatások** a bal oldali panelen, és keresse meg az "Alkalmazásregisztrációk".
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adja meg az alkalmazás nevét (például `Azure AD B2C App`).
1. Válassza a **Webalkalmazás/API** lehetőséget az alkalmazás típusaként.
1. A **bejelentkezési URL-**, adja meg a következő URL-címet, ahol `yourtenant` váltotta fel az Azure AD B2C-bérlő neve (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >"Yourtenant" értéke csak kisbetűket a a **bejelentkezési URL-**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Mentse az alkalmazás azonosítója.
1. Válassza ki az újonnan létrehozott alkalmazást.
1. Alatt a **beállítások** panelen válassza ki **tulajdonságok**.
1. Állítsa be **több-bérlős** való **Igen**.
1. Alatt a **beállítások** panelen válassza ki **kulcsok**.
1. Hozzon létre egy új kulcsot, és mentse azt. A következő szakasz lépései alkalmaznak.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>2. lépés Adja meg az Azure AD-kulcsot az Azure AD B2C-vel

Az Azure AD B2C beállításaiban az alkalmazáskulcsot regisztrálnia kell. Ehhez tegye a következőket:

1. Az Azure AD B2C nyissa meg a beállítások menü
1. Kattintson a **identitás-kezelőfelületi keretrendszer** > **Szabályzatbejegyzések**.
1. Válassza ki **+ Hozzáadás**.
1. Válassza ki vagy adja meg ezeket a beállításokat:
   * Válassza ki **manuális**.
   * A **neve**, válasszon egy nevet, amely megfelel az Azure AD-bérlő neve (például `AADAppSecret`).  Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
   * Illessze be az alkalmazáskulcsot az a **titkos** mezőbe.
   * Válassza ki **aláírás**.
1. Kattintson a **Létrehozás** gombra.
1. Győződjön meg arról, hogy a kulcs létrehozott `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>3. lépés A kiinduló szabályzatban egy jogcímszolgáltatótól hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be az Azure AD használatával, definiálhatja az Azure AD egy jogcímszolgáltatótól szeretne. Más szóval meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont, győződjön meg arról, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít. 

Meghatározhatja az Azure AD egy jogcímszolgáltatótól, az Azure AD-hozzáadásával a `<ClaimsProvider>` csomópont a szabályzat a bővítmény fájlban:

1. A bővítményfájl (TrustFrameworkExtensions.xml) nyissa meg a munkakönyvtár.
1. Keresse meg a `<ClaimsProviders>` szakaszban. Ha nem létezik, adja hozzá a legfelső szintű csomópontja alatt.
1. Vegyen fel egy új `<ClaimsProvider>` csomópont az alábbiak szerint:

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

1. Alatt a `<ClaimsProvider>` csomópont, frissítse az értéket a `<Domain>` egyedi értéket, hogy megkülönböztesse a más identitásszolgáltatókkal is használható.
1. Alatt a `<TechnicalProfile>` csomópont, frissítse az értéket a `<DisplayName>`. Ez az érték a Bejelentkezés gombra a bejelentkezési képernyőn jelennek meg.
1. Frissítse az értéket a `<Description>`.
1. Állítsa be `<Item Key="client_id">` , az Azure ad-ben mulity-tenant alkalmazásregisztráció Alkalmazásazonosítója.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Az Azure AD-bérlőt egy adott lista hozzáférés korlátozása 3.1. lépés

> [!NOTE]
> Használatával `https://sts.windows.net` értékeként **ValidTokenIssuerPrefixes** lehetővé teszi az összes Azure AD-felhasználók bejelentkeznie az alkalmazásba.

Token az érvényes kiállítók listáját, és korlátozza a hozzáférést adott listájához is bejelentkeznek az Azure AD-bérlőt kell. Szerezze be az értékeket, szüksége lesz a metaadatokat az egyes az adott meg, hogy szeretné, hogy a felhasználók Azure AD-bérlőt jelentkezhessen be. Az adatok formátumának a következőhöz hasonló: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, ahol `yourAzureADtenant` az Azure AD-bérlő neve (contoso.com vagy bármely más Azure AD-bérlővel) van.
1. Nyissa meg a böngészőt, és nyissa meg a metaadatok URL-címe.
1. A böngészőben keresse meg a "kiállító" objektumot, és másolja az értéket. A következőhöz hasonlóan kell kinéznie: `https://sts.windows.net/{tenantId}/`.
1. Illessze be az értéket a `ValidTokenIssuerPrefixes` kulcsot. Több, vesszővel elválasztva adhat hozzá. Erre példa a minta XML fenti van ellátva.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>4. lépés Az Azure ad-ben fiók jogcím-szolgáltató regisztrálása

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1. lépés a felhasználói út másolatot készíteni.

Most szüksége az Azure ad-ben identitásszolgáltató hozzáadása a felhasználói utak egyike. Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrálási-regisztrálási vagy bejelentkezési képernyőt.

Elérhető legyen, rendszer létrehoz egy meglévő sablon felhasználói interakciósorozat másolatát, és ezután módosítsa, hogy az Azure ad-ben identitásszolgáltató is rendelkezik:

1. Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
1. Keresse meg a `<UserJourneys>` elem és a teljes másolási `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"`.
1. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adjon hozzá egyet.
1. Illessze be a teljes `<UserJourney>` csomópont gyermekeként kimásolt a `<UserJourneys>` elemet.
1. Nevezze át az új felhasználói interakciósorozat azonosítója (mint például nevezze át `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>A "gombot" lépés 4.2 megjelenítése

A `<ClaimsProviderSelection>` elem ehhez hasonló regisztrálási-regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy `<ClaimsProviderSelection>` elem az Azure ad-ben, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadása:

1. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"` az Ön által létrehozott felhasználói interakciósorozat.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Állítsa be `TargetClaimsExchangeId` egy megfelelő értékre. Javasoljuk, hogy kövesse a, a többi felületéével azonos konvenciókat követnek:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>A gombra kattintva művelet 4.3. lépés-hivatkozás

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz, az Azure ad-vel fogadhatnak jogkivonatot. A gomb összekapcsolása egy műveletet az Azure ad-ben jogcím-szolgáltatói összekapcsolásának a technikai profil:

1. Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Frissítés `Id` ugyanarra az értékre, mint a `TargetClaimsExchangeId` az előző szakaszban.
1. Frissítés `TechnicalProfileReferenceId` a technikai profil azonosítója létrehozott korábbi (közös – AAD).

## <a name="step-5-create-a-new-rp-policy"></a>5. lépés: Új RP-házirend létrehozása

Akkor most frissítenie kell a függő entitásonkénti (RP) fájl, megkezdődik az imént létrehozott felhasználói interakciósorozatban:
 
1. A munkakönyvtárban SignUpOrSignIn.xml másolatot készíteni, és nevezze át (például nevezze át a SignUpOrSignInWithAAD.xml).  
1. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel (például SignUpOrSignInWithAAD). Ez lesz a házirend nevét (például a B2C\_1A\_SignUpOrSignInWithAAD). 
1. Módosítsa a `ReferenceId` attribútum `<DefaultUserJourney>` megfelelően (SignUpOrSignUsingAzureAD) létrehozott új felhasználói interakciósorozat azonosítója. 
1. Mentse a módosításokat, és töltse fel a fájlt. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6. lépés: A szabályzat feltöltése a bérlőhöz

1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **Azure AD B2C-vel**.
1. Válassza ki **identitás-kezelőfelületi keretrendszer**.
1. Válassza ki **összes szabályzat**.
1. Válassza ki **szabályzat feltöltése**.
1. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.
1. Töltse fel a `TrustFrameworkExtensions.xml` és az RP-fájlt (pl. `SignUpOrSignInWithAAD.xml`) és ellenőrizze, hogy azok megfelelnek az ellenőrzés.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7. lépés: Az egyéni házirend tesztelése a Futtatás most

1. Válassza ki **Azure AD B2C-beállítások**, majd válassza ki **identitás-kezelőfelületi keretrendszer**.
    > [!NOTE]
    > Futtatás most kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.

1. Nyissa meg a függő entitásonkénti (RP) egyéni házirend-feltöltött (*B2C\_1A\_SignUpOrSignInWithAAD*), majd válassza ki **Futtatás most**.
1. Most kell tudni az Azure AD-fiók használatával jelentkezzen be.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Nem kötelező) 8. lépés: Regisztráljon az Azure ad-ben fiók jogcímszolgáltató Profilmódosítás felhasználói interakciósorozat

Emellett érdemes az Azure ad-ben fiók identitásszolgáltató hozzáadása a `ProfileEdit` felhasználói interakciósorozat. A felhasználói út elérhetővé, ismételje meg az 4 – 6. Ezúttal válassza a `<UserJourney>` tartalmazó csomópont `Id="ProfileEdit"`. Mentse, töltse fel, és tesztelje a házirendet.

## <a name="troubleshooting"></a>Hibaelhárítás

Diagnosztizálhatja a problémákat, olvassa el [hibaelhárítási](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>További lépések

Visszajelzést küldhet [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
