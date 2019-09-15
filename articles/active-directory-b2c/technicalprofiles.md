---
title: TechnicalProfiles | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének TechnicalProfiles elemének megadásához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e1192c8d0057d77306a1ffb06dd9bae12b7634ca
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998745"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **TechnicalProfiles** elem a jogcím-szolgáltató által támogatott technikai profilok készletét tartalmazza. Minden jogcím-szolgáltatónak rendelkeznie kell egy vagy több olyan technikai profillal, amely meghatározza a végpontokat és a jogcím-szolgáltatóval való kommunikációhoz szükséges protokollokat. A jogcím-szolgáltató több technikai profillal is rendelkezhet.

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

A **kivonatjogcím** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
|---------|---------|---------|
| Id | Igen | A technikai profil egyedi azonosítója. A technikai profil hivatkozhat erre az azonosítóra a házirend fájljának más elemeiről. Például: **OrchestrationSteps** és **ValidationTechnicalProfile**. |

A **kivonatjogcím** a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Domain | 0:1 | A technikai profil tartományneve. Ha például a technikai profil megadja a Facebook-identitás szolgáltatóját, a tartománynév Facebook.com. |
| DisplayName | 0:1 | A felhasználók számára megjeleníthető technikai profil neve. |
| Leírás | 0:1 | A felhasználók számára megjeleníthető technikai profil leírása. |
| Protocol | 0:1 | A másik féllel folytatott kommunikációhoz használt protokoll. |
| Metaadatok | 0:1 | Kulcs/érték párok gyűjteménye, amelyeket a protokoll használ a végponttal való kommunikációhoz a tranzakció során. |
| InputTokenFormat | 0:1 | A bemeneti jogkivonat formátuma. Lehetséges értékek: `JSON` `JWT` `SAML2`,,, vagy. `SAML11` Az `JWT` érték az IETF-specifikációnak megfelelő JSON web tokent jelöl. Az `SAML11` érték az SAML 1,1 biztonsági jogkivonatot jelöli, mint az Oasis-specifikáció.  Az `SAML2` érték az SAML 2,0 biztonsági jogkivonatot jelöli, mint az Oasis-specifikáció. |
| OutputTokenFormat | 0:1 | A kimeneti token formátuma. Lehetséges értékek: `JSON` `JWT` `SAML2`,,, vagy. `SAML11` |
| CryptographicKeys | 0:1 | A technikai profilban használt titkosítási kulcsok listája. |
| InputClaimsTransformations | 0:1 | A jogcímek átalakítására korábban meghatározott hivatkozások listája, amelyeket végre kell hajtani ahhoz, hogy a jogcímeket a jogcím-szolgáltató vagy a függő entitás megkapja. |
| Szabályzattípushoz | 0:1 | A korábban meghatározott, a technikai profilban bemenetként használt jogcím-típusokra mutató hivatkozások listája. |
| PersistedClaims | 0:1 | A korábban meghatározott, a jogcím-szolgáltató által a technikai profilhoz kapcsolódó jogcímek számára megőrzött hivatkozások listája. |
| OutputClaims | 0:1 | A korábban meghatározott, a technikai profilban kimenetként használt jogcím-típusokra mutató hivatkozások listája. |
| OutputClaimsTransformations | 0:1 | A korábban meghatározott, a jogcím-átalakításokra mutató hivatkozásokat tartalmazó lista, amelyet a jogcím-szolgáltatótól érkező jogcímek fogadása után kell végrehajtani. |
| ValidationTechnicalProfiles | 0: n | Azon egyéb műszaki profilokra mutató hivatkozások listája, amelyeket a technikai profil az ellenőrzési célokra használ. További információ: [érvényesítési technikai profil](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | A tulajdonos nevének olyan jogkivonatokban való előállítását szabályozza, amelyekben a tulajdonos neve külön van megadva a jogcímek között. Például: OAuth vagy SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Egy olyan műszaki profil azonosítója, amelyből hozzá kívánja adni az összes bemeneti és kimeneti jogcímet ehhez a technikai profilhoz. A hivatkozott technikai profilt ugyanabban a házirend-fájlban kell definiálni. |
| IncludeTechnicalProfile |0:1 | Egy műszaki profil azonosítója, amelyből a technikai profilba felvenni kívánt összes adat bekerül. A hivatkozott technikai profilnak ugyanabban a házirend-fájlban kell lennie. |
| UseTechnicalProfileForSessionManagement | 0:1 | A munkamenet-kezeléshez használt másik technikai profil. |
|EnabledForUserJourneys| 0:1 |Azt szabályozza, hogy a technikai profilt egy felhasználói úton hajtják végre.  |

### <a name="protocol"></a>Protocol

A **protokoll** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | A technikai profil részeként használt Azure AD B2C által támogatott érvényes protokoll neve. Lehetséges értékek: `OAuth1` `OAuth2` ,`OpenIdConnect` ,,`self-asserted`, ,`session management`,, vagy .`None` `SAML2` `Proprietary` |
| kezelő | Nem | Ha a protokoll neve értékre van `Proprietary`állítva, adja meg annak a szerelvénynek a teljesen minősített nevét, amelyet a Azure ad B2C használ a protokollkezelő meghatározásához. |

### <a name="metadata"></a>Metaadatok

A **metaadat** -elemek a következő elemeket tartalmazzák:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0: n | A technikai profilhoz kapcsolódó metaadatok. Az egyes technikai profilok különböző metaadat-elemekből állnak. További információt a technikai profilok típusai című szakaszban talál. |

#### <a name="item"></a>Elem

A **metaadatok** elem elem eleme a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Kulcs | Igen | A metaadat-kulcs. A metaadat-elemek listájának megtekintéséhez tekintse meg az egyes műszaki profilok típusait. |

### <a name="cryptographickeys"></a>CryptographicKeys

A **CryptographicKeys** elem a következő elemet tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Kulcs | 1: n | A technikai profilban használt titkosítási kulcs. |

#### <a name="key"></a>Kulcs

A **Key** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Nem | Egy adott kulcspár egyedi azonosítója, amelyet a rendszer a házirend fájljának más elemeiből hivatkozik. |
| StorageReferenceId | Igen | A termékazonosító más elemeitől hivatkozott tárolási kulcstároló-tárolók egyike. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

A **InputClaimsTransformations** elem a következő elemet tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Egy jogcím-átalakítás azonosítója, amelyet el kell végezni, mielőtt a rendszer a jogcímeket elküldi a jogcím-szolgáltatónak vagy a függő entitásnak. A jogcímek átalakításával módosíthatók a meglévő ClaimsSchema-jogcímek, vagy újak is létrehozhatók. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

A **InputClaimsTransformation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | Egy jogcímek átalakításának azonosítója, amely már definiálva van a házirend fájljában vagy a szülő házirend fájljában. |

### <a name="inputclaims"></a>Szabályzattípushoz

A **szabályzattípushoz** elem a következő elemet tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Egy várt bemeneti jogcím típusa. |

#### <a name="inputclaim"></a>InputClaim

A **InputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában. |
| defaultValue | Nem | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcímet a technikai profil InputClaim használhatja. |
| PartnerClaimType | Nem | Azon külső partner jogcím-típusának azonosítója, amelyhez a megadott házirend-jogcím típusa le van képezve. Ha a PartnerClaimType attribútum nincs megadva, a rendszer a megadott házirend-jogcím típusát az azonos nevű partner jogcím-típusra rendeli. Akkor használja ezt a tulajdonságot, ha a jogcím típusának neve eltér a másik féltől. Például az első jogcím neve "givenName", míg a partner egy "first_name" nevű jogcímet használ. |

### <a name="persistedclaims"></a>PersistedClaims

A **PersistedClaims** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | A megmaradó jogcím típusa. |

#### <a name="persistedclaim"></a>PersistedClaim

A **PersistedClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában. |
| defaultValue | Nem | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcímet a technikai profil InputClaim használhatja. |
| PartnerClaimType | Nem | Azon külső partner jogcím-típusának azonosítója, amelyhez a megadott házirend-jogcím típusa le van képezve. Ha a PartnerClaimType attribútum nincs megadva, a rendszer a megadott házirend-jogcím típusát az azonos nevű partner jogcím-típusra rendeli. Akkor használja ezt a tulajdonságot, ha a jogcím típusának neve eltér a másik féltől. Például az első jogcím neve "givenName", míg a partner egy "first_name" nevű jogcímet használ. |

### <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem a következő elemet tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| outputClaim | 1: n | A várt kimeneti jogcím típusa. |

#### <a name="outputclaim"></a>outputClaim

A **OutputClaim** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájl vagy a szülő házirend fájljában. |
| defaultValue | Nem | A jogcím létrehozásához használt alapértelmezett érték, ha a ClaimTypeReferenceId által jelzett jogcím nem létezik, így az eredményül kapott jogcímet a technikai profil InputClaim használhatja. |
|AlwaysUseDefaultValue |Nem |Az alapértelmezett érték használatának kényszerítése.  |
| PartnerClaimType | Nem | Azon külső partner jogcím-típusának azonosítója, amelyhez a megadott házirend-jogcím típusa le van képezve. Ha a PartnerClaimType attribútum nincs megadva, a rendszer a megadott házirend-jogcím típusát az azonos nevű partner jogcím-típusra rendeli. Akkor használja ezt a tulajdonságot, ha a jogcím típusának neve eltér a másik féltől. Például az első jogcím neve "givenName", míg a partner egy "first_name" nevű jogcímet használ. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

A **OutputClaimsTransformations** elem a következő elemet tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | A jogcímek átalakításának a jogcím-szolgáltatóba vagy a függő entitásba való elküldése előtt végrehajtandó azonosítók. A jogcímek átalakításával módosíthatók a meglévő ClaimsSchema-jogcímek, vagy újak is létrehozhatók. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

A **OutputClaimsTransformation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | Egy jogcímek átalakításának azonosítója, amely már definiálva van a házirend fájljában vagy a szülő házirend fájljában. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

A **ValidationTechnicalProfiles** elem a következő elemet tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | A felhasználható műszaki profilok azonosítói a hivatkozó technikai profil kimeneti jogcímeinek némelyikét vagy mindegyikét ellenőrzik. A hivatkozott technikai profil összes bemeneti jogcímének szerepelnie kell a hivatkozó technikai profil kimeneti jogcímeiben. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

A **ValidationTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirend-fájlban vagy a szülő-házirend fájlban már definiált technikai profil azonosítója. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

A **SubjectNamingInfo** a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ClaimType | Igen | Egy jogcím-típus azonosítója már definiálva van a ClaimsSchema szakaszban a házirend fájljában. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

A **IncludeTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirend-fájlban vagy a szülő-házirend fájlban már definiált technikai profil azonosítója. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

A **UseTechnicalProfileForSessionManagement** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirend-fájlban vagy a szülő-házirend fájlban már definiált technikai profil azonosítója. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
A felhasználói utazás **ClaimsProviderSelections** a jogcím-szolgáltató kiválasztási lehetőségeinek listáját és sorrendjét határozza meg. A szűrő **EnabledForUserJourneys** eleme alapján a felhasználó számára elérhető jogcím-szolgáltató. A **EnabledForUserJourneys** elem a következő értékek egyikét tartalmazza:

- **Mindig**hajtsa végre a technikai profilt.
- **Soha ne**ugorja át a technikai profilt.
- A **OnClaimsExistence** csak akkor hajtható végre, ha a technikai profilban megadott jogcím létezik.
- **OnItemExistenceInStringCollectionClaim**, csak akkor hajtható végre, ha egy elem létezik egy karakterlánc-gyűjtési jogcímben.
- A **OnItemAbsenceInStringCollectionClaim** csak akkor hajtható végre, ha egy objektum nem létezik karakterlánc-gyűjteményi jogcímben.

A **OnClaimsExistence**, a **OnItemExistenceInStringCollectionClaim** vagy a **OnItemAbsenceInStringCollectionClaim**használatával a következő metaadatokat kell megadnia: A **ClaimTypeOnWhichToEnable** meghatározza a kiértékelni kívánt jogcím típusát, a **ClaimValueOnWhichToEnable** pedig meghatározza az összehasonlítani kívánt értéket.

A következő technikai profilt csak akkor hajtja végre a rendszer, ha a **identityProviders** karakterlánc `facebook.com`-gyűjtemény a következők értékét tartalmazza:

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
