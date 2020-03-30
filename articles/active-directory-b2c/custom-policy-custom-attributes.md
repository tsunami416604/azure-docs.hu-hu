---
title: Saját attribútumok hozzáadása egyéni házirendekhez
titleSuffix: Azure AD B2C
description: Forgatókönyv a bővítménytulajdonságok és az egyéni attribútumok használatáról, valamint azok a felhasználói felületen való használatáról.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473659"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: Egyéni attribútumok engedélyezése egyéni profilházirendben

A [Jogcímek hozzáadása és a felhasználói bevitel testreszabása egyéni házirendek használatával](custom-policy-configure-user-input.md) című cikkben megtudhatja, hogyan használhatja a beépített felhasználói profil [attribútumait.](user-profile-attributes.md) Ebben a cikkben egy egyéni attribútumot engedélyez az Azure Active Directory B2C (Azure AD B2C) címtárban. Később az új attribútumot egyéni jogcímként használhatja [a felhasználói folyamatokban](user-flow-overview.md) vagy [az egyéni házirendekben](custom-policy-get-started.md) egyidejűleg.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Előfeltételek

Kövesse az Azure [Active Directory B2C: Ismerkedés](custom-policy-get-started.md)az egyéni házirendek című cikkben leírt lépésekkel.

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Egyéni attribútumok használata az ügyfelekkel kapcsolatos információk gyűjtésére 

Az Azure AD B2C könyvtár hoz egy [beépített attribútumkészlet.](user-profile-attributes.md) Azonban gyakran létre kell hoznia saját attribútumait az adott forgatókönyv kezeléséhez, például akkor, ha:

* Az ügyfél felé néző alkalmazásnak meg kell persistnia egy **LoyaltyId attribútumot.**
* Az identitásszolgáltató egyedi felhasználói azonosítóval, **uniqueUserGUID**azonosítóval rendelkezik, amelyet meg kell tartani.
* Az egyéni felhasználói útnak meg kell persistnie a felhasználó állapotát, **migrationStatus**, hogy más logika működjön.

Az Azure AD B2C lehetővé teszi az egyes felhasználói fiókokban tárolt attribútumok készletének bővítését. Ezeket az attribútumokat a [Microsoft Graph API](manage-user-accounts-graph-api.md)segítségével is elolvashatja és írhatja.

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C bővítmények alkalmazás

A bővítményattribútumok csak akkor regisztrálhatók egy alkalmazásobjektumon, ha azok egy felhasználó adatait is tartalmazhatják. A bővítmény attribútum a b2c-extensions-app nevű alkalmazáshoz van csatolva. Ne módosítsa ezt az alkalmazást, mivel az Azure AD B2C felhasználói adatok tárolására használja. Ez az alkalmazás az Azure AD B2C, alkalmazásregisztrációk.

A *kifejezéskiterjesztés tulajdonság*, *egyéni attribútum*és *egyéni jogcím* hivatkoznak ugyanarra a dologra a cikk összefüggésében. A név a környezettől, például az alkalmazástól, az objektumtól vagy a házirendtől függően változik.

## <a name="get-the-application-properties"></a>Az alkalmazás tulajdonságainak beszereznie

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előnézet)** lehetőséget, majd a **Minden alkalmazás**lehetőséget.
1. Válassza `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` ki az alkalmazást.
1. Másolja a következő azonosítókat a vágólapra, és mentse őket:
    * **Alkalmazásazonosító**. Példa: `11111111-1111-1111-1111-111111111111`.
    * **Objektumazonosító**. Példa: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Az egyéni házirend módosítása

Ha engedélyezni szeretné az egyéni attribútumokat a házirendben, adja meg az **alkalmazásazonosítót** és az **alkalmazásobjektum-azonosítót** az AAD-Common technikai profil metaadataiközött. Az *AAD-Common* technikai profil megtalálható az alap [Azure Active Directory](active-directory-technical-profile.md) technikai profil, és támogatja az Azure AD felhasználói felügyeletét. Más Azure AD technikai profilok közé tartozik az AAD-common a konfiguráció kihasználásához. Az AAD-Common technikai profil felülbírálása a mellékfájlban.

1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
1. Keresse meg a ClaimsProviders elemet. Adjon hozzá egy új jogcímszolgáltatót a ClaimsProviders elemhez.
1. Cserélje `ApplicationObjectId` le a korábban rögzített objektumazonosítóra. Ezután `ClientId` cserélje le az alkalmazásazonosítót, amelyet korábban rögzített az alábbi kódrészletben.

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
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Az egyéni szabályzat feltöltése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD B2C-bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
4. Válassza **az Identitáskezelési keretrendszert**.
5. Válassza **az Egyéni házirend feltöltése**lehetőséget, majd töltse fel a módosított TrustFrameworkExtensions.xml házirendfájlokat.

> [!NOTE]
> Az első alkalommal, amikor az Azure AD technikai profil megmarad a címtárban, ellenőrzi, hogy az egyéni attribútum létezik-e. Ha nem, létrehozza az egyéni attribútumot.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Egyéni attribútum létrehozása az Azure Portalon keresztül

Ugyanazok a bővítményattribútumok vannak megosztva a beépített és az egyéni házirendek között. Amikor egyéni attribútumokat ad hozzá a portálon keresztül, ezek az attribútumok regisztrálva vannak a **b2c-extensions-app** használatával, amely minden B2C-bérlőben megtalálható.

Ezeket az attribútumokat a portál felhasználói felületének használatával hozhatja létre az egyéni házirendekben való használatuk előtt vagy után. Kövesse az egyéni attribútumok definiálásának útmutatóját [az Azure Active Directory B2C-ben.](user-flow-custom-attributes.md) Amikor **hűségazonosítót** hoz létre a portálon, a következőképpen kell hivatkoznia rá:

|Név     |Használt |
|---------|---------|
|`extension_loyaltyId`  | Egyéni szabályzat|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

A következő példa bemutatja az egyéni attribútumok használatát egy Azure AD B2C egyéni házirend-jogcímdefinícióban.

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

A következő példa bemutatja egy egyéni attribútum használatát az Azure AD B2C egyéni szabályzat egy technikai profil, bemenet, kimenet és a megőrzött jogcímek használatát.

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

## <a name="use-a-custom-attribute-in-a-policy"></a>Egyéni attribútum használata házirendben

Kövesse a [jogcímek hozzáadására és a felhasználói bevitel egyéni házirendek használatával történő testreszabására vonatkozó útmutatást.](custom-policy-configure-user-input.md) Ez a minta egy beépített "város" követelést használ. Egyéni attribútum használatához cserélje le a "város" szót a saját egyéni attribútumaival.


## <a name="next-steps"></a>További lépések

További információk:

- [Az Azure AD B2C felhasználói profil attribútumai](user-profile-attributes.md)
- [Bővítményattribútum-definíció](user-profile-attributes.md#extension-attributes)
- [Azure AD B2C felhasználói fiókok kezelése a Microsoft Graph segítségével](manage-user-accounts-graph-api.md)
