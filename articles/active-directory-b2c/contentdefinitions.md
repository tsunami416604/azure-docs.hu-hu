---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Adja meg az egyéni szabályzat ContentDefinitions elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074a0a39090e22a29f778fc1c99060848c6bfd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051496"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Testreszabhatja a megjelenését és hangulatát minden [önálló állította technikai profil](self-asserted-technical-profile.md). Az Azure Active Directory B2C (Azure AD B2C) az ügyfél böngészőjében futtatja a kódot, és egy modern megközelítést, az úgynevezett cross-origin erőforrás-megosztást (CORS) használ.

A felhasználói felület testreszabásához adjon meg egy URL-címet a **ContentDefinition** elemben, testreszabott HTML-tartalommal. Az önérvényesítő technikai profilban vagy az **OrchestrationStep**alkalmazásban az adott tartalomdefiníció-azonosítóra mutat. A tartalomdefiníció tartalmazhat egy **LocalizedResourcesReferences** elemet, amely megadja a betöltandó honosított erőforrások listáját. Az Azure AD B2C egyesíti a felhasználói felület elemeit az URL-címről betöltött HTML-tartalommal, majd megjeleníti a lapot a felhasználó számára.

A **ContentDefinitions** elem olyan HTML5-sablonok URL-címeit tartalmazza, amelyek használhatók a felhasználói út során. A HTML5-oldal URI-ja a felhasználói felület megadott lépéséhez használatos. Például a bejelentkezési vagy feliratkozási, jelszó-visszaállítási vagy hibaoldalak. A HTML5-fájl LoadUri-jának felülbírálásával módosíthatja a megjelenést. Az igényeinek megfelelően új tartalomdefiníciókat hozhat létre. Ez az elem tartalmazhat egy honosított erőforrás-hivatkozást a [Honosítási](localization.md) elemben megadott honosítási azonosítóra.

A következő példa a tartalomdefiníció-azonosítót és a honosított erőforrások definícióját mutatja be:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

A **LocalAccountSignUpWithLogonEmail** saját érvényesítésű technikai profil metaadatai a **ContentDefinitionReferenceId** tartalomdefiníciós azonosítót tartalmazzák,`api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>Tartalomdefiníció

A **ContentDefinition** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Tartalomdefiníció azonosítója. Az érték a lap **későbbi, tartalomdefiníciós azonosítók** szakaszában megadott érték. |

A **ContentDefinition** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| LoadUri (LoadUri) | 1:1 | A tartalomdefiníció HTML5 lapjának URL-címét tartalmazó karakterlánc. |
| RecoveryUri | 1:1 | A HTML-lap URL-címét tartalmazó karakterlánc a tartalomdefinícióval kapcsolatos hiba megjelenítéséhez. Jelenleg nincs használatban, `~/common/default_page_error.html`az értéknek kell lennie. |
| DataUri között | 1:1 | Olyan karakterlánc, amely egy HTML-fájl relatív URL-címét tartalmazza, amely a lépéshez való felhasználói élményt biztosítja. |
| Metaadatok | 0:1 | Kulcs-/értékpárok gyűjteménye, amely a tartalomdefiníció által használt metaadatokat tartalmazza. |
| Honosított erőforrásokhivatkozásai | 0:1 | Honosított erőforrások hivatkozásainak gyűjteménye. Ezzel az elemmel testreszabhatja a felhasználói felület és a jogcímattribútum honosítását. |

### <a name="datauri"></a>DataUri között

A **DataUri-elem** az oldalazonosító megadására szolgál. Az Azure AD B2C az oldalazonosítót használja a felhasználói felület elemeinek és az ügyféloldali JavaScript betöltésének és elindításának. Az `urn:com:microsoft:aad:b2c:elements:page-name:version`érték formátuma . Az alábbi táblázat a használható oldalazonosítókat sorolja fel.

| Oldalazonosító | Leírás |
| ----- | ----------- |
| `globalexception` | Egy hibalapot jelenít meg, ha kivétel vagy hiba lép fel. |
| `providerselection`, `idpselection` | Felsorolja az identitásszolgáltatók, hogy a felhasználók közül választhatnak a bejelentkezés során.  |
| `unifiedssp` | Egy e-mail-címen vagy felhasználónéven alapuló helyi fiókkal történő bejelentkezéshez szolgáló űrlapot jelenít meg. Ez az érték a "bejelentkezési funkció megtartása" és az "Elfelejtette a jelszavát?" funkciót is tartalmazza. Link. |
| `unifiedssd` | Egy e-mail-címen vagy felhasználónéven alapuló helyi fiókkal történő bejelentkezéshez szolgáló űrlapot jelenít meg. |
| `multifactor` | A telefonszámok ellenőrzése szöveges vagy hangalapú módon a regisztráció vagy a bejelentkezés során. |
| `selfasserted` | Egy felhasználótól adatokat gyűjtő űrlap megjelenítése. Lehetővé teszi például a felhasználók számára a profil létrehozásához vagy frissítéséhez. |

### <a name="select-a-page-layout"></a>Oldalelrendezés kijelölése

A [JavaScript ügyféloldali kódot](javascript-samples.md) az `contract` oldaltípus közötti `elements` beszúrással engedélyezheti. Például: `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A [verzió](page-layout.md) része `DataUri` határozza meg a html, CSS és JavaScript a felhasználói felület elemeit a házirendben. Ha engedélyezni kívánja a JavaScript ügyféloldali kódját, a JavaScript-alapú elemeknek nem módosíthatóknak kell lenniük. Ha nem módosíthatók, a módosítások nem várt viselkedést okozhatnak a felhasználói oldalakon. A problémák elkerülése érdekében kényszerítse ki a lapelrendezés használatát, és adja meg a lapelrendezés-verziót. Ezzel biztosíthatja, hogy a JavaScriptet a JavaScriptet összesen nem módosítható tartalomdefiníciók nem módosíthatók. Még ha nem is kívánja engedélyezni a JavaScriptet, akkor is meg kell adnia az oldalak oldalelrendezési verzióját.

A következő példa a `selfasserted` verzió `1.2.0` **DataUri-ját** mutatja be:

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Áttelepítés lapelrendezésbe

Az érték formátumának tartalmaznia `contract`kell a következő szót: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. Ha egy régi **DataUri-értéket** használó egyéni házirendben szeretne lapelrendezést megadni, az alábbi táblázat segítségével térjen át az új formátumra.

| Régi DataUri-érték | Új DataUri-érték |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>Metaadatok

A **metaadat-elem** a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Elem | 0:n | A tartalomdefinícióhoz kapcsolódó metaadatok. |

A **Metaadat** elem **Elem** eleme a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Kulcs | Igen | A metaadat-kulcs.  |

#### <a name="metadata-keys"></a>Metaadat-kulcsok

A tartalomdefiníció a következő metaadatelemeket támogatja:

| Kulcs | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DisplayName | Nem | A tartalomdefiníció nevét tartalmazó karakterlánc. |

### <a name="localizedresourcesreferences"></a>Honosított erőforrásokhivatkozásai

A **LocalizedResourcesReferences** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Honosított erőforrásokhivatkozás | 1:n | A tartalomdefiníció honosított erőforrás-hivatkozásainak listája. |

A **LocalizedResourcesReference** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Nyelv | Igen | Olyan karakterlánc, amely az RFC 5646 - Nyelvi azonosító címkék házirendjének támogatott nyelvét tartalmazza. |
| Honosított erőforrásokhivatkozás-azonosító | Igen | A **Honosított erőforrások elem azonosítója.** |

A következő példa egy regisztrációs vagy bejelentkezési tartalomdefiníciót mutat be, amely az angol, a francia és a spanyol honosításra hivatkozik:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Ha tudni szeretné, hogyan vehet fel honosítási támogatást a tartalomdefiníciókhoz, olvassa el a [Honosítás című témakört.](localization.md)

## <a name="content-definition-ids"></a>Tartalomdefiníció-azonosítók

A **ContentDefinition** elem azonosító attribútuma a tartalomdefinícióhoz kapcsolódó lap típusát határozza meg. Az elem határozza meg azt a környezetet, amelyet egy egyéni HTML5/CSS sablon alkalmazni fog. Az alábbi táblázat az identitáskezelési keretrendszer által felismert tartalomdefiníciós azonosítók készletét és a hozzájuk kapcsolódó oldaltípusokat ismerteti. Tetszőleges azonosítóval hozhat létre saját tartalomdefiníciókat.

| ID (Azonosító) | Alapértelmezett sablon | Leírás |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hibalap** – Hibalapot jelenít meg, ha kivétel vagy hiba lép fel. |
| **api.idpsválasztások** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitásszolgáltató kiválasztása lap** – Felsorolja az identitásszolgáltatók, amelyek a felhasználók közül választhatnak a bejelentkezés során. A lehetőségek általában vállalati identitásszolgáltatók, közösségi identitásszolgáltatók, például a Facebook és a Google+, vagy a helyi fiókok. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitásszolgáltató kiválasztása a regisztrációhoz** – Felsorolja azokat az identitásszolgáltatókat, amelyek közül a felhasználók a regisztráció során választhatnak. A lehetőségek általában vállalati identitásszolgáltatók, közösségi identitásszolgáltatók, például a Facebook és a Google+, vagy a helyi fiókok. |
| **api.localaccountpasswordreset** | [önérvényesítő.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszólap** – Olyan űrlapot jelenít meg, amelyet a felhasználóknak ki kell tölteniük a jelszó-visszaállítás kezdeményezéséhez. |
| **api.localaccountsignin** | [önérvényesítő.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiókbejelentkezési lap** – Egy e-mail-cím vagy felhasználónév alapján helyi fiókkal történő bejelentkezéshez szolgáló űrlapot jelenít meg. Az űrlap szövegbeviteli mezőt és jelszóbeviteli mezőt tartalmazhat. |
| **api.localaccountsignup** | [önérvényesítő.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiókregisztrációs lap** – Egy e-mail-cím vagy felhasználónév alapján létrehozott helyi fiók regisztrálási űrlapját jeleníti meg. Az űrlap különböző beviteli vezérlőket tartalmazhat, például szövegbeviteli mezőt, jelszóbeviteli mezőt, választógombot, egyszeres legördülő mezőket és többszörös jelölőnégyzeteket. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Többtényezős hitelesítési oldal** – A telefonszámok ellenőrzése szöveg vagy hang használatával, a regisztráció vagy a bejelentkezés során. |
| **api.selfasserted** | [önérvényesítő.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiókra való feliratkozási lap** – Olyan űrlapot jelenít meg, amelyet a felhasználóknak ki kell tölteniük, amikor egy közösségi identitásszolgáltató meglévő fiókjával regisztrálnak. Ez az oldal hasonló az előző közösségi fiók regisztrációs lapjához, kivéve a jelszóbeviteli mezőket. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profilfrissítési lap** – Olyan űrlapot jelenít meg, amelyhez a felhasználók hozzáférhetnek a profilfrissítéshez. Ez az oldal hasonló a közösségi fiók regisztrációs lapjához, kivéve a jelszóbeviteli mezőket. |
| **api.signuporsignin** | [egyesített.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési lap** – Kezeli a felhasználói regisztrációs és bejelentkezési folyamatot. A felhasználók használhatnak vállalati identitásszolgáltatókat, közösségi identitásszolgáltatókat, például a Facebookot vagy a Google+-t, vagy helyi fiókokat. |

## <a name="next-steps"></a>További lépések

A felhasználói felület tartalomdefiníciók használatával történő testreszabásának például a következő témakörökben található:

[Az alkalmazás felhasználói felületének testreszabása egyéni házirend használatával](custom-policy-ui-customization.md)
