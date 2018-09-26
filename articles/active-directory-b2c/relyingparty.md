---
title: RelyingParty – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az egyéni szabályzat RelyingParty elem Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8f56f287fde9e17d2a17298ca04eda63b69e3636
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159924"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **RelyingParty** elem azt határozza meg a felhasználói út az aktuális kérelem Azure Active Directory (Azure AD) B2C-vel kényszerítésére. A kiállított token részeként a jogcímek, a függő entitásonkénti (RP) alkalmazása szükséges listája is megadja. Függő Entitás az alkalmazás, például egy webes, mobil és asztali alkalmazás, a helyreállítási pont Védettként házirendfájl meghívja. A helyreállítási pont Védettként házirendfájl végrehajt egy adott feladat, például a bejelentkezés, a jelszó alaphelyzetbe állítása vagy a profil szerkesztését. Több alkalmazás használhatja ugyanazt az RP-házirendet, és egyetlen alkalmazás több szabályzatokat használhatja. Az összes helyreállítási pont Védettként alkalmazások kap erőforrásigényekkel ugyanezt a tokent, és a felhasználó végighalad az ugyanazon felhasználói interakciósorozat.

A következő példa bemutatja egy **RelyingParty** eleme a *B2C_1A_signup_signin* házirendfájl:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" />
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description> 
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

A választható **RelyingParty** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Az RP-alkalmazás alapértelmezett felhasználói interakciósorozat. |
| UserJourneyBehaviors | 0:1 | A felhasználói interakciósorozat viselkedés hatókörét. |
| TechnicalProfile | 1:1 | Technikai profil az RP-alkalmazás által támogatott. A technikai profil egy szerződést az RP alkalmazás lépjen kapcsolatba az Azure AD B2C-t biztosít. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

A `DefaultUserJourney` elem azt határozza meg egy hivatkozást, amely általában az alap- vagy bővítmények szabályzat van megadva a felhasználói interakciósorozat azonosítóját. Az alábbi példák bemutatják a megadott regisztrációs vagy bejelentkezési felhasználói interakciósorozat a **RelyingParty** elem:

*B2C_1A_signup_signin* házirend:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* vagy *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

A **DefaultUserJourney** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| a referenceid megadása | Igen | A házirend a felhasználói út azonosítója. További információ megtekintéséhez [felhasználói utak](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

A **UserJourneyBehaviors** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Egyszeri bejelentkezés | 0:1 | Egyszeri bejelentkezés (SSO) munkamenet viselkedését egy felhasználói interakciósorozat hatókörét. |
| SessionExpiryType |0:1 | A hitelesítési viselkedést, a munkamenet. A lehetséges értékek: `Rolling` vagy `Absolute`. A `Rolling` érték (alapértelmezett) azt jelzi, hogy a felhasználó bejelentkezve marad-e, amíg a felhasználó az alkalmazásban folyamatosan aktív. A `Absolute` érték azt jelzi, hogy hitelesítse magát újra, az alkalmazás munkamenet által megadott időszak után a felhasználónak kötelező élettartamát. |
| SessionExpiryInSeconds | 0:1 | Az Azure AD B2C munkamenet cookie-k egész számként megadott élettartama tárolja a felhasználó böngészőjében a sikeres hitelesítést követően. |
| JourneyInsights | 0:1 | Az Azure Application Insights kialakítási kulcsot használni. |
| ContentDefinitionParameters | 0:1 | A kulcs-érték párral kell csatolni a tartalomdefiníció terhelés URI listája. |

### <a name="singlesignon"></a>Egyszeri bejelentkezés

A **SingleSignOn** elem tartalmazza a következő attribútum:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Hatókör | Igen | Egyszeri bejelentkezés viselkedésének hatókörét. A lehetséges értékek: `Suppressed`, `Tenant`, `Application`, vagy `Policy`. A `Suppressed` érték azt jelzi, hogy a viselkedés nem jelenik meg. Például egy egyszeri bejelentkezés munkamenet, a felhasználó nem munkamenet változatlan marad, és a felhasználó mindig a rendszer egy identitás-szolgáltató kiválasztása. A `TrustFramework` érték azt jelzi, hogy a lesz alkalmazva a bizalmi keretrendszer minden szabályzat. Például egy felhasználó a bizalmi keretrendszer két szabályzat teljesített végiglépkedhet nem kell megadnia az identitás szolgáltató kiválasztása. A `Tenant` érték azt jelzi, hogy a viselkedés a bérlő összes szabályzat van hozzárendelve. Ha például két szabályzat Journey végiglépkedhet a bérlőhöz tartozó felhasználó nem kell megadnia egy identitás-szolgáltató kiválasztása. A `Application` érték azt jelzi, hogy a lesz alkalmazva a kérést az alkalmazás minden házirendben. Például egy felhasználó az alkalmazás két szabályzat Journey végiglépkedhet nem kell megadnia az identitás szolgáltató kiválasztása. A `Policy` érték azt jelzi, hogy a csak történnek egy házirend. Például egy felhasználó a bizalmi keretrendszer két szabályzat teljesített végiglépkedhet a rendszer egy identitás-szolgáltató kiválasztása házirendek közötti váltáskor. |

## <a name="journeyinsights"></a>JourneyInsights

A **JourneyInsights** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| TelemetryEngine | Igen | Az értéknek kell lennie `ApplicationInsights`. | 
| InstrumentationKey | Igen | A karakterlánc, amely tartalmazza a kialakítási kulcsot az application insights elemhez. |
| DeveloperMode | Igen | A lehetséges értékek: `true` vagy `false`. Ha `true`, az Application Insights felgyorsítja a telemetriát a feldolgozási folyamat keresztül. Ez a beállítás jó fejlesztéshez, de nagy mennyiségben, a részletes tevékenységeket tartalmazó naplók úgy tervezték, ezzel elősegítve az egyéni szabályzatok fejlesztését, csak korlátozott. Fejlesztői mód nem használható éles környezetben. Naplók összegyűjtése minden jogcím küldött és az identitás-szolgáltatóktól származó fejlesztés során. Ha éles környezetben használja, a fejlesztői felelősséget személyazonosításra alkalmas adatok (közvetlenül a Microsoftnak azonosításra alkalmas adatokat) a saját App Insights napló gyűjtése. Ha ez az érték beállítása a részletes naplók program csak `true`.|
| ClientEnabled | Igen | A lehetséges értékek: `true` vagy `false`. Ha `true`, elküldi az Application Insights ügyféloldali parancsfájlok oldal nézet és az ügyféloldali hibák nyomon követéséhez. | 
| ServerEnabled | Igen | A lehetséges értékek: `true` vagy `false`. Ha `true`, a meglévő UserJourneyRecorder JSON, egyéni eseményt küld az Application Insights. | 
| TelemetryVersion | Igen | Az értéknek kell lennie `1.0.0`. | 

További információkért lásd: [naplók összegyűjtése](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Azure AD B2C-vel egyéni szabályzatok használatával küldhet egy lekérdezési karakterlánc paramétert. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. Az Azure AD B2C lekérdezésikarakterlánc-paramétereket továbbítja a lekérdezés dinamikus HTML-fájlba, például aspx fájl. 

Az alábbi példa továbbítja nevű paraméter `campaignId` értékkel `hawaii` a lekérdezési karakterlánc:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

A **ContentDefinitionParameters** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | A kulcs-érték pár, amelyek számára a lekérdezési karakterlánc egy tartalomdefiníció terhelés URI-t tartalmazó karakterlánc. |

A **ContentDefinitionParameter** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | A kulcs-érték pár neve. |

További információkért lásd: [konfigurálása a felhasználói felület dinamikus tartalom egyéni szabályzatok használatával](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

A **TechnicalProfile** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- | 
| Azonosító | Igen | Az értéknek kell lennie `PolicyProfile`. |

A **TechnicalProfile** a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Megjelenítendő név | 0:1 | A technikai profil felhasználók számára megjelenő nevét tartalmazó karakterlánc. |
| Leírás | 0:1 | A karakterlánc, amely tartalmazza a felhasználók számára megjelenő technikai profil leírása. |
| Protokoll | 1:1 | Az összevonási használt protokollt. |
| Metaadatok | 0:1 | Gyűjteményét *elem* kulcs/érték párok képviselőinkkel egy tranzakciónak a végponttal való kommunikációhoz a protokoll által használt fel, a függő entitáshoz, és egyéb közösségi résztvevők közötti interakciók konfigurálása. |
| OutputClaims | 0:1 | Jogcímtípusok, amelyeket a rendszer a technikai profilban kimenetként listája. Az ilyen elemek hivatkozást tartalmaz egy **takar** már definiálva a **ClaimsSchema** szakasz vagy egy szabályzatot, amelyen ez a házirend a fájl örökli. |
| SubjectNamingInfo | 0:1 | A tulajdonos nevét a jogkivonatok használt. |

A **protokoll** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | A technikai profil részeként használt Azure AD B2C által támogatott érvényes protokoll neve. A lehetséges értékek: `OpenIdConnect` vagy `SAML2`. A `OpenIdConnect` értékét jelöli az OpenID Connect 1.0 protokoll standard OpenID foundation specifikációnak megfelelően. A `SAML2` OASIS specifikációnak megfelelően a SAML 2.0 protokoll standard jelöli. Éles környezetben ne használja a SAML-jogkivonat. |

## <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| kimeneti jogcím | 0: n | A támogatott listán egy várt jogcímtípus, amelyhez a függő entitás feliratkozik a szabályzat neve. Ezt az igényt a kimenetként a technikai profil szolgálja ki. |

A **kimeneti jogcím** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | Egy hivatkozást egy **takar** már definiálva a **ClaimsSchema** szakaszt a házirend-fájlban. |
| DefaultValue érték | Nem | Alapértelmezett érték is használható, ha a jogcím értéke üres. |
| PartnerClaimType | Nem | A jogcímet küld egy másik nevet az takar meghatározása szerint. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Az a **SubjectNameingInfo** elemben meghatározhatja, hogy a jogkivonat tárgyában értékét:
- **JTW jogkivonat** – a `sub` jogcím. Ez a arról, hogy mely a token használjon esetleg imperatív állításokat információkat, például egy alkalmazás a felhasználó rendszerbiztonsági tag. Ez az érték nem módosítható és nem hozzárendelni és újra felhasználható. Használható például az erőforrások eléréséhez a token használatakor a biztonságos hitelesítési ellenőrzések elvégzéséhez. Alapértelmezés szerint megjelenik a tulajdonos jogcímet a felhasználó Objektumazonosítóját. További információkért lásd: [jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak](active-directory-b2c-token-session-sso.md).
- **SAML-jogkivonat** – a `<Subject><NameID>` elem, amely azonosítja a tulajdonos elemben.

A **SubjectNamingInfo** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Takar | Igen | Egy hivatkozást egy kimenő jogcímet **PartnerClaimType**. A kimeneti jogcímek definiálni kell a függő entitás házirend **OutputClaims** gyűjtemény. |

Az alábbi példa bemutatja, hogyan egy függő entitás OpenId Connect meghatározásához. A tulajdonos neve info van konfigurálva, a `objectId`:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
A JWT jogkivonat tartalmazza a `sub` a felhasználó objectId jogcím:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


