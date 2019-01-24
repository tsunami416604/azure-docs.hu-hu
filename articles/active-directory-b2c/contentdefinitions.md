---
title: ContentDefinitions – az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg az egyéni szabályzat ContentDefinitions elem Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1fa5688b5895ed7418161234f6af06fecace631c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849379"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Testre szabhatja a megjelenését és működését bármely [önálló kiszolgáló által megerősített technikai profil](self-asserted-technical-profile.md). Az Azure Active Directory (Azure AD) B2C futtatja a kódot az ügyfél böngészőben, és eltérő eredetű erőforrások megosztása (CORS) nevű modern megközelítését használja. 

A felhasználói felület testreszabása, adjon meg egy URL-cím a **ContentDefinition** elem az egyéni HTML-tartalmakat. Az önellenőrzött technikai profilban vagy **OrchestrationStep**, mutasson a megadott tartalomdefiníció-azonosítóval. A tartalomdefiníció tartalmazhat egy **LocalizedResourcesReferences** elem, amely felsorolja a honosított erőforrások betöltése. Az Azure AD B2C felhasználói felületi elemekre, amely be töltve az URL-címről, és ezután megjeleníti a felhasználónak az oldal HTML-tartalmakat a egyesíti.

A **ContentDefinitions** elem URL-címeket tartalmazza a HTML5-sablonok, a felhasználói út használható. A HTML5-alapú oldal URI-ja egy megadott felhasználói felület lépés használható. Ha például a bejelentkezési vagy regisztrációs, jelszó-visszaállítás, vagy hibalapok. A HTML5-fájl a LoadUri letiltásával módosíthatja a megjelenését és működését. Új tartalomdefiníciók igény szerint hozhat létre. Ez az elem tartalmazhat honosított erőforrások hivatkozást, a megadott honosítási azonosítóval a [honosítási](localization.md) elemet.

Az alábbi példa bemutatja a tartalomdefiníció azonosítóját, valamint a honosított erőforrások definíciója:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metaadatait a **LocalAccountSignUpWithLogonEmail** önálló kiszolgáló által megerősített technikai profil tartalmaz tartalomdefiníció azonosító **ContentDefinitionReferenceId** beállítása `api.localaccountsignup`

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


## <a name="contentdefinition"></a>ContentDefinition

A **ContentDefinition** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Tartalomdefiníció-azonosítója. Érték szerepel a **tartalom-definíció azonosítók** szakasz későbbi részében ezen a lapon. |

A **ContentDefinition** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | A tartalom definíciójában a HTML5-alapú lap URL-CÍMÉT tartalmazó karakterlánc. |
| RecoveryUri | 0:1 | A HTML-oldalt a tartalomdefiníció kapcsolatos hiba megjelenítése az URL-CÍMÉT tartalmazó karakterlánc. | 
| dataUri | 1:1 | A HTML-fájl, amely meghívni a felhasználói élményt biztosít a lépéshez relatív URL-CÍMÉT tartalmazó karakterlánc. |  
| Metaadatok | 1:1 | Kulcs/érték párok gyűjteménye, amely tartalmazza a tartalomdefiníció az egyes metaadatait. | 
| LocalizedResourcesReferences | 0:1 | Honosított erőforrások hivatkozások gyűjteménye. Ez az elem segítségével testre szabhatja a felhasználói felület és a jogcímek attribútum honosítása. |

### <a name="datauri"></a>dataUri

A **DataUri** elem segítségével adja meg a lap azonosítóját. Az Azure AD B2C betölteni, és a felhasználói felületi elemeket, és a kliensoldali JavaScript lap azonosítóját használja. Az érték formátuma `urn:com:microsoft:aad:b2c:elements:page-name:version`.  Az alábbi táblázat felsorolja a lap azonosítók is használhatja.

| Érték |   Leírás |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Megjeleníti a hibalap, amikor a kivétel vagy hibát észlelt. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Felsorolja az identitás-szolgáltatóktól, amelyeket a felhasználók a bejelentkezés során. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Bejelentkezik egy helyi fiók egy e-mail-cím vagy felhasználónév alapján egy űrlap látható. Ezt az értéket is tartalmaz. a "maradni bejelentkezési funkciójának" és "Elfelejtette a jelszavát?" hivatkozás. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Bejelentkezik egy helyi fiók egy e-mail-cím vagy felhasználónév alapján egy űrlap látható. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Telefonszámok ellenőrzi a szöveges vagy szóbeli regisztrálási vagy bejelentkezési során. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Megjeleníti egy űrlapot, amely lehetővé teszi a felhasználók létrehozásához vagy frissítéséhez igazodjon. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

A **LocalizedResourcesReferences** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Honosított erőforrás mutató hivatkozásokat a tartalomdefiníció listája. | 

A **LocalizedResourcesReferences** elem tartalmazza a következő attribútumokat:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| Nyelv | Igen | A szabályzat RFC 5646 - kiszolgálónként egy támogatott nyelvre tartalmazó karakterláncot címkék nyelvek azonosítása. |
| LocalizedResourcesReferenceId | Igen | Az azonosítója a **LocalizedResources** elemet. |

Az alábbi példa bemutatja egy regisztrálási vagy bejelentkezési tartalomdefiníció:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

Az alábbi példa bemutatja egy regisztrálási vagy bejelentkezési tartalomdefiníció angol, francia és spanyol honosításának hivatkozva:

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

Honosítási támogatás hozzáadása a tartalomdefiníciók kapcsolatban lásd: [honosítási](localization.md).

## <a name="content-definition-ids"></a>Tartalomdefiníció azonosítók

Az azonosító attribútuma a **ContentDefinition** elem határozza meg, amely kapcsolódik a tartalomdefiníció az oldal. Az elem definiálja a környezetet, amely a HTML5-alapú/CSS egyéni sablont fogja alkalmazni. A következő táblázat ismerteti a tartalomdefiníció azonosítók ismeri fel az identitás-kezelőfelületi keretrendszer és a hozzájuk kapcsolódó lap típusok készlete. Saját tartalom definíciókat hozhat létre egy tetszőleges.

| ID (Azonosító) | Alapértelmezett sablon | Leírás | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hibalap** – jeleníti meg a hiba lapon, ha kivétel, vagy hiba esetén. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitásszolgáltató kiválasztása oldal** – identitás-szolgáltatóktól, amelyeket a felhasználók a bejelentkezés során sorolja fel. A beállítások általában: vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy helyi fiókot. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési** – felsorolja az identitás-szolgáltatóktól, amelyeket a felhasználók a regisztráció során. A beállítások általában: vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy helyi fiókot. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszó oldal** -megjelenít egy űrlapot, felhasználók kezdeményezni a jelszó alaphelyzetbe állítása kell végrehajtani. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési oldal** – alapján egy e-mail-cím vagy felhasználónév helyi fiókkal bejelentkezik egy űrlap látható. Az űrlap egy bemeneti szövegmezőt és a jelszó mező tartalmazhat. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók regisztrálási oldala** – egy helyi fiókot, egy e-mail-cím vagy felhasználónév alapján egy űrlap látható. Az űrlap tartalmazhat különböző bemeneti vezérlők, például: egy szövegbeviteli mezőbe, a jelszó mező, választógomb, egyszeri kiválasztásos legördülő listák, és a többszörös kijelöléssel jelölje be. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **A multi-factor authentication-oldal** -telefonszámokat, ellenőrzi a szöveges vagy szóbeli, a regisztrálási vagy bejelentkezési során. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiók regisztrálására szolgáló oldal** -megjelenít egy űrlapot, hogy a felhasználók hajtsa végre, amikor regisztrál egy közösségi identitásszolgáltatót a meglévő fiók használatával. Ezen a lapon a hasonló az előző közösségi fiók regisztrációs oldala, a jelszó számbeviteli mezők kivételével. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Frissítés profillapján** -megjelenít egy űrlapot, hogy a felhasználók saját profil frissítése. Ez az oldal hasonlít a közösségi fiók regisztrációs oldala, a jelszó számbeviteli mezők kivételével. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrálási vagy bejelentkezési oldal** -kezeli a felhasználói regisztrációs és bejelentkezési folyamatot. Felhasználók a vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például Facebook vagy a Google + vagy helyi fiókot. |
 
