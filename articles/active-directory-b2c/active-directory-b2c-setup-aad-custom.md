---
title: Egy Azure AD-szolgáltató hozzáadása az Azure Active Directory B2C-vel egyéni szabályzatok használatával |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: af97a85b4d5d9c38f0e2bf8947482a0585fa6ee1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338311"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Az Azure Active Directory B2C: Jelentkezzen be az Azure AD-fiókok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk bemutatja, hogyan bejelentkezés engedélyezése a felhasználók számára egy adott Azure Active Directory (Azure AD) szervezet használatával [egyéni szabályzatok](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Előfeltételek

A lépések elvégzéséhez a [Ismerkedés az egyéni szabályzatok](active-directory-b2c-get-started-custom.md) cikk.

Ezeket a lépéseket tartalmazza:

1. Az Azure Active Directory B2C létrehozása (Azure AD B2C) bérlő.
2. Az Azure AD B2C-alkalmazás létrehozása.
3. Két csoportházirend-motor alkalmazás regisztrálásakor.
4. Kulcsok beállításához.
5. Az alapszintű csomag beállítása.

## <a name="create-an-azure-ad-app"></a>Az Azure AD-alkalmazás létrehozása

Ahhoz, hogy jelentkezzen be egy meghatározott felhasználók számára az Azure AD a szervezeten belül, kell regisztrálni egy alkalmazást a szervezeti Azure AD-bérlővel.

>[!NOTE]
> A szervezeti használjuk a "contoso.com" Azure AD-bérlő és a "fabrikamb2c.onmicrosoft.com" a következő utasítások az Azure AD B2C-bérlő.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az a felső menüsávon válassza a fiókját. Az a **Directory** menüben válassza ki a szervezeti Azure AD-bérlővel, ahol szeretné regisztrálni az alkalmazást (contoso.com).
3. Válassza ki **további szolgáltatások** a bal oldali panelen, és keresse meg az "Alkalmazásregisztrációk".
4. Válassza az **Új alkalmazás regisztrálása** elemet.
5. Adja meg az alkalmazás nevét (például `Azure AD B2C App`).
6. Válassza a **Webalkalmazás/API** lehetőséget az alkalmazás típusaként.
7. A **bejelentkezési URL-**, adja meg a következő URL-címet, ahol `yourtenant` váltotta fel az Azure AD B2C-bérlő neve (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >"Yourtenant" értéke csak kisbetűket a a **bejelentkezési URL-**.

    ```
    https://yourtenant.b2clogin.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. Mentse az alkalmazás azonosítója.
9. Válassza ki az újonnan létrehozott alkalmazást.
10. Alatt a **beállítások** panelen válassza ki **kulcsok**.
11. Adja meg a kulcs leírása, válassza ki az időtartamot, és kattintson **mentése**. A kulcsnak az értéke jelenik meg. Másolja ki azt a beállítást, mert fogja használni a következő szakasz lépéseit.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Adja meg az Azure AD-kulcsot az Azure AD B2C-vel

Az Azure AD B2C-bérlőben a contoso.com Alkalmazáskulcs tárolására van szüksége. Ehhez tegye a következőket:
1. Nyissa meg az Azure AD B2C-bérlő, és válassza ki **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer** > **Szabályzatbejegyzések**.
1. Válassza ki **+ Hozzáadás**.
1. Válassza ki vagy adja meg ezeket a beállításokat:
   * Válassza ki **manuális**.
   * A **neve**, válasszon egy nevet, amely megfelel az Azure AD-bérlő neve (például `ContosoAppSecret`).  Az előtag `B2C_1A_` automatikusan hozzáadódik a kulcs neve.
   * Illessze be az alkalmazáskulcsot az a **titkos** mezőbe.
   * Válassza ki **aláírás**.
1. Kattintson a **Létrehozás** gombra.
1. Győződjön meg arról, hogy a kulcs létrehozott `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>A kiinduló szabályzatban egy jogcímszolgáltatótól hozzáadása

Ha azt szeretné, hogy a felhasználók számára, hogy jelentkezzen be az Azure AD használatával, definiálhatja az Azure AD egy jogcímszolgáltatótól szeretne. Más szóval meg kell adnia egy végpontot, amely az Azure AD B2C-vel kommunikál. A végpont, győződjön meg arról, hogy egy adott felhasználó rendelkezik hitelesítése Azure AD B2C által használt jogcímeket biztosít. 

Meghatározhatja az Azure AD egy jogcímszolgáltatótól, az Azure AD-hozzáadásával a `<ClaimsProvider>` csomópont a szabályzat a bővítmény fájlban:

1. A bővítményfájl (TrustFrameworkExtensions.xml) nyissa meg a munkakönyvtár.
1. Keresse meg a `<ClaimsProviders>` szakaszban. Ha nem létezik, adja hozzá a legfelső szintű csomópontja alatt.
1. Vegyen fel egy új `<ClaimsProvider>` csomópont az alábbiak szerint:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Alatt a `<ClaimsProvider>` csomópont, frissítse az értéket a `<Domain>` egyedi értéket, hogy megkülönböztesse a más identitásszolgáltatókkal is használható.
1. Alatt a `<ClaimsProvider>` csomópont, frissítse az értéket a `<DisplayName>` , egy rövid nevet a jogcímszolgáltató. Ez az érték jelenleg nem használja.

### <a name="update-the-technical-profile"></a>A technikai profil frissítése

Egy token beszerzése az Azure AD-végpontról, adja meg a protokoll, amely az Azure AD B2C-vel való kommunikációhoz, az Azure ad-vel kell használnia kell. Ez a `<TechnicalProfile>` eleme `<ClaimsProvider>`.
1. Frissítés azonosítója a `<TechnicalProfile>` csomópont. Ez az azonosító segítségével tekintse meg a házirend egyéb részei a technikai profil.
1. Frissítse az értéket a `<DisplayName>`. Ez az érték a Bejelentkezés gombra a bejelentkezési képernyőn jelennek meg.
1. Frissítse az értéket a `<Description>`.
1. Azure ad-ben az OpenID Connect protokollt használja, ezért győződjön meg arról, hogy az érték `<Protocol>` van `"OpenIdConnect"`.

Frissítenie kell a `<Metadata>` szakasz az XML-fájlt az említett korábban a konkrét konfigurációs beállításoknak az Azure AD-bérlővel. Az XML-fájlban frissítse a metaadatokat a következő:

1. Állítsa be `<Item Key="METADATA">` való `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, ahol `yourAzureADtenant` van az Azure AD-bérlő neve (contoso.com).
1. Nyissa meg a böngészőt, és nyissa meg a `METADATA` imént frissített URL-CÍMÉT.
1. A böngészőben keresse meg a "kiállító" objektumot, és másolja az értéket. A következőhöz hasonlóan kell kinéznie: `https://sts.windows.net/{tenantId}/`.
1. Illessze be az értéket a `<Item Key="ProviderName">` az XML-fájlban.
1. Állítsa be `<Item Key="client_id">` való az alkalmazás regisztrációját Alkalmazásazonosítója.
1. Állítsa be `<Item Key="IdTokenAudience">` való az alkalmazás regisztrációját Alkalmazásazonosítója.
1. Ügyeljen arra, hogy `<Item Key="response_types">` értékre van állítva `id_token`.
1. Ügyeljen arra, hogy `<Item Key="UsePolicyInRedirectUri">` értékre van állítva `false`.

Is kell összekapcsolni az Azure ad-ben titkos kulcsot, hogy regisztrált az Azure AD B2C-bérlő az Azure ad `<ClaimsProvider>` információkat:

* Az a `<CryptographicKeys>` szakaszban az előző XML-fájl, módosítsa a `StorageReferenceId` , a titkos kulcsot, amelyet megadott hivatkozási azonosítója (például `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Az ellenőrzéshez a bővítmény-fájl feltöltése

Már konfigurálta a szabályzatot, hogy az Azure AD B2C-vel képes kommunikálni az Azure AD-címtárhoz. A szabályzat csak, győződjön meg arról, hogy minden problémát, amennyiben nem rendelkezik a bővítmény fájlt töltsön fel. Ehhez tegye a következőket:

1. Nyissa meg a **összes szabályzat** panel az Azure AD B2C-bérlőben.
1. Ellenőrizze a **szabályzat felülírása, ha létezik** mezőbe.
1. Töltse fel a kiterjesztésű fájlt (TrustFrameworkExtensions.xml), és győződjön meg arról, hogy azt a az érvényesítése nem hiúsul.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Jogcím-szolgáltató regisztrálása az Azure AD a felhasználói út

Most szüksége az Azure ad-ben identitásszolgáltató hozzáadása a felhasználói utak egyike. Ezen a ponton az identitásszolgáltató be lett állítva, de nem érhető el az összes regisztrálási-regisztrálási vagy bejelentkezési képernyőt. Elérhető legyen, rendszer létrehoz egy meglévő sablon felhasználói interakciósorozat másolatát, és ezután módosítsa, hogy az Azure ad-ben identitásszolgáltató is rendelkezik:

1. Nyissa meg a szabályzat (például TrustFrameworkBase.xml) alapszintű fájlt.
1. Keresse meg a `<UserJourneys>` elem és a teljes másolási `<UserJourney>` tartalmazó csomópont `Id="SignUpOrSignIn"`.
1. Nyissa meg a kiterjesztésű fájlt (például TrustFrameworkExtensions.xml), és keresse meg a `<UserJourneys>` elemet. Ha az elem nem létezik, adjon hozzá egyet.
1. Illessze be a teljes `<UserJourney>` csomópont gyermekeként kimásolt a `<UserJourneys>` elemet.
1. Nevezze át az új felhasználói interakciósorozat azonosítója (mint például nevezze át `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>A "gombot" megjelenítése

A `<ClaimsProviderSelection>` elem ehhez hasonló regisztrálási-regisztrálási vagy bejelentkezési képernyőn egy identitás szolgáltató a gombhoz. Ha hozzáad egy `<ClaimsProviderSelection>` elem az Azure ad-ben, egy új gomb megjelenik-e, amikor egy felhasználó hajtanak végre az oldalon. Ez az elem hozzáadása:

1. Keresse meg a `<OrchestrationStep>` tartalmazó csomópont `Order="1"` az imént létrehozott felhasználói interakciósorozat.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Állítsa be `TargetClaimsExchangeId` egy megfelelő értékre. Javasoljuk, hogy kövesse a, a többi felületéével azonos konvenciókat követnek:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>A gomb összekapcsolása egy műveletet

Most, hogy egyetlen helyen, amelyekkel hozzákapcsolhatja egy műveletet kell. A művelet, ebben az esetben pedig az Azure AD B2C-vel való kommunikációhoz, az Azure ad-vel fogadhatnak jogkivonatot. A gomb összekapcsolása egy műveletet az Azure ad-ben jogcím-szolgáltatói összekapcsolásának a technikai profil:

1. Keresse meg a `<OrchestrationStep>` tartalmazó `Order="2"` a a `<UserJourney>` csomópont.
1. Adja hozzá a következőket:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Frissítés `Id` ugyanarra az értékre, mint a `TargetClaimsExchangeId` az előző szakaszban.
1. Frissítés `TechnicalProfileReferenceId` a technikai profil azonosítója létrehozott korábbi (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>A frissített kiterjesztésű fájl feltöltése

Elkészült a kiterjesztésű fájl módosítása. Mentse a fájlt. Ezután feltöltheti a fájlt, és győződjön meg arról, hogy az összes ellenőrzés sikeres.

### <a name="update-the-rp-file"></a>A helyreállítási pont Védettként fájl frissítése

Akkor most frissítenie kell a függő entitásonkénti (RP) fájl, megkezdődik az imént létrehozott felhasználói interakciósorozatban:

1. A munkakönyvtárban SignUpOrSignIn.xml másolatot készíteni, és nevezze át (például nevezze át a SignUpOrSignInWithAAD.xml).
1. Nyissa meg az új fájlt, és frissítse a `PolicyId` az attribútum `<TrustFrameworkPolicy>` egyedi értékkel (például SignUpOrSignInWithAAD). <br> Ez lesz a házirend nevét (például a B2C\_1A\_SignUpOrSignInWithAAD).
1. Módosítsa a `ReferenceId` attribútum `<DefaultUserJourney>` megfelelően (SignUpOrSignUsingContoso) létrehozott új felhasználói interakciósorozat azonosítója.
1. Mentse a módosításokat, és töltse fel a fájlt.

## <a name="troubleshooting"></a>Hibaelhárítás

Az egyéni házirend, amely az imént feltöltött hozzá tartozó panel megnyitásával, és kattintson a teszt **Futtatás most**. Diagnosztizálhatja a problémákat, olvassa el [hibaelhárítási](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>További lépések

Visszajelzést küldhet [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
