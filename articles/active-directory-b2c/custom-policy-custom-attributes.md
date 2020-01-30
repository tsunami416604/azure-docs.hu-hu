---
title: Saját attribútumok hozzáadása egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A bővítmény tulajdonságainak és egyéni attribútumainak, valamint a felhasználói felületen való használatát bemutató bemutató.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 04cc45956fc5aedc4c14dfb138be5db02ddec500
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847055"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: egyéni attribútumok használata egyéni profil-szerkesztési házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebben a cikkben egy egyéni attribútumot hoz létre a Azure Active Directory B2C (Azure AD B2C) könyvtárban. Ezt az új attribútumot egyéni jogcímként fogja használni a profil szerkesztése felhasználói úton.

## <a name="prerequisites"></a>Előfeltételek

Kövesse a [Azure Active Directory B2C: Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című cikk lépéseit.

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Egyéni attribútumok használata az ügyfelekkel kapcsolatos adatok gyűjtéséhez Azure AD B2C egyéni szabályzatok használatával
A Azure AD B2C könyvtára egy beépített attribútumokkal rendelkezik. Ilyenek például az **Utónév**, a **vezetéknév**, a **város**, az **Irányítószám**és a **userPrincipalName**. Gyakran létre kell hoznia saját attribútumait, például a következő példákat:
* Egy ügyfél felé irányuló alkalmazásnak meg kell őriznie egy olyan attribútumot, mint a **LoyaltyNumber.**
* Az identitás-szolgáltató egyedi felhasználói azonosítóval rendelkezik, például **uniqueUserGUID** , amelyet menteni kell.
* Egy egyéni felhasználói útra van szükség egy olyan felhasználó állapotára, mint a **migrationStatus**.

Azure AD B2C kiterjeszti az egyes felhasználói fiókokban tárolt attribútumok készletét. Ezeket az attribútumokat az [Azure AD Graph API](manage-user-accounts-graph-api.md)használatával is elolvashatja és elvégezheti.

A bővítmény tulajdonságai a címtárban lévő felhasználói objektumok sémáját bővítik. A használati feltételek *kiterjesztésének tulajdonsága*, az *egyéni attribútum*és az *egyéni jogcím* a jelen cikk kontextusában ugyanezt a dolgot tekinti át. A név a környezettől, például az alkalmazástól, az objektumtól vagy a házirendtől függően változhat.

A bővítmény tulajdonságai csak akkor regisztrálhatók egy alkalmazás-objektumon, ha egy felhasználóhoz tartozó adatmennyiséget tartalmaznak. A tulajdonság az alkalmazáshoz van csatolva. Az Application objektumnak írási hozzáféréssel kell rendelkeznie a Extension tulajdonság regisztrálásához. Az összes típus és az összes alkalmazás száz kiterjesztési tulajdonsága egyetlen objektumba is írható. A bővítmény tulajdonságai hozzáadódnak a cél könyvtár-típushoz, és azonnal elérhetővé válnak a Azure AD B2C Directory-bérlőben.
Ha az alkalmazás törölve van, akkor a bővítmény tulajdonságai és az összes felhasználóhoz tartozó adategység is törlődik. Ha az alkalmazás egy kiterjesztési tulajdonságot töröl, az el lesz távolítva a cél Directory-objektumokon, és az értékek törlődnek.

A bővítmény tulajdonságai csak a bérlőben regisztrált alkalmazások kontextusában léteznek. Az alkalmazás **kivonatjogcím** szerepelnie kell a-t használó objektumban.

>[!NOTE]
>A Azure AD B2C könyvtár általában `b2c-extensions-app`nevű webalkalmazást tartalmaz. Ezt az alkalmazást elsősorban a B2C beépített szabályzatai használják a Azure Portal használatával létrehozott egyéni jogcímekhez. Azt javasoljuk, hogy csak a speciális felhasználók regisztrálják a B2C egyéni szabályzatok bővítményeit az alkalmazás használatával.
Az utasításokat a jelen cikk **következő lépések** szakasza tartalmazza.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Új alkalmazás létrehozása a bővítmény tulajdonságainak tárolásához

1. Nyisson meg egy böngészési munkamenetet, és navigáljon a [Azure Portal](https://portal.azure.com). Jelentkezzen be a konfigurálni kívánt B2C-címtár rendszergazdai hitelesítő adataival.
2. A bal oldali navigációs menüben válassza a **Azure Active Directory** lehetőséget. Előfordulhat, hogy a **További szolgáltatások**lehetőség kiválasztásával kell megkeresnie.
3. Válassza az **Alkalmazásregisztrációk** elemet. Válassza az **Új alkalmazás regisztrálása** elemet.
4. Adja meg a következő bejegyzéseket:
    * A webalkalmazás neve: **WebApp-GraphAPI-DirectoryExtensions**.
    * Az alkalmazás típusa: **Web App/API**.
    * A bejelentkezési URL-cím: **https://{tenantName}. onmicrosoft. com/WebApp-GraphAPI-DirectoryExtensions**.
5. Kattintson a **Létrehozás** gombra.
6. Válassza ki az újonnan létrehozott webalkalmazást.
7. Válassza a **beállítások** > a **szükséges engedélyek**lehetőséget.
8. Válassza ki az API **Windows Azure Active Directory**.
9. Adja meg az alkalmazás engedélyeinek bejelölését: **Címtáradatok olvasása és írása**. Ezután válassza a **Save** (Mentés) lehetőséget.
10. Válassza az **engedélyek megadása** lehetőséget, és erősítse meg az **Igen értéket**.
11. Másolja a következő azonosítókat a vágólapra, és mentse őket:
    * **Alkalmazás azonosítója**. Példa: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Objektumazonosító**. Példa: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Egyéni szabályzat módosítása a **ApplicationObjectId** hozzáadásához

Amikor követte a [Azure Active Directory B2C: Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit, letöltötte és módosította **a TrustFrameworkBase. XML**, a **TrustFrameworkExtensions. XML**, a **SignUpOrSignin. XML**, a **ProfileEdit. XML**és a **PasswordReset. XML**nevű [fájlokat](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . Ebben a lépésben több módosítást hajt végre a fájlokon.

* Nyissa meg a **TrustFrameworkBase. XML** fájlt, és adja hozzá a `Metadata` szakaszt az alábbi példában látható módon. Szúrja be a korábban rögzített objektumazonosítót a `ApplicationObjectId` értékhez és a `ClientId` értékhez rögzített alkalmazás AZONOSÍTÓját:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> Ha a **kivonatjogcím** első alkalommal ír az újonnan létrehozott Extension tulajdonságra, előfordulhat, hogy egyszeri hibát tapasztal. A bővítmény tulajdonságot az első használatkor hozza létre a rendszer.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Az új bővítmény tulajdonság vagy az egyéni attribútum használata felhasználói úton

1. Nyissa meg a **ProfileEdit. XML** fájlt.
2. Adjon hozzá egy egyéni jogcímet `loyaltyId`. A `<RelyingParty>` elemben szereplő egyéni jogcím belefoglalásával az alkalmazás jogkivonatában szerepel.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Nyissa meg a **TrustFrameworkExtensions. XML** fájlt, és adja hozzá a`<ClaimsSchema>` elemet és annak alárendelt elemeit a `BuildingBlocks` elemhez:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Adja hozzá ugyanazt a `ClaimType` definíciót a **TrustFrameworkBase. xml fájlhoz**. Az alap-és a kiterjesztési fájlokban nem szükséges `ClaimType` definíciót hozzáadni. A következő lépésekben azonban adja hozzá a `extension_loyaltyId`t a **TechnicalProfiles** az alapfájlban. Így a szabályzat-érvényesítő elutasítja az alapfájl feltöltését anélkül. Hasznos lehet a **ProfileEdit** nevű felhasználói út végrehajtásának nyomon követése a **TrustFrameworkBase. XML** fájlban. Keresse meg a szerkesztőben ugyanazt a nevet használó felhasználói utat. Figyelje meg, hogy az 5. lépés a következőt hívja meg: **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Keresse meg és vizsgálja meg ezt a **kivonatjogcím** , hogy megismerkedjen a folyamattal.

5. Nyissa meg a **TrustFrameworkBase. XML** fájlt, és adja hozzá a `loyaltyId` bemeneti és kimeneti jogcímként a **Kivonatjogcím SelfAsserted-ProfileUpdate**:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. A **TrustFrameworkBase. XML** fájlban adja hozzá a `loyaltyId` jogcímet a **Kivonatjogcím HRE-UserWriteProfileUsingObjectId**. Ez a kiegészítés megőrzi a jogcím értékét a könyvtár aktuális felhasználójának bővítmény tulajdonságában:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. A **TrustFrameworkBase. XML** fájlban adja hozzá a `loyaltyId` jogcímet a **Kivonatjogcím HRE-UserReadUsingObjectId** elemhez, hogy minden alkalommal beolvassa a bővítmény attribútum értékét, amikor egy felhasználó bejelentkezik. Eddig a **TechnicalProfiles** csak a helyi fiókok forgalmában módosultak. Ha azt szeretné, hogy az új attribútum egy közösségi vagy összevont fiókban legyen, egy másik **TechnicalProfiles** kell módosítani. Lásd a **következő lépések** szakaszt.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Nyissa meg a Azure AD B2C panelt, és navigáljon az **Identity Experience Framework** > **Egyéni házirendek**elemre.
1. Válassza ki a feltöltött egyéni szabályzatot. Válassza a **Futtatás most**lehetőséget.
1. Regisztráljon egy e-mail-cím használatával.

Az alkalmazásnak visszaadott azonosító jogkivonat magában foglalja az új bővítmény tulajdonságot, amely a **extension_loyaltyId**előtt megjelenő egyéni jogcím. Lásd a következő példát:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Következő lépések

1. A következő **TechnicalProfiles**megváltoztatásával adja hozzá az új jogcímet a folyamatokhoz a közösségi fiókba való bejelentkezéshez. A közösségi és összevont fiókok ezt a két **TechnicalProfiles** használják a bejelentkezéshez. A felhasználó adatai a **alternativeSecurityId** , a felhasználói objektum lokátorának használatával írhatók és olvashatók.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Használja ugyanazt a bővítmény-attribútumot a beépített és az egyéni házirendek között. Ha bővítményt vagy egyéni, attribútumokat ad hozzá a portál felületén keresztül, ezeket az attribútumokat az összes B2C-bérlőben található **B2C-Extensions-app** használatával regisztrálja a rendszer. Az alábbi lépéseket követve használhatja a bővítmény attribútumait az egyéni házirendben:

   a. A portal.azure.com-ben a B2C-bérlőn belül navigáljon **Azure Active Directory** , és válassza a **Alkalmazásregisztrációk**lehetőséget.
   b. Keresse meg a **B2C-Extensions-app** elemet, és válassza ki.
   c. Az **Essentials (alapvető**) területen adja meg az **alkalmazás azonosítóját** és az **objektum azonosítóját**.
   d. Vegye fel őket a **HRE-Common kivonatjogcím-** metaadatokba:

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Maradjon konzisztens a portál felületén. Az egyéni szabályzatok használata előtt hozza létre ezeket az attribútumokat a portál felhasználói felületén. Amikor **ActivationStatus** hoz létre a portálon, az alábbiak szerint kell megadnia:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Leírások

További információ a bővítmények tulajdonságairól: [Directory sémakezelő bővítmények | Graph API fogalmakat](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * A **kivonatjogcím** egy olyan elemtípus vagy függvény, amely meghatározza a végpont nevét, metaadatait és protokollját. A **kivonatjogcím** részletezi az identitási keretrendszer által végrehajtott jogcímek cseréjét. Ha ezt a függvényt egy előkészítési lépésben vagy egy másik **kivonatjogcím**hívja meg, a **Szabályzattípushoz** és a **OutputClaims** paraméterként van megadva a hívónak.
> * A Graph API-bővítmény attribútumai a Convention `extension_ApplicationObjectID_attributename`használatával vannak elnevezve.
> * Az egyéni házirendek a bővítmény attribútumait **extension_attributenameként**tekintik meg. Ez a hivatkozás kihagyja a **ApplicationObjectId** az XML-ben.
> * Az attribútum AZONOSÍTÓját a következő formátumban kell megadnia **extension_attributename** bárhol legyen hivatkozva.
