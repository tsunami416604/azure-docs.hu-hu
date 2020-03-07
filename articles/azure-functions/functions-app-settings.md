---
title: Az Azure Functions – alkalmazásbeállítási referencia
description: Az Azure Functions-alkalmazás beállításai vagy a környezeti változók dokumentációja.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355704"
---
# <a name="app-settings-reference-for-azure-functions"></a>Az Azure Functions – alkalmazásbeállítási referencia

Alkalmazásbeállításokat a függvényalkalmazáshoz, amelyek befolyásolják, hogy a függvényalkalmazás a függvények globális konfigurációs beállításokat tartalmaznak. Helyileg futtatva ezek a beállítások helyi [környezeti változókként](functions-run-local.md#local-settings-file)érhetők el. Ez a cikk az alkalmazásbeállításokat a függvényalkalmazások sorolja fel.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

A [Host. JSON](functions-host-json.md) fájlban és a [Local. Settings. JSON](functions-run-local.md#local-settings-file) fájlban más globális konfigurációs lehetőségek is vannak.

## <a name="appinsights_instrumentationkey"></a>ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY

Az Application Insights kialakítási kulcsot, az Application Insights használata. Lásd: [figyelő Azure functions](functions-monitoring.md).

|Paraméter|Mintaérték|
|---|------------|
|ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

A functions futtatókörnyezet 2. x vagy újabb verziójában a futásidejű környezet alapján konfigurálja az alkalmazások viselkedését. Az [inicializálás során](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)ez az érték olvasható. Bármilyen értékre beállíthatja a `AZURE_FUNCTIONS_ENVIRONMENT`t, de [három érték](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) támogatott: [fejlesztés](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [előkészítés](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)és [éles környezet](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Ha `AZURE_FUNCTIONS_ENVIRONMENT` nincs beállítva, az alapértelmezett érték `Development` helyi környezetben, és `Production` az Azure-ban. A futásidejű környezet beállítása `ASPNETCORE_ENVIRONMENT` helyett ezt a beállítást kell használni. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcionális Storage-fiókhoz tartozó kapcsolatok karakterlánca a naplók tárolásához és megjelenítéséhez a portál **figyelés** lapján. Ez a beállítás csak olyan alkalmazások esetén érvényes, amelyek az Azure Functions futtatókörnyezet 1. x verzióját célozzák meg. A storage-fiókot, amely támogatja a blobok, üzenetsorok és táblák általános célú egy kell lennie. További információ: a [Storage-fiókra vonatkozó követelmények](storage-considerations.md#storage-account-requirements).

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName =<name>; AccountKey =<key>|

> [!NOTE]
> A jobb teljesítmény és a használhatóság érdekében a Runtime 2. x és újabb verziói a `AzureWebJobsDashboard`helyett APPINSIGHTS_INSTRUMENTATIONKEY és alkalmazás-bepillantást használnak a figyeléshez.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` azt jelenti, hogy letiltja a Function app gyökerének URL-címéhez tartozó alapértelmezett kezdőlapot. Az alapértelmezett érték a `false`.

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Ha ez az Alkalmazásbeállítások ki van hagyva vagy a `false`értékre van állítva, az URL `<functionappname>.azurewebsites.net`-címre adott válaszban az alábbi példához hasonló oldal jelenik meg.

![Függvény app kezdőlapja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` azt jelenti, hogy a kiadási módot használja a .NET-kód fordításakor; `false` a hibakeresési mód használatát jelenti. Az alapértelmezett érték a `true`.

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Bétaverzió funkciók engedélyezéséhez vesszővel tagolt listája. Ezek a jelölők által engedélyezett bétaverzió funkciói nem éles üzemre, de kísérleti használata esetén is engedélyezhető, mielőtt az élő esemény indításra.

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Megadja a tárházban vagy a szolgáltató kulcs tárolására. A támogatott adattárak jelenleg a blob storage ("Blob") és a helyi fájlrendszer ("fájlok"). Az alapértelmezett érték a 2. verzióban blob és a fájlrendszerhez az 1. verzióban.

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|Fájlok|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Az Azure Functions runtime a tárfiók kapcsolati sztringje HTTP által aktivált függvények kivételével az összes függvényt használja. A storage-fiókot, amely támogatja a blobok, üzenetsorok és táblák általános célú egy kell lennie. Lásd a [Storage-fiókra](functions-infrastructure-as-code.md#storage-account) és a [Storage-fiókra vonatkozó követelményeket](storage-considerations.md#storage-account-requirements).

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

A használt TypeScript-fordítóprogram elérési útja. Ha szeretné az alapértelmezett beállítás felülbírálásával teszi lehetővé.

|Paraméter|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_alkalmazás\_\_mód szerkesztése

Azt diktálja, hogy engedélyezve van-e a Azure Portal szerkesztése. Érvényes értékek: "readwrite" és "csak olvasható".

|Paraméter|Mintaérték|
|---|------------|
|FUNCTION\_alkalmazás\_\_mód szerkesztése|csak olvasható|

## <a name="functions_extension_version"></a>FÜGGVÉNYEK\_BŐVÍTMÉNY\_verziója

A használatához a függvényalkalmazást a Functions futtatókörnyezetének verzióját. A főverzió tilde azt jelenti, hogy jelentős 2-es verziójú (például "~") legújabb verzióját használja. Érhetők el ugyanazon új verziók, automatikusan települ a függvényalkalmazáshoz. Alkalmazás rögzítése a egy adott verziót, használja a teljes verziószám (például "2.0.12345"). Alapértelmezett érték a "~ 2". `~1` az alkalmazás a futtatókörnyezet 1. x verziójára való PIN-kód.

|Paraméter|Mintaérték|
|---|------------|
|FÜGGVÉNYEK\_BŐVÍTMÉNY\_verziója|KB. 2|

## <a name="functions_v2_compatibility_mode"></a>FÜGGVÉNYEK\_v2\_kompatibilitási\_mód

Ez a beállítás lehetővé teszi, hogy a Function alkalmazás 2. x kompatibilis módban fusson a 3. x verziójú futtatókörnyezetben. Ezt a beállítást csak akkor használja, ha problémák merülnek [fel a Function alkalmazás 2. x és 3. x verzióra való frissítésekor](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Ez a beállítás csak rövid távú megkerülő megoldás, ha úgy frissíti az alkalmazást, hogy megfelelően fusson a 3. x verzióban. Ez a beállítás akkor támogatott, ha a [2. x futtatókörnyezet támogatott](functions-versions.md). Ha olyan problémák merülnek fel, amelyek megakadályozzák, hogy az alkalmazás a 3. x verzión fusson anélkül, hogy ezt a beállítást használja, [jelentse a problémát](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Megköveteli, hogy a [függvények\_bővítmény\_verziójának](functions-app-settings.md#functions_extension_version) `~3`re legyen állítva.

|Paraméter|Mintaérték|
|---|------------|
|FÜGGVÉNYEK\_v2\_kompatibilitási\_mód|true|

## <a name="functions_worker_process_count"></a>FUNCTIONs\_WORKer\_folyamat\_DARABSZÁM

Meghatározza a nyelvi munkavégző folyamatok maximális számát `1`alapértelmezett értékkel. A maximálisan megengedett érték `10`. A függvények meghívása egyenletesen oszlik meg a nyelvi munkavégző folyamatok között. A nyelv munkavégző folyamatai 10 másodpercenként vannak elindítva, amíg a függvények száma\_a feldolgozó\_a folyamat\_a szám elérte a száma értéket. Több nyelvi feldolgozó folyamat használata nem ugyanaz, mint a [skálázás](functions-scale.md). Akkor érdemes ezt a beállítást használni, ha a számítási feladathoz CPU-kötésű és I/O-kötésű hívás is tartozik. Ez a beállítás az összes non-.NET nyelvre vonatkozik.

|Paraméter|Mintaérték|
|---|------------|
|FUNCTIONs\_WORKer\_folyamat\_DARABSZÁM|2|


## <a name="functions_worker_runtime"></a>FUNCTIONs\_WORKer\_RUNTIME

A nyelvi feldolgozói modul betöltése a függvényalkalmazáshoz.  Ez felel meg a nyelvet, az alkalmazásban (például "dotnet") használja. Functions több nyelven is szüksége lesz, ha közzétesszük őket, több alkalmazás, amelyek mindegyike egy megfelelő értéket a worker futásidejű.  Az érvényes értékek: `dotnet`C#(F#/), `node` (JavaScript/írógéppel), `java` (Java), `powershell` (PowerShell) és `python` (Python).

|Paraméter|Mintaérték|
|---|------------|
|FUNCTIONs\_WORKer\_RUNTIME|DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Csak a prémium csomagok fogyasztása &. A függvény kódját és konfigurációs tároló storage-fiókhoz tartozó kapcsolati karakterláncot. Lásd: [Function-alkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Paraméter|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [kulcs]|

## <a name="website_contentshare"></a>WEBHELY\_CONTENTSHARE

Csak a prémium csomagok fogyasztása &. A fájl elérési útja a függvény kódját és a konfiguráció. Az WEBSITE_CONTENTAZUREFILECONNECTIONSTRING használni. Alapértelmezés szerint egy egyedi karakterlánccá, amely a függvényalkalmazás neve kezdődik. Lásd: [Function-alkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Paraméter|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBHELY\_maximális\_dinamikus\_alkalmazás\_SKÁLÁZÁSi\_

A függvényalkalmazás lehet horizontálisan-példányok maximális száma. Alapértelmezett érték a nincs korlátozva.

> [!NOTE]
> Ez a beállítás nem egy előzetes verziójú funkció - és csak megbízható if értéke < = 5

|Paraméter|Mintaérték|
|---|------------|
|WEBHELY\_maximális\_dinamikus\_alkalmazás\_SKÁLÁZÁSi\_|5|

## <a name="website_node_default_version"></a>WEBHELY\_NODE\_DEFAULT_VERSION

_Csak Windows._  
Beállítja a Node. js azon verzióját, amelyet a Function alkalmazás Windows rendszeren való futtatásakor használ. Ha a futásidejű modult használja, használjon egy tilde (~) verziót a célként megadott főverzió legújabb elérhető verziójának használatára. Ha például az `~10`értékre van állítva, a Node. js 10 legújabb verziója van használatban. Ha egy főverziót egy tilde megcéloz, nem kell manuálisan frissítenie a másodlagos verziót. 

|Paraméter|Mintaérték|
|---|------------|
|WEBHELY\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBHELY\_\_futtatása\_CSOMAGból

A függvényalkalmazás egy csatlakoztatott alkalmazáscsomag-fájl futtatását engedélyezi.

|Paraméter|Mintaérték|
|---|------------|
|WEBHELY\_\_futtatása\_CSOMAGból|1|

Az érvényes értékek egy URL-cím, amely feloldja a központi telepítési csomag fájljának helyét, vagy `1`. Ha `1`re van állítva, a csomagnak a `d:\home\data\SitePackages` mappában kell lennie. Ezzel a beállítással telepítési zip használatakor a csomag automatikusan fel a rendszer ezen a helyen. Az előzetes verzióban a beállítás neve `WEBSITE_RUN_FROM_ZIP`. További információkért lásd: [függvények futtatása csomagfájl alapján](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Alapértelmezés szerint a Functions-proxyk egy API-hívások küldése a proxyk függvényei ugyanaz a Függvényalkalmazás ahelyett, hogy hozzon létre új HTTP-kérést közvetlenül a helyi rendszer használatára. Ezzel a beállítással lehetővé teszi, hogy tiltsa le ezt a viselkedést.

|Paraméter|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|A helyi függvényalkalmazás függvényére mutató háttér-URL-címmel rendelkező hívások a továbbiakban nem lesznek közvetlenül a függvénynek elküldve, és helyette a függvényalkalmazás a HTTP-kezelőfelületre lesznek irányítva.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Ez az alapértelmezett érték. A helyi függvényalkalmazás függvényére mutató háttérbeli URL-címmel rendelkező hívások közvetlenül erre a függvényre lesznek továbbítva|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Ez a beállítás szabályozza, hogy 2F. % dekódolni, perjeleket az útvonal-paraméterek, be a háttérkiszolgáló URL-címe behelyezésekor. 

|Paraméter|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Útvonal-paraméterek a kódolt perjeleket őket dekódolni fog rendelkezni. `example.com/api%2ftest` lesz `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Ez az az alapértelmezett viselkedést. Az összes útvonal paraméterek továbbít változatlan marad.|

### <a name="example"></a>Példa

Íme egy példa proxies.json a függvényalkalmazás az URL-cím myfunction.com:

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL-cím-dekódolás|Input (Bemenet)|Kimenet|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/API
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan frissítheti az alkalmazás beállításait](functions-how-to-use-azure-function-app-settings.md#settings)

[Lásd: globális beállítások a Host. JSON fájlban](functions-host-json.md)

[További Alkalmazásbeállítások App Service alkalmazásokhoz](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
