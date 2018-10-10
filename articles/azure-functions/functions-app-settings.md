---
title: Az Azure Functions – alkalmazásbeállítási referencia
description: Az Azure Functions-alkalmazás beállításai vagy a környezeti változók dokumentációja.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: b3b96bec7a56a0d1f8471f0290ae928ef80d7b3a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883737"
---
# <a name="app-settings-reference-for-azure-functions"></a>Az Azure Functions – alkalmazásbeállítási referencia

Alkalmazásbeállításokat a függvényalkalmazáshoz, amelyek befolyásolják, hogy a függvényalkalmazás a függvények globális konfigurációs beállításokat tartalmaznak. Ha helyileg futtatja, ezeket a beállításokat a környezeti változók találhatók. Ez a cikk az alkalmazásbeállításokat a függvényalkalmazások sorolja fel.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Egyéb globális konfigurációs lehetőségek a [host.json](functions-host-json.md) fájl és a [local.settings.json](functions-run-local.md#local-settings-file) fájlt.

## <a name="appinsightsinstrumentationkey"></a>ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY

Az Application Insights kialakítási kulcsot, az Application Insights használata. Lásd: [Azure Functions monitorozása](functions-monitoring.md).

|Kulcs|Mintaérték|
|---|------------|
|ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Nem kötelező tárfiók kapcsolati sztringje naplók tárolására, és azokat megjelenítése a **figyelő** lapot a portálon. A storage-fiókot, amely támogatja a blobok, üzenetsorok és táblák általános célú egy kell lennie. Lásd: [tárfiók](functions-infrastructure-as-code.md#storage-account) és [Storage-fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

> [!TIP]
> A teljesítmény és felhasználói élményt érdekében javasolt használni állítani az APPINSIGHTS_INSTRUMENTATIONKEY és az App Insights AzureWebJobsDashboard helyett

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` azt jelenti, hogy tiltsa le az alapértelmezett kezdőlap függvényalkalmazás gyökér URL-címe jelenik meg. Az alapértelmezett szint a `false`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Ha ennek az alkalmazásbeállításnak nincs megadva, vagy állítsa `false`, az alábbi példához hasonló lap jelenik meg az URL-címet adott válaszként `<functionappname>.azurewebsites.net`.

![Függvény app kezdőlapja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` azt jelenti, hogy kiadási módban használja, ha a .NET-kód; fordítása `false` azt jelenti, hogy a hibakeresési módot használja. Az alapértelmezett szint a `true`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Bétaverzió funkciók engedélyezéséhez vesszővel tagolt listája. Ezek a jelölők által engedélyezett bétaverzió funkciói nem éles üzemre, de kísérleti használata esetén is engedélyezhető, mielőtt az élő esemény indításra.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

A gyökérkönyvtár elérési útja, a *host.json* fájl- és függvény mappák találhatók. A függvényalkalmazás, az alapértelmezett érték `%HOME%\site\wwwroot`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Megadja a tárházban vagy a szolgáltató kulcs tárolására. A támogatott adattárak jelenleg blob ("Blob") és a fájlrendszert ("tiltott"). Az alapértelmezett érték ("tiltott") fájlrendszer.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|letiltva|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Az Azure Functions runtime a tárfiók kapcsolati sztringje HTTP által aktivált függvények kivételével az összes függvényt használja. A storage-fiókot, amely támogatja a blobok, üzenetsorok és táblák általános célú egy kell lennie. Lásd: [tárfiók](functions-infrastructure-as-code.md#storage-account) és [Storage-fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

A használt TypeScript-fordítóprogram elérési útja. Ha szeretné az alapértelmezett beállítás felülbírálásával teszi lehetővé.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FÜGGVÉNY\_ALKALMAZÁS\_SZERKESZTÉSE\_MÓD

Érvényes értékek: "readwrite" és "csak olvasható".

|Kulcs|Mintaérték|
|---|------------|
|FÜGGVÉNY\_ALKALMAZÁS\_SZERKESZTÉSE\_MÓD|csak olvasható|

## <a name="functionsextensionversion"></a>FÜGGVÉNYEK\_BŐVÍTMÉNY\_VERZIÓ

A használatához a függvényalkalmazást a Functions futtatókörnyezetének verzióját. A főverzió tilde azt jelenti, hogy jelentős 2-es verziójú (például "~") legújabb verzióját használja. Érhetők el ugyanazon új verziók, automatikusan települ a függvényalkalmazáshoz. Alkalmazás rögzítése a egy adott verziót, használja a teljes verziószám (például "2.0.12345"). Alapértelmezett érték a "~ 2". Érték `~1` verziójára az alkalmazás rögzíti a futtatókörnyezet 1.x.

|Kulcs|Mintaérték|
|---|------------|
|FÜGGVÉNYEK\_BŐVÍTMÉNY\_VERZIÓ|KB. 2|

## <a name="functionsworkerruntime"></a>FÜGGVÉNYEK\_FELDOLGOZÓ\_MODUL

A nyelvi feldolgozói modul betöltése a függvényalkalmazáshoz.  Ez felel meg a nyelvet, az alkalmazásban (például "dotnet") használja. Functions több nyelven is szüksége lesz, ha közzétesszük őket, több alkalmazás, amelyek mindegyike egy megfelelő értéket a worker futásidejű.  Érvényes értékek a következők `dotnet` (C# /F #), `node` (JavaScript), és `java` (Java).

|Kulcs|Mintaérték|
|---|------------|
|FÜGGVÉNYEK\_FELDOLGOZÓ\_MODUL|DotNet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

A használatalapú csomagok csak. A függvény kódját és konfigurációs tároló storage-fiókhoz tartozó kapcsolati karakterláncot. Lásd: [hozzon létre egy függvényalkalmazást](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="websitecontentshare"></a>WEBHELY\_CONTENTSHARE

A használatalapú csomagok csak. A fájl elérési útja a függvény kódját és a konfiguráció. Az WEBSITE_CONTENTAZUREFILECONNECTIONSTRING használni. Alapértelmezés szerint egy egyedi karakterlánccá, amely a függvényalkalmazás neve kezdődik. Lásd: [hozzon létre egy függvényalkalmazást](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBHELY\_MAXIMÁLIS\_DINAMIKUS\_ALKALMAZÁS\_MÉRETEZÉSI\_KI

A függvényalkalmazás lehet horizontálisan-példányok maximális száma. Alapértelmezett érték a nincs korlátozva.

> [!NOTE]
> Ez a beállítás nem egy előzetes verziójú funkció - és csak megbízható if értéke < = 5

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_MAXIMÁLIS\_DINAMIKUS\_ALKALMAZÁS\_MÉRETEZÉSI\_KI|5|

## <a name="websitenodedefaultversion"></a>WEBHELY\_CSOMÓPONT\_DEFAULT_VERSION

Az alapértelmezett érték "8.11.1".

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_CSOMÓPONT\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEBHELY\_FUTTATÁSA\_FROM\_CSOMAG

A függvényalkalmazás egy csatlakoztatott alkalmazáscsomag-fájl futtatását engedélyezi.

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_FUTTATÁSA\_FROM\_CSOMAG|1|

Érvényes értékek a következők vagy egy URL-címet, amelyet a központi telepítési csomag fájl helyét, vagy `1`. Ha beállítása `1`, a csomagot kell lennie a `d:\home\data\SitePackages` mappát. Ezzel a beállítással telepítési zip használatakor a csomag automatikusan fel a rendszer ezen a helyen. Előzetes verzióban ez a beállítás neve volt `WEBSITE_RUN_FROM_ZIP`. További információkért lásd: [a functions futtatása egy csomagfájlt](run-functions-from-deployment-package.md).

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan-beállítások frissítése](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Tekintse meg a host.json fájl globális beállításai](functions-host-json.md)

[Tekintse meg az App Service-alkalmazások más app beállításait](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
