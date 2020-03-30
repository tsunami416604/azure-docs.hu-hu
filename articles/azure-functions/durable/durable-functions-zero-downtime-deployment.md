---
title: Nulla állásidő-telepítés a tartós függvényekhez
description: Ismerje meg, hogyan engedélyezheti a durable functions vezénylési nulla állásidő-üzemelő példányok.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.openlocfilehash: 8e12d58c0077084c181d111b0b017665b74b9157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231256"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Nulla állásidő-telepítés a tartós függvényekhez

A tartós függvények [megbízható végrehajtási modellje](durable-functions-checkpointing-and-replay.md) megköveteli, hogy a vezénylések determinisztikusak legyenek, ami további kihívást jelent a frissítések telepítésekor. Ha egy központi telepítés a tevékenységfüggvény-aláírások vagy az orchestrator logika módosításait tartalmazza, a repülés közbeni vezénylési példányok sikertelenek. Ez a helyzet különösen a hosszú ideig futó vezénylések esetében jelent problémát, amelyek órákat vagy munkanapokat jelenthetnek.

A hibák előfordulásának megakadályozásához két lehetősége van: 
- Az üzembe helyezés késleltetése, amíg az összes futó vezénylési példány befejeződik.
- Győződjön meg arról, hogy minden futó vezénylési példányok a függvények meglévő verzióit használja. 

> [!NOTE]
> Ez a cikk útmutatást nyújt a tartós függvényeket 1.x-et célzó függvényalkalmazásokhoz. Nem lett frissítve, hogy figyelembe vegye a Durable Functions 2.x-ben bevezetett módosításokat. A bővítményverziók közötti különbségekről a [Durable Functions verziók című](durable-functions-versions.md)témakörben talál további információt.

Az alábbi táblázat a három fő stratégiát hasonlítja össze a tartós függvények nulla állásidejű telepítésének eléréséhez: 

| Stratégia |  A következő esetekben használja | Előnyök | Hátrányok |
| -------- | ------------ | ---- | ---- |
| [Verziókezelés](#versioning) |  Olyan alkalmazások, amelyek nem tapasztalnak gyakori [törési módosításokat.](durable-functions-versioning.md) | Egyszerűen megvalósítható. |  Megnövekedett függvényalkalmazás-méret a memóriában és a függvények számában.<br/>Kódmásolás. |
| [Állapot-ellenőrzés bővítőhellyel](#status-check-with-slot) | Olyan rendszer, amely nem rendelkezik 24 óránál hosszabb ideig tartó vagy gyakran egymást átfedő vezénylési rendszerekkel. | Egyszerű kódbázis.<br/>Nem igényel további függvényalkalmazás-kezelést. | További tárfiók- vagy feladatközpont-kezelést igényel.<br/>Olyan időszakokat igényel, amikor nem futvezés. |
| [Alkalmazás-útválasztás](#application-routing) | Olyan rendszer, amely nem rendelkezik olyan időszakokkal, amikor a vezénylések nem futnak, például azok az időszakok, amelyek 24 óránál tovább tartanak, vagy amelyek gyakran egymást átfedő vezénylési műveleteket. | Kezeli a rendszerek új verzióit folyamatosan futó vezénylések, amelyek törés változásokat. | Intelligens alkalmazás-útválasztót igényel.<br/>Az előfizetés által engedélyezett függvényalkalmazások számát maxolhatja ki. Az alapértelmezett érték 100. |

## <a name="versioning"></a>Verziókezelés

A függvények új verzióinak definiálása és a régi verziók a függvényalkalmazásban. Amint az a diagramon is látható, a függvény verziója a nevének részévé válik. Mivel a függvények korábbi verziói megőrződnek, a fedélzeti vezénylési példányok továbbra is hivatkozhatnak rájuk. Eközben az új vezénylési példányok a legújabb verziót, amely a vezénylési ügyfél függvény hivatkozhat egy alkalmazásbeállítás.

![Verziószámozási stratégia](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Ebben a stratégiában minden funkciót le kell másolni, és frissíteni kell a más függvényekre való hivatkozásait. A parancsfájl írásával megkönnyítheti a dolgát. Íme egy [mintaprojekt](https://github.com/TsuyoshiUshio/DurableVersioning) áttelepítési parancsfájllal.

>[!NOTE]
>Ez a stratégia telepítési helyeket használ az üzembe helyezés során az állásidő elkerülése érdekében. Az új központi telepítési helyek létrehozásáról és használatáról az [Azure Functions központi telepítési bővítőhely-bővítőhely-bővítőhely-tárolóhelyeket](../functions-deployment-slots.md)talál.

## <a name="status-check-with-slot"></a>Állapot-ellenőrzés bővítőhellyel

Amíg a függvényalkalmazás aktuális verziója fut az éles környezetben, telepítse a függvényalkalmazás új verzióját az átmeneti tárolóhelyre. Az éles és átmeneti tárolóhelyek cseréje előtt ellenőrizze, hogy vannak-e futó vezénylési példányok. Miután az összes vezénylési példány befejeződött, meg tudod csinálni a swap. Ez a stratégia akkor működik, ha kiszámítható időszakok, amikor nincs vezénylési példányok repülés közben. Ez a legjobb megközelítés, ha a vezénylési nem régóta futó, és ha a vezénylési végrehajtások nem gyakran fedik át egymást.

### <a name="function-app-configuration"></a>A függvényalkalmazás konfigurációja

A forgatókönyv beállításához kövesse az alábbi eljárást.

1. [Üzembe helyezési helyek hozzáadása](../functions-deployment-slots.md#add-a-slot) a függvényalkalmazáshoz az előkészítéshez és az éles környezethez.

1. Minden egyes tárolóhelyhez állítsa be az [AzureWebJobsStorage alkalmazásbeállítást](../functions-app-settings.md#azurewebjobsstorage) egy megosztott tárfiók kapcsolati karakterláncára. Ezt a tárfiók-kapcsolati karakterláncot az Azure Functions futásidejű használja. Ezt a fiókot az Azure Functions futásidejű használja, és kezeli a függvény kulcsait.

1. Hozzon létre egy új alkalmazásbeállítást minden `DurableManagementStorage`egyes foglalathoz, például . Állítsa be az értékét a különböző tárfiókok kapcsolati karakterláncára. Ezeket a tárfiókokat a Durable Functions bővítmény használja a [megbízható végrehajtás érdekében.](durable-functions-checkpointing-and-replay.md) Minden tárolóhelyhez használjon külön tárfiókot. Ne jelölje meg ezt a beállítást telepítési helymeghatározási helyként.

1. A függvényalkalmazás [host.json fájljában a durableTask szakaszban](durable-functions-bindings.md#hostjson-settings)adja meg `azureStorageConnectionStringName` a 3.

Az alábbi ábrán a központi telepítési tárolóhelyek és a tárfiókok leírt konfigurációja látható. Ebben a lehetséges üzembe helyezés előtti forgatókönyvben egy függvényalkalmazás 2-es verziója fut az éles tárolóhelyen, míg az 1-es verzió az átmeneti tárolóhelyen marad.

![Telepítési tárolóhelyek és tárfiókok](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>példák host.json

A következő JSON-töredékek példák a *host.json* fájlban lévő kapcsolati karakterlánc-beállításra.

#### <a name="functions-20"></a>Funkciók 2.0

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

Konfigurálja a CI/CD-folyamatot csak akkor telepítse, ha a függvényalkalmazás nem rendelkezik függőben lévő vagy futó vezénylési példányokkal. Az Azure Pipelines használata során létrehozhat egy függvényt, amely ellenőrzi ezeket a feltételeket, akövetkező példához képest:

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

Ezután konfigurálja az átmeneti kaput, hogy várjon, amíg nem futnak vezénylések. További információ: [A központi telepítés vezérlésének feloldása kapukkal](/azure/devops/pipelines/release/approvals/gates?view=azure-devops)

![Üzembe helyezési kapu](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Az Azure Pipelines ellenőrzi a függvényalkalmazást vezénylési példányok futtatásához a központi telepítés megkezdése előtt.

![Telepítési kapu (futás)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Most a függvényalkalmazás új verzióját kell telepíteni az átmeneti tárolóhelyre.

![Átmeneti hely](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Végül cserélje ki a bővítőhelyeket. 

A központi telepítési hely beállításaiként nem megjelölt alkalmazásbeállítások is fel vannak cserélve, így a 2-es verziójú alkalmazás megtartja az A tárfiókra való hivatkozását. Mivel a vezénylési állapot nyomon követhető a tárfiókban, a 2-es verziójú alkalmazáson futó vezénylési műveletek megszakítás nélkül futnak az új tárolóhelyen.

![Üzembehelyezési pont](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Ha mindkét bővítőhelyhez ugyanazt a tárfiókot szeretné használni, módosíthatja a feladatközpontok nevét. Ebben az esetben a tárolóhelyek állapotát és az alkalmazás HubName beállításait kell kezelnie. További információ: [Feladatközpontok a Tartós függvények alkalmazásban.](durable-functions-task-hubs.md)

## <a name="application-routing"></a>Alkalmazás-útválasztás

Ez a stratégia a legösszetettebb. Azonban használható függvényalkalmazások, amelyek nem rendelkeznek idő közötti futó vezénylések.

Ehhez a stratégiához létre kell hoznia egy *alkalmazás-útválasztót* a tartós funkciók előtt. Ez az útválasztó tartós funkciókkal valósítható meg. A router felelőssége:

* Telepítse a függvényalkalmazást.
* A Tartós függvények verziójának kezelése. 
* Vezénylési kérelmek függvényalkalmazások.

Amikor első alkalommal érkezik vezénylési kérelem, az útválasztó a következő feladatokat végzi el:

1. Új függvényalkalmazást hoz létre az Azure-ban.
2. A függvényalkalmazás kódját az Azure-beli új függvényalkalmazásba telepíti.
3. Továbbítja a vezénylési kérelmet az új alkalmazásnak.

Az útválasztó azt az állapotot kezeli, hogy az alkalmazás kódjának melyik verziójára van telepítve, melyik függvényalkalmazáshoz az Azure-ban.

![Alkalmazás-útválasztás (első alkalommal)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Az útválasztó a kérelemmel küldött verzió alapján irányítja az üzembe helyezési és vezénylési kérelmeket a megfelelő függvényalkalmazásba. Ez nem vesz tudomásul a folt változat.

Ha az alkalmazás új verzióját törésnélkül telepíti, a javítási verziót is megvetheti. Az útválasztó telepíti a meglévő függvényalkalmazást, és a kód régi és új verzióira vonatkozó kéréseket küld, amelyek ugyanahhoz a függvényalkalmazáshoz vannak irányítva.

![Alkalmazás-útválasztás (nincs törésváltozás)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Ha az alkalmazás új verzióját egy törésmódosítással telepíti, a fő- vagy alverzió növekménye is megnagyobbodhat. Ezután az alkalmazás-útválasztó létrehoz egy új függvényalkalmazást az Azure-ban, telepíti azt, és továbbítja az alkalmazás új verziójára vonatkozó kérelmeket. A következő ábrán az alkalmazás 1.0.1-es verzióján futó vezénylések folyamatosan futnak, de az 1.1.0-s verzióra vonatkozó kérelmek az új függvényalkalmazásba kerülnek.

![Alkalmazás-útválasztás (megszakító módosítás)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Az útválasztó figyeli az 1.0.1-es verzió vezénylési állapotát, és eltávolítja az alkalmazásokat az összes vezénylés befejezése után. 

### <a name="tracking-store-settings"></a>Az üzlet beállításainak nyomon követése

Minden függvényalkalmazásnak külön ütemezési várólistákat kell használnia, esetleg külön tárfiókokban. Ha azt szeretné, hogy az alkalmazás összes verziójában az összes vezénylési példánylekérdezést, megoszthatja a példány- és előzménytáblákat a függvényalkalmazások között. A táblákat úgy oszthatja meg, hogy a `trackingStoreConnectionStringName` `trackingStoreNamePrefix` [host.json beállításfájlban](durable-functions-bindings.md#host-json) lévő és beállításokat úgy konfigurálja, hogy mindegyik ugyanazokat az értékeket használja.

További információt a [Példányok kezelése az Azure-ban a tartós függvényekben című témakörben talál.](durable-functions-instance-management.md)

![Az üzlet beállításainak nyomon követése](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartós függvények verziószámozása](durable-functions-versioning.md)

