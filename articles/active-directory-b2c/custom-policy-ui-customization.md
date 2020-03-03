---
title: Egyéni szabályzattal testreszabhatja az alkalmazás felhasználói felületét
titleSuffix: Azure AD B2C
description: Útmutató felhasználói felület egyéni házirenddel történő testreszabásához Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8e07d3e1815c1b47b9d37c08e8fac5359b71fe7c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189020"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Testre szabhatja az alkalmazás felhasználói felületét egy egyéni házirend használatával Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A cikkben ismertetett lépések végrehajtásával létrehozhat egy regisztrációs és bejelentkezési egyéni szabályzatot a márkával és a megjelenéssel. A Azure Active Directory B2C (Azure AD B2C) használatával szinte teljes mértékben vezérelheti a felhasználók számára megjelenített HTML-és CSS-tartalmakat. Ha egyéni házirendet használ, a felhasználói felület testreszabását az XML-ben kell konfigurálnia ahelyett, hogy a Azure Portal vezérlőit kellene használnia.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A bejelentkezéshez és a helyi fiókokkal való bejelentkezéshez egyéni szabályzatot kell használnia.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

## <a name="4-modify-the-extensions-file"></a>4. a bővítmények fájljának módosítása

A felhasználói felület testreszabásának konfigurálásához másolja a **ContentDefinition** és annak alárendelt elemeit az alapfájlból a kiterjesztések fájlba.

1. Nyissa meg a szabályzat alapfájlját. Például <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em>  . Ez az alapfájl az egyéni házirend alapszintű csomagban található egyik házirend-fájl, amelyet az előfeltételben kell megszereznie az [Egyéni szabályzatok használatának első lépéseiben](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Keresse meg és másolja a **ContentDefinitions** elem teljes tartalmát.
1. Nyissa meg a kiterjesztési fájlt. Például: *TrustFrameworkExtensions. XML*. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Illessze be a **ContentDefinitions** elem teljes tartalmát, amelyet a **BuildingBlocks** elem gyermekeiként másolt.
1. Keresse meg a **ContentDefinition** elemet, amely a másolt XML-ben `Id="api.signuporsignin"` tartalmaz.
1. Módosítsa a **tartalomdefinícióban** értékét a Storage-ba feltöltött HTML-fájl URL-címére. Például: `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Az egyéni szabályzatnak a következő kódrészlethez hasonlóan kell kinéznie:

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

1. Mentse a bővítmények fájlt.

## <a name="5-upload-and-test-your-updated-custom-policy"></a>5. a frissített egyéni szabályzat feltöltése és tesztelése

### <a name="51-upload-the-custom-policy"></a>5,1 az egyéni szabályzat feltöltése

1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Keresse meg és válassza ki a **Azure ad B2C**.
1. A **szabályzatok**területen válassza az **identitási élmény keretrendszere**elemet.
1. Válassza az **egyéni házirend feltöltése**lehetőséget.
1. Töltse fel a korábban módosított kiterjesztéseket tartalmazó fájlt.

### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 az egyéni házirend tesztelése a **Futtatás most** használatával

1. Válassza ki a feltöltött szabályzatot, majd válassza a **Futtatás most**lehetőséget.
1. Regisztrálnia kell egy e-mail-cím használatával.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Dinamikus egyéni oldal tartalmi URI-ja konfigurálása

Azure AD B2C egyéni szabályzatok használatával az URL-címen vagy egy lekérdezési karakterláncban is elküldheti a paramétereket. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. A paraméter lehet bármely [jogcím-feloldó](claim-resolver-overview.md), például az alkalmazás azonosítója, a nyelvi azonosító vagy az egyéni lekérdezési karakterlánc paraméter, például `campaignId`.

### <a name="sending-query-string-parameters"></a>Lekérdezési karakterlánc paramétereinek küldése

Lekérdezési karakterlánc paramétereinek küldéséhez a [függő entitás házirendjében](relyingparty.md)adjon hozzá egy `ContentDefinitionParameters` elemet az alább látható módon.

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

A tartalom definíciójában módosítsa `LoadUri` értékét `https://<app_name>.azurewebsites.net/home/unified`re. Az egyéni házirend `ContentDefinition` a következő kódrészlethez hasonlóan kell kinéznie:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Amikor Azure AD B2C betölti a lapot, meghívja a webkiszolgáló-végpontot:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dinamikus oldal tartalmi URI-ja

A tartalom a használt paraméterek alapján különböző helyekről is kihúzható. A CORS-kompatibilis végponton állítsa be a mappa szerkezetét a tartalom tárolására. Például a következő struktúrában rendezheti a tartalmakat. Gyökérmappa */mappa/nyelv/a HTML-fájlok*. Az egyéni oldal URI-ja például az alábbihoz hasonló lehet:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C elküldi a két betűs ISO-kódot a nyelvhez, `fr` franciául:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>További lépések

További információ a testreszabható felhasználói felületi elemekről: [útmutató a felhasználói folyamatokhoz használható felhasználói felület testreszabásához](customize-ui-overview.md).
