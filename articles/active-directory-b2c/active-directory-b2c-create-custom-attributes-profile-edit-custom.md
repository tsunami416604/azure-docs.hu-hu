---
title: A saját attribútumokat adhat hozzá egyéni szabályzatokat az Azure Active Directory B2C |} A Microsoft Docs
description: A forgatókönyv a bővítménytulajdonságok, egyéni attribútumok használata, és többek között azokat a felhasználói felületen.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ecde4d8cd8ee454290b16b640ba05d310cf348fe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450241"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Az Azure Active Directory B2C: Létrehozásával és a egy egyéni profilt az egyéni attribútumok használata szabályzat szerkesztése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebben a cikkben vlastní atribut létrehozása az Azure AD B2C-címtárát, és a profil szerkesztése felhasználói interakciósorozatban szereplő egyéni jogcím használja ezt az új attribútumot.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírtak elvégzése [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Egyéni attribútumok használata az ügyfelek az Azure Active Directory B2C-vel egyéni szabályzatok használatával kapcsolatos adatok gyűjtése
Az Azure Active Directory (Azure AD) B2C-címtár tartalmaz egy beépített attribútumok: Adja meg a nevet, Vezetéknév, város, postai irányítószám, userPrincipalName, stb.  Milyen gyakran szeretne létrehozni a saját attribútumok.  Példa:
* A ügyfél felé irányuló kérelmet kell megőrizni egy attribútum, például "LoyaltyNumber."
* Az identitásszolgáltató rendelkezik egy egyedi felhasználói azonosító, amelyet kell menteni, például a "uniqueUserGUID." "
* Egyéni felhasználói út kell megőrizni a felhasználó például "migrationStatus." állapota

Az Azure AD B2C az attribútumokat, minden egyes felhasználói fiókjában tárolt bővítheti. Is olvasása és írása, ezek az attribútumok használatával a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Bővítménytulajdonságok a címtárban lévő felhasználói objektumok sémája bővíthető.  Ez a cikk kontextusában ugyanaz tekintse meg a feltételek bővítménytulajdonság, az egyéni attribútum és egyéni jogcím, és a környezet (alkalmazás, objektumot vagy házirendalapú) függően változik, a neve.

Bővítménytulajdonságok csak lehet regisztrálni egy alkalmazásobjektumot annak ellenére, hogy egy felhasználó adatokat is tartalmaznak. A tulajdonság az alkalmazás csatlakoztatva van. Az alkalmazásobjektum regisztrálni egy bővítménytulajdonságra írási hozzáférést kell adni. 100 bővítménytulajdonságok (között az összes típust és az összes alkalmazás) csak írható egyetlen objektumhoz sem. Bővítménytulajdonságok hozzáadódnak a célcímtár típusához, és az Azure AD B2C directory-bérlő azonnal elérhetővé válik.
Ha az alkalmazást törlik, ezeket a bővítménytulajdonságok együtt az összes felhasználó bennük tárolt adatok is törlődnek. Ha egy bővítménytulajdonság nem törli azokat az alkalmazást, a rendszer eltávolítja a cél címtárobjektum, és törli az értékeket.

Bővítménytulajdonságok létezik csak a bérlő regisztrált alkalmazás környezetében. Az alkalmazás objektumazonosítóját az azt használó TechnicalProfile kell szerepelnie.

>[!NOTE]
>Az Azure AD B2C-címtár közé tartozik jellemzően nevű webalkalmazás `b2c-extensions-app`.  Ez az alkalmazás elsősorban az egyéni jogcímek, az Azure Portalon létrehozott beépített b2c-szabályzatok használják.  Csak a tapasztalt felhasználók számára az alkalmazás regisztrálása a B2C-vel egyéni szabályzatok-bővítmények használata ajánlott.  Ehhez útmutatást a következő lépések szakasz ebben a cikkben szerepelnek.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>A bővítménytulajdonságok tárolásához egy új alkalmazás létrehozása

1. Nyisson meg egy böngészési munkamenetet, és keresse meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be rendszergazdai hitelesítő adatait a B2C-címtárat szeretne beállítani.
2. Kattintson a **Azure Active Directory** a bal oldali navigációs menüben. Szükség lehet, és keresse meg a további szolgáltatások kiválasztásával >.
3. Válassza ki **alkalmazásregisztrációk** kattintson **új alkalmazás regisztrálása**
4. Adja meg a következő ajánlott bejegyzéseket:
    * Adjon meg egy nevet a webalkalmazáshoz: **WebApp-GraphAPI-DirectoryExtensions**
    * Alkalmazás típusa: Web app és az API
    * Sign-on URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
5. Kattintson a **Létrehozás** gombra.
6. Válassza ki az újonnan létrehozott webalkalmazást.
7. Válassza ki **beállítások** > **szükséges engedélyek**.
8. API kiválasztása **Windows Azure Active Directory**.
9. Jelölje be az Alkalmazásengedélyek: **címtáradatok olvasása és írása**, majd válassza ki **mentése**.
10. Válasszon **engedélyeket** , majd erősítse meg **Igen**.
11. Másolja a vágólapra, és mentse a következő azonosítók:
    * **Alkalmazásazonosító** . Példa: `103ee0e6-f92d-4183-b576-8c3739027780`
    * **Objektumazonosító:**. Példa: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Az egyéni szabályzat hozzáadása a ApplicationObjectId módosítása

Ha elvégezte a lépéseket a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md), letöltött és módosított [fájlok](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nevű *TrustFrameworkBase.xml*,  *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, és *PasswordReset.xml*. A következő lépésekben továbbra is hajtsa végre ezeket a fájlokat a módosításokat.

1. Nyissa meg a *TrustFrameworkBase.xml* fájlt, és adja hozzá a `Metadata` szakasz az alábbi példában látható módon. Helyezze be a korábban rögzített Objektumazonosítóját a `ApplicationObjectId` érték és az Alkalmazásazonosítót feljegyzett a `ClientId` érték: 

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

>[!NOTE]
>A TechnicalProfile először az újonnan létrehozott bővítménytulajdonság ír, amikor egy egyszeri hibát tapasztalhat. A bővítmény tulajdonságának jön létre az első alkalommal használják.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Új bővítménytulajdonság segítségével / egyéni attribútum egy felhasználói interakciósorozatban szereplő

1. Nyissa meg a *ProfileEdit.xml* fájlt.
2. Adjon hozzá egy egyéni jogcímszabályok `loyaltyId`.  A jogcím alapján, beleértve az egyéni a `<RelyingParty>` elem, az alkalmazás token részét képezi.
    
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

3. Nyissa meg a *TrustFrameworkExtensions.xml* fájlt, és adja hozzá a`<ClaimsSchema>` elem és az alárendelt elemei, a `BuildingBlocks` elem:

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

4. Adja hozzá az azonos `ClaimType` definíciót *TrustFrameworkBase.xml*. Hozzáadása egy `ClaimType` definition, az alap- és a bővítmények fájl már általában nem szükséges, azonban mivel a következő lépéseket fogja hozzáadni a `extension_loyaltyId` TechnicalProfiles az alap fájlban, hogy a szabályzat érvényesítési elutasítják a feltöltés, a kiinduló fájl nélkül azt. A felhasználói út végrehajtása a neve "ProfileEdit" nyomkövetési hasznos lehet a *TrustFrameworkBase.xml* fájlt.  Keresse meg a felhasználói út az azonos nevű, a szerkesztőben, és figyelje meg, hogy a Vezénylési lépés 5 meghívja a TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate".  Keresse meg és vizsgálja meg a TechnicalProfile, és ismerje meg a flow-val.

5. Nyissa meg a *TrustFrameworkBase.xml* fájlt, és `loyaltyId` , egy bemeneti és kimeneti jogcím a technicalprofile "SelfAsserted-ProfileUpdate":

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

6. Az a *TrustFrameworkBase.xml* fájlt, adja hozzá a `loyaltyId` jogcímet TechnicalProfile "AAD-UserWriteProfileUsingObjectId" megőrizni az aktuális felhasználó a könyvtárban található a bővítmény tulajdonságának a jogcím értéke:

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

7. Az a *TrustFrameworkBase.xml* fájlt, adja hozzá a `loyaltyId` TechnicalProfile "AAD-UserReadUsingObjectId" minden alkalommal, amikor egy felhasználó bejelentkezik, olvassa el a mellék attribútum értékét a jogcímet. Eddigi a TechnicalProfiles megváltoztak a folyamat csak a helyi fiókok.  Az új attribútumot a folyamat egy közösségi/összevont fiók van szükség, ha egy másik készletét TechnicalProfiles módosítani kell. Lásd a következő lépéseket.

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

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Nyissa meg a **Azure AD B2C paneljén** , és keresse meg **identitás-kezelőfelületi keretrendszer > egyéni szabályzatok**.
1. Válassza ki az egyéni házirend feltöltött, majd kattintson a **Futtatás most** gombra.
1. Regisztráció e-mail-címmel kell lennie.

Az azonosító jogkivonat küldi vissza az alkalmazás tartalmazza az új bővítménytulajdonság előzi meg extension_loyaltyId egyéni jogcímként. Látható példa.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="next-steps"></a>További lépések

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Adja hozzá az új jogcímet a flow közösségi fiók bejelentkezések az alább felsorolt TechnicalProfiles módosításával. E két TechnicalProfiles írása és olvasása a felhasználói adatokat a alternativeSecurityId használja, mint a felhasználói objektum-lokátor használják fiók társadalombiztosítási/összevont bejelentkezéseket.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

A beépített és egyéni szabályzatok között ugyanazon bővítményattribútumok használata.
Kiterjesztési attribútumot (más néven egyéni attribútumokat) keresztül a portál felülete hozzáadásakor meg ezek az attribútumok használatával regisztrált a ** b2c-kiterjesztések alkalmazását, amely minden b2c-bérlőben.  Ezeket a bővítményattribútumokat használatához az egyéni házirendek:
1. A b2c-bérlő a Portal.Azure.com címen, Ugrás **Azure Active Directory** válassza **alkalmazásregisztrációk**
2. Keresse meg a **b2c-kiterjesztések alkalmazását** , és jelölje ki
3. Rekord "Essentials" alatt a **Alkalmazásazonosító** és a **objektum azonosítója**
4. Vegye fel őket az AAD-közös technikai profil metaadataiban például a következőképpen:

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

A portál felhasználói élményét konzisztencia fenntartása, hozzon létre a portál felhasználói felületének használatával ezek az attribútumok *előtt* a egyéni szabályzatait használni őket.  Amikor létrehoz egy attribútum "ActivationStatus" a portálon, akkor kell hivatkoznia, a következő:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Leírások

* A **technikai profilban (TP)** elemtípuson is értelmezhetők, van egy *függvény* , amely meghatározza egy végpont nevét, a metaadatait, a protokoll, és részletesen a jogcímek, az exchange, az identitás Végre kell hajtania, keretrendszert.  Ha ez *függvény* egy vezénylési lépés neve, vagy egy másik TechnicalProfile, InputClaims és OutputClaims paraméterek által biztosított a hívónak.


* A bővítménytulajdonságok teljes kezelését ismertető cikkben [DIRECTORY SÉMAKITERJESZTÉSEI |} GRAPH API-FOGALMAK](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Kiterjesztési attribútumot a Graph API használatával az egyezmény nevesített `extension_ApplicationObjectID_attributename`. Egyéni szabályzatok bővítmények attribútumok hivatkozunk extension_attributename, így felsorolhatja az XML-ApplicationObjectId
