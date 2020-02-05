---
title: Önérvényesített technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Önérvényesített műszaki profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6c70e1a5c7e5b81157c09a794ff75e276a20d1f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982738"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Önérvényesített technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) összes olyan interakciója, ahol a felhasználónak meg kell adnia a bemenetet, saját maga által megadott technikai profilok. Például egy regisztrációs oldal, bejelentkezési oldal vagy jelszó-visszaállítási oldal.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem **Name** attribútumát `Proprietary`értékre kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure ad B2C által használt protokollkezelő-szerelvény teljes nevét, amely a következő: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Az alábbi példa egy önérvényesített technikai profilt mutat be az e-mailek regisztrálásához:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Bemeneti jogcímek

Önérvényesített technikai profilban a **szabályzattípushoz** és a **InputClaimsTransformations** elemek használatával előre feltöltheti az önérvényesített lapon megjelenő jogcímek értékét (a megjelenítési jogcímeket). Például a profil szerkesztése területen a felhasználói út először beolvassa a felhasználói profilt a Azure AD B2C Directory szolgáltatásból, majd az önérvényesített technikai profil beállítja a bemeneti jogcímeket a felhasználói profilban tárolt felhasználói adatokkal. Ezeket a jogcímeket a rendszer a felhasználói profilból gyűjti be, majd bemutatja azt a felhasználót, aki szerkesztheti a meglévő adatokat.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Jogcímek megjelenítése

A megjelenítési jogcímek szolgáltatás jelenleg **előzetes**verzióban érhető el.

A **DisplayClaims** elem tartalmazza azon jogcímek listáját, amelyeket a képernyőn kell megjeleníteni az adatok felhasználónak való összegyűjtéséhez. A kimeneti jogcímek értékeinek előre való feltöltéséhez használja a korábban leírt bemeneti jogcímeket. Az elem tartalmazhat alapértelmezett értéket is.

A **DisplayClaims** lévő jogcímek sorrendje határozza meg, hogy a Azure ad B2C milyen sorrendben jelenítse meg a jogcímeket a képernyőn. Ha szeretné kényszeríteni a felhasználót, hogy adjon meg egy értéket egy adott jogcím számára, állítsa a **DisplayClaim** elem **kötelező** attribútumát `true`értékre.

A **DisplayClaims** -gyűjtemény **claimType** elemének a **UserInputType** elemet kell beállítania a Azure ad B2C által támogatott összes felhasználói bemeneti típusra. Például `TextBox` vagy `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Hivatkozás hozzáadása egy DisplayControl

A megjelenítési jogcímek gyűjteményében a létrehozott [DisplayControl](display-controls.md) mutató hivatkozást is megadhat. A megjelenítési vezérlő egy olyan felhasználói felületi elem, amely speciális funkciókkal rendelkezik, és a Azure AD B2C háttérrendszer-szolgáltatással kommunikál. Lehetővé teszi a felhasználó számára, hogy műveleteket hajtson végre az oldalon, amely a háttérbeli érvényesítési technikai profilt hívja meg. Például egy e-mail-cím, telefonszám vagy ügyfél-hűség számának ellenőrzése.

A következő példában `TechnicalProfile` szemlélteti a megjelenítési vezérlőkkel rendelkező megjelenítési jogcímek használatát.

* Az első megjelenítési jogcím a `emailVerificationControl` megjelenítési vezérlőelemre hivatkozik, amely összegyűjti és ellenőrzi az e-mail-címet.
* Az ötödik megjelenítési jogcím a `phoneVerificationControl` megjelenítési vezérlőelemre hivatkozik, amely a telefonszámot gyűjti és ellenőrzi.
* A többi megjelenítési jogcímet a felhasználó ClaimTypes gyűjti.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Ahogy azt korábban említettük, a megjelenítési vezérlőelemre mutató hivatkozás a saját érvényesítését is futtathatja, például az e-mail cím ellenőrzésével. Emellett az önérvényesített oldal támogatja az érvényesítési technikai profil használatát a teljes oldal ellenőrzéséhez, beleértve a felhasználói bevitelt (jogcím-típusok vagy megjelenítési vezérlők), mielőtt továbblép a következő előkészítési lépésre.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>A megjelenítési jogcímek és a kimeneti jogcímek használatának összevonása körültekintően

Ha egy vagy több **DisplayClaim** -elemet ad meg egy önérvényesített technikai profilban, *minden* olyan jogcím esetében DisplayClaim kell használnia, amelyet a képernyőn megjeleníteni kíván, és a felhasználótól gyűjt. Nem jelenik meg a kimeneti jogcímek egy olyan önérvényesített technikai profilban, amely legalább egy megjelenítési jogcímet tartalmaz.

Vegye figyelembe a következő példát, amikor egy `age` jogcím **kimeneti** jogcímként van definiálva egy alapházirendben. Ahhoz, hogy megjelenjenek a megjelenítési jogcímek az önérvényesített technikai profilhoz, a rendszer a `age` jogcímet jeleníti meg a képernyőn a felhasználótól származó adatgyűjtés céljából:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Ha egy olyan levélre vonatkozó házirend, amely ezt az alapszintű örökli, a `officeNumber` **megjelenítési** jogcímként adja meg:

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Az alapházirendben lévő `age` jogcím már nem jelenik meg a képernyőn a felhasználó számára – ez gyakorlatilag "rejtett". A `age` jogcím megjelenítéséhez és a kor értékének a felhasználótól való összegyűjtéséhez hozzá kell adnia egy `age` **DisplayClaim**.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a következő előkészítési lépéshez visszaadott jogcímek listáját tartalmazza. A **DefaultValue** attribútum csak akkor lép életbe, ha a jogcím még nincs beállítva. Ha egy korábbi előkészítési lépésben lett beállítva, az alapértelmezett érték nem lép érvénybe, még akkor sem, ha a felhasználó üresen hagyja az értéket. Az alapértelmezett érték használatának kényszerítéséhez állítsa `true`értékre a **AlwaysUseDefaultValue** attribútumot.

> [!NOTE]
> Az Identity Experience Framework (IEF) korábbi verzióiban a kimeneti jogcímeket a rendszer a felhasználó adatainak gyűjtésére használta. Ha adatokat szeretne gyűjteni a felhasználótól, használjon helyette egy **DisplayClaims** -gyűjteményt.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="when-you-should-use-output-claims"></a>Ha kimeneti jogcímeket szeretne használni

Az önellenőrzött technikai profilokban a kimeneti jogcímek gyűjteménye a következő előkészítési lépéshez adja vissza a jogcímeket.

A kimeneti jogcímeket a következőket kell használnia:

- **A jogcímeket kimeneti jogcím-átalakítás**eredményezi.
- A **kimeneti jogcímek alapértelmezett értékének beállítása** anélkül, hogy adatokat kellene begyűjtenie a felhasználótól, vagy vissza kell adni az adatokat az érvényesítési technikai profilból. A `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profil állítja be a **végrehajtott SelfAsserted-bemeneti** jogcímet, hogy `true`.
- **Az érvényesítési technikai profil a kimeneti jogcímeket adja vissza** – a technikai profil egy érvényesítési műszaki profilt hívhat meg, amely visszaadja a jogcímeket. Előfordulhat, hogy fel kívánja készíteni a jogcímeket, és visszaadja azokat a felhasználói utazás következő hangolási lépéseihez. Ha például helyi fiókkal jelentkezik be, a `SelfAsserted-LocalAccountSignin-Email` nevű önérvényesített technikai profil meghívja a `login-NonInteractive`nevű érvényesítési technikai profilt. Ez a technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és a felhasználói profilt is visszaadja. Ilyen például a "userPrincipalName", a "displayName", a "givenName" és a "vezetéknév".
- A **megjelenítési vezérlő visszaadja a kimeneti jogcímeket** – a technikai profil egy [megjelenítési vezérlőelemre](display-controls.md)mutató hivatkozással rendelkezhet. A megjelenítési vezérlő bizonyos jogcímeket ad vissza, például az ellenőrzött e-mail-címet. Előfordulhat, hogy fel kívánja készíteni a jogcímeket, és visszaadja azokat a felhasználói utazás következő hangolási lépéseihez. A megjelenítési vezérlő szolgáltatás jelenleg **előzetes**verzióban érhető el.

Az alábbi példa azt mutatja be, hogyan használható egy önérvényesített technikai profil, amely a megjelenítési jogcímeket és a kimeneti jogcímeket egyaránt használja.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Jogcímek fenntartása

Ha a **PersistedClaims** elem hiányzik, az önérvényesített technikai profil nem őrzi meg Azure ad B2C. Ehelyett egy olyan érvényesítési technikai profilra irányuló hívás történik, amely felelős az adatmegőrzésért. A regisztrálási szabályzat például a `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profilt használja az új felhasználói profil összegyűjtéséhez. A `LocalAccountSignUpWithLogonEmail` műszaki profil az érvényesítési technikai profilt hívja meg a fiók létrehozásához Azure AD B2Cban.

## <a name="validation-technical-profiles"></a>Ellenőrzési technikai profilok

Egy érvényesítési műszaki profil a hivatkozó technikai profil kimeneti jogcímeinek érvényesítésére szolgál. Az érvényesítési technikai profil bemeneti jogcímeit az önérvényesített technikai profil kimeneti jogcímeiben kell megjelennie. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, és hibát ad vissza a felhasználónak.

Az érvényesítési technikai profil a szabályzat bármely műszaki profilja lehet, például [Azure Active Directory](active-directory-technical-profile.md) vagy egy [REST API](restful-technical-profile.md) technikai profil. Az előző példában a `LocalAccountSignUpWithLogonEmail` technikai profil ellenőrzi, hogy a signinName nem létezik-e a könyvtárban. Ha nem, az érvényesítési technikai profil létrehoz egy helyi fiókot, és visszaadja a objectId, a authenticationSource, a newUser. A `SelfAsserted-LocalAccountSignin-Email` műszaki profil a felhasználói hitelesítő adatok ellenőrzéséhez meghívja a `login-NonInteractive` ellenőrzési technikai profilt.

Az üzleti logikával meghívhat egy REST API technikai profilt, felülírhatja a bemeneti jogcímeket, vagy gazdagíthatja a felhasználói adatokat a vállalati üzletági alkalmazások további integrálásával. További információ: [érvényesítési technikai profil](validation-technical-profile.md)

## <a name="metadata"></a>Metaadatok

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Setting. operatingMode | Nem | A bejelentkezési oldalon ez a tulajdonság szabályozza a Felhasználónév mező viselkedését, például a bemeneti ellenőrzés és a hibaüzenetek értékét. Várt értékek: `Username` vagy `Email`. |
| AllowGenerationOfClaimsWithNullValues| Nem| Egy NULL értékű jogcím létrehozását teszi lehetővé. Ha például a felhasználó nem jelöli be a jelölőnégyzetet.|
| ContentDefinitionReferenceId | Igen | Az ehhez a technikai profilhoz társított [tartalom-definíció](contentdefinitions.md) azonosítója. |
| EnforceEmailVerification | Nem | A regisztrációhoz vagy a profil szerkesztéséhez érvényesíti az e-mailek ellenőrzését. Lehetséges értékek: `true` (alapértelmezett) vagy `false`. |
| Setting. retryLimit | Nem | Meghatározza, hogy a felhasználók hányszor próbálják meg megadni az érvényesítési technikai profilban ellenőrzött adatmennyiséget. Egy felhasználó például megpróbál egy olyan fiókkal regisztrálni, amely már létezik, és megtartja a próbálkozást, amíg el nem éri a korlátot.
| SignUpTarget | Nem | A regisztrációs cél Exchange-azonosítója. Amikor a felhasználó a feliratkozás gombra kattint, Azure AD B2C végrehajtja a megadott Exchange-azonosítót. |
| Setting. showCancelButton | Nem | Megjeleníti a Mégse gombot. Lehetséges értékek: `true` (alapértelmezett) vagy `false` |
| Setting. showContinueButton | Nem | Megjeleníti a folytatás gombot. Lehetséges értékek: `true` (alapértelmezett) vagy `false` |
| Setting. showSignupLink | Nem | Megjeleníti a regisztrációs gombot. Lehetséges értékek: `true` (alapértelmezett) vagy `false` |
| Setting. forgotPasswordLinkLocation| Nem| Az elfelejtett jelszó hivatkozását jeleníti meg. Lehetséges értékek: `AfterInput` (alapértelmezett) a hivatkozás megjelenik az oldal alján, vagy `None` eltávolítja az elfelejtett jelszó hivatkozást.| 
## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem nincs használatban.
