---
title: Nulla állásidő üzembe helyezése Durable Functions
description: Megtudhatja, hogyan engedélyezheti Durable Functions előkészítését az állásidő nélküli üzembe helyezésekhez.
services: functions
author: tsushi
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: b47604f2c8703ba587e98d68dc30552e5944f562
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614503"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Nulla állásidő üzembe helyezése Durable Functions

A Durable Functions [megbízható végrehajtási modellje](durable-functions-checkpointing-and-replay.md) megköveteli, hogy a rendszer determinisztikus, ami egy további kihívást jelent, amelyet a frissítések központi telepítésekor figyelembe kell venni. Ha egy központi telepítés a tevékenységi függvények aláírásait vagy a Orchestrator logikát tartalmazza, a fedélzeti előkészítési példányok meghiúsulnak. Ez a helyzet különösen a hosszan futó munkafolyamatok példányai esetében fordul elő, amely órákat vagy munkanapokat is jelenthet.

A hibák megelőzése érdekében vagy késleltetni kell a telepítést, amíg az összes futó előkészítési példány be nem fejeződik, vagy győződjön meg arról, hogy a futó előkészítési példányok a függvények meglévő verzióit használják. További információ a verziószámozásról: [verziószámozás a Durable Functionsban](durable-functions-versioning.md).

> [!NOTE]
> Ez a cikk útmutatást nyújt a functions-alkalmazásokhoz Durable Functions 1. x. Még nem frissült a Durable Functions 2. x verzióban bevezetett változások miatt. A bővítmény-verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

Az alábbi táblázat összehasonlítja a három fő stratégiát, amelyekkel a Durable Functions nulla állásidőt lehet elérni: 

| Stratégia |  A következő esetekben használja | Szakemberek | Hátrányok |
| -------- | ------------ | ---- | ---- |
| **[Verziószámozás](#versioning)** |  Olyan alkalmazások, amelyek nem tapasztalnak gyakori [feltörési változásokat.](durable-functions-versioning.md) | Egyszerűen implementálható. |  A Function app-méret növelése a memóriában és a függvények száma.<br/>A kód duplikálása. |
| **[Állapot-ellenőrzési tárolóhelytel](#status-check-with-slot)** | Olyan rendszer, amely nem rendelkezik a 24 óránál hosszabb ideig futó, vagy gyakran átfedésben lévő előkészítési folyamatokkal. | Egyszerű kód alapja.<br/>Nincs szükség további Function app-felügyeletre. | További Storage-fiókot vagy a Task hub felügyeletét igényli.<br/>Olyan időszakot igényel, amikor a rendszer nem fut. |
| **[Alkalmazás-Útválasztás](#application-routing)** | Olyan rendszer, amely nem rendelkezik olyan időtartammal, amely nem fut, mint például a 24 óránál hosszabb ideig tartó vagy a gyakran átfedésben lévő Összehangolók. | Kezeli a rendszerek új verzióit, és folyamatosan futó, a változásokat megsértő munkafolyamatokat. | Intelligens alkalmazás-útválasztót igényel.<br/>Az előfizetés által engedélyezett Function Apps-alkalmazások maximális száma (alapértelmezett 100). |

## <a name="versioning"></a>Verziókezelés

Adja meg a függvények új verzióit, és hagyja meg a régi verziókat a Function alkalmazásban. Ahogy az ábrán látható, a függvény verziója a neve részévé válik. Mivel a függvények korábbi verziói megmaradnak, a fedélzeti előkészítési példányok továbbra is hivatkozhatnak rájuk. Eközben az új előkészítési példányokra vonatkozó kérések a legújabb verziót kérik, amelyet a rendszerelőkészítési ügyfél függvénye hivatkozhat az alkalmazás beállításától függően.

![Verziószámozási stratégia](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Ebben a stratégiában minden függvényt át kell másolni, és a más funkciókra való hivatkozásokat frissíteni kell. Egyszerűbbé teheti a parancsfájlok írását. Itt látható egy áttelepítési parancsfájlt tartalmazó [minta projekt](https://github.com/TsuyoshiUshio/DurableVersioning) .

>[!NOTE]
>Ez a stratégia üzembe helyezési résidőket használ az üzembe helyezés során felmerülő állásidő elkerüléséhez. Az új üzembe helyezési pontok létrehozásáról és használatáról az [Azure functions üzembe helyezési](../functions-deployment-slots.md)pontok című témakörben olvashat részletesebben.

## <a name="status-check-with-slot"></a>Állapot-ellenőrzési tárolóhelytel

Amíg a Function alkalmazás aktuális verziója fut az üzemi tárolóhelyen, a Function app új verzióját üzembe helyezheti az átmeneti tárolóhelyen. Az éles üzemi és átmeneti tárolóhelyek cseréje előtt ellenőrizze, hogy fut-e a rendszer. Az összes előkészítési példány befejezése után elvégezheti a cserét. Ez a stratégia akkor működik, ha előre jelezhető időszakok állnak rendelkezésre, ha nincsenek előkészítési példányok a repülésben. Ez az a legjobb megoldás, ha a rendszer nem működik, és ha az előkészítési műveletek nem gyakran átfedésben vannak.

### <a name="function-app-configuration"></a>Function app-konfiguráció

Az alábbi eljárást követve állíthatja be a forgatókönyvet:

1. [Üzembe helyezési pontok hozzáadása](../functions-deployment-slots.md#add-a-slot) a Function alkalmazáshoz átmeneti és éles környezetben.

1. Az egyes tárolóhelyek esetében állítsa be a [AzureWebJobsStorage alkalmazás beállítását](../functions-app-settings.md#azurewebjobsstorage) egy megosztott Storage-fiók kapcsolódási karakterláncára. Ezt a Azure Functions futtatókörnyezet fogja használni. Ezt a fiókot a Azure Functions futtatókörnyezet fogja használni, és a függvény kulcsait kezeli.

1. Az egyes tárolóhelyek esetében hozzon létre egy új Alkalmazásbeállítás (például:. DurableManagementStorage), és állítsa be az értékét a különböző Storage-fiókok kapcsolati karakterláncára. Ezeket a Storage-fiókokat a Durable Functions-bővítmény fogja használni a [megbízható végrehajtáshoz](durable-functions-checkpointing-and-replay.md). Mindegyik tárolóhelyhez külön Storage-fiókot használjon. Ne jelölje be a beállítást üzembe helyezési tárolóhelyként beállításként.

1. A Function app [Host. JSON fájljának durableTask szakaszában](durable-functions-bindings.md#hostjson-settings)adja meg a azureStorageConnectionStringName nevet a 3. lépésben létrehozott Alkalmazásbeállítás neveként.

Az alábbi ábra az üzembe helyezési pontok és a Storage-fiókok leírt konfigurációját mutatja be. Ebben a lehetséges előzetes üzembe helyezési forgatókönyvben a functions-alkalmazás 2. verziója az üzemi tárolóhelyen fut, míg az 1. verzió az átmeneti tárolóhelyen marad.

![Üzembehelyezési pont](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>Host. JSON példák

A következő JSON-töredékek a Host. JSON fájlban található kapcsolatok karakterlánc-beállításra mutatnak példákat.

#### <a name="functions-20"></a>Függvények 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "DurableManagementStorage"
    }
  }
}
```

#### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>CI/CD-folyamat konfigurációja

Konfigurálja a CI/CD-folyamatot úgy, hogy csak akkor telepítsen, ha a Function alkalmazás nem rendelkezik függőben lévő vagy nem futó munkafolyamati példányokkal. Az Azure-folyamatok használatakor létrehozhat egy függvényt, amely ezeket a feltételeket ellenőrzi, ahogy az alábbi példában is látható:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var status = await client.GetStatusAsync(new DateTime(2015,10,10), null, runtimeStatus);
    return (ActionResult) new OkObjectResult(new Status() {HasRunning = (status.Count != 0)});
}
```

Ezután konfigurálja az átmeneti kaput, és várjon, amíg a rendszer nem fut. További információ: üzembehelyezési [üzembe helyezési vezérlő kiadása Gates használatával](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Üzembe helyezési kapu](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Az Azure-folyamatok az üzembe helyezés megkezdése előtt ellenőrzik a Function alkalmazást a rendszer-előkészítési példányok futtatásához.

![Üzembe helyezési kapu (fut)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Most a Function alkalmazás új verzióját kell telepíteni az átmeneti tárolóhelyre.

![Üzembehelyezési pont](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Végül cserélje le a tárolóhelyeket. 

Az üzembe helyezési pont beállításainak meg nem jelölt Alkalmazásbeállítások is felcserélve lesznek, így a 2-es verziójú alkalmazás az A Storage-fiókra hivatkozik. Mivel a rendszer a hangfelvételi állapotot a Storage-fiókban követi nyomon, a 2. verziójú alkalmazáson futó összes előkészítési művelet megszakítás nélkül fut az új tárolóhelyen.

![Üzembehelyezési pont](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Ha ugyanazt a Storage-fiókot szeretné használni mindkét tárolóhelyhez, módosíthatja a feladatok hubok nevét. Ebben az esetben a tárolóhelyek állapotát és az alkalmazások HubName beállításait kell kezelnie. További információ: [Durable Functionsban található feladatok hubok](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Alkalmazás-Útválasztás

Ez a stratégia a legbonyolultabb. Azonban olyan Function-alkalmazásokhoz is használható, amelyek nem rendelkeznek időponttal a futók között.

Ehhez a stratégiához létre kell hoznia egy *alkalmazás-útválasztót* a Durable functions előtt. Ezt az útválasztót Durable Functions használatával lehet megvalósítani, és a következő feladatai vannak:

* A Function alkalmazás üzembe helyezése.
* Durable Functions verziójának kezelése. 
* Útválasztási előkészítési kérelmek az alkalmazások működéséhez.

Amikor a rendszer első alkalommal egy előkészítési kérést kap, az útválasztó végzi a feladatokat:

1. Létrehoz egy új Function alkalmazást az Azure-ban.
2. Üzembe helyezi a Function alkalmazás kódját az új Function alkalmazásban az Azure-ban.
3. Továbbítja a előkészítési kérelmet az új alkalmazásnak.

Az útválasztó felügyeli, hogy az alkalmazás kódjának melyik verziója van telepítve az Azure-beli Function alkalmazáshoz.

![Alkalmazás-útválasztás (első alkalommal)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Az útválasztó az üzembe helyezési és-előkészítési kérelmeket a kéréssel elküldött `version` alapján irányítja a megfelelő függvény alkalmazásba, figyelmen kívül hagyva a javítás verzióját.

Ha új verziót helyez üzembe az alkalmazásban, és ez *nem* módosul, megnövelheti a javítási verziót. Az útválasztó üzembe helyezi a meglévő Function alkalmazást, és a kód régi és új verzióira vonatkozó kérelmeket küldi át ugyanahhoz a Function alkalmazáshoz.

![Alkalmazás-útválasztás (nincs megszakítási változás)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Ha új verziót helyez üzembe az alkalmazásban, akkor megnövelheti a fő-vagy alverziót. Ezután az alkalmazás-útválasztó létrehoz egy új Function alkalmazást az Azure-ban, üzembe helyezi azt, és átirányítja az alkalmazás új verziójára irányuló kérelmeket. Az alábbi ábrán az alkalmazás 1.0.1-es verziójában futó rendszerindító fut, de a 1.1.0-verzióra vonatkozó kérések az új Function alkalmazásba lesznek irányítva.

![Alkalmazás-útválasztás (megszakítási változás)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Az útválasztó a (z) 1.0.1-es verziójában figyeli a munkafolyamatok állapotát, és eltávolítja az alkalmazásokat az összes előkészítés befejezése után.  

### <a name="tracking-store-settings"></a>Nyomkövetési tároló beállításai

Mindegyik Function alkalmazásnak külön ütemezési várólistákat kell használnia, valószínűleg külön Storage-fiókokban. Ha azonban az alkalmazás összes verziójában le szeretné kérdezni az összes előkészítési példányt, megoszthatja a példányok és az előzmények táblázatait a Function apps szolgáltatásban. A táblák megosztásához konfigurálja a `trackingStoreConnectionStringName` és `trackingStoreNamePrefix` a [Host. JSON-beállítási](durable-functions-bindings.md#host-json) fájlban, hogy mindegyik ugyanazt az értéket használja.

További részletekért [Durable functions Azure-beli példányait kezelheti](durable-functions-instance-management.md).

![Nyomkövetési tároló beállításai](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Verziószámozás Durable Functions](durable-functions-versioning.md)

