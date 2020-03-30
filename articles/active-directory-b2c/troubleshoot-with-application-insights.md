---
title: Egyéni házirendek – az Application Insights használata – problémamegoldás
titleSuffix: Azure AD B2C
description: Hogyan állíthatja be az Application Insightsot az egyéni szabályzatok végrehajtásának nyomon követésére.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186267"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C-naplók gyűjtése az Application Insights segítségével

Ez a cikk az Active Directory B2C (Azure AD B2C) naplóinak gyűjtését ismerteti, hogy diagnosztizálhassa az egyéni szabályzatokkal kapcsolatos problémákat. Az Application Insights segítségével diagnosztizálhatja a kivételeket, és vizualizálhatja az alkalmazások teljesítményével kapcsolatos problémákat. Az Azure AD B2C tartalmaz egy szolgáltatást az Application Insights adatok küldéséhez.

Az itt leírt részletes tevékenységnaplókat **csak** az egyéni szabályzatok fejlesztése során engedélyezni kell.

> [!WARNING]
> Ne engedélyezze a fejlesztési módot éles környezetben. A naplók az identitásszolgáltatóknak küldött és az identitásszolgáltatóktól küldött összes jogcímeket gyűjtik. Ön, mint a fejlesztő felelősséget vállal az Application Insights-naplókban gyűjtött személyes adatokért. Ezek a részletes naplók csak akkor kerülnek össze, ha a házirend **developer módban**van elhelyezve.

## <a name="set-up-application-insights"></a>Az Alkalmazáselemzési adatok beállítása

Ha még nem rendelkezik ilyen, hozzon létre egy példányt az Application Insights az előfizetésben.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, majd válassza ki az Azure-előfizetést tartalmazó könyvtárat (nem az Azure AD B2C könyvtárat).
1. Válassza az **Erőforrás létrehozása parancsot** a bal oldali navigációs menüben.
1. Keresse meg és válassza **az Application Insights (Alkalmazáselemzési**adatok) lehetőséget, majd válassza a **Létrehozás lehetőséget.**
1. Töltse ki az űrlapot, válassza **a Véleményezés + létrehozás**lehetőséget, majd a **Létrehozás gombot.**
1. A központi telepítés befejezése után válassza az **Ugrás az erőforrásra**lehetőséget.
1. Az Application Insights menü **Konfigurálás** területén válassza a **Tulajdonságok lehetőséget.**
1. Jegyezze fel a **műszerezési kulcsot** egy későbbi lépésben való használatra.

## <a name="configure-the-custom-policy"></a>Az egyéni házirend konfigurálása

1. Nyissa meg a függő entitás (RP) fájlját, például *SignUpOrSignin.xml*.
1. Adja hozzá a `<TrustFrameworkPolicy>` következő attribútumokat az elemhez:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Ha még nem létezik, `<UserJourneyBehaviors>` adjon hozzá egy `<RelyingParty>` gyermekcsomópontot a csomóponthoz. Közvetlenül a utána `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`kell elhelyezni.
1. Adja hozzá a következő csomópontot `<UserJourneyBehaviors>` az elem gyermekeként. Győződjön meg `{Your Application Insights Key}` arról, hogy cserélje ki az Application Insights **instrumentation kulcs,** amely korábban rögzített.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`Azt mondja ApplicationInsights, hogy gyorsítsa fel a telemetriai adatokat a feldolgozási folyamaton keresztül. Jó a fejlődéshez, de nagy mennyiségben korlátozott.
    * `ClientEnabled="true"`az ApplicationInsights ügyféloldali parancsfájlt küldi az oldalnézet és az ügyféloldali hibák nyomon követéséhez. Ezeket megtekintheti a **browserTimings** táblában az Application Insights portálon. Beállításával, `ClientEnabled= "true"`adja hozzá az Application Insights az oldal script, és kapsz időzítések oldal betöltése és AJAX hívások, számít, részleteit böngésző kivételek és AJAX hibák, valamint a felhasználói és munkamenet számít. Ez a mező **nem**kötelező `false` , és alapértelmezés szerint be van állítva.
    * `ServerEnabled="true"`elküldi a meglévő UserJourneyRecorder JSON egyéni eseményként az Application Insights.

    Példa:

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

1. Töltse fel a házirendet.

## <a name="see-the-logs-in-application-insights"></a>Tekintse meg a naplókat az Application Insightsban

Van egy rövid késleltetés, általában kevesebb, mint öt perc, mielőtt láthatja az új naplók az Application Insights.

1. Nyissa meg az Azure Insights-erőforrást, amelyet az [Azure Portalon](https://portal.azure.com)hozott létre.
1. Az **Áttekintés menüben** válassza az **Analytics**lehetőséget.
1. Nyisson meg egy új lapot az Application Insights ban.

Az alábbi lekérdezések listája a naplók megtekintéséhez használható:

| Lekérdezés | Leírás |
|---------------------|--------------------|
`traces` | Az Azure AD B2C által létrehozott összes napló megtekintése |
`traces | where timestamp > ago(1d)` | Az Azure AD B2C által az utolsó napra létrehozott összes napló megtekintése

A bejegyzések hosszúak lehetnek. Exportáljon a CSV-be, hogy közelebbről is megnézhassa.

A lekérdezésről további információt az [Azure Monitor naplólekérdezéseinek áttekintése](../azure-monitor/log-query/log-query-overview.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

A közösség kifejlesztett egy felhasználói utazás-megjelenítőt, hogy segítse az identitásfejlesztőket. Az Application Insights-példányból olvas, és jól strukturált nézetet biztosít a felhasználói út eseményeiről. Beszerzi a forráskódot, és telepíti azt a saját megoldásában.

A microsoft nem támogatja a felhasználói utazáslejátszót, és szigorúan elérhetővé válik.

A megtekintő nek azt a verzióját, amely a GitHubon lévő Application Insights-eseményeket olvassa, itt találja:

[Azure-Samples/active-directory-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
