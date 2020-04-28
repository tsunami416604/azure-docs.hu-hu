---
title: Önérvényesített technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Önérvényesített műszaki profilt definiálhat egy egyéni szabályzatban Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332514"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Önérvényesített technikai profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) összes olyan interakciója, ahol a felhasználónak meg kell adnia a bemenetet, saját maga által megadott technikai profilok. Például egy regisztrációs oldal, bejelentkezési oldal vagy jelszó-visszaállítási oldal.

## <a name="protocol"></a>Protocol (Protokoll)

A **protokoll** elem `Proprietary` **Name** attribútumát be kell állítani. A **kezelő** attribútumnak tartalmaznia kell a Azure ad B2C által használt protokollkezelő-szerelvény teljes nevét, amelyet a rendszer az önellenőrzéshez használ:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

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

A **DisplayClaims** elem tartalmazza azon jogcímek listáját, amelyeket a képernyőn kell megjeleníteni az adatok felhasználónak való összegyűjtéséhez. A megjelenítési jogcímek értékének előre való feltöltéséhez használja a korábban leírt bemeneti jogcímeket. Az elem tartalmazhat alapértelmezett értéket is.

A **DisplayClaims** lévő jogcímek sorrendje határozza meg, hogy a Azure ad B2C milyen sorrendben jelenítse meg a jogcímeket a képernyőn. Ha szeretné kényszeríteni a felhasználót, hogy adjon meg egy értéket egy adott jogcím számára, állítsa a **DisplayClaim** elem `true` **kötelező** attribútumát a következőre:.

A **DisplayClaims** -gyűjtemény **claimType** elemének a **UserInputType** elemet kell beállítania a Azure ad B2C által támogatott összes felhasználói bemeneti típusra. Például `TextBox` vagy `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Hivatkozás hozzáadása egy DisplayControl

A megjelenítési jogcímek gyűjteményében a létrehozott [DisplayControl](display-controls.md) mutató hivatkozást is megadhat. A megjelenítési vezérlő egy olyan felhasználói felületi elem, amely speciális funkciókkal rendelkezik, és a Azure AD B2C háttérrendszer-szolgáltatással kommunikál. Lehetővé teszi a felhasználó számára, hogy műveleteket hajtson végre az oldalon, amely a háttérbeli érvényesítési technikai profilt hívja meg. Például egy e-mail-cím, telefonszám vagy ügyfél-hűség számának ellenőrzése.

Az alábbi példa `TechnicalProfile` azt szemlélteti, hogyan használhatja a megjelenítési jogcímeket a megjelenítési vezérlőkkel.

* Az első megjelenítési jogcím egy hivatkozást tartalmaz a `emailVerificationControl` megjelenítési vezérlőelemre, amely összegyűjti és ellenőrzi az e-mail-címet.
* Az ötödik megjelenítési jogcím hivatkozik a `phoneVerificationControl` megjelenítési vezérlőelemre, amely egy telefonszámot gyűjt és ellenőriz.
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

Vegye figyelembe a következő példát, amikor `age` egy jogcím **kimeneti** jogcímként van definiálva egy alapházirendben. Ahhoz, hogy megjelenjenek a megjelenítési jogcímek az önérvényesített technikai `age` profilhoz, a rendszer a felhasználótól származó adatok gyűjtésére szolgáló képernyőn jeleníti meg a jogcímet:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Ha egy olyan levélre vonatkozó szabályzat, amely ezt az `officeNumber` alapszintű örökli, a **megjelenítési** jogcímként adja meg a következőket:

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

Az `age` alapházirendben lévő jogcím már nem jelenik meg a képernyőn a felhasználó számára – ez gyakorlatilag "rejtett". A `age` jogcím megjelenítéséhez és a kor értékének a felhasználótól való összegyűjtéséhez hozzá `age` kell adnia egy **DisplayClaim**.

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a következő előkészítési lépéshez visszaadott jogcímek listáját tartalmazza. A **DefaultValue** attribútum csak akkor lép életbe, ha a jogcím még nincs beállítva. Ha egy korábbi előkészítési lépésben lett beállítva, az alapértelmezett érték nem lép érvénybe, még akkor sem, ha a felhasználó üresen hagyja az értéket. Az alapértelmezett érték használatának kényszerítéséhez állítsa a **AlwaysUseDefaultValue** attribútumot a következőre `true`:.

Biztonsági okokból a jelszó-jogcím értéke (`UserInputType` értéke `Password`) csak az önjelölt technikai profil érvényesítési technikai profiljaiban érhető el. A következő hangolási lépések során nem használhatja a jelszó jogcímet. 

> [!NOTE]
> Az Identity Experience Framework (IEF) korábbi verzióiban a kimeneti jogcímeket a rendszer a felhasználó adatainak gyűjtésére használta. Ha adatokat szeretne gyűjteni a felhasználótól, használjon helyette egy **DisplayClaims** -gyűjteményt.

A **OutputClaimsTransformations** elem olyan **OutputClaimsTransformation** -elemek gyűjteményét is tartalmazhatja, amelyek a kimeneti jogcímek módosítására vagy újak előállítására szolgálnak.

### <a name="when-you-should-use-output-claims"></a>Ha kimeneti jogcímeket szeretne használni

Az önellenőrzött technikai profilokban a kimeneti jogcímek gyűjteménye a következő előkészítési lépéshez adja vissza a jogcímeket.

Kimeneti jogcímek használata:

- **A jogcímeket kimeneti jogcím-átalakítás**eredményezi.
- A **kimeneti jogcímek alapértelmezett értékének beállítása** anélkül, hogy adatokat kellene begyűjtenie a felhasználótól, vagy vissza kell adni az adatokat az érvényesítési technikai profilból. Az `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profil állítja be a **végrehajtotta a SelfAsserted-input** jogcímet a következőre: `true`.
- **Az érvényesítési technikai profil a kimeneti jogcímeket adja vissza** – a technikai profil egy érvényesítési műszaki profilt hívhat meg, amely visszaadja a jogcímeket. Előfordulhat, hogy fel kívánja készíteni a jogcímeket, és visszaadja azokat a felhasználói utazás következő hangolási lépéseihez. Ha például helyi fiókkal jelentkezik be, a nevű `SelfAsserted-LocalAccountSignin-Email` önaláírt technikai profil meghívja a nevű `login-NonInteractive`érvényesítési technikai profilt. Ez a technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és a felhasználói profilt is visszaadja. Ilyen például a "userPrincipalName", a "displayName", a "givenName" és a "vezetéknév".
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

A PersistedClaims elem nincs használatban. Az önérvényesített technikai profil nem tartja Azure AD B2Cba az adatvédelmet. Ehelyett egy olyan érvényesítési technikai profilra irányuló hívás történik, amely felelős az adatmegőrzésért. A regisztrálási szabályzat például az `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profilt használja az új felhasználói profil összegyűjtéséhez. A `LocalAccountSignUpWithLogonEmail` technikai profil meghívja az érvényesítési technikai profilt, hogy létrehozza a fiókot Azure ad B2Cban.

## <a name="validation-technical-profiles"></a>Ellenőrzési technikai profilok

Egy érvényesítési műszaki profil a hivatkozó technikai profil kimeneti jogcímeinek érvényesítésére szolgál. Az érvényesítési technikai profil bemeneti jogcímeit az önérvényesített technikai profil kimeneti jogcímeiben kell megjelennie. Az érvényesítési technikai profil ellenőrzi a felhasználó által megadott adatokat, és hibát ad vissza a felhasználónak.

Az érvényesítési technikai profil a szabályzat bármely műszaki profilja lehet, például [Azure Active Directory](active-directory-technical-profile.md) vagy egy [REST API](restful-technical-profile.md) technikai profil. Az előző példában a `LocalAccountSignUpWithLogonEmail` technikai profil ellenőrzi, hogy a signinName nem létezik-e a címtárban. Ha nem, az érvényesítési technikai profil létrehoz egy helyi fiókot, és visszaadja a objectId, a authenticationSource, a newUser. A `SelfAsserted-LocalAccountSignin-Email` technikai profil meghívja `login-NonInteractive` az érvényesítési technikai profilt a felhasználói hitelesítő adatok ellenőrzéséhez.

Az üzleti logikával meghívhat egy REST API technikai profilt, felülírhatja a bemeneti jogcímeket, vagy gazdagíthatja a felhasználói adatokat a vállalati üzletági alkalmazások további integrálásával. További információ: [érvényesítési technikai profil](validation-technical-profile.md)

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Setting. operatingMode <sup>1</sup>| Nem | A bejelentkezési oldalon ez a tulajdonság szabályozza a Felhasználónév mező viselkedését, például a bemeneti ellenőrzés és a hibaüzenetek értékét. Várt értékek: `Username` vagy `Email`.  |
| AllowGenerationOfClaimsWithNullValues| Nem| Egy NULL értékű jogcím létrehozását teszi lehetővé. Ha például a felhasználó nem jelöli be a jelölőnégyzetet.|
| ContentDefinitionReferenceId | Igen | Az ehhez a technikai profilhoz társított [tartalom-definíció](contentdefinitions.md) azonosítója. |
| EnforceEmailVerification | Nem | A regisztrációhoz vagy a profil szerkesztéséhez érvényesíti az e-mailek ellenőrzését. Lehetséges értékek: `true` (alapértelmezett) vagy `false`. |
| Setting. retryLimit | Nem | Meghatározza, hogy a felhasználók hányszor próbálják meg megadni az érvényesítési technikai profilban ellenőrzött adatmennyiséget. Egy felhasználó például megpróbál egy olyan fiókkal regisztrálni, amely már létezik, és megtartja a próbálkozást, amíg el nem éri a korlátot.
| <sup>1</sup> . SignUpTarget| Nem | A regisztrációs cél Exchange-azonosítója. Amikor a felhasználó a feliratkozás gombra kattint, Azure AD B2C végrehajtja a megadott Exchange-azonosítót. |
| Setting. showCancelButton | Nem | Megjeleníti a Mégse gombot. Lehetséges értékek: `true` (alapértelmezett), vagy`false` |
| Setting. showContinueButton | Nem | Megjeleníti a folytatás gombot. Lehetséges értékek: `true` (alapértelmezett), vagy`false` |
| Setting. showSignupLink <sup>2</sup>| Nem | Megjeleníti a regisztrációs gombot. Lehetséges értékek: `true` (alapértelmezett), vagy`false` |
| Setting. forgotPasswordLinkLocation <sup>2</sup>| Nem| Az elfelejtett jelszó hivatkozását jeleníti meg. Lehetséges értékek: `AfterInput` (alapértelmezett) a hivatkozás megjelenik az oldal alján, vagy `None` eltávolítja az elfelejtett jelszó hivatkozást.|
| Setting. enableRememberMe <sup>2</sup>| Nem| Megjeleníti a [bejelentkezett marad](custom-policy-keep-me-signed-in.md) jelölőnégyzetet. Lehetséges értékek: `true` , vagy `false` (alapértelmezett). |
| IncludeClaimResolvingInClaimsHandling  | Nem | A bemeneti és a kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true`, vagy `false`  (alapértelmezett). Ha a technikai profilban a jogcím-feloldót szeretné használni, állítsa be a következőt: `true`. |

Megjegyzések:
1. Elérhető a Content definition [DataUri](contentdefinitions.md#datauri) típusa `unifiedssp`vagy `unifiedssd`.
1. Elérhető a Content definition [DataUri](contentdefinitions.md#datauri) típusa `unifiedssp`vagy `unifiedssd`. Az [oldal elrendezésének](page-layout.md) 1.1.0-es vagy újabb verziója.

## <a name="cryptographic-keys"></a>Titkosítási kulcsok

A **CryptographicKeys** elem nincs használatban.
