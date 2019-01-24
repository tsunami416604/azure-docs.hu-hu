---
title: Az Azure Active Directory B2C az egyéni szabályzatokkal kapcsolatos problémák elhárítása az Application Insights |} A Microsoft Docs
description: hogyan kell Application Insights nyomon követése a végrehajtás egyéni házirendek beállítása.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 814ae29d4b50ecb44db45534c5d2a6bfb5b97a59
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846506"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Naplók gyűjtése

Ez a cikk lépéseit a naplók gyűjtésére az Azure AD B2C-ből, így diagnosztizálhatja a problémákat az egyéni házirendeknek.

>[!NOTE]
>Jelenleg úgy tervezték, a részletes tevékenységnaplókat, az itt leírtak szerint **csak** , ezzel elősegítve az egyéni szabályzatok fejlesztését. Fejlesztői mód nem használható éles környezetben.  Naplók összegyűjtése minden jogcím küldött és az identitás-szolgáltatóktól származó fejlesztés során.  Ha éles környezetben használja, a fejlesztői felelősséget személyazonosításra alkalmas adatok (közvetlenül a Microsoftnak azonosításra alkalmas adatokat) a saját App Insights napló gyűjtése.  Ezek a részletes naplók csak gyűjtött, ha a házirend a **fejlesztői mód**.


## <a name="use-application-insights"></a>Az Application Insights használata

Az Azure AD B2C szolgáltatás adatokat küld az Application Insights támogatja.  Az Application Insights lehetővé teszi kivételek diagnosztizálása és alkalmazásteljesítménnyel kapcsolatos problémák megjelenítését.

### <a name="setup-application-insights"></a>Az Application Insights beállítása

1. Nyissa meg az [Azure Portal](https://portal.azure.com). Ellenőrizze, hogy a bérlő az Azure-előfizetésében (nem Azure AD B2C-bérlőben).
1. Kattintson a **+ új** a bal oldali navigációs menü.
1. Keresse meg és válassza **Application Insights**, majd kattintson a **létrehozás**.
1. Töltse ki az űrlapot, és kattintson a **létrehozás**. Válassza ki **általános** számára a **alkalmazástípus**.
1. Ha az erőforrás létrejött, nyissa meg az Application Insights-erőforrást.
1. Keresés **tulajdonságok** a bal oldali menüben, és kattintson rá.
1. Másolás a **kialakítási kulcs** , és mentse a következő szakaszban.

### <a name="set-up-the-custom-policy"></a>Az egyéni házirend beállítása

1. Nyissa meg a helyreállítási pont Védettként fájlt (például SignUpOrSignin.xml).
1. A következő attribútumok hozzáadása a `<TrustFrameworkPolicy>` elem:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Ha már nem létezik, adjon hozzá egy alárendelt csomópont `<UserJourneyBehaviors>` , a `<RelyingParty>` csomópont. Található után azonnal kell lennie a `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Adja hozzá a következő csomópont gyermekeként a `<UserJourneyBehaviors>` elemet. Cserélje le `{Your Application Insights Key}` együtt a **kialakítási kulcs** Application Insights az előző szakaszban beszerzett.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` ApplicationInsights fejlesztéshez, de korlátozott nagy mennyiségben jó gyorsíthatja fel a telemetriát a feldolgozási folyamat keresztül közli.
  * `ClientEnabled="true"` elküldi a ApplicationInsights ügyféloldali parancsfájl (nem kötelező) oldal nézet és az ügyféloldali hibák nyomon követéséhez.
  * `ServerEnabled="true"` a meglévő UserJourneyRecorder JSON, egyéni eseményt küld az Application Insights.
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

3. A szabályzat feltöltése.

### <a name="see-the-logs-in-application-insights"></a>Tekintse meg a naplókat az Application insights szolgáltatásban

>[!NOTE]
> Van egy rövid késleltetés (kevesebb mint öt perc alatt) ahhoz, hogy az új naplók az Application Insightsban.

1. Nyissa meg az Application Insights-erőforrást, amelyet a [az Azure portal](https://portal.azure.com).
1. Az a **áttekintése** menüben kattintson a **Analytics**.
1. Az Application Insights új lap megnyitásához.
1. A naplók megtekintéséhez használhatja a lekérdezések listája

| Lekérdezés | Leírás |
|---------------------|--------------------|
nyomok | Megjelenik az összes Azure AD B2C által létrehozott naplók |
nyomok \| amelyben időbélyeg > ago(1d) | Tekintse meg az utolsó napjára Azure AD B2C által létrehozott naplók mindegyikét

A bejegyzések hosszú lehet.  Exportálás CSV-FÁJLBA a részletes bemutatása.

Az Analytics eszközzel kapcsolatos többet is megtudhat [Itt](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>A Közösség egy felhasználói interakciósorozat megjelenítő identitás fejlesztők segítségével fejlesztett ki.  Nem Microsoft által támogatott és szigorúan lehetőségként elérhetővé tegyen-van.  Az Application Insights-példány olvas, és a felhasználó jól strukturált áttekintést nyújt a utazási események.  Szerezze be a forráskódot, és üzembe helyezni a saját megoldását.

A megjelenítő, amely eseményeket olvas az Application Insights-verzió nem található [Itt](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>Jelenleg úgy tervezték, a részletes tevékenységnaplókat, az itt leírtak szerint **csak** , ezzel elősegítve az egyéni szabályzatok fejlesztését. Fejlesztői mód nem használható éles környezetben.  Naplók összegyűjtése minden jogcím küldött és az identitás-szolgáltatóktól származó fejlesztés során.  Ha éles környezetben használja, a fejlesztői felelősséget személyazonosításra alkalmas adatok (közvetlenül a Microsoftnak azonosításra alkalmas adatokat) a saját App Insights napló gyűjtése.  Ezek a részletes naplók csak gyűjtött, ha a házirend a **fejlesztői mód**.

[GitHub-tárházat a nem támogatott egyéni szabályzat minták és a kapcsolódó eszközök](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>További lépések

Fedezze fel az adatokat az Application Insights segítségével megismerheti, hogyan során lép fel az identitás-kezelőfelületi keretrendszer alapul szolgáló B2C működik, hogy a saját identitását.
