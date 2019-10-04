---
title: ContentDefinitions – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének ContentDefinitions elemének megadásához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f8acf499d4d82c49096e4e5beff8209d0970b421
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064341"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Testre szabhatja a [saját maga által vezérelt technikai profilok](self-asserted-technical-profile.md)megjelenését és működését. Azure Active Directory B2C (Azure AD B2C) kódot futtat az ügyfél böngészőjében, és egy modern megközelítést használ az eltérő eredetű erőforrás-megosztás (CORS) néven.

A felhasználói felület testreszabásához a testreszabott HTML-tartalommal rendelkező **ContentDefinition** elemben meg kell adnia egy URL-címet. Az önérvényesített technikai profilban vagy **OrchestrationStep**mutasson erre a tartalom-definíciós azonosítóra. A tartalom definíciója tartalmazhat egy **LocalizedResourcesReferences** elemet, amely meghatározza a betöltendő honosított erőforrások listáját. Azure AD B2C egyesíti a felhasználói felület elemeit az URL-címről betöltött HTML-tartalommal, majd megjeleníti a lapot a felhasználónak.

A **ContentDefinitions** elem a HTML5-sablonok URL-címeit tartalmazza, amelyeket a felhasználói utazás során használhat. A HTML5-lap URI-ja a megadott felhasználói felületi lépéshez használatos. Például a bejelentkezés vagy a regisztráció, a jelszó alaphelyzetbe állítása vagy a hibák lapja. A megjelenést és a működést úgy változtathatja meg, hogy felülbírálja a HTML5-fájl Tartalomdefinícióban. Az igényeknek megfelelően új tartalmi definíciókat hozhat létre. Ez az elem honosított erőforrás-hivatkozást tartalmazhat a [honosítási](localization.md) elemben megadott honosítási azonosítóra.

Az alábbi példa a Content definition azonosítóját és a honosított erőforrások definícióját mutatja be:

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

A **LocalAccountSignUpWithLogonEmail** önérvényesített technikai profil metaadatai a **ContentDefinitionReferenceId** beállított tartalom-definíciós azonosítót tartalmazzák`api.localaccountsignup`

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

A **ContentDefinition** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Igen | A tartalom definíciójának azonosítója. Az érték a **tartalom-definíciós azonosítók** szakaszban, a lap későbbi részében van megadva. |

A **ContentDefinition** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Egy karakterlánc, amely tartalmazza a tartalom definíciójának HTML5-oldalának URL-címét. |
| RecoveryUri | 0:1 | Egy karakterlánc, amely tartalmazza a tartalom-definícióval kapcsolatos hibát megjelenítő HTML-oldal URL-címét. |
| dataUri | 1:1 | Egy olyan karakterlánc, amely a lépéshez szükséges felhasználói élményt biztosító HTML-fájl relatív URL-címét tartalmazza. |
| Metaadatok | 1:1 | Kulcs/érték párok gyűjteménye, amely a tartalom definíciójában használt metaadatokat tartalmazza. |
| LocalizedResourcesReferences | 0:1 | Honosított erőforrások gyűjteménye. Ezzel az elemmel testreszabható a felhasználói felület és a jogcím attribútum honosítása. |

### <a name="datauri"></a>dataUri

A **DataUri** elem az oldal azonosítójának megadására szolgál. Azure AD B2C az oldal azonosítóját használja a felhasználói felületi elemek és az ügyféloldali JavaScript betöltéséhez és elindításához. Az érték `urn:com:microsoft:aad:b2c:elements:page-name:version`formátuma:.  A következő táblázat felsorolja a használható oldal-azonosítókat.

| Value |   Leírás |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Hibaüzenetet jelenít meg, ha kivétel vagy hiba történt. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Felsorolja azokat az identitás-szolgáltatókat, amelyeket a felhasználók a bejelentkezés során választhatnak. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Megjeleníti a helyi fiókkal való bejelentkezéshez használt űrlapot, amely egy e-mail-cím vagy egy Felhasználónév alapján jelenik meg. Ez az érték a "bejelentkezési funkciók megtartása" funkciót és az "elfelejtette jelszavát?" is tartalmazza hivatkozás. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Megjeleníti a helyi fiókkal való bejelentkezéshez használt űrlapot, amely egy e-mail-cím vagy egy Felhasználónév alapján jelenik meg. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Megerősíti a telefonszámokat szöveg vagy hang használatával a regisztráció vagy a bejelentkezés során. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Megjelenít egy űrlapot, amely lehetővé teszi a felhasználók számára a profil létrehozását vagy frissítését. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

A **LocalizedResourcesReferences** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | A tartalom definíciójának honosított erőforrás-hivatkozásainak listája. |

A **LocalizedResourcesReferences** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Nyelv | Igen | Egy karakterlánc, amely az RFC 5646-címkék által támogatott nyelvet tartalmaz a nyelvek azonosításához. |
| LocalizedResourcesReferenceId | Igen | A **LocalizedResources** elem azonosítója. |

A következő példa egy regisztrációs vagy bejelentkezési tartalom definícióját mutatja be:

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

Az alábbi példa egy regisztrációs vagy bejelentkezési tartalom definícióját mutatja be az angol, francia és spanyol nyelv honosítására való hivatkozással:

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

Ha szeretné megtudni, hogyan adhat honosított támogatást a tartalmi definíciók számára, tekintse meg a [honosítás](localization.md)című témakört.

## <a name="content-definition-ids"></a>Tartalom-definíciós azonosítók

A **ContentDefinition** elem ID attribútuma határozza meg a tartalom-definícióhoz kapcsolódó oldal típusát. Az elem határozza meg azt a környezetet, amelyet az egyéni HTML5/CSS-sablon alkalmazni fog. A következő táblázat ismerteti az identitási élmény keretrendszere által felismert tartalmi definíciós azonosítókat, valamint az azokhoz kapcsolódó lapokat. Saját tartalmi definíciókat tetszőleges AZONOSÍTÓval hozhat létre.

| id | Alapértelmezett sablon | Leírás |
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hiba lap** – hibaüzenetet jelenít meg, ha kivétel vagy hiba történt. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitás-szolgáltató kiválasztása lap** – felsorolja azokat az azonosítókat, amelyeket a felhasználók a bejelentkezés során választhatnak. A lehetőségek általában a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Az Identitáskezelő kiválasztása a regisztrációhoz** – felsorolja azokat az identitás-szolgáltatókat, amelyeket a felhasználók a regisztráció során választhatnak. A lehetőségek általában a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszó lap** – megjelenít egy űrlapot, amelyet a felhasználóknak be kell fejezniük a jelszó-visszaállítás elindításához. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési oldala** – megjelenít egy űrlapot, amely egy olyan helyi fiókkal jelentkezik be, amely e-mail-cím vagy Felhasználónév alapján jelenik meg. Az űrlap tartalmazhatja a szövegbeviteli és a jelszó-beviteli mezőt is. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók regisztrálása lap** – megjelenít egy űrlapot, amely az e-mail-cím vagy Felhasználónév alapján létrehozott helyi fiókra regisztrál. Az űrlap különböző beviteli vezérlőket tartalmazhat, például a következőket: szövegbeviteli mező, jelszó-beviteli mező, választógomb, egyszeres kijelölés legördülő lista és többszörös kijelölés jelölőnégyzet. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication lap** – a telefonszámok vagy a hangfelvételek, valamint a bejelentkezés vagy a bejelentkezés során ellenőrzi a telefonszámokat. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiók regisztrálása lap** – megjeleníti azt az űrlapot, amelyet a felhasználóknak be kell fejezniük, amikor regisztrálnak egy meglévő fiókkal egy közösségi identitás-szolgáltatótól. Ez az oldal hasonló az előző közösségi fiók regisztráció lapjához, a jelszó-beviteli mezők kivételével. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil frissítése lap** – megjelenít egy űrlapot, amelyet a felhasználók a profiljuk frissítéséhez tudnak elérni. Ez az oldal hasonló a közösségi fiók regisztrálása oldalhoz, a jelszó-beviteli mezők kivételével. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési oldal** – kezeli a felhasználói regisztrációt és a bejelentkezési folyamatot. A felhasználók a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy a helyi fiókok használatával használhatják a felhasználókat. |

