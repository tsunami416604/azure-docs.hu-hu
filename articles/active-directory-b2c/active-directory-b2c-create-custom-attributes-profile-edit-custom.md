---
title: "Az Azure Active Directory B2C: Saját attribútumokat adhat hozzá egyéni házirendeket, és használja a profil szerkesztése |} Microsoft Docs"
description: "A forgatókönyv bővítmény tulajdonságok, egyéni attribútumok használatát, és többek között azokat a felhasználói felületen"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 33c9e4322444895a3affc16e11af5443f2db6b6d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Az Azure Active Directory B2C: Létrehozása és az egyéni attribútumok használata egy egyéni profilt a házirend szerkesztése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ebben a cikkben egy egyéni attribútum létrehozása a Azure AD B2C-címtárban, és egy egyéni jogcímet a felhasználó utazás profil szerkesztése az új attribútum használja.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a cikk a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Az ügyfelek az Azure Active Directory B2C egyéni házirendekkel kapcsolatos információk összegyűjtéséhez használja az egyéni attribútumok
Azure Active Directory (Azure AD) B2C-címtárban tartalmaz egy beépített attribútumok: megadott név, Vezetéknév, város, irányítószám, userPrincipalName, stb.  Gyakran a saját attribútumok létrehozásához szükséges.  Példa:
* Egy ügyfélkapcsolati alkalmazást kell megőrizni a egy attribútum, például a "LoyaltyNumber."
* Az identitásszolgáltató rendelkezik egy egyedi felhasználói azonosító, amelyet kell menteni, például a "uniqueUserGUID." "
* Egyéni felhasználói út kell megőrizni a felhasználó például "migrationStatus." állapota

Az Azure AD B2C-ben az attribútumokat, minden egyes felhasználói fiókjában tárolt bővítheti. Is olvasási és írási ezek az attribútumok használatával a [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Bővítmény tulajdonságai a felhasználó a címtárban található objektumokhoz sémája bővíthető.  A feltételek bővített tulajdonság, az egyéni attribútum és az egyéni jogcím tekintse meg az ugyanaz a cikk a környezetében, és nevét a környezetben (alkalmazás, objektum, a házirend) függ.

Bővítmény tulajdonságai csak regisztrálható az alkalmazásobjektum, annak ellenére, hogy egy felhasználó lehet, hogy adatokat tartalmaznak. Az alkalmazás a tulajdonság van csatolva. Az Application objektum regisztrálni egy bővített tulajdonság írási hozzáférést kell rendelni. 100 bővítmény tulajdonságai (közötti összes típusa és az összes alkalmazás) csak írható egyetlen objektumhoz sem. Bővítmény tulajdonságai a céltípus directory adnak, és az Azure AD B2C directory bérlő azonnal elérhető lesz.
Az alkalmazás törlése, ha az összes felhasználó számára a bennük található adatokat ilyen bővítmény tulajdonságok is törlődnek. Egy bővített tulajdonság nem törli azokat az alkalmazást, ha a rendszer eltávolítja a cél címtárobjektumok, és törli az értékeket.

Bővítmény tulajdonságai csak a bérlő regisztrált alkalmazás környezetében található. Az objektumazonosító alkalmazás az azt használó TechnicalProfile kell szerepelnie.

>[!NOTE]
>Az Azure AD B2C-címtár közé tartozik a webes alkalmazás neve `b2c-extensions-app`.  Ez az alkalmazás elsősorban a b2c beépített házirendek az Azure-portálon létrehozott egyéni jogcímek esetében.  Egyéni házirendek b2c-bővítmények regisztrálni az alkalmazás használata csak haladó felhasználóknak javasolt.  Ehhez útmutatást a következő lépések című részben szerepelnek.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>A bővítmény tulajdonságok tárolásához egy új alkalmazás létrehozása

1. Nyissa meg a böngésző munkamenetet, és keresse meg a [Azure-portálon](https://portal.azure.com) és jelentkezzen be rendszergazdai hitelesítő adataival a B2C-címtárban való konfigurálásához.
1. Kattintson a **Azure Active Directory** a bal oldali navigációs menü. Szükség lehet további szolgáltatások kiválasztásával kereséséhez >.
1. Válassza ki **App regisztrációk** kattintson **új alkalmazás regisztrációja**
1. Adja meg az alábbi ajánlott bejegyzéseket:
  * Adjon meg egy nevet a webalkalmazás: **WebApp-GraphAPI-DirectoryExtensions**
  * Alkalmazás típusa: webes alkalmazás/API-t
  * Sign-on URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Válassza ki ** létrehozása. Sikeres létrehozása után megjelenik a **értesítések**
1. Válassza ki az újonnan létrehozott webalkalmazás: **WebApp-GraphAPI-DirectoryExtensions**
1. Válassza a beállítások: **szükséges engedélyek**
1. Az API lehetőséget választhatja **Windows Azure Active Directoryban**
1. Jelölje be az Alkalmazásengedélyek: **címtáradatok olvasása és írása**, és **mentése**
1. Válasszon **engedélyeket** , majd erősítse meg **Igen**.
1. A vágólapra másolja ki és mentse a következő azonosítók a webalkalmazás-GraphAPI-DirectoryExtensions > Beállítások > Tulajdonságok >
*  **Alkalmazásazonosító** . Példa: `103ee0e6-f92d-4183-b576-8c3739027780`
* **Objektumazonosító:**. Példa: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Az egyéni házirend hozzáadása a ApplicationObjectId módosítása

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
>A <TechnicalProfile Id="AAD-Common"> nevezzük "általános", mert az elemei szerepel, és használja fel újra az összes az Azure Active Directory TechnicalProfiles az elem használatával: `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>A TechnicalProfile először írja az újonnan létrehozott bővített tulajdonság, egy egyszeri hibát tapasztalhatnak.  A bővített tulajdonság jön létre a rendszer először.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>A bővítmény új tulajdonsággal egyéni attribútum a felhasználó út /


1. Nyissa meg a függő Party(RP) fájlt, amely bemutatja a szerkesztő felhasználói út.  Ha indítja, töltse le a már konfigurált RP-PolicyEdit fájl az Azure portál Azure B2C egyéni házirend szakaszából tanácsos lehet.  Azt is megteheti nyissa meg az XML-fájl a tárolási mappából.
2. Adja hozzá az egyéni jogcímleírásokat `loyaltyId`.  A jogcímek az egyéni-ot a `<RelyingParty>` elem, a UserJourney TechnicalProfiles átadott paraméterként, és a az alkalmazás a tokenben.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. A bővítményfájl házirend hozzáadása egy jogcím-definíció `TrustFrameworkExtensions.xml` belül a `<ClaimsSchema>` elem látható módon.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Adja hozzá ugyanazt az alap házirendfájl-definíciót a jogcím `TrustFrameworkBase.xml`.  
>Hozzáadás a `ClaimType` az alap- és a bővítmények fájl definíciójában általában nem szükség, azonban a következő lépéseket a extension_loyaltyId felveszi az alap fájlban TechnicalProfiles, mert a házirend-érvényesítő elutasítják az alap fájl feltöltése nélkül.
>Az a TrustFrameworkBase.xml fájlban a "ProfileEdit" nevű felhasználó út végrehajtása nyomkövetéséhez hasznos lehet.  Keresse meg a felhasználó út a szerkesztőben azonos nevű, és figyelje meg, hogy az Orchestration 5. lépés meghívja a TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate".  Keresse meg és vizsgálja meg a TechnicalProfile, és ismerje meg az a folyamat.
5. Adja hozzá a loyaltyId jogcímként bemeneti és kimeneti a a TechnicalProfile "SelfAsserted-ProfileUpdate"
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

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Jogcím hozzáadása a TechnicalProfile "AAD-UserWriteProfileUsingObjectId" megőrizni az aktuális felhasználó a címtárban a bővített tulajdonság a jogcím értéke.
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
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Jogcím hozzáadása a TechnicalProfile "AAD-UserReadUsingObjectId" a mellék attribútum értékének olvasásához minden alkalommal, amikor a felhasználó jelentkezik be. A TechnicalProfiles eddigi csak a helyi fiókok folyamata megváltoztak.  Ha az új attribútumhoz társadalombiztosítási/összevont fiók folyamata van szükség, TechnicalProfiles különböző szabálykészleteket kell módosítani. Tekintse meg a következő lépéseket.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
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
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>A IncludeTechnicalProfile elem hozzáadása a TechnicalProfile az AAD-közös minden elemét.

## <a name="test-the-custom-policy-using-run-now"></a>Az egyéni házirend használatával "Futtatás most" tesztelése
1. Nyissa meg a **panel az Azure AD B2C** , és keresse meg **identitás élmény keretrendszer > egyéni házirendek**.
1. Válassza ki az egyéni házirendet, feltöltött, majd kattintson a **futtatása most** gombra.
1. Iratkozhat fel e-mail cím használatával kell lennie.

Az azonosító tokent küldött vissza az alkalmazásba az új bővített tulajdonság extension_loyaltyId utasításnak egyéni jogcímként magában foglalja. Lásd a példát.

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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Adja hozzá a közösségi fiók bejelentkezések során a viszonylatában új jogcímet a lenti TechnicalProfiles módosításával. E két TechnicalProfiles írható és olvasható a felhasználói adatokat a alternativeSecurityId használja, mint a lokátor felhasználói objektum társadalombiztosítási/összevont fiók bejelentkezések használják.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Beépített és egyéni házirendek közötti azonos kiterjesztési attribútumot használja.
Amikor kiterjesztési attribútumot (más néven egyéni attribútumok) keresztül a portál élményt, azok használatával regisztrált a ** b2c-bővítmények-alkalmazást, amely minden b2c-bérlő szerepel.  Ezeket a bővítményattribútumokat használatához az egyéni házirendek:
1. Lépjen a b2c bérlő portal.azure.com belül **Azure Active Directory** válassza **App regisztrációk**
2. Keresés a **b2c-bővítmények-alkalmazás** , és jelölje ki
3. A "Essentials" rekord a **Alkalmazásazonosító** és a **objektum azonosítója**
4. Tartalmazza azokat az AAD-gyakori technikai profil metaadatai között, például a következőképpen:

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

A portál nyújthassunk konzisztencia fenntartása, hozzon létre a portál felhasználói felületének használatával ezek az attribútumok *előtt* azokat az egyéni házirendeket használ.  Amikor létrehoz egy attribútum "ActivationStatus" a portálon, meg kell hivatkozik rá az alábbiak szerint:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Leírások

* A **műszaki profil (TP)** egy elem típus, amely-re, egy *függvény* , amely definiál egy végpont nevét, a metaadatait, a protokollal, és a cseréjének részletezi, amelyek az identitás Felhasználói élmény keretrendszer végre kell hajtania.  Ha ez *függvény* az orchestration lépésben neve, vagy egy másik TechnicalProfile, a InputClaims és OutputClaims vannak megadva, a paraméterek a hívó által.


* A bővítmény tulajdonságai teljes kezelését, tekintse meg a cikket [DIRECTORY-SÉMA bővítményei |} GRAPH API FOGALMAK](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>A Graph API a bővítményattribútumokat megnevezett az konvenció `extension_ApplicationObjectID_attributename`. Bővítmények attribútumok extension_attributename, így kihagyása az XML ApplicationObjectId lesz az egyéni házirendek
