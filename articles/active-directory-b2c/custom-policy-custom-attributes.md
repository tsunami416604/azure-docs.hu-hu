---
title: Saját attribútumok hozzáadása egyéni házirendekhez
titleSuffix: Azure AD B2C
description: A bővítmény tulajdonságainak és egyéni attribútumainak, valamint a felhasználói felületen való használatát bemutató bemutató.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389326"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: egyéni attribútumok engedélyezése egyéni profil házirendben

A [jogcímek hozzáadása és a felhasználói bevitel testreszabása Egyéni szabályzatok használatával](custom-policy-configure-user-input.md) című cikkből megtudhatja, hogyan használhatja a beépített [felhasználói profilok attribútumait](user-profile-attributes.md). Ebben a cikkben egy egyéni attribútumot engedélyez a Azure Active Directory B2C (Azure AD B2C) címtárban. Később az új attribútumot egyéni jogcímként használhatja [felhasználói folyamatokban](user-flow-overview.md) vagy [Egyéni szabályzatokban](custom-policy-get-started.md) .

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Előfeltételek

Kövesse a [Azure Active Directory B2C: Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című cikk lépéseit.

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Egyéni attribútumok használata az ügyfelek adatainak gyűjtéséhez 

A Azure AD B2C könyvtára egy [beépített attribútumokkal](user-profile-attributes.md)rendelkezik. Azonban gyakran kell létrehoznia saját attribútumokat az adott forgatókönyv kezeléséhez, például a következő esetekben:

* Egy ügyfél felé irányuló alkalmazásnak meg kell őriznie egy **LoyaltyId** attribútumot.
* Az identitás-szolgáltató egyedi felhasználói azonosítóval rendelkezik, amelyet **uniqueUserGUID**kell megőrizni.
* Az egyéni felhasználói utazásnak meg kell őriznie a felhasználó ( **migrationStatus**) állapotát, hogy más logika is működjön.

Azure AD B2C segítségével kiterjesztheti az egyes felhasználói fiókokban tárolt attribútumok készletét. Ezeket az attribútumokat a [Microsoft Graph API](manage-user-accounts-graph-api.md)használatával is elolvashatja és elvégezheti.

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C Extensions-alkalmazás

A bővítmény attribútumai csak az Application objektumon regisztrálhatók, annak ellenére, hogy egy felhasználóhoz tartozó adatmennyiséget tartalmaznak. A bővítmény attribútum a B2C-Extensions-app nevű alkalmazáshoz van csatolva. Ne módosítsa ezt az alkalmazást, mert az Azure AD B2C használja a felhasználói adattároláshoz. Az alkalmazás az Azure AD B2C, az alkalmazások regisztrációja alatt található.

A használati feltételek *kiterjesztésének tulajdonsága*, az *egyéni attribútum*és az *egyéni jogcím* a jelen cikk kontextusában ugyanezt a dolgot tekinti át. A név a környezettől, például az alkalmazástól, az objektumtól vagy a házirendtől függően változhat.

## <a name="get-the-application-properties"></a>Az alkalmazás tulajdonságainak beolvasása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.
1. Válassza ki a `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` alkalmazást.
1. Másolja a következő azonosítókat a vágólapra, és mentse őket:
    * **Alkalmazásazonosító**. Példa: `11111111-1111-1111-1111-111111111111`.
    * **Objektumazonosító**. Példa: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Egyéni szabályzat módosítása

A házirendben szereplő egyéni attribútumok engedélyezéséhez adja meg az **alkalmazás azonosítóját** és az ALKALMAZÁSOBJEKTUM- **azonosítót** a AAD-Common technikai profil metaadataiban. A *HRE-Common* Technical profil az alapszintű [Azure Active Directory](active-directory-technical-profile.md) technikai profilban található, és támogatást nyújt az Azure ad-felhasználók felügyeletéhez. A többi Azure AD technikai profil tartalmazza a konfigurációjának kihasználása AAD-Common. Bírálja felül az AAD-Common technikai profilt a kiterjesztési fájlban.

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Keresse meg a ClaimsProviders elemet. Adjon hozzá egy új ClaimsProvider a ClaimsProviders elemhez.
1. Cserélje le `ApplicationObjectId` a helyére a korábban rögzített objektumazonosító értékét. Ezután cserélje le `ClientId` az alkalmazást az alábbi kódrészletben korábban rögzített alkalmazás-azonosítóra.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Egyéni szabályzat feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a Azure ad B2C bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **identitási élmény keretrendszert**.
5. Válassza az **egyéni házirend feltöltése**lehetőséget, majd töltse fel a módosított TrustFrameworkExtensions.xml házirend-fájlokat.

> [!NOTE]
> Amikor az Azure AD műszaki profilja megőrzi a jogcímet a címtárba, ellenőrzi, hogy létezik-e az egyéni attribútum. Ha nem, akkor az egyéni attribútumot hozza létre.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Egyéni attribútum létrehozása Azure Portal használatával

Ugyanazok a bővítmény-attribútumok vannak megosztva a beépített és az egyéni házirendek között. Ha egyéni attribútumokat ad hozzá a portál felületén keresztül, ezeket az attribútumokat az összes B2C-bérlőben található **B2C-Extensions-app** használatával regisztrálja a rendszer.

Ezeket az attribútumokat az egyéni szabályzatok használata előtt vagy után is létrehozhatja a portál felhasználói felületén. A [Azure Active Directory B2C egyéni attribútumainak definiálásához](user-flow-custom-attributes.md)kövesse az útmutatást. Amikor **loyaltyId** hoz létre a portálon, az alábbiak szerint kell megadnia:

|Név     |Használatban |
|---------|---------|
|`extension_loyaltyId`  | Egyéni szabályzat|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

Az alábbi példa az egyéni attribútumok használatát mutatja be egy Azure AD B2C egyéni házirend-jogcím definíciójában.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Az alábbi példa egy egyéni attribútum használatát mutatja be Azure AD B2C egyéni szabályzatban egy technikai profilban, bemenetben, kimenetben és megőrzött jogcímek között.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Egyéni attribútum használata egy házirendben

Kövesse a [jogcímek hozzáadását és a felhasználói bevitel egyéni házirendek használatával történő testreszabását](custom-policy-configure-user-input.md)ismertető útmutatót. Ez a példa egy beépített "City" jogcímet használ. Egyéni attribútum használatához cserélje le a "City" kulcsszót a saját egyéni attribútumaira.


## <a name="next-steps"></a>További lépések

További információk:

- [Felhasználói profil attribútumainak Azure AD B2C](user-profile-attributes.md)
- [Bővítmény attribútumainak definíciója](user-profile-attributes.md#extension-attributes)
- [Azure AD B2C felhasználói fiókok kezelése Microsoft Graph](manage-user-accounts-graph-api.md)
