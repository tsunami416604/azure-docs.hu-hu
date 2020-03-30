---
title: Az alkalmazás felhasználói felületének testreszabása egyéni szabályzattal
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan szabhat testre egy felhasználói felületet egyéni szabályzathasználatával az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87c3a3a904705b9fcb702c4745c4c80a4b447e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476722"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Az alkalmazás felhasználói felületének testreszabása egyéni szabályzat használatával az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A cikkben leírt lépések végrehajtásával létrehoz egy regisztrációs és bejelentkezési egyéni szabályzatot a márkájával és megjelenésével. Az Azure Active Directory B2C (Azure AD B2C) segítségével szinte teljes mértékben szabályozhatja a felhasználók számára bemutatott HTML- és CSS-tartalmakat. Ha egyéni szabályzatot használ, a felhasználói felület testreszabását XML-ben konfigurálja az Azure Portalon lévő vezérlők használata helyett.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a helyi fiókkal való bejelentkezéshez.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

## <a name="4-modify-the-extensions-file"></a>4. Módosítsa a kiterjesztések fájl

A felhasználói felület testreszabásának konfigurálásához másolja a **ContentDefinition** és gyermekelemeit az alapfájlból a bővítményfájlba.

1. Nyissa meg a házirend alapfájlját. <em> `SocialAndLocalAccounts/` </em>Például. Ez az alapfájl az egyéni házirend-kezdőcsomagegyik házirendfájlja, amelyet az egyéni házirendek használata című előfeltételben kellett volna [beszereznie.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)
1. Keresse meg és másolja a **ContentDefinitions** elem teljes tartalmát.
1. Nyissa meg a bővítményfájlt. Például *TrustFrameworkExtensions.xml*. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Illessze be a **BuildingBlocks** elem gyermekként másolt **ContentDefinitions** elem teljes tartalmát.
1. Keresse meg a másolt `Id="api.signuporsignin"` XML-ben található **ContentDefinition** elemet.
1. Módosítsa a **LoadUri** értékét a tárolóba feltöltött HTML-fájl URL-címére. Például: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Az egyéni házirendnek a következő kódrészlethez kell hasonlítania:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Mentse a kiterjesztésfájlt.

## <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Töltse fel és tesztelje a frissített egyéni házirendet

### <a name="51-upload-the-custom-policy"></a>5.1 Az egyéni házirend feltöltése

1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Keresse meg és válassza az **Azure AD B2C**lehetőséget.
1. A **Házirendek**csoportban válassza **az Identitáskezelési keretrendszert**.
1. Válassza **az Egyéni házirend feltöltése lehetőséget.**
1. Töltse fel a korábban módosított bővítményfájlt.

### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Az egyéni házirend tesztelése a **Futtatás most** használatával

1. Válassza ki a feltöltött házirendet, majd válassza a **Futtatás most**lehetőséget.
1. E-mail cím használatával regisztrálhat.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Dinamikus egyéni laptartalom URI-jának konfigurálása

Az Azure AD B2C egyéni szabályzatok használatával küldhet egy paramétert az URL-elérési úton, vagy egy lekérdezési karakterlánc. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. A paraméter bármilyen [jogcímfeloldó](claim-resolver-overview.md)lehet, például az alkalmazásazonosító, a nyelvazonosító `campaignId`vagy az egyéni lekérdezési karakterlánc paraméter, például .

### <a name="sending-query-string-parameters"></a>Lekérdezési karakterlánc paramétereinek küldése

A lekérdezési karakterlánc paramétereinek küldéséhez a függő `ContentDefinitionParameters` entitás [házirendjében](relyingparty.md)adjon hozzá egy elemet az alábbiak szerint.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

A tartalomdefinícióban módosítsa a `LoadUri` `https://<app_name>.azurewebsites.net/home/unified`értékét. Az egyéni `ContentDefinition` házirendnek a következő kódrészlethez kell hasonlítania:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Amikor az Azure AD B2C betölti a lapot, a webkiszolgáló végpontját hívja:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dinamikus laptartalom URI

A tartalom a használt paraméterek alapján különböző helyekről is lehúzható. A CORS-kompatibilis végponton állítson be egy mappastruktúrát a tartalom tárolására. A tartalmat például a következő struktúrába rendezheti. *Gyökérmappa/ mappa nyelvenként / a html fájlokat*. Az egyéni oldal URI-ja például így nézhet ki:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Az Azure AD B2C a kétbetűs `fr` ISO-kódot küldi el a nyelvhez, a francia nyelvhez:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>További lépések

A testreszabható felhasználói felületi elemekről a [felhasználói felület felhasználói folyamatok testreszabására vonatkozó útmutatóban](customize-ui-overview.md)talál további információt.
