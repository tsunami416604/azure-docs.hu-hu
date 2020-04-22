---
title: RelyingParty - Azure Active Directory B2C | Microsoft dokumentumok
description: Adja meg az egyéni szabályzat RelyingParty elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 733a33881fe3acc962aeda4b05a1b01be4e148ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680363"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **RelyingParty** elem megadja a felhasználói út az Azure Active Directory B2C (Azure AD B2C) aktuális kérelem kényszerítéséhez. Azt is meghatározza a jogcímek listáját, hogy a függő entitás (RP) alkalmazás szükséges részeként a kiadott jogkivonat. Rp-alkalmazás, például egy webes, mobil- vagy asztali alkalmazás, meghívja az RP-házirendfájlt. Az RP házirendfájl egy adott feladatot hajt végre, például bejelentkezik, alaphelyzetbe állít egy jelszót vagy szerkeszt egy profilt. Több alkalmazás használhatja ugyanazt az RP-házirendet, és egyetlen alkalmazás több házirendet is használhat. Minden RP-alkalmazás ugyanazt a jogkivonatot kapja a jogcímekkel, és a felhasználó ugyanazt a felhasználói utat járja végig.

A következő példa egy **RelyingParty** elemet mutat be a *B2C_1A_signup_signin* házirendfájlban:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
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
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
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

Az opcionális **RelyingParty** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Az RP alkalmazás alapértelmezett felhasználói útja. |
| UserJourneyBehaviors (FelhasználóutazásViselkedéseK) | 0:1 | A felhasználói út viselkedésének hatóköre. |
| TechnicalProfile (Műszaki profil) | 1:1 | Az RP alkalmazás által támogatott technikai profil. A technikai profil szerződést biztosít az RP-alkalmazás hoz kapcsolatba az Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Az `DefaultUserJourney` elem a felhasználói út azonosítójára mutató hivatkozást ad meg, amelyet általában az Alap vagy a Bővítmények házirend határoz meg. A következő példák a **RelyingParty** elemben megadott regisztrációs vagy bejelentkezési felhasználói utat mutatják be:

*B2C_1A_signup_signin* politika:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* vagy *B2C_1A_TrustFrameworkExtensionPolicy:*

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

A **DefaultUserJourney** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendben a felhasználói út azonosítója. További információ: [Felhasználói utak](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors (FelhasználóutazásViselkedéseK)

A **UserJourneyBehaviors** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| SingleSignon (egyedi jel) | 0:1 | Az egyszeri bejelentkezési (SSO) munkamenet viselkedésének hatóköre egy felhasználói út. |
| SessionExpiryType típus |0:1 | A munkamenet hitelesítési viselkedése. Lehetséges `Rolling` értékek: `Absolute`vagy . Az `Rolling` érték (alapértelmezett) azt jelzi, hogy a felhasználó mindaddig bejelentkezve marad, amíg a felhasználó folyamatosan aktív az alkalmazásban. Az `Absolute` érték azt jelzi, hogy a felhasználó kénytelen újrahitelesíteni az alkalmazás munkamenetének élettartama által meghatározott időszak után. |
| SessionExpiryInSeconds | 0:1 | Az Azure AD B2C munkamenet-cookie-jának élettartama a felhasználó böngészőjében a sikeres hitelesítés torkollataként megadott egész számként. |
| JourneyInsights között | 0:1 | Az Azure Application Insights instrumentation kulcs használható. |
| ContentDefinitionParameters (ContentDefinitionParameters) | 0:1 | A tartalomdefiníciós betöltési URI-hoz hozzáfűzendő kulcsértékpárok listája. |
|Parancsfájlok végrehajtása| 0:1| A támogatott [JavaScript](javascript-samples.md) végrehajtási módok. Lehetséges `Allow` értékek: `Disallow` vagy (alapértelmezett).

### <a name="singlesignon"></a>SingleSignon (egyedi jel)

A **SingleSignOn** elem a következő attribútumban található:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Hatókör | Igen | Az egyszeri bejelentkezési viselkedés hatóköre. Lehetséges `Suppressed`értékek: `Tenant` `Application`, `Policy`, , vagy . Az `Suppressed` érték azt jelzi, hogy a viselkedés le van tiltva, és a felhasználó mindig kéri az identitásszolgáltató kiválasztását.  Az `Tenant` érték azt jelzi, hogy a viselkedés a bérlő összes házirendjében alkalmazva van. Például egy felhasználó navigálás két házirend-utak egy bérlő nem kéri az identitásszolgáltató kiválasztása. Az `Application` érték azt jelzi, hogy a viselkedés a kérelmet küldő alkalmazás összes házirendjére vonatkozik. Például egy felhasználó navigálás két házirend-utak egy alkalmazás nem kéri az identitásszolgáltató kiválasztása. Az `Policy` érték azt jelzi, hogy a viselkedés csak egy házirendre vonatkozik. Például egy felhasználó navigálkét házirend-utak egy megbízhatósági keretrendszer kéri az identitásszolgáltató kiválasztása, amikor a házirendek közötti váltás. |
| KeepAliveInDays között | Igen | Azt határozza meg, hogy a felhasználó mennyi ideig maradjon bejelentkezve. Ha az értéket 0-ra állítja, kikapcsolja a KMSI funkciót. További információ: [A bejelentkezve tartás](custom-policy-keep-me-signed-in.md)című témakörben talál. |
|EnforceIdTokenHintOnLogout| Nem|  Kényszerítse, hogy egy korábban kiadott azonosító jogkivonatot továbbítson a kijelentkezési végpontnak, mint egy tipp a végfelhasználó aktuális hitelesített munkamenetéről az ügyféllel. Lehetséges értékek: `false` (alapértelmezett) vagy `true`. További információt a [Webes bejelentkezés az OpenID Connect segítségével című témakörben talál.](openid-connect.md)  |


## <a name="journeyinsights"></a>JourneyInsights között

A **JourneyInsights** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| TelemettryEngine | Igen | Az értéknek `ApplicationInsights`. |
| InstrumentationKey (Műszerezési kulcs) | Igen | A karakterlánc, amely tartalmazza az application insights elem instrumentation kulcsát. |
| DeveloperMode | Igen | Lehetséges `true` értékek: `false`vagy . Ha `true`az Application Insights felgyorsítja a telemetriai adatokat a feldolgozási folyamaton keresztül. Ez a beállítás jó a fejlesztéshez, de nagy mennyiségben korlátozott A részletes tevékenységnaplók csak az egyéni házirendek fejlesztésének elősegítésére szolgálnak. Ne használja a fejlesztési módot éles környezetben. A naplók a fejlesztés során az identitásszolgáltatóknak küldött és az identitásszolgáltatóktól küldött összes jogcímeket gyűjtik. Ha éles környezetben használják, a fejlesztő felelősséget vállal a pii (magánazonosításra alkalmas adatok) gyűjtött az App Insights-napló, hogy a saját. Ezek a részletes naplók csak akkor kerülnek `true`összegyűjtésre, ha ez az érték a értékre van állítva.|
| ClientEnabled | Igen | Lehetséges `true` értékek: `false`vagy . Ha `true`a program az Application Insights ügyféloldali parancsfájlt küldi az oldalnézet és az ügyféloldali hibák nyomon követéséhez. |
| Kiszolgáló engedélyezve | Igen | Lehetséges `true` értékek: `false`vagy . Ha `true`a , elküldi a meglévő UserJourneyRecorder JSON egyéni eseményként application insights. |
| TelemettryVersion | Igen | Az értéknek `1.0.0`. |

További információ: [Naplók gyűjtése](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters (ContentDefinitionParameters)

Egyéni szabályzatok használatával az Azure AD B2C, küldhet egy paramétert egy lekérdezési karakterláncban. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. Az Azure AD B2C átadja a lekérdezési karakterlánc paramétereit a dinamikus HTML-fájlnak, például az aspx fájlnak.

A következő példa egy `campaignId` a lekérdezési `hawaii` karakterláncban megadott paramétert ad át:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

A **ContentDefinitionParameters** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ContentDefinitionParaméter | 0:n | Egy olyan karakterlánc, amely egy tartalomdefiníciós URI lekérdezési karakterláncához hozzáfűzött kulcsérték-párt tartalmazza. |

A **ContentDefinitionParameter** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | A kulcsérték-pár neve. |

További információ: [A felhasználói felület konfigurálása dinamikus tartalommal egyéni házirendek használatával](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>TechnicalProfile (Műszaki profil)

A **TechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Az értéknek `PolicyProfile`. |

A **TechnicalProfile** a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | A technikai profil nevét tartalmazó karakterlánc. |
| Leírás | 0:1 | A technikai profil leírását tartalmazó karakterlánc. |
| Protocol (Protokoll) | 1:1 | Az összevonáshoz használt protokoll. |
| Metaadatok | 0:1 | A protokoll által a végpontdal való kommunikációhoz a tranzakció során használt kulcs-/értékpárok *elemének* gyűjteménye a függő entitás és a közösség többi résztvevője közötti interakció konfigurálásához. |
| Kimeneti jogcímek | 1:1 | A technikai profilkimenetként figyelembe vett jogcímtípusok listája. Ezen elemek mindegyike egy olyan **Jogcímtípusra** vagy olyan házirendre hivatkozik, amelyből ez **a** házirendfájl örököl. |
| SubjectNamingInfo | 1:1 | A jogkivonatokban használt tulajdonosnév. |

A **Protokoll** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | Az Azure AD B2C által támogatott, a technikai profil részeként használt érvényes protokoll neve. Lehetséges `OpenIdConnect` értékek: `SAML2`vagy . Az `OpenIdConnect` érték az OpenID Connect 1.0 protokollszabványt jelöli az OpenID alapozási specifikáció szerint. Az `SAML2` az SAML 2.0 protokoll szabványt képviseli az OASIS specifikáció szerint. |

## <a name="outputclaims"></a>Kimeneti jogcímek

A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaim (Kimeneti jogcím) | 0:n | Annak a házirendnek a támogatott listájában szereplő várt jogcímtípus neve, amelyre a függő entitás előfizet. Ez az állítás a technikai profil kimeneteként szolgál. |

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípushivatkozásazonosító | Igen | Hivatkozás egy, a házirendfájl **ClaimsSchema** szakaszában már definiált **Jogcímtípusra.** |
| DefaultValue érték | Nem | Olyan alapértelmezett érték, amely akkor használható, ha a jogcímérték üres. |
| PartnerClaimType | Nem | A jogcím küldése a ClaimType definícióban konfigurált más néven. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

A **SubjectNameingInfo** összetevővel szabályozhatja a token tulajdonosának értékét:
- **JWT token** `sub` - a követelés. Ez egy olyan fő, amelyről a jogkivonat adatokat, például egy alkalmazás felhasználóját érvényesíti. Ez az érték nem módosítható, és nem rendelhető hozzá újra és nem használható fel újra. Biztonságos engedélyezési ellenőrzések végrehajtására használható, például amikor a jogkivonatot egy erőforrás eléréséhez használják. Alapértelmezés szerint a tulajdonosjogcím a címtárban lévő felhasználó objektumazonosítójával van feltöltve. További információ: [Token, session and single sign-on configuration](session-behavior.md).
- **SAML token** `<Subject><NameID>` - az elem, amely azonosítja a tárgy elem.

A **SubjectNamingInfo** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípus | Igen | Hivatkozás a kimeneti jogcím **PartnerClaimType - címére.** A kimeneti jogcímeket meg kell határozni a függő entitás házirend **OutputClaims** gyűjteményében. |

A következő példa bemutatja, hogyan definiálható egy OpenID Connect függő entitás. A tulajdonos névadatai a `objectId`következők:

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
A JWT-jogkivonat tartalmazza a `sub` jogcímet a felhasználó objectId azonosítójával:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
