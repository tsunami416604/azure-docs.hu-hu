---
title: Azure Functions – alkalmazásbeállítási referencia
description: Az Azure Functions alkalmazás-beállítások vagy környezeti változók referenciadokumentációja.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656791"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions – alkalmazásbeállítási referencia

A függvényalkalmazások alkalmazásbeállításai olyan globális konfigurációs beállításokat tartalmaznak, amelyek hatással vannak az adott függvényalkalmazás összes funkciójára. Ha helyileg futtatja ezeket a beállításokat, a program helyi környezeti változóként éri el [ezeket a beállításokat.](functions-run-local.md#local-settings-file) Ez a cikk a függvényalkalmazásokban elérhető alkalmazásbeállításokat sorolja fel.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Vannak más globális konfigurációs beállítások is a [host.json](functions-host-json.md) fájlban és a [local.settings.json](functions-run-local.md#local-settings-file) fájlban.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Az Application Insights instrumentation kulcsa. Csak a `APPINSIGHTS_INSTRUMENTATIONKEY` vagy `APPLICATIONINSIGHTS_CONNECTIONSTRING`a egyikét használja. További információ: [Monitor Azure Functions](functions-monitoring.md). 

|Kulcs|Mintaérték|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Az Application Insights kapcsolati karakterlánca. Használja `APPLICATIONINSIGHTS_CONNECTIONSTRING` ahelyett, `APPINSIGHTS_INSTRUMENTATIONKEY` ha a függvényalkalmazás a kapcsolati karakterlánc által támogatott hozzáadott testreszabásokat igényel. További információt a [Kapcsolati karakterláncok](../azure-monitor/app/sdk-connection-string.md)című témakörben talál. 

|Kulcs|Mintaérték|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey=[key];IngestionEndpoint=[url]; LiveEndpoint=[url]; ProfilerEndpoint=[url]; SnapshotEndpoint=[url];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

A Functions futásidejű 2.x-es és újabb verzióiban az alkalmazás viselkedését a futásidejű környezet alapján konfigurálja. Ezt az értéket olvassa be a [beolvasás az inicializálás során.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43) Bármilyen értékre `AZURE_FUNCTIONS_ENVIRONMENT` beállítható, de három [érték](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) támogatott: [Fejlesztés](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Előkészítés](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)és Éles [.](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production) Ha `AZURE_FUNCTIONS_ENVIRONMENT` nincs beállítva, alapértelmezés `Development` szerint a helyi `Production` környezetben és az Azure-ban. Ezt a beállítást kell `ASPNETCORE_ENVIRONMENT` használni a futásidejű környezet beállítása helyett. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Választható tárfiók-kapcsolati karakterlánc a naplók tárolásához és megjelenítéséhez a portál **Figyelő** lapján. Ez a beállítás csak az Azure Functions futásidejű 1.x-es verzióját célzó alkalmazásokra érvényes. A tárfióknak általános célúnak kell lennie, amely támogatja a blobokat, a várólistákat és a táblákat. További információ: [Tárfiók követelményei.](storage-considerations.md#storage-account-requirements)

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https; AccountName=<name>; AccountKey =<key>|

> [!NOTE]
> A jobb teljesítmény és tapasztalat érdekében a 2.x-es és újabb `AzureWebJobsDashboard`verziójú runtime verziók APPINSIGHTS_INSTRUMENTATIONKEY és Az App Insights-ot használják a figyeléshez.

## <a name="azurewebjobsdisablehomepage"></a>Az AzureWebJobsDisableHome lap

`true`azt jelenti, hogy letilthatja a függvényalkalmazás gyökér URL-címéhez megjelenített alapértelmezett céloldalt. Az alapértelmezett szint a `false`.

|Kulcs|Mintaérték|
|---|------------|
|Az AzureWebJobsDisableHome lap|igaz|

Ha ez az alkalmazásbeállítás ki `false`van hagyva, vagy a beállítás a `<functionappname>.azurewebsites.net`beállítás , az URL-címre válaszul az alábbi példához hasonló oldal jelenik meg.

![A Függvényalkalmazás kezdőlapja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`a .NET kód fordításakor használja a Kiadási módot; `false` azt jelenti, hogy használja a Debug módot. Az alapértelmezett szint a `true`.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|igaz|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureJelzők

Az engedélyezendő bétafunkciók vesszővel tagolt listája. Az ilyen jelzők által engedélyezett bétafunkciók nem állnak készen a termelésre, de engedélyezhetők kísérleti használatra, mielőtt éleskörnyezetben lépnének.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsFeatureJelzők|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Megadja a kulcstároláshoz használandó tárházat vagy szolgáltatót. Jelenleg a támogatott tárolók blob storage ("Blob") és a helyi fájlrendszer ("Fájlok"). Az alapértelmezett a blob a 2-es verzióban és a fájlrendszer az 1-es verzióban.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsSecretStorageType|Fájlok|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Az Azure Functions futásidejű használja ezt a tárfiók kapcsolati karakterlánc ot az összes függvény, kivéve a HTTP-aktivált függvények. A tárfióknak általános célúnak kell lennie, amely támogatja a blobokat, a várólistákat és a táblákat. Lásd: [Tárfiók](functions-infrastructure-as-code.md#storage-account) és [tárfiók követelmények.](storage-considerations.md#storage-account-requirements)

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https; AccountName=[name]; AccountKey=[kulcs]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

A TypeScript-hez használt fordító elérési útja. Lehetővé teszi az alapértelmezett felülbírálását, ha szükséges.

|Kulcs|Mintaérték|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\betűparancsfájl|

## <a name="function_app_edit_mode"></a>\_FÜGGVÉNYALKALMAZÁS\_SZERKESZTÉSI\_MÓDJA

Azt határozza meg, hogy engedélyezve van-e a szerkesztés az Azure Portalon. Az érvényes értékek az "írás" és a "csak olvasható" értékek.

|Kulcs|Mintaérték|
|---|------------|
|\_FÜGGVÉNYALKALMAZÁS\_SZERKESZTÉSI\_MÓDJA|csak olvasható|

## <a name="functions_extension_version"></a>\_FÜGGVÉNYBŐVÍTMÉNY\_VERZIÓJA

A függvényalkalmazásban használandó Functions futásidejű verzió. A főverzióval rendelkező tilde azt jelenti, hogy az adott főverzió legújabb verzióját használjuk (például "~2"). Ha ugyanahhoz a főverzióhoz új verziók érhetők el, azok automatikusan települnek a függvényalkalmazásban. Az alkalmazás egy adott verzióhoz való rögzítéséhez használja a teljes verziószámot (például "2.0.12345"). Az alapértelmezett érték a "~2". Az alkalmazás `~1` értéke a futásidejű 1.x verzióhoz.

|Kulcs|Mintaérték|
|---|------------|
|\_FÜGGVÉNYBŐVÍTMÉNY\_VERZIÓJA|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNKCIÓK\_V2\_\_KOMPATIBILITÁSI MÓD

Ez a beállítás lehetővé teszi, hogy a függvényalkalmazás 2.x-es verziójú kompatibilis módban fusson a 3.x-es verziójú futásidőben. Ezt a beállítást csak akkor használja, ha problémákmerülnek fel [a függvényalkalmazás futásidejű 2.x-ről 3.x-es verziójára történő frissítésekor.](functions-versions.md#migrating-from-2x-to-3x) 

>[!IMPORTANT]
> Ez a beállítás csak rövid távú megoldásként szolgál, miközben frissíti az alkalmazást, hogy megfelelően fusson a 3.x-es verzión. Ez a beállítás mindaddig támogatott, amíg a [2.x futásidő támogatott.](functions-versions.md) Ha olyan problémákkal szembesül, amelyek megakadályozzák, hogy az alkalmazás a 3.x-es verzión fusson a beállítás használata nélkül, kérjük, [jelentse a problémát.](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)

A [FUNCTIONS\_\_EXTENSION VERZIÓ BEÁLLÍTÁSát](functions-app-settings.md#functions_extension_version) a beállításra `~3`kell állítani.

|Kulcs|Mintaérték|
|---|------------|
|FUNKCIÓK\_V2\_\_KOMPATIBILITÁSI MÓD|igaz|

## <a name="functions_worker_process_count"></a>\_FÜGGVÉNYEK\_\_MUNKAVÉGZŐ FOLYAMATOK SZÁMA

Megadja a nyelvi munkavégző folyamatok maximális számát, `1`amelynek alapértelmezett értéke . A megengedett maximális `10`érték . A függvénymeghívások egyenletesen oszlanak meg a nyelvi munkavégző folyamatok között. A nyelvi munkavégző folyamatok 10 másodpercenként jönnek létre,\_\_amíg el nem éri a FUNCTIONS\_WORKER PROCESS COUNT által beállított számlálót. Több nyelvi munkavégző folyamat használata nem ugyanaz a [méretezéssel.](functions-scale.md) Érdemes ezt a beállítást használni, ha a számítási feladatok processzorhoz és I/O-hoz kötött meghívások vegyesen rendelkeznek. Ez a beállítás az összes non-.NET nyelvre vonatkozik.

|Kulcs|Mintaérték|
|---|------------|
|\_FÜGGVÉNYEK\_\_MUNKAVÉGZŐ FOLYAMATOK SZÁMA|2|


## <a name="functions_worker_runtime"></a>\_FÜGGVÉNYEK\_FELDOLGOZÓI FUTÁSIDEJŰ

A függvényalkalmazásban betölthető nyelvi munkavégző futásidő.  Ez megfelel az alkalmazásban használt nyelvnek (például "dotnet"). Több nyelvű függvények esetén több alkalmazásban is közzé kell tennie őket, amelyek mindegyike rendelkezik egy megfelelő munkavégző futásidejű értékkel.  Érvényes értékek `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` `powershell` (Java), (PowerShell) és `python` (Python).

|Kulcs|Mintaérték|
|---|------------|
|\_FÜGGVÉNYEK\_FELDOLGOZÓI FUTÁSIDEJŰ|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Csak a felhasználás & prémium csomagok esetén. Kapcsolati karakterlánc a függvényalkalmazás kódját és konfigurációját tároló tárfiókhoz. Lásd: [Függvényalkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https; AccountName=[name]; AccountKey=[kulcs]|

## <a name="website_contentshare"></a>WEBHELY\_TARTALOMMEGOSZTÁSA

Csak a felhasználás & prémium csomagok esetén. A függvényalkalmazás kódjának és konfigurációjának fájlelérési útja. Használt WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Az alapértelmezett egy egyedi karakterlánc, amely a függvényalkalmazás nevével kezdődik. Lásd: [Függvényalkalmazás létrehozása](functions-infrastructure-as-code.md#create-a-function-app).

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_\_MAX\_\_DINAMIKUS\_ALKALMAZÁS HORIZONTÁLIS FELSKÁLÁZÁSA

A függvényalkalmazás által kiskálázható példányok maximális száma. Az alapértelmezett érték nincs korlátozás.

> [!NOTE]
> Ez a beállítás egy előnézeti funkció - és csak akkor megbízható, ha értéke <= 5

|Kulcs|Mintaérték|
|---|------------|
|WEBSITE\_\_MAX\_\_DINAMIKUS\_ALKALMAZÁS HORIZONTÁLIS FELSKÁLÁZÁSA|5|

## <a name="website_node_default_version"></a>HONLAP\_NODE\_DEFAULT_VERSION

_Csak Windows._  
Beállítja a Node.js verzióját, amelyet a függvényalkalmazás Windows rendszeren való futtatásakor használ. Tilde (~) használatával a futásidejű a megcélzott főverzió legújabb elérhető verzióját használja. Ha például a `~10`beállítás a beállítás, a Node.js 10 legújabb verzióját használja a beállítás. Ha egy főverzió tilde-vel van megcélozva, nem kell manuálisan frissítenie az alverziót. 

|Kulcs|Mintaérték|
|---|------------|
|HONLAP\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBOLDAL\_\_FUTTATÁSA CSOMAGBÓL\_

Lehetővé teszi, hogy a függvényalkalmazás csatlakoztatott csomagfájlból fusson.

|Kulcs|Mintaérték|
|---|------------|
|WEBOLDAL\_\_FUTTATÁSA CSOMAGBÓL\_|1|

Az érvényes értékek vagy egy központi telepítési csomagfájl helyére `1`feloldó URL-cím, vagy a . Ha `1`a beállítás a , `d:\home\data\SitePackages` a csomagnak a mappában kell lennie. Ha ezzel a beállítással használja a zip-telepítést, a csomag automatikusan erre a helyre kerül feltöltésre. Az előzetes verzióban `WEBSITE_RUN_FROM_ZIP`ez a beállítás a neve . További információt [a Függvények futtatása csomagfájlból](run-functions-from-deployment-package.md)című témakörben talál.

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Alapértelmezés szerint a Functions proxyk egy parancsikont használnak az API-hívások küldéséhez a proxyktól közvetlenül az azonos Függvényalkalmazás függvényeibe, ahelyett, hogy új HTTP-kérelmet hozna létre. Ezzel a beállítással letilthatja ezt a viselkedést.

|Kulcs|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|igaz|A helyi függvényalkalmazás egy függvényére mutató háttérURL-lel rendelkező hívások at a továbbiakban nem küldi el közvetlenül a függvény, hanem a függvényalkalmazás HTTP előtér-végpontjára irányítja vissza.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|hamis|Ez az alapértelmezett érték. A helyi függvényalkalmazás egy függvényére mutató háttérURL-lel rendelkező hívásokat a függvény közvetlenül az adott függvényhez továbbítja.|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Ez a beállítás szabályozza, hogy a(z) %2F dekódolva legyen-e perjelként az útvonalparaméterekben, amikor bekerül a háttérbeli URL-be. 

|Kulcs|Érték|Leírás|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|igaz|A kódolt perjellel rendelkező útvonalparaméterek dekódolják őket. `example.com/api%2ftest`lesz`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|hamis|Ez az alapértelmezett viselkedés. Az összes útvonalparaméter változatlanul halad tovább|

### <a name="example"></a>Példa

Íme egy példa proxies.json egy függvényalkalmazásban az URL-myfunction.com

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

[További információ az alkalmazásbeállítások frissítéséhez](functions-how-to-use-azure-function-app-settings.md#settings)

[Globális beállítások megtekintése a host.json fájlban](functions-host-json.md)

[Az App Service-alkalmazások egyéb alkalmazásbeállításainak megtekintése](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
