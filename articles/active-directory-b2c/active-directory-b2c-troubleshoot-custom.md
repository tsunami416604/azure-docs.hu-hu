---
title: "Az Application Insights egyéni házirendek – hibaelhárítás az Azure AD B2C |} Microsoft Docs"
description: "a telepítő az Application Insights nyomon követésére, a végrehajtás, egyéni házirendek hogyan"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: saeda
ms.openlocfilehash: 4f71380917a5a29497da9831791cd9f86ec4c8ca
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Az Azure Active Directory B2C: Naplógyűjtés időtartamát

Ez a cikk lépéseit a naplógyűjtés időtartamát az Azure AD B2C-vel, hogy diagnosztizálhatja a problémákat az egyéni házirendeknek.

>[!NOTE]
>Az itt leírt részletes tevékenységi naplóit célja jelenleg **csak** a egyéni házirendek fejlesztésének segítése érdekében. Éles környezetben fejlesztői mód nem használható.  Naplók gyűjtése és az identitás-szolgáltatóktól származó a fejlesztés során küldött összes jogcímet.  Ha éles környezetben használt, a fejlesztői felelősséget PII (közvetlenül a Microsoftnak azonosításra alkalmas adatokat) gyűjtése a saját App Insights naplóban.  Ezek a részletes naplók csak gyűjtött, ha a házirend a **fejlesztői mód**.


## <a name="use-application-insights"></a>Az Application Insights használata

Az Azure AD B2C támogatja egy szolgáltatást, hogy az Application insights részére.  Az Application Insights kivételek diagnosztizálhatja és alkalmazás teljesítményproblémák megjelenítése lehetőséget biztosít.

### <a name="setup-application-insights"></a>Az Application Insights beállítása

1. Nyissa meg az [Azure Portal](https://portal.azure.com). Hogy a bérlő az Azure-előfizetéséhez (nem az Azure AD B2C-bérlő).
1. Kattintson a **+ új** a bal oldali navigációs menü.
1. Keresse meg és jelölje ki **Application Insights**, majd kattintson a **létrehozása**.
1. Töltse ki az űrlapot, és kattintson a **létrehozása**. Válassza ki **általános** a a **alkalmazástípus**.
1. Ha az erőforrás létrejött, nyissa meg az Application Insights-erőforrást.
1. Található **tulajdonságok** a bal oldali menüben, kattintson rá.
1. Másolás a **Instrumentation kulcs** , és mentse a következő szakasz.

### <a name="set-up-the-custom-policy"></a>Az egyéni házirend beállítása

1. Nyissa meg a függő Entitás fájlt (például SignUpOrSignin.xml).
1. A következő attribútumok hozzáadása a `<TrustFrameworkPolicy>` elem:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Ha már nem létezik, adja hozzá a gyermekcsomópontja `<UserJourneyBehaviors>` számára a `<RelyingParty>` csomópont. Kell elhelyezni után azonnal a `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Adja hozzá a következő csomópont gyermekeként a `<UserJourneyBehaviors>` elemet. Győződjön meg arról, hogy `{Your Application Insights Key}` rendelkező a **Instrumentation kulcs** az Application Insights az előző szakaszban beszerzett.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` be van állítva ApplicationInsights keresztül a feldolgozási sorban, a telemetria jó elősegítésére fejlesztési, de a nagy mennyiségük korlátozott.
  * `ClientEnabled="true"` elküldi a ApplicationInsights ügyféloldali parancsprogram nyomon követése lap megtekintése és ügyféloldali hibák (nem kötelező).
  * `ServerEnabled="true"` a meglévő UserJourneyRecorder JSON egyéni eseményként küld az Application Insights.
Minta:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Töltse fel a házirendet.

### <a name="see-the-logs-in-application-insights"></a>Tekintse meg a naplókat az Application Insightsban

>[!NOTE]
> Nincs a rövid késleltetés (kevesebb mint öt perc), új naplófájlok az Application Insights megjelenítéséhez.

1. Nyissa meg az Application Insights-erőforrás a [Azure-portálon](https://portal.azure.com).
1. Az a **áttekintése** menüben kattintson a **Analytics**.
1. Az Application Insightsban új lap megnyitásához.
1. Ez egy lista lekérdezések segítségével tekintse meg a naplókat

| Lekérdezés | Leírás |
|---------------------|--------------------|
nyomkövetések | Az összes Azure AD B2C által létrehozott naplók |
nyomkövetések \| ahol időbélyeg > ago(1d) | Az elmúlt nap során az Azure AD B2C által létrehozott naplók számú

A bejegyzések hosszú lehet.  Exportálás CSV-FÁJLBA a részletes bemutatása.

Az elemzés eszközzel kapcsolatos részletesebb [Itt](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>A Közösség dolgozott egy felhasználó út megjelenítő segítségével a fejlesztők identitás.  Nem Microsoft által támogatott és elérhetővé tegyen szigorúan-van.  Olvassa be az Application Insights-példány, és a felhasználó well-struktúra áttekintést nyújt a út események.  Szerezze be a forráskódot, és telepítheti saját megoldásban.

Olvassa be az eseményeket az Application Insights viewer verziója található [Itt](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>Az itt leírt részletes tevékenységi naplóit célja jelenleg **csak** a egyéni házirendek fejlesztésének segítése érdekében. Éles környezetben fejlesztői mód nem használható.  Naplók gyűjtése és az identitás-szolgáltatóktól származó a fejlesztés során küldött összes jogcímet.  Ha éles környezetben használt, a fejlesztői felelősséget PII (közvetlenül a Microsoftnak azonosításra alkalmas adatokat) gyűjtése a saját App Insights naplóban.  Ezek a részletes naplók csak gyűjtött, ha a házirend a **fejlesztői mód**.

[Github-tárházban nem támogatott egyéni házirend mintákat és a kapcsolódó eszközök](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>További lépések

Az Application Insights segítségével megtudhatja, hogyan képes biztosítani a saját identitás működik alapul szolgáló B2C identitás élmény keretében észlel az adatokba.
