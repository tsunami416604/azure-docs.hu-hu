---
title: Bejelentkezve az Azure Active Directory B2C-ben
description: Megtudhatja, hogy miként állíthatja be a Keep Me Signed In (KMSI) szolgáltatást az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803160"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>A Bejelentkezve (KMSI) szolgáltatás engedélyezése az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Engedélyezheti a Keep Me Signed In (KMSI) funkciókat a webes és natív alkalmazások felhasználói számára, akik helyi fiókkal rendelkeznek az Azure Active Directory B2C (Azure AD B2C) címtárban. Ez a funkció hozzáférést biztosít az alkalmazáshoz visszatérő felhasználók számára anélkül, hogy megkérné őket, hogy újra megadják a felhasználónevüket és jelszavukat. Ez a hozzáférés visszavonásra kerül, amikor egy felhasználó kijelentkezik.

A felhasználók nem engedélyezhetik ezt a beállítást nyilvános számítógépeken.

![Példa bejelentkezési lapra: A Be jelentkezés megtartása jelölőnégyzet](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure AD B2C-bérlő, amely a helyi fiókba való bejelentkezés engedélyezésére van konfigurálva. A KMSI nem támogatott a külső identitásszolgáltatófiókokhoz.
- Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md)

## <a name="configure-the-page-identifier"></a>Az oldalazonosító konfigurálása

A KMSI engedélyezéséhez állítsa `DataUri` a tartalomdefiníciós elemet [az oldalazonosítóra](contentdefinitions.md#datauri) `unifiedssp` és az *1.1.0-s* vagy újabb [verzióra.](page-layout.md)

1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például. Ez a kiterjesztésfájl az egyéni házirend kezdőcsomagjában található házirendfájlok egyike, amelyet az egyéni házirendek használata című [előfeltételben](custom-policy-get-started.md)kellett volna beszereznie.
1. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a **ContentDefinitions** elemet a házirend **BuildingBlocks** eleméhez.

    Az egyéni házirendnek a következő kódrészlethez kell hasonlítania:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>A metaadatok hozzáadása az önérvényesítő technikai profilhoz

Ha hozzá szeretné adni a KMSI jelölőnégyzetet a regisztrációs `setting.enableRememberMe` és bejelentkezési laphoz, állítsa a metaadatokat igaz értékre. Felülbírálja a SelfAsserted-LocalAccountSignin-Email technikai profilokat a bővítményfájlban.

1. Keresse meg a ClaimsProviders elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcímszolgáltatót a ClaimsProviders elemhez:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Mentse a kiterjesztésfájlt.

## <a name="configure-a-relying-party-file"></a>Függő entitásfájl konfigurálása

Frissítse a felhasználó entitás (RP) fájlját, amely a létrehozott felhasználói utat kezdeményezi.

1. Nyissa meg az egyéni házirendfájlt. Például *SignUpOrSignin.xml*.
1. Ha még nem létezik, `<UserJourneyBehaviors>` adjon hozzá egy `<RelyingParty>` gyermekcsomópontot a csomóponthoz. Közvetlenül a következő `<DefaultUserJourney ReferenceId="User journey Id" />`kután kell `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`elhelyezni, például: .
1. Adja hozzá a következő csomópontot `<UserJourneyBehaviors>` az elem gyermekeként.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Azt jelzi, hogy a munkamenet `SessionExpiryInSeconds` hogyan `KeepAliveInDays`hosszabbodik meg a és a megadott idővel. Az `Rolling` érték (alapértelmezett) azt jelzi, hogy a munkamenet minden alkalommal meghosszabbodik, amikor a felhasználó hitelesítést hajt végre. Az `Absolute` érték azt jelzi, hogy a felhasználó nak a megadott időszak után újra kell hitelesítenie magát.

    - **SessionExpiryInSeconds** - A munkamenet-cookie-k élettartama, amikor *bejelentkezve tart,* nincs engedélyezve, vagy ha a felhasználó nem választja a *bejelentkezést.* A munkamenet a `SessionExpiryInSeconds` munkamenet után lejár, vagy a böngésző bevan zárva.

    - **KeepAliveInDays** - Az élettartama munkamenet cookie-kat, ha *folyamatosan bejelentkezve* van engedélyezve, és a felhasználó kiválasztja *engem bejelentkezve*.  Az érték `KeepAliveInDays` elsőbbséget élvez `SessionExpiryInSeconds` az értékkel szemben, és diktálja a munkamenet lejárati idejét. Ha egy felhasználó bezárja a böngészőt, és később újra megnyitja, akkor is csendben bejelentkezhet, amíg az a KeepAliveInDays időszakon belül van.

    További információt a [felhasználói út viselkedése című témakörben talál.](relyingparty.md#userjourneybehaviors)

Javasoljuk, hogy a SessionExpiryInSeconds értékét rövid időszakra (1200 másodpercre) állítsa be, míg a KeepAliveInDays értéke viszonylag hosszú időszakra (30 napra) állítható be, amint az a következő példában látható:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>A szabályzat tesztelése

1. Mentse a módosításokat, majd töltse fel a fájlt.
1. A feltöltött egyéni szabályzat teszteléséhez az Azure Portalon lépjen a szabályzatlapra, és válassza a **Futtatás most**lehetőséget.
1. Írja be **felhasználónevét** és **jelszavát,** válassza **a Bejelentkezve maradjak**lehetőséget, majd kattintson **a bejelentkezés**gombra.
1. Lépjen vissza az Azure Portalra. Lépjen a házirendlapra, és válassza a **Másolás** lehetőséget a bejelentkezési URL-cím másolásához.
1. A böngésző címsorában távolítsa `&prompt=login` el a lekérdezési karakterlánc paramétert, amely arra kényszeríti a felhasználót, hogy adja meg a hitelesítő adatait a kéréshez.
1. A böngészőben kattintson az **Ugrás**gombra. Most az Azure AD B2C kiad egy hozzáférési jogkivonatot anélkül, hogy újra bejelentkezne. 

## <a name="next-steps"></a>További lépések

A mintaházirendet [itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)találja.
