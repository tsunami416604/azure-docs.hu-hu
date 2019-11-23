---
title: A fogyasztási terv költségeinek becslése Azure Functions
description: Megtudhatja, hogyan becsülheti meg jobban a Function alkalmazás Azure-beli használati tervben való futtatásakor felmerülő költségeket.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 9d81c99f3602e3d7ed5508884b0b313ef2f2fcaf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230858"
---
# <a name="estimating-consumption-plan-costs"></a>A fogyasztási terv költségeinek becslése

A Azure Functionsban futó alkalmazások esetében jelenleg háromféle üzemeltetési csomag létezik, és mindegyik csomag saját díjszabási modellel rendelkezik: 

| Felkészülés | Leírás |
| ---- | ----------- |
| [**Fogyasztás**](functions-scale.md#consumption-plan) | Csak a Function app által futtatott idő után kell fizetnie. Ez a csomag egy [ingyenes engedélyezési][díjszabási oldalon] tartalmaz előfizetés alapján.|
| [**Prémium szintű**](functions-scale.md#premium-plan) | Ugyanazokat a szolgáltatásokat és skálázási mechanizmust biztosítja, mint a használati terv, de a teljesítmény-és VNET-hozzáférés is elérhető. A díjak a választott díjszabási szinten alapulnak. További információ: [Azure functions Premium csomag](functions-premium-plan.md). |
| [**Dedikált (App Service)** ](functions-scale.md#app-service-plan) <br/>(alapszintű vagy magasabb) | Ha dedikált virtuális gépeken vagy elszigetelten kell futnia, használjon egyéni rendszerképeket, vagy szeretné használni a felesleges App Service csomag kapacitását. A [normál app Service csomag számlázását](https://azure.microsoft.com/pricing/details/app-service/)használja. A díjak a választott díjszabási szinten alapulnak.|

Úgy döntött, hogy a legjobban támogatja a függvény teljesítményére és a költséghatékonyságra vonatkozó követelményeket. További információ: [Azure functions skálázás és üzemeltetés](functions-scale.md).

Ez a cikk csak a fogyasztási tervvel foglalkozik, mivel ez a csomag változó költségeket eredményez. 

A Durable Functions egy felhasználási csomagban is futtatható. Ha többet szeretne megtudni a Durable Functions használatakor felmerülő díjszabásról, tekintse meg a [Durable functions számlázást](./durable/durable-functions-billing.md)ismertető témakört.

## <a name="consumption-plan-costs"></a>Használatalapú csomag költségei

Egy függvény végrehajtásának végrehajtási *költségeit* *GB-másodpercben*mérjük. A végrehajtási költségeket a memóriahasználat és a végrehajtási idő kombinálásával kell kiszámítani. Egy függvény, amely több költséget futtat, akárcsak egy függvény, amely több memóriát használ fel. 

Vegyünk egy esetet, amikor a függvény által használt memória mennyisége állandó marad. Ebben az esetben a költségeket egyszerű szorzással számítjuk ki. Tegyük fel például, hogy a függvény 3 másodpercig 0,5 GB-ot használt. Ezután a végrehajtás költsége `0.5GB * 3s = 1.5 GB-seconds`. 

Mivel az idő múlásával megváltozik a memóriahasználat, a számítás lényegében a memóriahasználat szerves része az idő múlásával.  A System ezt a számítást úgy végzi el, hogy a folyamat (az alárendelt folyamatokkal együtt) memória-használatát rendszeres időközönként mintavétel útján végzi. A [díjszabási oldalon]említettek szerint a memóriahasználat a legközelebbi 128 MB-os gyűjtőre lesz kerekítve. Ha a folyamat 160 MB-ot használ, a rendszer 256 MB-ot számít fel. A számítás figyelembe veszi a párhuzamosságot, amely egyszerre több egyidejű függvény végrehajtása ugyanabban a folyamatban.

> [!NOTE]
> Habár a CPU-használat nem veszi figyelembe közvetlenül a végrehajtási költségeket, hatással lehet a bekerülési időszakra, ha az hatással van a függvény végrehajtási idejére.

## <a name="other-related-costs"></a>Egyéb kapcsolódó költségek

A függvények bármely csomagban való futtatásának teljes díja miatt ne feledje, hogy a functions Runtime számos más Azure-szolgáltatást használ, amelyek mindegyike külön számlázható. A Function apps díjszabásának kiszámításakor a többi Azure-szolgáltatással integrálható eseményindítók és kötések a további szolgáltatások létrehozásához és fizetéséhez szükségesek. 

A használati tervben futó függvények esetében a teljes díj a függvények végrehajtási költsége, valamint a sávszélesség és a további szolgáltatások költsége. 

A Function app és a kapcsolódó szolgáltatások általános költségeinek kiszámításához használja az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Kapcsolódó díj | Leírás |
| ------------ | ----------- |
| **Storage-fiók** | Az egyes functions-alkalmazásokhoz hozzá kell rendelni egy általános célú [Azure Storage-fiókot](../storage/common/storage-introduction.md#types-of-storage-accounts), amely [külön számlázható](https://azure.microsoft.com/pricing/details/storage/). Ezt a fiókot a functions futtatókörnyezet belsőleg használja, de a tároló-eseményindítók és-kötések esetében is használható. Ha nincs Storage-fiókja, akkor a rendszer létrehoz egyet a Function alkalmazás létrehozásakor. További információ: a [Storage-fiókra vonatkozó követelmények](functions-scale.md#storage-account-requirements).|
| **Application Insights** | A függvények a [Application Insightson](../azure-monitor/app/app-insights-overview.md) támaszkodnak, hogy nagy teljesítményű figyelési élményt nyújtsanak a Function alkalmazásai számára. Habár nem kötelező, engedélyeznie kell [Application Insights integrációt](functions-monitoring.md#enable-application-insights-integration). Minden hónapban ingyenes telemetria-adatmennyiség szerepel. További információkért tekintse meg [a Azure monitor díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/monitor/). |
| **Hálózati sávszélesség** | Az azonos régióban található Azure-szolgáltatások közötti adatátvitelért nem kell fizetnie. Egy másik régióba vagy az Azure-on kívül is felmerülhetnek a kimenő adatátvitelek költségei. További tudnivalókért tekintse meg a [sávszélesség-díjszabás részleteit](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Végrehajtási időt érintő viselkedésmódok

A függvények következő viselkedései befolyásolhatják a végrehajtási időt:

+ **Eseményindítók és kötések**: a [függvény kötéseinek](functions-triggers-bindings.md) beolvasásához és kimenetből való írásához szükséges idő végrehajtási időnek számít. Ha például a függvény egy kimeneti kötés használatával ír egy üzenetet egy Azure Storage-várólistába, a végrehajtási idő magában foglalja az üzenetnek a várólistába való írásához szükséges időt, amelyet a függvény költsége számít. 

+ **Aszinkron végrehajtás**: az az idő, ameddig a függvény várakozik az aszinkron kérelmek eredményeire (`await` C#) a végrehajtási időpontnak számít. A GB-Second számítás a függvény kezdési és befejezési időpontjára, valamint az adott időszakon belüli memóriahasználat függ. A folyamat során a rendszer nem számítja fel a számítást a CPU-tevékenység szempontjából. Előfordulhat, hogy a [Durable functions](durable/durable-functions-overview.md)használatával csökkentheti a költségeket az aszinkron műveletek során. Nem számítunk fel díjat a Orchestrator functions szolgáltatásban várja.

## <a name="view-execution-data"></a>Végrehajtási adatbázis megtekintése

A [számlán](/azure/billing/billing-download-azure-invoice)megtekintheti az **összes végrehajtás – functions** és **végrehajtási idő – functions**költséggel kapcsolatos adatait, valamint a ténylegesen számlázott költségeket is. Azonban ez a számlázási érték egy korábbi számlázási időszak havi összesítése. 

A függvények költséghatékonyságának jobb megismeréséhez Azure Monitor használatával megtekintheti a Function apps által jelenleg létrehozott költséggel kapcsolatos metrikákat. Az adatok lekéréséhez használhatja a [Azure Portal] vagy a REST API-k [Azure monitor metrikák kezelőjét](../azure-monitor/platform/metrics-getting-started.md) is.

### <a name="monitor-metrics-explorer"></a>Metrikák figyelése Explorer

A [Azure monitor mérőszámok Explorerrel](../azure-monitor/platform/metrics-getting-started.md) grafikus formában tekintheti meg a használati tervben szereplő, költséghatékony adatokat. 

1. A **Search Services, az erőforrások és a docs** [Azure Portal] tetején keresse meg a `monitor`, és válassza a **figyelés** elemet a **szolgáltatások**területen.

1. A bal oldalon válassza ki a **metrikák** > **válasszon ki egy erőforrást**, majd a rendszerkép alatti beállítások használatával válassza ki a Function alkalmazást.

    ![Válassza ki a Function app-erőforrást](media/functions-consumption-costing/select-a-resource.png)

      
    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    | Előfizetést    |  Az Ön előfizetése  | Az előfizetés a Function alkalmazással.  |
    | Erőforráscsoport     | Az erőforráscsoport  | A Function alkalmazást tartalmazó erőforráscsoport.   |
    | Erőforrás típusa     |  App Services | A Function apps App Services példányként jelenik meg a figyelőben. |
    | Erőforrás     |  A Function alkalmazás  | A figyelni kívánt Function alkalmazás.        |

1. Válassza az **alkalmaz** lehetőséget, hogy kiválassza a Function alkalmazást a figyelni kívánt erőforrásként.

1. A **metrika**területen válassza a **függvény végrehajtásának száma** és az **összesítési** **összeg** elemet. Ezzel hozzáadja a végrehajtások összegét a kiválasztott időszakban a diagramhoz.

    ![A diagramhoz hozzáadandó functions-alkalmazás mérőszámának megadása](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Válassza a **metrika hozzáadása** lehetőséget, és ismételje meg a 2-4 lépéseket a **függvény-végrehajtási egységek** a diagramhoz való hozzáadásához. 

Az eredményül kapott diagram a kiválasztott időtartományban lévő végrehajtási mérőszámok összegeit tartalmazza, ami ebben az esetben két óra.

![Függvény-végrehajtási számok és végrehajtási egységek gráfja](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Mivel a végrehajtási egységek száma sokkal nagyobb a végrehajtások számánál, a diagram csak a végrehajtási egységeket mutatja.

Ez a diagram összesen 1 110 000 000 `Function Execution Units` használ két órás időszakban, MB-ezredmásodpercben mérve. Ha GB-másodpercre szeretné váltani, ossza meg a 1024000-as értéket. Ebben a példában a Function alkalmazás a `1110000000 / 1024000 = 1083.98` GB-os másodpercet használja. Ezt az értéket elvégezheti, és a [functions díjszabási]oldal[díjszabása lapon]megadhatja a végrehajtási idő aktuális árát, ami azt feltételezi, hogy már felhasználta a végrehajtási idő ingyenes támogatását. 

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI](/cli/azure/) a metrikák beolvasására szolgáló parancsokat tartalmaz. A CLI-t egy helyi parancssori környezetből vagy közvetlenül a portálról [Azure Cloud Shell](../cloud-shell/overview.md)használatával is használhatja. A következő az [monitor Metrics List](/cli/azure/monitor/metrics#az-monitor-metrics-list) parancs például az óránkénti adatokat adja vissza a korábban használt időszakra vonatkozóan.

Ügyeljen arra, hogy a `<AZURE_SUBSCRIPTON_ID>`t cserélje le a parancsot futtató Azure-előfizetés-AZONOSÍTÓra.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Ez a parancs egy JSON-adattartalmat ad vissza, amely a következő példához hasonlóan néz ki:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Ez az adott válasz azt mutatja, hogy `2019-09-11T21:46`ról `2019-09-11T23:18`ra, amely alatt az alkalmazás 1110000000 MB-os (1083,98 GB-os) értéket használt.

## <a name="determine-memory-usage"></a>Memóriahasználat meghatározása

A függvények végrehajtási egységei a végrehajtási idő és a memóriahasználat kombinációja, ami nehéz mérőszámot biztosít a memóriahasználat megértéséhez. A memória adatai nem a Azure Monitoron keresztül jelenleg elérhető metrikák. Ha azonban optimalizálni szeretné az alkalmazás memóriahasználat használatát, használhatja a Application Insights által összegyűjtött teljesítményszámláló-adatokat.  

Ha még nem tette meg, [engedélyezze a Application Insightst a Function alkalmazásban](functions-monitoring.md#enable-application-insights-integration). Ha ez az integráció engedélyezve van, ezt [a telemetria-adatlekérdezést a portálon kérdezheti](functions-monitoring.md#query-telemetry-data)le.  

A **figyelés**területen válassza a **naplók (Analitika)** lehetőséget, majd másolja a következő telemetria-lekérdezést, és illessze be a lekérdezési ablakba, majd válassza a **Futtatás**lehetőséget. Ez a lekérdezés a teljes memóriahasználat értékét adja vissza az egyes mintavételi időpontokban.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Az eredmények a következő példához hasonlóan jelennek meg:

| időbélyeg \[UTC\]          | név          | érték       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 AM | Saját bájtok | 209 932 288 |
| 9/12/2019, 1:06:14\.994 AM | Saját bájtok | 212 189 184 |
| 9/12/2019, 1:06:30\.010 AM | Saját bájtok | 231 714 816 |
| 9/12/2019, 1:07:15\.040 AM | Saját bájtok | 210 591 744 |
| 9/12/2019, 1:12:16\.285 AM | Saját bájtok | 216 285 184 |
| 9/12/2019, 1:12:31\.376 AM | Saját bájtok | 235 806 720 |

## <a name="function-level-metrics"></a>Függvény szintű mérőszámok

Azure Monitor nyomon követi a metrikákat az erőforrás szintjén, amely a functions alkalmazás. Application Insights integrációja a metrikákat a függvények alapján bocsátja ki. Íme egy példa az elemzési lekérdezésre egy függvény átlagos időtartamának lekéréséhez:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| név                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a monitoring Function-alkalmazásokról](functions-monitoring.md)

[díjszabási oldalon]: https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
