---
title: A saját attribútumokat adhat hozzá egyéni szabályzatokat az Azure Active Directory B2C |} A Microsoft Docs
description: A forgatókönyv a bővítménytulajdonságok és egyéni attribútumok használata, és többek között azokat a felhasználói felületen.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1f79330f12117c6ade8884165d1538623e19c7ea
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175264"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Egyéni attribútumok használata egyéni profil szabályzat szerkesztése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ez a cikk az Azure Active Directory (Azure AD) B2C-címtárban hoz létre egy egyéni attribútum. A profil szerkesztése felhasználói interakciósorozatban szereplő egyéni jogcímként használja, ezt az új attribútumot.

## <a name="prerequisites"></a>Előfeltételek

Kövesse a cikk a [Azure Active Directory B2C: Egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Egyéni attribútumok használata az Azure AD B2C-ben az ügyfelek adatainak gyűjtésére egyéni szabályzatok használatával
Az Azure AD B2C-címtár tartalmaz egy beépített attribútumok. Példa **Utónév**, **Vezetéknév**, **Város**, **irányítószám**, és **userPrincipalName**. Milyen gyakran szeretne létrehozni a példákat a saját attribútumok:
* A ügyfél felé irányuló kérelmet kell megőrizni egy attribútum, például a **LoyaltyNumber.**
* Az identitásszolgáltató van például egy egyedi felhasználói azonosítót **uniqueUserGUID** , el kell menteni.
* Egyéni felhasználói út kell megőrizni, például a felhasználói állapotra vonatkozó **migrationStatus**.

Az Azure AD B2C az attribútumokat, minden egyes felhasználói fiókjában tárolt terjeszti ki. Is olvasása és írása, ezek az attribútumok használatával a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Bővítménytulajdonságok a címtárban lévő felhasználói objektumok sémája bővíthető. A használati *bővítménytulajdonság*, *vlastního atributu*, és *egyéni jogcímszabályok* hivatkozhat ugyanarra a dologra, ez a cikk kontextusában. A név a környezetben, például az alkalmazás, az objektumot, vagy a szabályzat függően változik.

Bővítménytulajdonságok csak lehet regisztrálni egy alkalmazásobjektumot annak ellenére, hogy egy felhasználó adatainak tartalmaz. A tulajdonság az alkalmazás csatlakoztatva van. Az alkalmazásobjektum regisztrálni egy bővítménytulajdonságra írási hozzáféréssel kell rendelkeznie. Több száz bővítmény tulajdonságai, összes típust és valamennyi alkalmazásra, csak írható egyetlen objektumhoz sem. Bővítménytulajdonságok a célcímtár típusához hozzá, és azonnal elérhetővé válnak az Azure AD B2C directory-bérlőben.
Ha az alkalmazást törlik, ezeket a bővítménytulajdonságok együtt az összes felhasználó bennük tárolt adatok is törlődnek. Ha egy bővítménytulajdonság nem törli azokat az alkalmazást, a cél címtárobjektumok eltávolítja azt, és az értékek törlődnek.

Bővítménytulajdonságok létezik csak a bérlő regisztrált alkalmazás környezetében. Az objektum az adott alkalmazás Azonosítóját kell szerepelnie a **TechnicalProfile** , amely használja azt.

>[!NOTE]
>Az Azure AD B2C-címtár közé tartozik jellemzően nevű webalkalmazás `b2c-extensions-app`. Ez az alkalmazás elsősorban az egyéni jogcímek, az Azure Portalon létrehozott beépített B2C-szabályzatok használják. Azt javasoljuk, hogy csak a haladó felhasználók bővítmények a B2C-vel egyéni szabályzatok regisztrálja az alkalmazás használatával.  
Utasítások szerepelnek a **további lépések** szakasz ebben a cikkben.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Hozzon létre egy új alkalmazást tárolja a bővítmény tulajdonságai

1. Nyisson meg egy böngészési munkamenetet, és keresse meg a [az Azure portal](https://portal.azure.com). Jelentkezzen be rendszergazdai hitelesítő adataival a B2C-címtárat szeretne konfigurálni.
2. Válassza ki **Azure Active Directory** a bal oldali navigációs menüben. Szüksége lehet elolvasni kiválasztásával **további szolgáltatások**.
3. Válassza az **Alkalmazásregisztrációk** elemet. Válassza az **Új alkalmazás regisztrálása** elemet.
4. Adja meg az alábbi bejegyzéseket:
    * A webalkalmazás nevét: **WebApp-GraphAPI-DirectoryExtensions**.
    * Az alkalmazás típusa: **Web app és az API**.
    * A bejelentkezési URL-cím: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Kattintson a **Létrehozás** gombra.
6. Válassza ki az újonnan létrehozott webalkalmazást.
7. Válassza ki **beállítások** > **szükséges engedélyek**.
8. Válassza ki az API-t **Windows Azure Active Directory**.
9. Írjon be egy pipa Alkalmazásengedélyek: **Olvasási és írási címtáradatok**. Ezután válassza a **Save** (Mentés) lehetőséget.
10. Válasszon **engedélyeket** , majd erősítse meg **Igen**.
11. A következő azonosítók másolja a vágólapra, és mentse őket:
    * **Alkalmazásazonosító**. Példa: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Objektumazonosító:**. Példa: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Az egyéni házirend hozzáadása módosítása a **ApplicationObjectId**

Ha követte a lépéseket a [Azure Active Directory B2C: Egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md), letöltött és módosított [fájlok minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nevű **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml**, és **PasswordReset.xml**. Ebben a lépésben hajtsa végre ezeket a fájlokat további módosításokat.

* Nyissa meg a **TrustFrameworkBase.xml** fájlt, és adja hozzá a `Metadata` szakasz az alábbi példában látható módon. Helyezze be a korábban rögzített Objektumazonosítóját a `ApplicationObjectId` érték és az Alkalmazásazonosítót feljegyzett a `ClientId` érték: 

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
> Ha a **TechnicalProfile** ír először az újonnan létrehozott bővítménytulajdonság egyszeri hibát tapasztalhat. A bővítmény tulajdonságának jön létre az első alkalommal használják.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Az új bővítménytulajdonság vagy az egyéni attribútum használata a felhasználói út

1. Nyissa meg a **ProfileEdit.xml** fájlt.
2. Adjon hozzá egy egyéni jogcímszabályok `loyaltyId`. A jogcím alapján, beleértve az egyéni a `<RelyingParty>` elemben, a felvette a tokent az alkalmazáshoz.
    
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

3. Nyissa meg a **TrustFrameworkExtensions.xml** fájlt, és adja hozzá a`<ClaimsSchema>` elem és az alárendelt elemei, a `BuildingBlocks` elem:

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

4. Adja hozzá az azonos `ClaimType` definíciót **TrustFrameworkBase.xml**. Nem kell hozzáadni egy `ClaimType` az alap- és a bővítményfájlok definíciójában. Azonban adja hozzá a következő lépéseket a `extension_loyaltyId` való **TechnicalProfiles** az alap fájlban. Így a szabályzat érvényesítési elutasítja, e nélkül az alap fájl feltöltésének. Érdemes lehet a felhasználói út nevű végrehajtásának nyomon követése **ProfileEdit** a a **TrustFrameworkBase.xml** fájlt. Keresse meg a felhasználói út ugyanazzal a névvel, a saját szerkesztőben. Figyelje meg, hogy a Vezénylési lépés 5 meghívja a **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Keresse meg és vizsgálja meg, ez **TechnicalProfile** és ismerje meg a flow-val.

5. Nyissa meg a **TrustFrameworkBase.xml** fájlt, és `loyaltyId` módon a jogcím-bemenet és kimenet a **TechnicalProfile SelfAsserted-ProfileUpdate**:

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

6. Az a **TrustFrameworkBase.xml** fájlt, adja hozzá a `loyaltyId` jogcímet **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. A Hozzáadás továbbra is fennáll, az aktuális felhasználó a könyvtárban található a bővítmény tulajdonságának a jogcím értéke:

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

7. Az a **TrustFrameworkBase.xml** fájlt, adja hozzá a `loyaltyId` jogcímet **TechnicalProfile AAD-UserReadUsingObjectId** a mellék attribútum értékének olvasásához minden alkalommal, amikor egy felhasználó bejelentkezik. Eddig a **TechnicalProfiles** a folyamat csak a helyi fiókok megváltoztak. Ha azt szeretné, hogy az új attribútumot a folyamat egy közösségi vagy összevont fiók, más **TechnicalProfiles** módosítani kell. Tekintse meg a **további lépések** szakaszban.

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

1. Nyissa meg az Azure AD B2C paneljén, és navigáljon a **identitás-kezelőfelületi keretrendszer** > **egyéni szabályzatok**.
1. Válassza ki a feltöltött egyéni házirendet. Válassza ki **Futtatás most**.
1. Jelentkezzen egy e-mail címet.

Az azonosító jogkivonat küldi vissza az alkalmazásnak magában foglalja az új bővítménytulajdonság előzi meg egyéni jogcímként **extension_loyaltyId**. Lásd a következő példát:

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

## <a name="next-steps"></a>További lépések

1. Adja hozzá az új jogcímet a flow a közösségi fiókok jelentkezzen be a következő módosításával **TechnicalProfiles**. Közösségi és összevont fiókok használatára két **TechnicalProfiles** való bejelentkezéshez. Írási és a felhasználói adatok olvasása a használatával a **alternativeSecurityId** , a felhasználói objektum fájlkeresője.

  ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  ```

2. Használja ugyanazt a bővítményattribútumok beépített és egyéni szabályzatok között. Bővítmény, vagy egyéni, attribútumok keresztül a portál felülete hozzáadásakor ezek az attribútumok használatával regisztrált a **b2c-kiterjesztések alkalmazását** minden B2C-bérlőben található. Az alábbi lépéseket a bővítményattribútumok használatához az egyéni házirendek:

  a. A B2C-bérlő a Portal.Azure.com címen, Ugrás **Azure Active Directory** válassza **alkalmazásregisztrációk**.  
  b. Keresse meg a **b2c-kiterjesztések alkalmazását** , és jelölje ki.  
  c. A **Essentials**, adja meg a **Alkalmazásazonosító** és a **Objektumazonosító**.  
  d. Foglalja bele őket a **AAD-közös** TechnicalProfile metaadatok:  

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

3. A portál felülete konzisztens marad. Ezek az attribútumok létrehozása a portál felhasználói felületének használatával, a egyéni szabályzatait a használatba vétel előtt. Amikor létrehoz egy attribútum **ActivationStatus** a portálon, akkor kell hivatkoznia, a következő:

  ```
  extension_ActivationStatus in the custom policy.
  extension_<app-guid>_ActivationStatus via Graph API.
  ```

## <a name="reference"></a>Leírások

Bővítménytulajdonságok további információkért tekintse meg a cikket [Directory sémakiterjesztései |} Graph API-fogalmak](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * A **TechnicalProfile** elem típusa, vagy-függvény, amely meghatározza egy végpont nevét, a metaadatok és a protokoll. A **TechnicalProfile** a jogcímek, az identitás-kezelőfelületi keretrendszer végző exchange részletek. Ha ez a függvény neve egy vezénylési lépés vagy egy másik **TechnicalProfile**, a **InputClaims** és **OutputClaims** a hívó által biztosított paraméterek .  
> * A Graph API-ban a bővítményattribútumok vannak elnevezve a konvenció szerint `extension_ApplicationObjectID_attributename`.  
> * Egyéni szabályzatok bővítményattribútumok, tekintse meg **extension_attributename**. Ez a hivatkozás az áttekinthetőség kedvéért kihagyja a **ApplicationObjectId** XML-ben.
