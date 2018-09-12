---
title: TechnicalProfiles |} A Microsoft Docs
description: Adja meg a TechnicalProfiles elem egyéni szabályzat az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f1242b299c6d2278bd75b576f225987854a2d8a5
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381569"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **TechnicalProfiles** elem tartalmazza a jogcím-szolgáltató támogatja a technikai profilok. Minden jogcím-szolgáltatói rendelkeznie kell egy vagy több technikai profilok –, amelyek meghatározzák a végpontok és a jogcímszolgáltató folytatott kommunikációhoz szükséges protokollok. Jogcím-szolgáltatóktól rendelkezhet több technikai profil.

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

A **TechnicalProfile** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
|---------|---------|---------|
| Azonosító | Igen | A technikai profil egyedi azonosítója. A technikai profil az házirend fájl ezt az azonosítót az egyéb elemek használatával lehet hivatkozni. Ha például **OrchestrationSteps** és **ValidationTechnicalProfile**. |

A **TechnicalProfile** a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Domain | 0:1 | A tartomány neve, a technikai profil. Ha például a technikai profil határozza meg, a Facebook-identitásszolgáltató, ha a tartománynév Facebook.com weboldalt. |
| Megjelenítendő név | 0:1 | A technikai profil, a felhasználók számára megjelenített neve. |
| Leírás | 0:1 | A felhasználók számára megjelenő technikai profil leírása. |
| Protokoll | 0:1 | A másik fél szolgáltatással való kommunikációhoz használt protokoll. |
| Metaadatok | 0:1 | A protokoll egy tranzakció során a végponttal való kommunikációhoz használ kulcs/érték párok gyűjteménye. |
| InputTokenFormat | 0:1 | A bemeneti jogkivonat formátuma. A lehetséges értékek: `JSON`, `JWT`, `SAML11`, vagy `SAML2`. A `JWT` érték jelzi a JSON Web Token számú IETF-specifikáció alapján. A `SAML11` érték jelzi a SAML 1.1-es biztonsági jogkivonat OASIS specifikációnak megfelelően.  A `SAML2` érték jelzi a SAML 2.0 biztonsági jogkivonat OASIS specifikációnak megfelelően. |
| OutputTokenFormat | 0:1 | A kimeneti jogkivonat formátuma. A lehetséges értékek: `JSON`, `JWT`, `SAML11`, vagy `SAML2`. |
| CryptographicKeys | 0:1 | Az a technikai profil által használt kriptográfiai kulcsok listája. |
| InputClaimsTransformations | 0:1 | Egy korábban meghatározott hivatkozik, amely előtt a jogcímeket a jogcím-szolgáltató vagy a függő entitás küldött végrehajtja a jogcímek átalakítása listája. |
| InputClaims | 0:1 | A technikai profilban bemeneti jogcím-típusokat, amelyeket a rendszer, hogy a korábban meghatározott hivatkozások listája. |
| PersistedClaims | 0:1 | A korábban meghatározott hivatkozásokat megmaradnak a jogcímeket szolgáltató, amely kapcsolódik a technikai profil jogcímtípusok listáját. |
| OutputClaims | 0:1 | A korábban meghatározott hivatkozásokat, amelyeket a rendszer a technikai profilban kimenetként jogcímtípusok listáját. |
| OutputClaimsTransformations | 0:1 | Egy korábban meghatározott hivatkozik, amely után a jogcímeket a jogcímszolgáltatótól érkeznek végrehajtja a jogcímek átalakítása listája. |
| ValidationTechnicalProfiles | 0: n | Egyéb technikai profilok, amely a technikai profil használja, ellenőrzési célból mutató hivatkozások listája. További információkért lásd: [ellenőrzési technikai profil](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Az éles környezetben a jogkivonatokban, amelyben a tulajdonos neve van megadva külön-külön jogcímek a tulajdonos neve vezérli. Például OAuth vagy SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | A technikai profil, ahonnan minden, a bemeneti és kimeneti jogcímek adni a technikai profil azonosítója. A hivatkozott technikai profil házirend ugyanebben a fájlban definiálni kell. | 
| IncludeTechnicalProfile |0:1 | Egy, ahonnan a technikai profil hozzáadni kívánt összes adat technikai profil azonosítója. A hivatkozott technikai profil léteznie kell a ugyanazon házirend-fájlt. |
| UseTechnicalProfileForSessionManagement | 0:1 | Munkamenet-kezelés használható különböző technikai profil. |
|EnabledForUserJourneys| 0:1 |Ha a technikai profil a felhasználói út szabályozza.  |

### <a name="protocol"></a>Protokoll

A **protokoll** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | A technikai profil részeként használt Azure AD B2C által támogatott érvényes protokoll neve. A lehetséges értékek: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted`, vagy `None`. |
| Kezelő | Nem | Ha a protokoll nevét beállítása `Proprietary`, adja meg a szerelvény meghatározásához a protokoll-leíró Azure AD B2C által használt teljes nevét. |

### <a name="metadata"></a>Metaadatok

A **metaadatok** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0: n | A metaadatok, hogy a technikai profil vonatkozik. Minden egyes technikai profil típusát különböző metaadatelemek rendelkezik. Tekintse meg a technikai profil típusok további információt. |

#### <a name="item"></a>Elem

A **elem** eleme a **metaadatok** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Kulcs | Igen | A metaadat-kulcsot. Tekintse meg az egyes technikai Profiltípusok metaadat-elemek listáját. |

### <a name="cryptographickeys"></a>CryptographicKeys

A **CryptographicKeys** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Kulcs | 1: n | A technikai profil használt titkosítási kulcs. |

#### <a name="key"></a>Kulcs

A **kulcs** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Nem | Egy adott kulcspárt rájuk hivatkozni más elemek, a házirend-fájl egyedi azonosítója. |
| StorageReferenceId | Igen | Az egyéb elemek a szabályzat fájlban hivatkozott kulcs tárolót azonosítójának. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

A **InputClaimsTransformations** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | A jogcímek átalakításáról jogcímeket a jogcím-szolgáltató vagy a függő entitás elküldése előtt végrehajtandó azonosítóját. A jogcímek átalakításáról segítségével módosíthatja a meglévő ClaimsSchema jogcímeket, vagy hozzon létre újakat. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

A **InputClaimsTransformation** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| a referenceid megadása | Igen | A jogcímek átalakítását, a házirend vagy szülő házirend fájlt már definiált azonosítója. |

### <a name="inputclaims"></a>InputClaims

A **InputClaims** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Bemeneti jogcím | 1: n | Egy várt bemeneti jogcím típusa. |

#### <a name="inputclaim"></a>Bemeneti jogcím 

A **bemeneti jogcím** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | A szabályzat vagy a szülő házirend fájl ClaimsSchema szakasz már definiálva egy jogcímtípust azonosítóját. |
| DefaultValue érték | Nem | Hozzon létre egy jogcímet, ha a jogcímek ClaimTypeReferenceId által jelzett használandó alapértelmezett értéket, hogy az eredményül kapott a jogcímet, egy bemeneti jogcím használhatják a technikai profil nem létezik. |
| PartnerClaimType | Nem | A külső partnerek, hogy a megadott házirend jogcím típusa a jogcím típusa azonosítóját képez le. Ha a PartnerClaimType attribútum nincs megadva, a megadott jogcím típusának a partner jogcímtípus az azonos nevű lesz leképezve. Akkor használja ezt a tulajdonságot, ha a jogcím-típus neve eltér a másik fél. Például első jogcím név "givenName", míg a partner használja a "first_name" nevű jogcím. |

### <a name="persistedclaims"></a>PersistedClaims

A **PersistedClaims** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | A jogcímtípus is tartalmaz. |

#### <a name="persistedclaim"></a>PersistedClaim 

A **PersistedClaim** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | A szabályzat vagy a szülő házirend fájl ClaimsSchema szakasz már definiálva egy jogcímtípust azonosítóját. |
| DefaultValue érték | Nem | Hozzon létre egy jogcímet, ha a jogcímek ClaimTypeReferenceId által jelzett használandó alapértelmezett értéket, hogy az eredményül kapott a jogcímet, egy bemeneti jogcím használhatják a technikai profil nem létezik. |
| PartnerClaimType | Nem | A külső partnerek, hogy a megadott házirend jogcím típusa a jogcím típusa azonosítóját képez le. Ha a PartnerClaimType attribútum nincs megadva, a megadott jogcím típusának a partner jogcímtípus az azonos nevű lesz leképezve. Akkor használja ezt a tulajdonságot, ha a jogcím-típus neve eltér a másik fél. Például első jogcím név "givenName", míg a partner használja a "first_name" nevű jogcím. |

### <a name="outputclaims"></a>OutputClaims

A **OutputClaims** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| kimeneti jogcím | 1: n | Várt kimenet jogcím típusa. |

#### <a name="outputclaim"></a>kimeneti jogcím 

A **kimeneti jogcím** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Igen | A szabályzat vagy a szülő házirend fájl ClaimsSchema szakasz már definiálva egy jogcímtípust azonosítóját. |
| DefaultValue érték | Nem | Hozzon létre egy jogcímet, ha a jogcímek ClaimTypeReferenceId által jelzett használandó alapértelmezett értéket, hogy az eredményül kapott a jogcímet, egy bemeneti jogcím használhatják a technikai profil nem létezik. |
|AlwaysUseDefaultValue |Nem |Az alapértelmezett érték használatának kényszerítéséhez.  |
| PartnerClaimType | Nem | A külső partnerek, hogy a megadott házirend jogcím típusa a jogcím típusa azonosítóját képez le. Ha a PartnerClaimType attribútum nincs megadva, a megadott jogcím típusának a partner jogcímtípus az azonos nevű lesz leképezve. Akkor használja ezt a tulajdonságot, ha a jogcím-típus neve eltér a másik fél. Például első jogcím név "givenName", míg a partner használja a "first_name" nevű jogcím. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

A **OutputClaimsTransformations** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | A jogcímek átalakítása jogcímeket a jogcím-szolgáltató vagy a függő entitás elküldése előtt végrehajtandó azonosítói. A jogcímek átalakításáról segítségével módosíthatja a meglévő ClaimsSchema jogcímeket, vagy hozzon létre újakat. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

A **OutputClaimsTransformation** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| a referenceid megadása | Igen | A jogcímek átalakítását, a házirend vagy szülő házirend fájlt már definiált azonosítója. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

A **ValidationTechnicalProfiles** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | A használható technikai profilok-azonosítók vagy azok egy részét a kimeneti jogcímek a hivatkozó technikai profil ellenőrzése. A bemeneti jogcímek között, a hivatkozott technikai profil összes szerepelnie kell a kimeneti jogcímek a hivatkozó technikai profil. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

A **ValidationTechnicalProfile** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| a referenceid megadása | Igen | A házirend vagy szülő házirend fájlt már definiált egy technikai profil azonosítója. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

A **SubjectNamingInfo** tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Takar | Igen | Egy jogcímtípust az ClaimsSchema szakaszban a szabályzat fájlban már megadott azonosítója. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

A **IncludeTechnicalProfile** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| a referenceid megadása | Igen | A házirend vagy szülő házirend fájlt már definiált egy technikai profil azonosítója. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

A **UseTechnicalProfileForSessionManagement** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| a referenceid megadása | Igen | A házirend vagy szülő házirend fájlt már definiált egy technikai profil azonosítója. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
A **ClaimsProviderSelections** egy felhasználói interakciósorozat határozza meg a jogcímeket szolgáltató tanúsítványválasztási beállítások és a sorrendjük listáját. Az a **EnabledForUserJourneys** elem szűrése, mely a jogcím-szolgáltatói a felhasználó számára elérhető. A **EnabledForUserJourneys** elem a következő értékek egyikét tartalmazza:

- **Mindig**, hajtsa végre a technikai profil.
- **Soha ne**, hagyja ki a technikai profil. 
- **OnClaimsExistence** hajtható végre, csak akkor, ha egy bizonyos jogcím, a technikai profilban megadott létezik. 
- **OnItemExistenceInStringCollectionClaim**, hajtsa végre, csak ha az elem már létezik egy karakterlánc-gyűjtemény jogcímet. 
- **OnItemAbsenceInStringCollectionClaim** hajtható végre, csak ha egy elem nem létezik a gyűjtemény jogcím karakterlánc.

Használatával **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** vagy **OnItemAbsenceInStringCollectionClaim**, meg kell adnia a következő metaadatok: **ClaimTypeOnWhichToEnable** Megadja, hogy ki kell értékelni a jogcím típusa **ClaimValueOnWhichToEnable** adja meg az összehasonlítandó értéket.

A következő technikai profil hajtja végre, ha csak a **identityProviders** karakterlánc gyűjteményben értékét `facebook.com`:

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












