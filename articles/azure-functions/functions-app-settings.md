---
title: "Az Azure Functions alkalmazás-beállítások referenciája"
description: "Az Azure Functions Alkalmazásbeállítások vagy a környezeti változók referenciadokumentációt tartalmaz."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2017
ms.author: tdykstra
ms.openlocfilehash: ce7bf2cf650b0df7e8998766b2d3f5a37c4a1b72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="app-settings-reference-for-azure-functions"></a>Az Azure Functions alkalmazás-beállítások referenciája

Alkalmazásbeállítások függvény alkalmazásban a globális konfigurációs beállítások, amelyek hatással vannak az adott függvény alkalmazáshoz összes funkciójának tartalmaz. Amikor helyileg futtat, ezeket a beállításokat a környezeti változók vannak. Ez a cikk függvény alkalmazásokban elérhető alkalmazás beállításokat sorolja fel.

A más globális konfigurációs beállítások a [host.json](functions-host-json.md) fájl és a a [local.settings.json](functions-run-local.md#local-settings-file) fájlt.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Az Application Insights instrumentation kulcs, az Application Insights használata. Lásd: [figyelése az Azure Functions](functions-monitoring.md).

|Kulcs|Mintaérték|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Nem kötelező tárolási fiók kapcsolati karakterlánc naplók tárolásához, és azokat megjelenítése a **figyelő** lapon a portálon. A storage-fiók, amely támogatja a BLOB, üzenetsorok és táblák általános célú egy kell lennie. Lásd: [tárfiók](functions-infrastructure-as-code.md#storage-account) és [tárolási fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|Megadni: DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`azt jelenti, hogy tiltsa le az alapértelmezett kezdőlapján látható egy függvény alkalmazás gyökér URL-címe. Alapértelmezett érték a `false`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDisableHomepage|Igaz|

Ha a app beállítás nincs megadva vagy beállítása `false`, az alábbi példához hasonló lap jelenik meg az URL-cím válaszul `<functionappname>.azurewebsites.net`.

![Függvény alkalmazás kezdőlapja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`azt jelenti, hogy kiadás üzemmódot használják, ha a .NET-kódot; fordítása `false` azt jelenti, hogy hibakeresési mód használata. Alapértelmezett érték a `true`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|Igaz|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Ahhoz, hogy beta szolgáltatások vesszővel tagolt listája. Ezeknek a jelzőknek által engedélyezett Beta funkciók nem éles kész, de engedélyezhető kísérleti használatra futtatás előtt.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

A könyvtár elérési útját a legfelső szintű ahol a *host.json* fájl-és a függvény találhatók. Egy függvény alkalmazásban, az alapértelmezett érték `%HOME%\site\wwwroot`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

A tárház vagy használandó kulcstároló-szolgáltató határozza meg. A támogatott adattárak jelenleg blob ("Blob") és a fájlrendszer ("Letiltva"). Az alapértelmezett érték ("Letiltva") fájlrendszer.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|le van tiltva|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Az Azure Functions futtatókörnyezettel használ a tárolási fiók kapcsolati karakterlánc indított HTTP funkciók kivételével minden funkcióját. A storage-fiók, amely támogatja a BLOB, üzenetsorok és táblák általános célú egy kell lennie. Lásd: [tárfiók](functions-infrastructure-as-code.md#storage-account) és [tárolási fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsStorage|Megadni: DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

A fordítóprogram géppel használt elérési útja. Lehetővé teszi az alapértelmezett bírálja felül, ha szeretné.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FÜGGVÉNY\_APP\_SZERKESZTÉSE\_MÓD

Érvényes értékek: "readwrite" és "readonly".

|Kulcs|Mintaérték|
|---|------------|
|FÜGGVÉNY\_APP\_SZERKESZTÉSE\_MÓD|csak olvasható|

## <a name="functionsextensionversion"></a>FUNKCIÓK\_BŐVÍTMÉNY\_VERZIÓJA

A függvény alkalmazásban használni az Azure Functions futtatókörnyezettel verzióját. A főverzió tildével azt jelenti, hogy az adott főverziója (például "~ 1") legújabb verzióját használja. Az ugyanazon főverzióját új verziók érhetők el, automatikusan települ a függvény alkalmazásban. PIN-kód az alkalmazásnak, hogy egy bizonyos verzióra, használja a teljes verziószáma (például "1.0.12345"). Alapértelmezett érték a "~ 1".

|Kulcs|Mintaérték|
|---|------------|
|FUNKCIÓK\_BŐVÍTMÉNY\_VERZIÓJA|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Csak fogyasztás tervekhez. A függvény alkalmazáskódot és konfigurációs tároló tárfiók kapcsolati karakterláncot. Lásd: [függvény-alkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|Megadni: DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="websitecontentshare"></a>WEBSITE_CONTENTSHARE

Csak fogyasztás tervekhez. A függvény alkalmazáskódot és a konfigurációs fájl elérési útja WEBSITE_CONTENTAZUREFILECONNECTIONSTRING együtt használni. Alapértelmezett érték egy egyedi karakterlánc, amely a függvény alkalmazásnév kezdődik. Lásd: [függvény-alkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBHELY\_MAXIMÁLIS\_DINAMIKUS\_ALKALMAZÁS\_MÉRETEZÉSI\_KIMENŐ

A függvény app lehet horizontálisan-példányok maximális száma. Alapértelmezés szerint nincs korlát.

> [!NOTE]
> Ez a beállítás az előzetes verziójú funkciók van.

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_MAXIMÁLIS\_DINAMIKUS\_ALKALMAZÁS\_MÉRETEZÉSI\_KIMENŐ|10|

## <a name="websitenodedefaultversion"></a>WEBHELY\_CSOMÓPONT\_DEFAULT_VERSION

Alapértelmezett érték a "6.5.0".

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_CSOMÓPONT\_DEFAULT_VERSION|6.5.0|

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan-beállítások frissítése](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

> [!div class="nextstepaction"]
> [Tekintse meg a host.json fájl globális beállításai](functions-host-json.md)
