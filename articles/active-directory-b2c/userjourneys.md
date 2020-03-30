---
title: UserJourneys | Microsoft dokumentumok
description: Adja meg az egyéni szabályzat UserJourneys elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227006"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A felhasználói utak explicit elérési utakat határoznak meg, amelyeken keresztül a házirend lehetővé teszi, hogy a függő entitás alkalmazás beszerezze a kívánt jogcímeket a felhasználó számára. A felhasználó ezeken az elérési utakon keresztül a függő entitásszámára bemutatandó jogcímek lekéréséhez kerül sor. Más szóval a felhasználói utak határozzák meg az üzleti logikája, amit a végfelhasználó megy keresztül, mint az Azure AD B2C identitáskezelési keretrendszer feldolgozza a kérelmet.

Ezek a felhasználói utak sablonnak tekinthetők, amely kielégíti az érdekközösség különböző függő felei alapvető igényeit. A felhasználói utak megkönnyítik a függő entitás házirend-részének meghatározását. A házirend több felhasználói utazást is definiálhat. Minden felhasználói út vezénylési lépések sorozata.

A házirend által támogatott felhasználói utak meghatározásához a házirendfájl legfelső szintű eleme alá egy **UserJourneys** elem kerül hozzáadásra.

A **UserJourneys** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| UserJourney (Felhasználóutazás) | 1:n | Olyan felhasználói út, amely a teljes felhasználói folyamathoz szükséges összes konstrukciót definiálja. |

A **UserJourney** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A felhasználói út azonosítója, amely a házirend más elemeiből való hivatkozásra használható. A [függő entitás házirendjének](relyingparty.md) **DefaultUserJourney** eleme erre az attribútumra mutat. |

A **UserJourney** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Vezénylési lépések | 1:n | Vezénylési szekvencia, amelyet a sikeres tranzakcióhoz végig kell követni. Minden felhasználói út a sorrendben végrehajtott vezénylési lépések rendezett listájából áll. Ha bármelyik lépés sikertelen, a tranzakció sikertelen lesz. |

## <a name="orchestrationsteps"></a>Vezénylési lépések

A felhasználói út vezénylési szekvenciaként jelenik meg, amelyet a sikeres tranzakcióhoz végig kell követni. Ha bármelyik lépés sikertelen, a tranzakció sikertelen lesz. Ezek a vezénylési lépések az építőelemekre és a házirendfájlban engedélyezett jogcímszolgáltatókra hivatkoznak. A felhasználói élmény megjelenítéséért vagy megjelenítéséért felelős vezénylési lépés szintén rendelkezik hivatkozással a megfelelő tartalomdefiníció-azonosítóra.

Vezénylési lépések feltételesen hajthatók végre a vezénylési lépéselemben meghatározott előfeltételek alapján. Például ellenőrizheti, hogy végre egy vezénylési lépés csak akkor, ha egy adott jogcímek léteznek, vagy ha a jogcím egyenlő vagy nem a megadott értéket.

Vezénylési lépések rendezett listájának megadásához a házirend részeként egy **OrchestrationSteps** elem kerül hozzáadásra. Ez az elem szükséges.

A **OrchestrationSteps** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Vezénylési lépés | 1:n | Egy rendezett vezénylési lépés. |

A **OrchestrationStep** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Order` | Igen | A vezénylési lépések sorrendje. |
| `Type` | Igen | A vezénylési lépés típusa. Lehetséges értékek: <ul><li>**ClaimsProviderSelection** – Azt jelzi, hogy a vezénylési lépés különböző jogcímszolgáltatókat jelenít meg a felhasználónak, hogy kiválasszon egyet.</li><li>**CombinedSignInAndSignUp** – Azt jelzi, hogy a vezénylési lépés egy kombinált közösségi szolgáltató bejelentkezési és helyi fiókregisztrációs oldalt jelenít meg.</li><li>**ClaimsExchange** – Azt jelzi, hogy a vezénylési lépés jogcímeket cserél egy jogcímszolgáltatóval.</li><li>**GetClaims** – Azt jelzi, hogy a vezénylési lépés beolvassa a bemeneti jogcímeket.</li><li>**SendClaims** – Azt jelzi, hogy a vezénylési lépés elküldi a jogcímeket a függő entitás egy jogkivonatot egy jogcímkibocsátó által kiadott jogkivonattal.</li></ul> |
| ContentDefinitionReferenceId azonosító | Nem | A vezénylési lépéshez társított [tartalomdefiníció](contentdefinitions.md) azonosítója. Általában a tartalomdefinícióreferencia-azonosító az önérvényesítő technikai profilban van meghatározva. De vannak olyan esetek, amikor az Azure AD B2C kell megjeleníteni valamit technikai profil nélkül. Két példa van – ha a vezénylési lépés típusa a következők egyike: `ClaimsProviderSelection` vagy `CombinedSignInAndSignUp`az Azure AD B2C-nek meg kell jelenítenie az identitásszolgáltató kiválasztását technikai profil nélkül. |
| CpimIssuerTechnicalProfileReferenceId | Nem | A vezénylési lépés típusa `SendClaims`. Ez a tulajdonság határozza meg annak a jogcímszolgáltatónak a technikai profilazonosítóját, amely a függő entitás számára kiadja a jogkivonatot.  Ha hiányzik, nem jön létre függő entitás token. |


A **OrchestrationStep** elem a következő elemeket tartalmazhatja:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltételek | 0:n | A vezénylési lépés végrehajtásához teljesítendő előfeltételek listája. |
| ClaimsProviderSelections (Jogcímszolgáltatókiválasztása) | 0:n | A vezénylési lépés jogcímszolgáltatóinak kiválasztásai. |
| KövetelésekCseréjek | 0:n | A vezénylési lépés jogcímváltásainak listája. |

### <a name="preconditions"></a>Előfeltételek

Az **Előfeltételek** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltétel | 1:n | A használt technikai profiltól függően vagy átirányítja az ügyfelet a jogcímszolgáltató kiválasztása szerint, vagy kiszolgálóhívást kezdeményez jogcímek cseréjére. |


#### <a name="precondition"></a>Előfeltétel

Az **Előfeltétel** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Az előfeltételhez végrehajtandó ellenőrzés vagy lekérdezés típusa. Az érték lehet **ClaimsExist**, amely megadja, hogy a műveleteket végre kell hajtani, ha a megadott jogcímek léteznek a felhasználó aktuális jogcímkészletében, vagy **a ClaimEquals**, amely megadja, hogy a műveleteket végre kell hajtani, ha a megadott jogcím létezik, és értéke megegyezik a megadott értékkel. |
| `ExecuteActionsIf` | Igen | Használjon igaz vagy hamis tesztet annak eldöntéséhez, hogy az előfeltételben lévő műveleteket végre kell-e hajtani. |

Az **előfeltétel** elemei a következő elemeket tartalmazzák:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Érték | 1:n | A lekérdezendő ClaimTypeReferenceId azonosító. Egy másik értékelem az ellenőrizendő értéket tartalmazza.</li></ul>|
| Műveletek | 1:1 | A művelet, amelyet végre kell hajtani, ha az előfeltétel ellenőrzése egy vezénylési lépésen belül igaz. Ha a `Action` értéke `SkipThisOrchestrationStep`a értéke, `OrchestrationStep` akkor a társított értéket nem szabad végrehajtani. |

#### <a name="preconditions-examples"></a>Példák előfeltételekre

A következő előfeltételek ellenőrzik, hogy a felhasználó objectId létezik. A felhasználói út során a felhasználó úgy döntött, hogy helyi fiókkal jelentkezik be. Ha az objectId létezik, hagyja ki ezt a vezénylési lépést.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

A következő előfeltételek ellenőrzik, hogy a felhasználó közösségi fiókkal jelentkezett-e be. Kísérlet történt a felhasználói fiók keresésére a címtárban. Ha a felhasználó bejelentkezik, vagy regisztrál egy helyi fiókkal, hagyja ki ezt a vezénylési lépést.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az előfeltételek több előfeltételt is ellenőrizhetnek. A következő példa ellenőrzi, hogy létezik-e "objectId" vagy "email" . Ha az első feltétel igaz, az utazás átugrik a következő vezénylési lépésre.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>Jogcímszolgáltató kiválasztása

Vezénylési lépés, `ClaimsProviderSelection` vagy `CombinedSignInAndSignUp` tartalmazhat egy listát a jogcímszolgáltatók, hogy a felhasználó bejelentkezhet. Az `ClaimsProviderSelections` elemeken belüli elemek sorrendje határozza meg a felhasználónak bemutatott identitásszolgáltatók sorrendjét.

A **ClaimsProviderSelections** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Jogcímszolgáltató kiválasztása | 1:n | A kijelölhető jogcímszolgáltatók listáját tartalmazza.|

A **ClaimsProviderSelections** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DisplayOption| Nem | Egy olyan eset viselkedését szabályozza, amelyben egyetlen jogcímszolgáltató kiválasztása érhető el. Lehetséges értékek: `DoNotShowSingleProvider` (alapértelmezett) , a felhasználó azonnal átirányítja az összevont identitásszolgáltató. Vagy `ShowSingleProvider` az Azure AD B2C bemutatja a bejelentkezési lapot az egyetlen identitásszolgáltató kiválasztásával. Az attribútum használatához a [tartalomdefiníciós verziónak](page-layout.md) és felette kell lennie. `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0`|

A **ClaimsProviderSelection** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId azonosító | Nem | A jogcímcsere azonosítója, amely a jogcímszolgáltató kiválasztásának következő vezénylési lépésében kerül végrehajtásra. Ezt az attribútumot vagy a ValidationClaimsExchangeId attribútumot meg kell adni, de mindkettőt nem. |
| ValidationClaimsExchangeId | Nem | A jogcímcsere azonosítója, amely az aktuális vezénylési lépésben kerül végrehajtásra a jogcímszolgáltató kiválasztásának érvényesítéséhez. Ezt az attribútumot vagy a TargetClaimsExchangeId attribútumot meg kell adni, de mindkettőt nem. |

### <a name="claimsproviderselection-example"></a>Például ClaimsProviderSelection

A következő vezénylési lépésben a felhasználó dönthet úgy, hogy facebookos, LinkedIn-, Twitter-, Google-fiókkal vagy helyi fiókkal jelentkezik be. Ha a felhasználó kiválasztja az egyik közösségi identitásszolgáltatót, a második vezénylési lépés az `TargetClaimsExchangeId` attribútumban megadott kiválasztott jogcímcserével hajtvégre. A második vezénylési lépés átirányítja a felhasználót a közösségi identitás szolgáltatóa a bejelentkezési folyamat befejezéséhez. Ha a felhasználó úgy dönt, hogy jelentkezzen be a helyi fiókkal, az Azure AD B2C marad az azonos vezénylési lépés (ugyanaz ta-előfizetési oldal vagy bejelentkezési oldal), és kihagyja a második vezénylési lépés.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>KövetelésekCseréjek

A **ClaimsExchanges** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| KövetelésekExchange | 1:n | A használt technikai profiltól függően vagy átirányítja az ügyfelet a kiválasztott ClaimsProviderSelection szerint, vagy kiszolgálóhívást kezdeményez jogcímek cseréjére. |

A **ClaimsExchange** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A jogcímcsere-lépés azonosítója. Az azonosító a jogcímszolgáltató kiválasztási lépéséből származó jogcímváltásra hivatkozik a házirendben. |
| TechnicalProfileReferenceId | Igen | A végrehajtandó technikai profil azonosítója. |
