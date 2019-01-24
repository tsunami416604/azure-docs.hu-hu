---
title: Jelentkezzen be az Azure Active Directory B2C-vel egyéni szabályzatok használatával egy több-bérlős Azure AD-identitásszolgáltató beállítása |} A Microsoft Docs
description: Adjon hozzá egy több-bérlős Azure ad-ben identitásszolgáltatótól az egyéni szabályzatok – Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5e3af95dc0a28207d9c95c66bc729b9255115de
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54857182"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Állítsa be a bejelentkezési több-bérlős Azure Active Directory az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára a több-bérlős végpont használata az Azure Active Directory (Azure AD) használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md) Azure AD B2C-ben. Így a felhasználók több Azure AD-bérlő minden bérlő műszaki szolgáltató konfigurálása nélkül bejelentkezni az Azure AD B2C-vel. Azonban a tagjai bármely ezeket a bérlők vendég **nem lesz** tud bejelentkezni. Ehhez meg kell [külön-külön konfigurálása mindegyik bérlő](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com` a szervezeti használt Azure AD-bérlő és `fabrikamb2c.onmicrosoft.com` használja a következő utasítások az Azure AD B2C-bérlő.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a [az Azure Active Directory B2C-vel egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Egy alkalmazás regisztrálása

Ahhoz, hogy jelentkezzen be egy meghatározott felhasználók számára az Azure AD a szervezeten belül, kell regisztrálni egy alkalmazást a szervezeti Azure AD-bérlővel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja a könyvtárat, amely tartalmazza a szervezeti Azure AD-bérlő (contoso.com) kattintva a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
4. Válassza az **Új alkalmazás regisztrálása** elemet.
5. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
6. Az a **alkalmazástípus**válassza `Web app / API`.
7. A a **bejelentkezési URL-**, adja meg a következő URL-cím csupa kisbetűvel, ahol `your-tenant` váltja fel az Azure AD B2C-bérlő (fabrikamb2c.onmicrosoft.com) nevére:

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. Kattintson a **Create** (Létrehozás) gombra. Másolás a **Alkalmazásazonosító** későbbi felhasználás céljából.
9. Válassza ki az alkalmazást, és válassza **beállítások**.
10. Válassza ki **kulcsok**, adja meg a kulcs leírása, és válassza ki az időtartamot, majd kattintson a **mentése**. Másolja későbbi felhasználás jelenik meg a kulcs értékét.
11. Alatt **beállítások**válassza **tulajdonságok**állítsa be **több-bérlős** a `Yes`, és kattintson a **mentése**

## <a name="create-a-policy-key"></a>Hozzon létre egy házirendjének kulcsa

Kell tárolni, amelyet az Azure AD B2C-bérlő az alkalmazáskulcsot.

1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
2. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
3. Az Áttekintés oldalon válassza ki a **identitás-kezelőfelületi keretrendszer – előzetes verzió**.
4. Válassza ki **Szabályzatbejegyzések** majd **Hozzáadás**.
5. A **beállítások**, válassza a `Manual`.
6. Adjon meg egy **neve** a házirend-kulcs. Például: `ContosoAppSecret`.  Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
7. A **titkos**, adja meg a korábban feljegyzett alkalmazáskulcsot.
8. A **kulcshasználat**válassza `Signature`.
9. Kattintson a **Create** (Létrehozás) gombra.

## <a name="add-a-claims-provider"></a>A jogcímeket szolgáltató hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be az Azure AD használatával, definiálhatja egy jogcímszolgáltatótól, amely az Azure AD B2C-végponton keresztül képes kommunikálni az Azure AD szeretne. A végpont ellenőrzése, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít. 

Meghatározhatja az Azure AD egy jogcímszolgáltatótól, az Azure AD-hozzáadásával a **ClaimsProvider** elem a bővítmény fájlban a szabályzat.

1. Nyissa meg a *TrustFrameworkExtensions.xml*.
2. Keresse meg a **ClaimsProviders** elemet. Ha nem létezik, adja hozzá a legfelső szintű elem alatt.
3. Vegyen fel egy új **ClaimsProvider** módon:

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

4. Alatt a **ClaimsProvider** elem, frissítse az értéket a **tartomány** egyedi értéket, hogy megkülönböztesse a más identitásszolgáltatókkal is használható.
5. Alatt a **TechnicalProfile** elem, frissítse az értéket a **DisplayName**. Ez az érték a bejelentkezés gombot a bejelentkezési képernyőn jelenik meg.
6. Állítsa be **client_id** , az Azure ad-ben mulity-tenant alkalmazásregisztráció Alkalmazásazonosítója.

### <a name="restrict-access"></a>Hozzáférés korlátozása

> [!NOTE]
> Használatával `https://sts.windows.net` értékeként **ValidTokenIssuerPrefixes** lehetővé teszi, hogy az összes Azure AD-felhasználók jelentkezzen be az alkalmazást.

Token az érvényes kiállítók listáját, és korlátozza a hozzáférést egy adott listához bejelentkezhet az Azure AD bérlő felhasználók kell. Az értékek beszerzéséhez nézze meg a metaadatok mindegyike az adott kell bejelentkezni, hogy szeretné, hogy a felhasználók Azure AD-bérlőt. Az adatok formátumának a következőhöz hasonló: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, ahol `your-tenant` az Azure AD-bérlő neve (contoso.com vagy bármely más Azure AD-bérlővel) van.

1. Nyissa meg a böngészőt, és nyissa meg a **METAADATOK** URL-címet, és keresse meg a **kibocsátó** objektumot, és másolja az értéket. A következőhöz hasonlóan kell kinéznie: `https://sts.windows.net/tenant-id/`.
2. Másolja és illessze be az értéket a **ValidTokenIssuerPrefixes** kulcsot. Több, vesszővel elválasztva adhat hozzá. Erre példa a minta XML fenti van ellátva.

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

Már konfigurálta a szabályzatot, hogy az Azure AD B2C-vel képes kommunikálni az Azure AD-címtárhoz. A szabályzat csak, győződjön meg arról, hogy minden problémát, amennyiben nem rendelkezik a bővítmény fájlt töltsön fel.

1. Az a **egyéni szabályzatok** az Azure AD B2C-bérlő, válassza a lap **szabályzat feltöltése**.
2. Engedélyezése **szabályzat felülírása, ha létezik**, és keresse meg és válassza ki a *TrustFrameworkExtensions.xml* fájlt.
3. Kattintson a **Feltöltés** gombra.

## <a name="register-the-claims-provider"></a>A jogcím-szolgáltató regisztrálása

Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrálási-regisztrálási vagy bejelentkezési képernyőt. Elérhető legyen, hozzon létre egy meglévő sablon felhasználói interakciósorozat másolatát, és ezután módosítsa, hogy az Azure ad-ben identitásszolgáltató is rendelkezik.

1. Nyissa meg a *TrustFrameworkBase.xml* az alapszintű csomag fájlt.
2. Keresse meg és másolja ki a teljes tartalmát a **UserJourney** , amely tartalmazza az elem `Id="SignUpOrSignIn"`.
3. Nyissa meg a *TrustFrameworkExtensions.xml* , és keresse meg a **UserJourneys** elemet. Ha az elem nem létezik, adjon hozzá egyet.
4. Illessze be a teljes tartalmát a **UserJourney** gyermekeként kimásolt elem a **UserJourneys** elemet.
5. Nevezze át a felhasználói út azonosítója. Például: `SignUpSignInContoso`.

### <a name="display-the-button"></a>A gomb megjelenítése

A **hiányzik a ClaimsProviderSelection** elem ehhez hasonló regisztrálási-regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy **hiányzik a ClaimsProviderSelection** elem az Azure ad-ben, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon.

1. Keresse meg a **OrchestrationStep** , amely tartalmazza az elem `Order="1"` az Ön által létrehozott felhasználói interakciósorozat.
2. A **ClaimsProviderSelects**, adja hozzá a következő elemet. Állítsa az értékét **TargetClaimsExchangeId** egy megfelelő értéket, például a `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz, az Azure ad-vel fogadhatnak jogkivonatot. A gomb csatolása egy műveletet az Azure ad-ben jogcím-szolgáltatói összekapcsolásának a technikai profil.

1. Keresse meg a **OrchestrationStep** tartalmazó `Order="2"` a felhasználói interakciósorozatban szereplő.
2. Adja hozzá a következő **ClaimsExchange** gondoskodik róla, hogy ugyanazt az értéket használt elem **azonosító** során használt **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```
    
    Frissítse az értéket a **TechnicalProfileReferenceId** , a **azonosító** a korábban létrehozott technikai profil. Például: `Common-AAD`.

3. Mentse a *TrustFrameworkExtensions.xml* fájlt, és töltse fel újra az ellenőrzéshez.

## <a name="create-an-azure-ad-b2c-application"></a>Az Azure AD B2C-alkalmazás létrehozása

Kommunikáció az Azure AD B2C-vel hoz létre a bérlő alkalmazás keresztül történik. Ez a szakasz felsorolja a nem kötelező lépések is elvégezheti egy test-alkalmazás létrehozása, ha ezt még nem tette meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adja meg egy nevet az alkalmazásnak, például *testapp1*.
6. A **Web App / Web API**, jelölje be `Yes`, majd adja meg `https://jwt.ms` a a **válasz URL-cím**.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="update-and-test-the-relying-party-file"></a>Frissítse és a függő entitás fájl tesztelése

Frissítse a függő entitásonkénti (RP) fájl, amely az Ön által létrehozott felhasználói interakciósorozat kezdeményezi.

1. Készítsen másolatot *SignUpOrSignIn.xml* a munkakönyvtárban, és nevezze át. Például nevezze át, hogy *SignUpSignContoso.xml*.
2. Nyissa meg az új fájlt, és frissítse az értéket, a **PolicyId** az attribútum **TrustFrameworkPolicy** egyedi értékkel. Például: `SignUpSignInContoso`.
3. Frissítse az értéket a **PublicPolicyUri** URI-a szabályzat. Ha például`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Frissítse az értéket, a **hivatkozásazonosító** attribútum **DefaultUserJourney** megfelelően (SignUpSignContoso) létrehozott új felhasználói interakciósorozat azonosítója.
5. A módosítások mentéséhez, feltöltheti a fájlt, és válassza ki az új házirendet a listában.
6. Győződjön meg arról, hogy a létrehozott Azure AD B2C-alkalmazást az van kiválasztva, a **válassza ki az alkalmazás** mezőben, majd tesztelje kattintva **Futtatás most**.
