---
title: Önérvényesítő technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Saját érvényesítésű technikai profildefiniálása egyéni szabályzatban az Azure Active Directory B2C-ben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332514"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Önérvényesítő technikai profil definiálása az Azure Active Directory B2C egyéni szabályzatában

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az Azure Active Directory B2C (Azure AD B2C) minden olyan interakció, ahol a felhasználónak bemenetet kell biztosítania, önérvényesítő technikai profil. Például egy regisztrációs oldal, bejelentkezési lap vagy jelszó-visszaállítási lap.

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem **Name** attribútumát a `Proprietary`parancsra kell állítani. A **kezelő** attribútumának tartalmaznia kell az Azure AD B2C által használt protokollkezelő szerelvény teljesen minősített nevét az önérvényesítéshez:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

A következő példa egy önérvényesítő technikai profilt mutat be az e-mailekre való regisztrációhoz:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Bemeneti jogcímek

Egy önérvényesítő technikai profilban használhatja az **InputClaims** és **inputclaimsTransformations** elemeket az önérvényesítő oldalon megjelenő jogcímek értékének (jogcímek megjelenítése) előre. Például a szerkesztési profil házirend, a felhasználói út először beolvassa a felhasználói profilt az Azure AD B2C címtárszolgáltatásból, majd a saját érvényesítése technikai profil beállítja a bemeneti jogcímeket a felhasználói profilban tárolt felhasználói adatokkal. Ezeket a jogcímeket a rendszer a felhasználói profilból gyűjti, majd bemutatja a felhasználónak, aki ezután szerkesztheti a meglévő adatokat.

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

A megjelenítési jogcímek szolgáltatás jelenleg **előzetes verzióban**érhető el.

A **DisplayClaims** elem a képernyőn a felhasználótól történő adatgyűjtéshez bemutatandó jogcímek listáját tartalmazza. A megjelenítési jogcímek értékeinek előzetes feltöltéséhez használja a korábban leírt bemeneti jogcímeket. Az elem alapértelmezett értéket is tartalmazhat.

A **displayclaims** jogcímek sorrendje határozza meg, hogy az Azure AD B2C milyen sorrendben jeleníti meg a jogcímeket a képernyőn. Ha azt szeretné, hogy a felhasználó értéket adjon meg egy adott jogcímhez, állítsa a **DisplayClaim** elem `true` **Kötelező** attribútumát a értékre.

A **DisplayClaims** gyűjtemény **ClaimType** elemének be kell állítania a **UserInputType** elemet az Azure AD B2C által támogatott bármely felhasználói beviteli típusra. Például `TextBox` vagy `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Hivatkozás hozzáadása DisplayControl vezérlőhöz

A megjelenítési jogcímek gyűjteményében megadhat egy hivatkozást a létrehozott [DisplayControl vezérlőre.](display-controls.md) A megjelenítési vezérlő egy olyan felhasználói felületelem, amely speciális funkciókkal rendelkezik, és együttműködik az Azure AD B2C háttérszolgáltatással. Lehetővé teszi a felhasználó számára, hogy olyan műveleteket hajtson végre az oldalon, amelyek érvényesítési technikai profilt hívnak meg a háttérben. Például egy e-mail cím, telefonszám vagy ügyfélhűségi szám ellenőrzése.

A következő `TechnicalProfile` példa a megjelenítési jogcímek használatát mutatja be a megjelenítési vezérlőkkel.

* Az első megjelenítési jogcím `emailVerificationControl` hivatkozik a kijelző vezérlőre, amely összegyűjti és ellenőrzi az e-mail címet.
* Az ötödik megjelenítési állítás `phoneVerificationControl` a kijelzővezérlőre hivatkozik, amely összegyűjti és ellenőrzi a telefonszámot.
* A többi megjelenítési jogcím a felhasználótól gyűjtendő Jogcímtípusok.

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

Mint említettük, a megjelenítési vezérlőre hivatkozó megjelenítési jogcím saját érvényesítést futtathat, például az e-mail cím ellenőrzését. Ezenkívül az önérvényesítő oldal támogatja egy érvényesítési technikai profil használatát a teljes oldal érvényesítéséhez, beleértve a felhasználói bevitelt (jogcímtípusok at vagy a megjelenítési vezérlőket), mielőtt továbblépne a következő vezénylési lépésre.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Gondosan kombinálja a megjelenítési és kimeneti jogcímek használatát

Ha egy önérvényesítő technikai profilban egy vagy több **DisplayClaim** elemet ad meg, akkor displayclaim-et kell *használnia minden olyan* követeléshez, amelyet meg szeretne jeleníteni a képernyőn, és amelyet a felhasználótól szeretne begyűjteni. Nem jelennek meg kimeneti jogcímek egy önérvényesítő technikai profil, amely legalább egy megjelenítési jogcímet tartalmaz.

Vegye figyelembe a következő `age` példát, amelyben a jogcím **egy alapházirend kimeneti** jogcímként van definiálva. Mielőtt bármilyen megjelenítési jogcímet hozzáadná az `age` önérvényesítő technikai profilhoz, a jogcím megjelenik a képernyőn a felhasználótól való adatgyűjtéshez:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Ha egy levélházirend, amely örökli `officeNumber` ezt az alapot, később **megjelenítési** jogcímként van megadva:

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

Az `age` alapházirendben lévő jogcím már nem jelenik meg a képernyőn a felhasználó számára - gyakorlatilag "rejtett". A `age` jogcím megjelenítéséhez és a korérték nek `age` a felhasználótól való beszedéséhez hozzá kell adnia egy **DisplayClaim -t.**

## <a name="output-claims"></a>Kimeneti jogcímek

A **OutputClaims** elem a következő vezénylési lépéshez visszaadandó jogcímek listáját tartalmazza. A **DefaultValue** attribútum csak akkor lép érvénybe, ha a jogcím még soha nem lett beállítva. Ha egy korábbi vezénylési lépésben van beállítva, az alapértelmezett érték nem lép érvénybe, még akkor sem, ha a felhasználó üresen hagyja az értéket. Az alapértelmezett érték használatának kényszerítéséhez állítsa az **AlwaysUseDefaultValue** attribútumot a értékre. `true`

Biztonsági okokból a jelszójogcímértéke `Password`(érték)`UserInputType` csak az önérvényesítő technikai profil ellenőrzési technikai profiljai számára érhető el. A jelszójogcímeket nem használhatja a következő vezénylési lépésekben. 

> [!NOTE]
> Az Identity Experience Framework (IEF) korábbi verzióiban a kimeneti jogcímek a felhasználótól származó adatok gyűjtésére szolgálnak. Ha adatokat szeretne gyűjteni a felhasználótól, használja a **DisplayClaims** gyűjteményt.

A **OutputClaimsTransformations** elem tartalmazhat **OutputClaimsTransformations** elemek gyűjteményét, amelyek a kimeneti jogcímek módosítására vagy újak létrehozására szolgálnak.

### <a name="when-you-should-use-output-claims"></a>Mikor érdemes használni a kimeneti jogcímeket?

Egy önérvényesített technikai profilban a kimeneti jogcímek gyűjteménye visszaadja a jogcímeket a következő vezénylési lépés.

Kimeneti jogcímek használata:

- **A jogcímek kimeneti jogcímek átalakítása szerint kerülnek kibocsátásra**.
- **Alapértelmezett érték beállítása egy kimeneti jogcímben** anélkül, hogy adatokat gyűjtene a felhasználótól, vagy visszaadna az adatokat az érvényesítési technikai profilból. Az `LocalAccountSignUpWithLogonEmail` önérvényesítő technikai profil a **végrehajtott-SelfAsserted-Input** igényt állítja be a rendszerre. `true`
- **Az érvényesítési technikai profil visszaadja a kimeneti jogcímeket** – A technikai profil hívhat egy érvényesítési technikai profilt, amely bizonyos jogcímeket ad vissza. Előfordulhat, hogy szeretné buborék fel a jogcímeket, és vissza a következő vezénylési lépéseket a felhasználói út. Ha például egy helyi fiókkal jelentkezik be, az `SelfAsserted-LocalAccountSignin-Email` önérvényesítő technikai `login-NonInteractive`profil, amelynek neve meghívja az érvényesítési technikai profilt. Ez a technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és a felhasználói profilt is visszaadja. Ilyen például a "userPrincipalName", "displayName", "givenName" és "surName".
- **A kijelzővezérlő a kimeneti jogcímeket adja vissza** - A technikai profil ban lehet hivatkozás egy [kijelzővezérlőre.](display-controls.md) A megjelenítési vezérlő bizonyos jogcímeket, például az ellenőrzött e-mail címet ad vissza. Előfordulhat, hogy szeretné buborék fel a jogcímeket, és vissza a következő vezénylési lépéseket a felhasználói út. A megjelenítésvezérlési funkció jelenleg **előzetes verzióban érhető el.**

A következő példa bemutatja egy önérvényesítő technikai profil használatát, amely a megjelenítési jogcímek és a kimeneti jogcímek egyaránt használja.

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

## <a name="persist-claims"></a>Követelések megőrzése

A PersistedClaims elem nem használatos. Az önérvényesítő technikai profil nem marad meg az adatok at Azure AD B2C. Ehelyett egy érvényesítési technikai profilt hív meg, amely felelős az adatok megőrzéséért. A regisztrációs szabályzat például az `LocalAccountSignUpWithLogonEmail` önérvényesített technikai profilt használja az új felhasználói profil összegyűjtéséhez. A `LocalAccountSignUpWithLogonEmail` technikai profil meghívja az érvényesítési technikai profilt a fiók létrehozásához az Azure AD B2C-ben.

## <a name="validation-technical-profiles"></a>Érvényesítési technikai profilok

Az érvényesítési technikai profil a hivatkozó technikai profil kimeneti jogcímeinek egy részének vagy egészének érvényesítésére szolgál. Az érvényesítési technikai profil bemeneti jogcímeinek meg kell jelenniük az önérvényesítő technikai profil kimeneti jogcímében. Az érvényesítési technikai profil ellenőrzi a felhasználói bevitelt, és hibát adhat vissza a felhasználónak.

Az érvényesítési technikai profil lehet bármilyen technikai profil a szabályzatban, például az [Azure Active Directory](active-directory-technical-profile.md) vagy a REST [API](restful-technical-profile.md) technikai profilok. Az előző példában `LocalAccountSignUpWithLogonEmail` a technikai profil ellenőrzi, hogy a signinName nem létezik a címtárban. Ha nem, az érvényesítési technikai profil létrehoz egy helyi fiókot, és visszaadja az objectId, authenticationSource, newUser. A `SelfAsserted-LocalAccountSignin-Email` technikai profil `login-NonInteractive` meghívja az érvényesítési technikai profilt a felhasználói hitelesítő adatok érvényesítéséhez.

A REST API műszaki profilját az üzleti logikával is meghívhatja, felülírhatja a bemeneti jogcímeket, vagy tovább gazdagíthatja a felhasználói adatokat a vállalati üzletági alkalmazással való további integrálásával. További információ: [Validation technical profile](validation-technical-profile.md)

## <a name="metadata"></a>Metaadatok

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| Nem | Bejelentkezési lap esetén ez a tulajdonság szabályozza a felhasználónév mező működését, például a bemeneti érvényesítést és a hibaüzeneteket. Várt `Username` értékek: `Email`vagy .  |
| A jogcímek engedélyezéseNullértékekkel| Nem| Null értékű jogcím létrehozásának engedélyezése. Egy esetben például a felhasználó nem jelöl ki jelölőnégyzetet.|
| ContentDefinitionReferenceId azonosító | Igen | A technikai profilhoz társított [tartalomdefiníció](contentdefinitions.md) azonosítója. |
| EnforceEmailVerification (EmailVerification) | Nem | A regisztráció hoz vagy profil szerkesztés, kényszeríti e-mail ellenőrzés. Lehetséges értékek: `true` (alapértelmezett) vagy `false`. |
| setting.retryLimit | Nem | Azt határozza meg, hogy a felhasználó hányszor próbálhatja meg megadni az érvényesítési technikai profillal összevetett adatokat. Például egy felhasználó megpróbál regisztrálni egy olyan fiókkal, amely már létezik, és addig próbálkozik, amíg el nem éri a korlátot.
| SignUpTarget <sup>1</sup>| Nem | A regisztrációs célcsere-azonosító. Amikor a felhasználó a regisztrációs gombra kattint, az Azure AD B2C végrehajtja a megadott csereazonosítót. |
| setting.showCancelButton | Nem | Megjeleníti a Mégse gombot. Lehetséges értékek: `true` (alapértelmezett) vagy`false` |
| setting.showContinueButton | Nem | Megjeleníti a Folytatás gombot. Lehetséges értékek: `true` (alapértelmezett) vagy`false` |
| setting.showSignupLink <sup>2</sup>| Nem | Megjeleníti a feliratkozásgombot. Lehetséges értékek: `true` (alapértelmezett) vagy`false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| Nem| Megjeleníti az elfelejtett jelszóhivatkozást. Lehetséges értékek: `AfterInput` (alapértelmezett) a hivatkozás a lap alján `None` jelenik meg, vagy eltávolítja az elfelejtett jelszóhivatkozást.|
| setting.enableRememberMe <sup>2</sup>| Nem| Megjeleníti a [Bejelentkezve bejelentkezve](custom-policy-keep-me-signed-in.md) tartás jelölőnégyzetet. Lehetséges értékek: `true` `false` vagy (alapértelmezett). |
| IncludeClaimResolvingInClaimsHandling  | Nem | Bemeneti és kimeneti jogcímek esetén megadja, hogy a [jogcímek feloldása](claim-resolver-overview.md) szerepel-e a technikai profilban. Lehetséges értékek: `true` `false`  vagy (alapértelmezett). Ha a technikai profilban jogcímfeloldót szeretne `true`használni, állítsa ezt a beállításra. |

Megjegyzések:
1. A [tartalomdefiníciós DataUri](contentdefinitions.md#datauri) `unifiedssp`típusú `unifiedssd`vagy.
1. A [tartalomdefiníciós DataUri](contentdefinitions.md#datauri) `unifiedssp`típusú `unifiedssd`vagy. [Lapelrendezés](page-layout.md) 1.1.0-s vagy újabb verziója.

## <a name="cryptographic-keys"></a>Kriptográfiai kulcsok

A **Kriptográfiai kulcsok** elem nem használatos.
