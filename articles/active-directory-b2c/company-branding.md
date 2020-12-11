---
title: Arculat hozzáadása a szervezet bejelentkezési oldalához
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhatja hozzá a szervezet arculatát a Azure Active Directory B2C lapokhoz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111302"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Arculat hozzáadása a szervezet Azure Active Directory B2C oldalaihoz

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure AD B2C lapokat a szalagcím emblémával, a háttérképgel és a háttérszínsel testreszabhatja Azure Active Directory [vállalati védjegyezés](../active-directory/fundamentals/customize-branding.md)használatával. A vállalat arculata magában foglalja a regisztrációt, a bejelentkezést, a profil szerkesztését és a jelszó-visszaállítást. 

> [!TIP]
> Ha testre szeretné szabni a felhasználói folyamatok oldalain a szalagcím emblémáján, a háttérképen vagy a háttérszínen kívül más szempontokat is, tekintse meg [a felhasználói felület testreszabása HTML-sablonnal](customize-ui-with-html.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


A felhasználói folyamatok lapjain való testreszabáshoz először a vállalati arculatot kell konfigurálnia Azure Active Directoryban, majd a felhasználói folyamatokban lévő lapok elrendezésében Azure AD B2C.

## <a name="configure-company-branding"></a>Vállalati arculat konfigurálása

Először állítsa be a szalagcím emblémáját, a háttérképet és a háttérszínt a **vállalati arculaton** belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **kezelés** területen válassza a **vállalati védjegyezés** lehetőséget.
1. Kövesse a [branding hozzáadása a szervezet Azure Active Directory bejelentkezési oldalához](../active-directory/fundamentals/customize-branding.md)című témakör lépéseit.

Tartsa szem előtt ezeket a dolgokat, amikor a vállalati arculatot konfigurálja Azure AD B2Cban:

* A Azure AD B2C vállalati arculata jelenleg a **háttérképre**, a **szalagcím emblémára** és a **háttérszínek** testreszabására korlátozódik. A vállalati arculati panel egyéb tulajdonságai, például a **speciális beállításokban** szereplő egyéb tulajdonságok *nem támogatottak*.
* A felhasználói folyamatok oldalain a háttér színe látható a háttérkép betöltése előtt. Javasoljuk, hogy olyan háttérszínt válasszon, amely szorosan illeszkedik a háttérkép színeihez a zökkenőmentes betöltési élmény érdekében.
* A szalagcím embléma a felhasználóknak küldött ellenőrző e-mailekben jelenik meg, amikor kezdeményeznek egy regisztrációs felhasználói folyamatot.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Védjegyezés engedélyezése a felhasználói folyamatok oldalain

Miután konfigurálta a vállalati arculatot, engedélyezze azt a felhasználói folyamatokban.

1. A Azure Portal bal oldali menüjében válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amely számára engedélyezni szeretné a vállalati arculatot. A vállalati védjegyezés **nem támogatott** a szabványos *bejelentkezéshez* és a normál *profilhoz* tartozó felhasználói folyamatok szerkesztéséhez.
1. A **Testreszabás** **területen válassza a** lapelrendezések lehetőséget, majd válassza ki azt az elrendezést, amelyet a márka számára szeretne kijelölni. Válassza például az **egyesített regisztráció vagy a bejelentkezés lapot**.
1. A lapelrendezés **verziója (előzetes verzió)** területen válassza a **1.2.0** vagy újabb verzió elemet.
1. Válassza a **Mentés** lehetőséget.

Ha a felhasználói folyamat összes lapját szeretné felvenni, állítsa be az oldalelrendezés verzióját a felhasználói folyamat minden egyes lapjához.

![Az oldal elrendezésének kiválasztása Azure AD B2C a Azure Portal](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Lapelrendezés kiválasztása

A vállalat arculatának engedélyezéséhez meg kell [határoznia egy](contentdefinitions.md#migrating-to-page-layout) lapelrendezés-verziót, amely az `contract` Egyéni szabályzat *összes* tartalmi definíciója esetében az oldal verziószámát adja meg. Az érték formátumának tartalmaznia kell a szót `contract` : _urn: com: Microsoft: HRE: B2C: Elements:**Szerződés**:p Age-Name: Version_. A régi **DataUri** értéket használó egyéni szabályzatokban megjelenő lapelrendezés megadása.

Megtudhatja, hogyan [telepítheti át az oldal elrendezését](contentdefinitions.md#migrating-to-page-layout) az oldal verziójával.

A következő példa a Content definition-azonosítókat és a hozzá tartozó **DataUri** mutatja be: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

Az alábbi példa egy egyéni szalagcím-emblémát és háttérképet mutat be egy olyan *regisztrációs és bejelentkezési* felhasználói folyamat oldalon, amely az Ocean Blue sablont használja:

![A Azure AD B2C által kiszolgált márkás regisztrációs/bejelentkezési oldal](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Vállalati arculati eszközök használata egyéni HTML-kódban

Ha a vállalati arculati eszközöket [egyéni HTML](customize-ui-with-html.md)-kódban szeretné használni, adja hozzá a következő címkéket a `<div id="api">` címkén kívül:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A képforrást a háttérkép és a szalagcím emblémája helyettesíti.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával Azure Active Directory B2Cban](customize-ui-with-html.md).