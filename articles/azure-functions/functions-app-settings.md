---
title: Azure Functions – alkalmazásbeállítási referencia
description: A Azure Functions Alkalmazásbeállítások vagy környezeti változók dokumentációja.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656791"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions – alkalmazásbeállítási referencia

A Function alkalmazásban az Alkalmazásbeállítások olyan globális konfigurációs beállításokat tartalmaznak, amelyek az adott Function alkalmazás összes funkcióját érintik. Helyileg futtatva ezek a beállítások helyi [környezeti változókként](functions-run-local.md#local-settings-file)érhetők el. Ez a cikk a Function apps alkalmazásban elérhető Alkalmazásbeállítások listáját tartalmazza.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

A [Host. JSON](functions-host-json.md) fájlban és a [Local. Settings. JSON](functions-run-local.md#local-settings-file) fájlban más globális konfigurációs lehetőségek is vannak.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights rendszerállapot-kulcsa. Csak a vagy `APPLICATIONINSIGHTS_CONNECTIONSTRING`a `APPINSIGHTS_INSTRUMENTATIONKEY` valamelyikét használja. További információ: [Azure functions figyelése](functions-monitoring.md). 

|Kulcs|Mintaérték|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

A Application Insightshoz tartozó kapcsolatok karakterlánca. Akkor `APPLICATIONINSIGHTS_CONNECTIONSTRING` használja, `APPINSIGHTS_INSTRUMENTATIONKEY` ha a Function alkalmazáshoz a kapcsolati sztring használatával támogatott további testreszabások szükségesek. További információ: a [kapcsolatok karakterláncai](../azure-monitor/app/sdk-connection-string.md). 

|Kulcs|Mintaérték|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey = [kulcs]; IngestionEndpoint = [URL]; LiveEndpoint = [URL]; ProfilerEndpoint = [URL]; SnapshotEndpoint = [URL];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

A functions futtatókörnyezet 2. x vagy újabb verziójában a futásidejű környezet alapján konfigurálja az alkalmazások viselkedését. Az [inicializálás során](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)ez az érték olvasható. `AZURE_FUNCTIONS_ENVIRONMENT` Bármelyik értéket megadhatja, de [három érték](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) támogatott: [fejlesztés](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [előkészítés](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)és [éles környezet](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Ha `AZURE_FUNCTIONS_ENVIRONMENT` nincs beállítva, az alapértelmezett `Development` érték egy helyi környezetben és `Production` az Azure-ban. Ezt a beállítást a futásidejű környezet beállítása `ASPNETCORE_ENVIRONMENT` helyett kell használni. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcionális Storage-fiókhoz tartozó kapcsolatok karakterlánca a naplók tárolásához és megjelenítéséhez a portál **figyelés** lapján. Ez a beállítás csak olyan alkalmazások esetén érvényes, amelyek az Azure Functions futtatókörnyezet 1. x verzióját célozzák meg. A Storage-fióknak olyan általános célúnak kell lennie, amely támogatja a blobokat, a várólistákat és a táblákat. További információ: a [Storage-fiókra vonatkozó követelmények](storage-considerations.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName =<name>; AccountKey =<key>|

> [!NOTE]
> A jobb teljesítmény és a használhatóság érdekében a Runtime 2. x vagy újabb verziója a APPINSIGHTS_INSTRUMENTATIONKEY és az alkalmazás-ellenőrzési funkciókat használja a `AzureWebJobsDashboard`figyeléshez.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`azt jelenti, hogy le kell tiltania az alapértelmezett kezdőlapot, amely megjelenik a Function app gyökér URL-címénél. Az alapértelmezett szint a `false`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDisableHomepage|igaz|

Ha ezt az alkalmazást kihagyja vagy beállítja `false`, a következő példához hasonló oldal jelenik meg az URL-címre `<functionappname>.azurewebsites.net`adott válaszban.

![Function app kezdőlapja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`azt jelenti, hogy a kiadási módot használja a .NET-kód fordításakor; `false` hibakeresési módot használ. Az alapértelmezett szint a `true`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|igaz|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Az engedélyezni kívánt béta-funkciók vesszővel tagolt listája. A jelzők által engedélyezett béta-funkciók nem állnak készen a gyártásra, de a kísérleti használatra engedélyezhető a működésük előtt.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Megadja a kulcstárolóhoz használandó tárházat vagy szolgáltatót. Jelenleg a támogatott adattárak a blob Storage (blob) és a helyi fájlrendszer ("fájlok"). Az alapértelmezett érték a blob a 2. és a fájlrendszerben az 1. verzióban.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|Fájlok|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

A Azure Functions futtatókörnyezet ezt a Storage-fiókhoz tartozó kapcsolatok sztringjét használja az összes függvényhez, kivéve a HTTP által aktivált függvények esetében. A Storage-fióknak olyan általános célúnak kell lennie, amely támogatja a blobokat, a várólistákat és a táblákat. Lásd a [Storage-fiókra](functions-infrastructure-as-code.md#storage-account) és a [Storage-fiókra vonatkozó követelményeket](storage-considerations.md#storage-account-requirements).

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [név]; AccountKey = [kulcs]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Az írógéppel használt fordító elérési útja. Lehetővé teszi az alapértelmezett érték felülbírálását, ha szükséges.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNKCIÓ\_alkalmazás\_szerkesztési\_módja

Azt diktálja, hogy engedélyezve van-e a Azure Portal szerkesztése. Az érvényes értékek: "READWRITE" és "ReadOnly".

|Kulcs|Mintaérték|
|---|------------|
|FUNKCIÓ\_alkalmazás\_szerkesztési\_módja|ReadOnly|

## <a name="functions_extension_version"></a>\_functions\_bővítmény verziója

A Function alkalmazásban használni kívánt functions futtatókörnyezet verziója. A nagyobb verziószámú tilde a főverzió legújabb verzióját használja (például "~ 2"). Ha az azonos főverzióhoz tartozó új verziók elérhetők, a rendszer automatikusan telepíti őket a Function alkalmazásba. Az alkalmazás egy adott verzióra való rögzítéséhez használja a teljes verziószámot (például "2.0.12345"). Az alapértelmezett érték a "~ 2". Az alkalmazás a `~1` futtatókörnyezet 1. x verziójára való PIN-kód.

|Kulcs|Mintaérték|
|---|------------|
|\_functions\_bővítmény verziója|~ 2|

## <a name="functions_v2_compatibility_mode"></a>\_Functions\_v2\_kompatibilitási mód

Ez a beállítás lehetővé teszi, hogy a Function alkalmazás 2. x kompatibilis módban fusson a 3. x verziójú futtatókörnyezetben. Ezt a beállítást csak akkor használja, ha problémák merülnek [fel a Function alkalmazás 2. x és 3. x verzióra való frissítésekor](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Ez a beállítás csak rövid távú megkerülő megoldás, ha úgy frissíti az alkalmazást, hogy megfelelően fusson a 3. x verzióban. Ez a beállítás akkor támogatott, ha a [2. x futtatókörnyezet támogatott](functions-versions.md). Ha olyan problémák merülnek fel, amelyek megakadályozzák, hogy az alkalmazás a 3. x verzión fusson anélkül, hogy ezt a beállítást használja, [jelentse a problémát](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Megköveteli, hogy a [functions\_bővítmény\_verziója](functions-app-settings.md#functions_extension_version) legyen beállítva. `~3`

|Kulcs|Mintaérték|
|---|------------|
|\_Functions\_v2\_kompatibilitási mód|igaz|

## <a name="functions_worker_process_count"></a>\_functions\_munkavégző folyamatok\_száma

Meghatározza a nyelv munkavégző folyamatainak maximális számát, alapértelmezett értékkel `1`. A megengedett maximális érték: `10`. A függvények meghívása egyenletesen oszlik meg a nyelvi munkavégző folyamatok között. A nyelv munkavégző folyamatai 10 másodpercenként jönnek létre, amíg el nem éri a\_\_functions\_munkavégző folyamatának száma. Több nyelvi feldolgozó folyamat használata nem ugyanaz, mint a [skálázás](functions-scale.md). Akkor érdemes ezt a beállítást használni, ha a számítási feladathoz CPU-kötésű és I/O-kötésű hívás is tartozik. Ez a beállítás az összes non-.NET nyelvre vonatkozik.

|Kulcs|Mintaérték|
|---|------------|
|\_functions\_munkavégző folyamatok\_száma|2|


## <a name="functions_worker_runtime"></a>\_functions\_Worker futtatókörnyezet

A Function alkalmazásban betölteni kívánt nyelvi feldolgozó futtatókörnyezet.  Ez megfelel az alkalmazásban használt nyelvnek (például "DotNet"). A függvények több nyelven is közzétehető több alkalmazásban, amelyek mindegyike egy megfelelő munkavégző futásidejű értékkel rendelkezik.  Érvényes értékek: `dotnet` (C#/f #), `node` (JavaScript/írógéppel), `java` (Java), `powershell` (PowerShell) és `python` (Python).

|Kulcs|Mintaérték|
|---|------------|
|\_functions\_Worker futtatókörnyezet|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Csak a prémium csomagok fogyasztása &. A Storage-fiókhoz tartozó, a Function app-kód és a konfiguráció tárolására szolgáló hálózati karakterlánc. Lásd: [Function-alkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [név]; AccountKey = [kulcs]|

## <a name="website_contentshare"></a>WEBHELY\_CONTENTSHARE

Csak a prémium csomagok fogyasztása &. A függvény alkalmazás kódjának és konfigurációjának elérési útja. WEBSITE_CONTENTAZUREFILECONNECTIONSTRING használatával használható. Az alapértelmezett érték egy egyedi karakterlánc, amely a Function alkalmazás nevével kezdődik. Lásd: [Function-alkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>a\_webhely\_maximális\_dinamikus\_alkalmazás\_-felskálázása

Azon példányok maximális száma, amelyeket a Function alkalmazás fel tud skálázásra. Az alapértelmezett érték nem korlát.

> [!NOTE]
> Ez a beállítás egy előzetes verziójú szolgáltatás, és csak akkor megbízható, ha értékre van állítva <= 5

|Kulcs|Mintaérték|
|---|------------|
|a\_webhely\_maximális\_dinamikus\_alkalmazás\_-felskálázása|5|

## <a name="website_node_default_version"></a>WEBHELY\_-\_csomópont DEFAULT_VERSION

_Csak Windows._  
Beállítja a Node. js azon verzióját, amelyet a Function alkalmazás Windows rendszeren való futtatásakor használ. Ha a futásidejű modult használja, használjon egy tilde (~) verziót a célként megadott főverzió legújabb elérhető verziójának használatára. Ha például a értékre `~10`van állítva, a rendszer a Node. js 10 legújabb verzióját használja. Ha egy főverziót egy tilde megcéloz, nem kell manuálisan frissítenie a másodlagos verziót. 

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_-\_csomópont DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBHELY\_futtatása\_\_csomagból

Lehetővé teszi, hogy a Function alkalmazás egy csatlakoztatott csomagfájl használatával fusson.

|Kulcs|Mintaérték|
|---|------------|
|WEBHELY\_futtatása\_\_csomagból|1|

Az érvényes értékek egy URL-cím, amely feloldja a központi telepítési csomag fájljának helyét, vagy `1`. Ha a értékre `1`van állítva, a csomagnak `d:\home\data\SitePackages` a mappában kell lennie. Ha a zip-telepítést ezzel a beállítással használja, a csomag automatikusan erre a helyre lesz feltöltve. Az előzetes verzióban ez a beállítás `WEBSITE_RUN_FROM_ZIP`neve. További információkért lásd: [függvények futtatása csomagfájl alapján](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Alapértelmezés szerint a függvények proxyi olyan parancsikont használnak, amely az API-hívásokat közvetlenül az azonos függvényalkalmazás lévő függvények számára küldi el, nem pedig új HTTP-kérést hoz létre. Ezzel a beállítással letilthatja ezt a viselkedést.

|Kulcs|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|igaz|A helyi függvényalkalmazás függvényére mutató háttér-URL-címmel rendelkező hívások a továbbiakban nem lesznek közvetlenül a függvénynek elküldve, és helyette a függvényalkalmazás a HTTP-kezelőfelületre lesznek irányítva.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|hamis|Ez az alapértelmezett érték. A helyi függvényalkalmazás függvényére mutató háttérbeli URL-címmel rendelkező hívások közvetlenül erre a függvényre lesznek továbbítva|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Ezzel a beállítással megadható, hogy a (z)% 2F dekódolva van-e a háttérbeli URL-címekbe illesztett útvonal-paraméterekben. 

|Kulcs|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|igaz|A kódolt perjelekkel ellátott útválasztási paraméterek dekódolva lesznek. `example.com/api%2ftest`lesz`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|hamis|Ez az alapértelmezett viselkedés. A rendszer az összes útvonal paraméterét változatlanul adja át|

### <a name="example"></a>Példa

Íme egy példa a proxys. JSON fájlra az URL-myfunction.com található Function alkalmazásban.

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
|URL-dekódolás|Input (Bemenet)|Kimenet|
|-|-|-|
|igaz|myfunction.com/test%2fapi|example.com/test/api
|hamis|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan frissítheti az alkalmazás beállításait](functions-how-to-use-azure-function-app-settings.md#settings)

[Lásd: globális beállítások a Host. JSON fájlban](functions-host-json.md)

[További Alkalmazásbeállítások App Service alkalmazásokhoz](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
