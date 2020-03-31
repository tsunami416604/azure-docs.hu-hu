---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Adja meg az egyéni szabályzat TechnicalProfiles elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5196615b6b935e4d37565298be03ad315163d132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264309"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **TechnicalProfiles** elem a jogcímszolgáltató által támogatott technikai profilok készletét tartalmazza. Minden jogcímszolgáltatónak rendelkeznie kell egy vagy több technikai profillal, amely meghatározza a jogcímszolgáltatóval való kommunikációhoz szükséges végpontokat és protokollokat. A jogcímszolgáltató több technikai profillal is rendelkezhet.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

A **TechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
|---------|---------|---------|
| Azonosító | Igen | A technikai profil egyedi azonosítója. A technikai profilra a házirendfájl más elemeiből származó azonosító segítségével lehet hivatkozni. Például **OrchestrationSteps** és **ValidationTechnicalProfile**. |

A **TechnicalProfile** a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Domain | 0:1 | A technikai profil tartományneve. Ha például a technikai profilod megadja a Facebook identitásszolgáltatóját, a tartománynév Facebook.com. |
| DisplayName | 1:1 | A felhasználók számára megjeleníthető technikai profil neve. |
| Leírás | 0:1 | A felhasználók számára megjeleníthető technikai profil leírása. |
| Protocol (Protokoll) | 0:1 | A másik féllel való kommunikációhoz használt protokoll. |
| Metaadatok | 0:1 | Kulcs-/értékpárok gyűjteménye, amelyeket a protokoll a tranzakció során a végpontdal való kommunikációhoz használ. |
| InputTokenFormat | 0:1 | A bemeneti jogkivonat formátuma. Lehetséges `JSON`értékek: `JWT` `SAML11`, `SAML2`, , vagy . Az `JWT` érték egy JSON webtokent jelöl az IETF specifikációszerint. Az `SAML11` érték egy SAML 1.1 biztonsági tokent jelöl az OASIS specifikációszerint.  Az `SAML2` érték egy SAML 2.0 biztonsági tokent jelöl az OASIS specifikációszerint. |
| OutputTokenFormat | 0:1 | A kimeneti jogkivonat formátuma. Lehetséges `JSON`értékek: `JWT` `SAML11`, `SAML2`, , vagy . |
| Kriptográfiai billentyűk | 0:1 | A technikai profilban használt titkosítási kulcsok listája. |
| InputClaimsTransformations | 0:1 | A jogcímátalakításokra korábban definiált hivatkozások listája, amelyeket a jogcímek szolgáltatójának vagy a függő entitásnak történő elküldéselőtt végre kell hajtani. |
| InputClaims (InputClaims) | 0:1 | A technikai profilban inputként figyelembe vett jogcímtípusokra mutató, korábban definiált hivatkozások listája. |
| Tartós jogcímek | 0:1 | A technikai profilhoz kapcsolódó jogcímszolgáltató által megőrzött jogcímtípusokra korábban definiált hivatkozások listája. |
| Megjelenítési jogcímek | 0:1 | A jogcímszolgáltató által az [önérvényesítő technikai profilhoz](self-asserted-technical-profile.md)kapcsolódó jogcímtípusokra korábban definiált hivatkozások listája . A DisplayClaims funkció jelenleg **előzetes verzióban**érhető el. |
| Kimeneti jogcímek | 0:1 | A technikai profilkimenetként felvett jogcímtípusokra mutató, korábban definiált hivatkozások listája. |
| OutputClaimsTransformations | 0:1 | A jogcímszolgáltatótól a jogcímek szolgáltatójától való beérkezést követően végrehajtandó jogcímátalakításokra mutató, korábban definiált hivatkozások listája. |
| ValidtechnicalProfiles | 0:n | A technikai profil által érvényesítési célokra használt egyéb műszaki profilokra mutató hivatkozások listája. További információ: [validation technical profile](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | A tulajdonosnevének tokenekben történő előállítását szabályozza, ahol a tulajdonos neve a jogcímektől elkülönítve van megadva. Például OAuth vagy SAML.  |
| IncludeInSso | 0:1 |  A technikai profil használatának kell-e alkalmaznia egyszeri bejelentkezési (SSO) viselkedést a munkamenetre, vagy inkább explicit interakciót igényel.Whether usage of this technical profile should apply single-on(SSO) behavior for the session, or instead require explicit interaction. Ez az elem csak az érvényesítési technikai profilban használt önérvényesítő profilokban érvényes. Lehetséges értékek: `true` (alapértelmezett) vagy `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Egy technikai profil azonosítója, amelyből az összes bemeneti és kimeneti jogcím hozzá adható ehhez a technikai profilhoz. A hivatkozott technikai profilt ugyanabban a házirendfájlban kell definiálni. |
| IncludeTechnicalProfile |0:1 | Egy technikai profil azonosítója, amelyből minden adatot hozzá szeretne adni ehhez a technikai profilhoz. |
| UseTechnicalProfileForSessionManagement | 0:1 | A munkamenet-kezeléshez használandó másik technikai profil. |
|EnabledForUserJourneys| 0:1 |Azt szabályozza, hogy a technikai profil végrehajtása felhasználói út során történik-e.  |

## <a name="protocol"></a>Protocol (Protokoll)

A **Protokoll** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Név | Igen | Az Azure AD B2C által támogatott, a technikai profil részeként használt érvényes protokoll neve. `OAuth1`Lehetséges értékek: `OAuth2` `SAML2`, `OpenIdConnect` `Proprietary`, `None`, , vagy . |
| Kezelő | Nem | Ha a protokoll neve `Proprietary`van beállítva, adja meg a teljesen minősített nevét a szerelvény, amely az Azure AD B2C a protokollkezelő meghatározásához használt. |

## <a name="metadata"></a>Metaadatok

A **metaadat-elem** a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0:n | A technikai profilhoz kapcsolódó metaadatok. A technikai profilok minden típusa különböző metaadat-elemekkel rendelkezik. További információt a technikai profiltípusok című részben talál. |

### <a name="item"></a>Elem

A **Metaadat** elem **Elem** eleme a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Kulcs | Igen | A metaadat-kulcs. A metaadatelemek listáját az egyes technikai profiltípusokban láthatja. |

## <a name="cryptographickeys"></a>Kriptográfiai billentyűk

A **Kriptográfiai kulcsok** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Kulcs | 1:n | A technikai profilban használt kriptográfiai kulcs. |

### <a name="key"></a>Kulcs

A **kulcselem** a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Nem | A házirendfájl más elemeiből származó adott kulcspár egyedi azonosítója. |
| StorageReferenceId azonosító | Igen | A házirendfájl más elemeiből hivatkozott tárolókulcs-tároló azonosítója. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

Az **InputClaimsTransformations** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | A jogcímátalakítás azonosítója, amelyet végre kell hajtani, mielőtt bármilyen jogcímszolgáltatónak vagy a függő entitásnak elküldenék a jogcímeket. A jogcímátalakítás meglévő ClaimsSchema jogcímek módosítására vagy újak létrehozására használható. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Az **InputClaimsTransformation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendfájlban vagy a szülőházirend-fájlban már definiált jogcímátalakítás azonosítója. |

## <a name="inputclaims"></a>InputClaims (InputClaims)

Az **InputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| InputClaim (Bemeneti jogcím) | 1:n | Egy várt bemeneti jogcímtípus. |

### <a name="inputclaim"></a>InputClaim (Bemeneti jogcím)

Az **InputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípushivatkozásazonosító | Igen | A házirendfájl vagy a szülőházirend-fájl ClaimsSchema szakaszában már definiált jogcímtípus azonosítója. |
| DefaultValue érték | Nem | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcím a technikai profil inputclaim-ként használható. |
| PartnerClaimType | Nem | Annak a külső partnernek az azonosítója, amelyhez a megadott házirend-jogcímtípus le képezi a jogcímtípusát. Ha a PartnerClaimType attribútum nincs megadva, akkor a megadott házirend-jogcímtípus az azonos nevű partnerjogcímtípushoz lesz rendelve. Akkor használja ezt a tulajdonságot, ha a jogcímtípus neve eltér a másik féltől. Az első jogcím neve például "givenName", míg a partner egy "first_name" nevű jogcímet használ. |

## <a name="displayclaims"></a>Megjelenítési jogcímek

A **DisplayClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Egy várt bemeneti jogcímtípus. |

A DislayClaims funkció jelenleg **előzetes verzióban**érhető el.

### <a name="displayclaim"></a>DisplayClaim

A **DisplayClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípushivatkozásazonosító | Nem | A házirendfájl vagy a szülőházirend-fájl ClaimsSchema szakaszában már definiált jogcímtípus azonosítója. |
| DisplayControlReferenceId azonosító | Nem | A házirendfájl vagy a szülőházirend-fájl ClaimsSchema szakaszában már definiált [megjelenítési vezérlő](display-controls.md) azonosítója. |
| Kötelező | Nem | Azt jelzi, hogy szükség van-e a megjelenítési jogcímre. |

A **DisplayClaim használatához** meg `ClaimTypeReferenceId` kell `DisplayControlReferenceId`adnia a vagy a .

### <a name="persistedclaims"></a>Tartós jogcímek

A **PersistedClaims** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | A megmaradni a jogcímtípusa. |

### <a name="persistedclaim"></a>PersistedClaim

A **PersistedClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípushivatkozásazonosító | Igen | A házirendfájl vagy a szülőházirend-fájl ClaimsSchema szakaszában már definiált jogcímtípus azonosítója. |
| DefaultValue érték | Nem | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcím a technikai profil inputclaim-ként használható. |
| PartnerClaimType | Nem | Annak a külső partnernek az azonosítója, amelyhez a megadott házirend-jogcímtípus le képezi a jogcímtípusát. Ha a PartnerClaimType attribútum nincs megadva, akkor a megadott házirend-jogcímtípus az azonos nevű partnerjogcímtípushoz lesz rendelve. Akkor használja ezt a tulajdonságot, ha a jogcímtípus neve eltér a másik féltől. Az első jogcím neve például "givenName", míg a partner egy "first_name" nevű jogcímet használ. |

## <a name="outputclaims"></a>Kimeneti jogcímek

A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaim (Kimeneti jogcím) | 1:n | Egy várt kimeneti jogcímtípus. |

### <a name="outputclaim"></a>OutputClaim (Kimeneti jogcím)

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípushivatkozásazonosító | Igen | A házirendfájl vagy a szülőházirend-fájl ClaimsSchema szakaszában már definiált jogcímtípus azonosítója. |
| DefaultValue érték | Nem | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcím a technikai profil inputclaim-ként használható. |
|MindigHasználja ADefaultValue értéket |Nem |Az alapértelmezett érték használatának kényszerítése.  |
| PartnerClaimType | Nem | Annak a külső partnernek az azonosítója, amelyhez a megadott házirend-jogcímtípus le képezi a jogcímtípusát. Ha a PartnerClaimType attribútum nincs megadva, akkor a megadott házirend-jogcímtípus az azonos nevű partnerjogcímtípushoz lesz rendelve. Akkor használja ezt a tulajdonságot, ha a jogcímtípus neve eltér a másik féltől. Az első jogcím neve például "givenName", míg a partner egy "first_name" nevű jogcímet használ. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

A **OutputClaimsTransformations** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | A jogcímátalakítások azonosítói, amelyeket a jogcímek elküldése előtt végre kell hajtani a jogcímszolgáltatónak vagy a függő entitásnak. A jogcímátalakítás meglévő ClaimsSchema jogcímek módosítására vagy újak létrehozására használható. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

A **OutputClaimsTransformation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendfájlban vagy a szülőházirend-fájlban már definiált jogcímátalakítás azonosítója. |

## <a name="validationtechnicalprofiles"></a>ValidtechnicalProfiles

A **ValidationTechnicalProfiles** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ValidtechnicalProfil | 1:n | A használt technikai profilok azonosítói ellenőrzik a hivatkozó technikai profil kimeneti jogcímeinek egy részét vagy egészét. A hivatkozott műszaki profil valamennyi bemeneti igényének szerepelnie kell a hivatkozó műszaki profil kimeneti jogcímében. |

### <a name="validationtechnicalprofile"></a>ValidtechnicalProfil

A **ValidationTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendfájlban vagy a szülőházirend-fájlban már definiált technikai profil azonosítója. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

A **SubjectNamingInfo** a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Jogcímtípus | Igen | A házirendfájl ClaimsSchema szakaszában már definiált jogcímtípus azonosítója. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Az **IncludeTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendfájlban vagy a szülőházirend-fájlban már definiált technikai profil azonosítója. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

A **UseTechnicalProfileForSessionManagement** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirendfájlban vagy a szülőházirend-fájlban már definiált technikai profil azonosítója. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

A **ClaimsProviderSelections** egy felhasználói út határozza meg a jogcímszolgáltató kiválasztási beállításainak listáját és azok sorrendjét. Az **EnabledForUserJourneys** összetevővel szűrheti, amely jogcímszolgáltató elérhető a felhasználó számára. Az **EnabledForUserJourneys** elem az alábbi értékek egyikét tartalmazza:

- **Mindig**, hajtsa végre a technikai profilt.
- **Soha,** hagyja ki a technikai profilt.
- **Az OnClaimsExistence** csak akkor hajtható végre, ha létezik egy bizonyos, a technikai profilban megadott jogcím.
- **OnItemExistenceInStringCollectionClaim**, csak akkor hajtsa végre, ha egy elem létezik egy karakterlánc-gyűjtemény jogcímében.
- **Az OnItemAbsenceInStringCollectionClaim** csak akkor hajtható végre, ha egy elem nem létezik karakterlánc-gyűjteményjogcímben.

Az **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** vagy **onItemAbsenceInStringCollectionClaim**használatával a következő metaadatokat kell megadnia: **A ClaimTypeOnWhichToEnable** a kiértékelendő jogcím típusát adja meg, a **ClaimValueOnWhichToEnable** az összehasonlítandó értéket adja meg.

A következő technikai profil csak akkor kerül végrehajtásra, `facebook.com`ha az **identityProviders** karakterlánc-gyűjtemény a következőket tartalmazza:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
