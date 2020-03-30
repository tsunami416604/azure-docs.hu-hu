---
title: A felhasználási terv költségeinek becslése az Azure Functionsben
description: Ismerje meg, hogyan becsülheti meg jobban azazure-csomag azure-beli felhasználási csomagban a függvényalkalmazás futtatásakor felmerülő költségeket.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963988"
---
# <a name="estimating-consumption-plan-costs"></a>A felhasználási terv költségeinek becslése

Jelenleg háromféle üzemeltetési csomag létezik egy azure Functions ben futó alkalmazáshoz, és mindegyik csomag saját díjszabási modellel rendelkezik: 

| Felkészülés | Leírás |
| ---- | ----------- |
| [**Használat**](functions-scale.md#consumption-plan) | Csak a függvényalkalmazás futási idejéért kell fizetnie. Ez a csomag előfizetésenként tartalmaz egy [ingyenes támogatási][díjszabási oldalt.]|
| [**Prémium**](functions-scale.md#premium-plan) | Ugyanazokat a funkciókat és méretezési mechanizmust biztosítja, mint a felhasználási terv, de a jobb teljesítmény és a virtuális hálózat eléréséhez. A költség a kiválasztott tarifacsomagon alapul. További információ: [Azure Functions Premium csomag.](functions-premium-plan.md) |
| [**Dedikált (App Service)**](functions-scale.md#app-service-plan) <br/>(alapszint vagy magasabb) | Ha dedikált virtuális gépeken vagy elszigetelten kell futtatnia, használjon egyéni rendszerképeket, vagy szeretné használni a felesleges App Service-csomag kapacitását. [Rendszeres Alkalmazásszolgáltatási csomag számlázást](https://azure.microsoft.com/pricing/details/app-service/)használ. A költség a kiválasztott tarifacsomagon alapul.|

Azt a csomagot választotta, amely a legjobban támogatja a függvény teljesítményét és költségkövetelményeit. További információ: [Azure Functions scale and hosting](functions-scale.md).

Ez a cikk csak a Felhasználási tervvel foglalkozik, mivel ez a terv változó költségeket eredményez. Ez a cikk felülírja a [felhasználási terv költség számlázási GYIK](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) cikket.

A tartós függvények a felhasználási tervben is futtathatók. Ha többet szeretne tudni a tartós függvények használatakor a költségszempontokról, olvassa el a [Tartós függvények számlázása című témakört.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Használatalapú csomag költségei

Az egyetlen függvény végrehajtásának végrehajtási *költsége* *GB-másodpercben*van mérhető. A végrehajtási költség kiszámítása a memóriahasználat és a végrehajtási idő kombinálásával történik. A hosszabb ideig futó függvény többe kerül, csakúgy, mint egy olyan függvény, amely több memóriát fogyaszt. 

Vegyünk egy olyan esetet, amikor a függvény által használt memória mennyisége állandó marad. Ebben az esetben a költség kiszámítása egyszerű szorzás. Tegyük fel például, hogy a függvény 3 másodpercig 0,5 GB-ot fogyasztott. Akkor a végrehajtási `0.5GB * 3s = 1.5 GB-seconds`költség . 

Mivel a memóriahasználat idővel változik, a számítás lényegében a memóriahasználat szerves része az idő múlásával.  A rendszer ezt a számítást úgy végzi el, hogy rendszeres időközönként mintavételezi a folyamat memóriahasználatát (a gyermekfolyamatokkal együtt). Ahogy az az [árképzési oldalon]is szerepel, a memóriahasználat a legközelebbi 128 MB-os gyűjtőre kerül. Ha a folyamat 160 MB-ot használ, 256 MB-ot számítunk fel. A számítás figyelembe veszi az egyidejűséget, amely több egyidejű függvény végrehajtásugyanabban a folyamatban.

> [!NOTE]
> Míg a CPU-használat nem közvetlenül figyelembe kell venni a végrehajtási költség, hatással lehet a költség, ha befolyásolja a függvény végrehajtási idejét.

## <a name="other-related-costs"></a>Egyéb kapcsolódó költségek

A függvények bármely csomagban való futtatásának teljes költségének becslésénél ne feledje, hogy a Functions futtatótime számos más Azure-szolgáltatást használ, amelyek mindegyike külön-külön kerül számlázásra. A függvényalkalmazások díjszabásának kiszámításakor a más Azure-szolgáltatásokkal integrálható eseményindítók és kötések megkövetelik a további szolgáltatások létrehozását és kifizetését. 

A felhasználási tervben futó függvények esetében a teljes költség a függvények végrehajtási költsége, valamint a sávszélesség és a további szolgáltatások költsége. 

A függvényalkalmazás és a kapcsolódó szolgáltatások teljes költségének becslésénél használja az [Azure díjkalkulátorát.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Kapcsolódó költség | Leírás |
| ------------ | ----------- |
| **Tárfiók** | Minden függvényalkalmazáshoz hozzá kell férnie egy általános célú [Azure Storage-fiókhoz,](../storage/common/storage-introduction.md#types-of-storage-accounts)amelyet [külön számlázunk ki.](https://azure.microsoft.com/pricing/details/storage/) Ezt a fiókot a Functions futásidejű belsőhasználatra használja, de storage-eseményindítókhoz és kötésekhez is használhatja. Ha nem rendelkezik tárfiókkal, a függvényalkalmazás létrehozásakor létrejön egy. További információ: [Tárfiók követelményei.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | A függvények az [Application Insights](../azure-monitor/app/app-insights-overview.md) szolgáltatásra támaszkodnak, hogy nagy teljesítményű figyelési élményt nyújtsanak a függvényalkalmazásokhoz. Bár nem szükséges, engedélyeznie kell [az Application Insights-integrációt.](functions-monitoring.md#enable-application-insights-integration) A telemetriai adatok ingyenes megadása minden hónapban szerepel. További információ: [az Azure Monitor díjszabási lapja.](https://azure.microsoft.com/pricing/details/monitor/) |
| **Hálózati sávszélesség** | Nem kell fizetnie az Azure-szolgáltatások közötti adatátvitelért ugyanabban a régióban. Azonban költségek merülhetnek fel a kimenő adatátvitel egy másik régióba vagy az Azure-on kívülre. További információt a [Sávszélesség-díjszabás részletei ben talál.](https://azure.microsoft.com/pricing/details/bandwidth/) |

## <a name="behaviors-affecting-execution-time"></a>A végrehajtási időt befolyásoló viselkedések

A függvények alábbi viselkedése hatással lehet a végrehajtási időre:

+ **Eseményindítók és kötések:** A [függvénykötések](functions-triggers-bindings.md) bemeneti és írási kimenetének olvasásához szükséges idő végrehajtási időnek számít. Ha például a függvény egy kimeneti kötést használ egy üzenet írásához egy Azure storage-várólistába, a végrehajtási idő tartalmazza az üzenet üzenet írásához szükséges időt a várólistába, amely szerepel a függvény költségének kiszámításában. 

+ **Aszinkron végrehajtás:** Az az idő, amikor a függvény megvárja az`await` aszinkron kérelmek (C#) eredményeit, végrehajtási időnek számít. A GB-másodperc számítás a függvény kezdő és záró időpontján, valamint az adott időszakban használt memóriahasználaton alapul. Mi történik ez idő alatt szempontjából CPU tevékenység nem veszik figyelembe a számítást. Az aszinkron műveletek során a [Tartós funkciók](durable/durable-functions-overview.md)használatával csökkentheti a költségeket. Nem kell fizetnie az orchestrator függvényekben a várakozással töltött időért.

## <a name="view-execution-data"></a>Végrehajtási adatok megtekintése

A [számlán](/azure/billing/billing-download-azure-invoice)megtekintheti az összes végrehajtás **- függvények** és végrehajtási idő **- függvények**költséggel kapcsolatos adatait, valamint a tényleges számlázott költségeket. Ezek a számlaadatok azonban egy korábbi számlaidőszak havi összesítései. 

A függvények költséghatásának jobb megértéséhez az Azure Monitor segítségével megtekintheti a függvényalkalmazások által jelenleg létrehozott, költségalapú metrikákat. Az Azure [Monitor metrikák explorer](../azure-monitor/platform/metrics-getting-started.md) az [Azure Portalon] vagy a REST API-k az adatok bekésezéséhez használhatja.

### <a name="monitor-metrics-explorer"></a>Mérőszámok figyelése kezelő

Az [Azure Monitor metrikakezelője](../azure-monitor/platform/metrics-getting-started.md) segítségével grafikus formátumban tekintheti meg a fogyasztási csomag függvényalkalmazásaiköltséggel kapcsolatos adatait. 

1. Az [Azure Portal] tetején a **Keresési szolgáltatások, erőforrások és dokumentumok** keresés `monitor` és válassza **monitor** a **Szolgáltatások**csoportban.

1. A bal oldalon válassza **ki a Metrikák** > **Válasszon ki egy erőforrást,** majd a kép alatti beállításokkal válassza ki a függvényalkalmazást.

    ![A függvényalkalmazás-erőforrás kiválasztása](media/functions-consumption-costing/select-a-resource.png)

      
    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    | Előfizetés    |  Az Ön előfizetése  | Az előfizetés a függvényalkalmazással.  |
    | Erőforráscsoport     | Az erőforráscsoport  | A függvényalkalmazást tartalmazó erőforráscsoport.   |
    | Erőforrás típusa     |  App Services | A függvényalkalmazások App Services-példányként jelennek meg a Figyelőben. |
    | Erőforrás     |  A függvényalkalmazás  | A figyelni figyelt függvényalkalmazás.        |

1. Válassza az **Alkalmaz** lehetőséget, ha a függvényalkalmazást szeretné figyelni erőforrásként kiválasztani.

1. A **Metrikus területen**válassza **a Függvényvégrehajtási szám** és az **Összesítés** **összegzése** lehetőséget. Ezzel hozzáadja a kiválasztott időszakban a végrehajtási számok összegét a diagramhoz.

    ![A diagramhoz hozzáadni a függvényalkalmazás mérőszámának meghatározása](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Válassza **a Metrika hozzáadása** **Function Execution Units** és a 2-4. 

Az eredményül kapott diagram tartalmazza a kiválasztott időtartomány mindkét végrehajtási mutatójának összesítéseit, ami ebben az esetben két óra.

![A függvényvégrehajtási késések és végrehajtási egységek grafikonja](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Mivel a végrehajtási egységek száma sokkal nagyobb, mint a végrehajtási számláló, a diagram csak a végrehajtási egységeket mutatja.

Ez a diagram összesen 1,11 milliárd `Function Execution Units` felhasznált két órás időszakban, MB-ezredmásodpercben mérve. Gb-másodpercre való konvertáláshoz ossza el 1024000-el. Ebben a példában a `1110000000 / 1024000 = 1083.98` függvényalkalmazás GB-másodperceket fogyasztott. Ezt az értéket a [Funkciók árképzési oldal][árképzési lapján,]amely megadja ennek a két órának a költségét, ezt az értéket megszorozhatja a Végrehajtási idő aktuális árával, feltéve, hogy már felhasználta a végrehajtási idő ingyenes juttatásait. 

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI](/cli/azure/) a metrikák lekéréséhez kiadott parancsokkal rendelkezik. A CLI-t helyi parancskörnyezetből vagy közvetlenül a portálról használhatja az [Azure Cloud Shell](../cloud-shell/overview.md)használatával. Például a következő [az monitor metrikák lista](/cli/azure/monitor/metrics#az-monitor-metrics-list) parancs akorábban használt azonos időszakóránkénti adatokat adja vissza.

Győződjön meg `<AZURE_SUBSCRIPTON_ID>` arról, hogy cserélje ki a parancsot futtató Azure-előfizetés-azonosítóját.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Ez a parancs a következő példához hasonló JSON-hasznos adatértéket ad vissza:

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
Ez a válasz `2019-09-11T21:46` azt `2019-09-11T23:18`mutatja, hogy a , az alkalmazás fogyasztott 1110000000 MB-ezredmásodperc (1083,98 GB-másodperc).

## <a name="determine-memory-usage"></a>Memóriahasználat meghatározása

A függvényvégrehajtási egységek a végrehajtási idő és a memóriahasználat kombinációja, ami megnehezíti a memóriahasználat megértését. A memóriaadatok nem az Azure Monitoron keresztül jelenleg elérhető mérőszám. Ha azonban optimalizálni szeretné az alkalmazás memóriahasználatát, használhatja az Application Insights által gyűjtött teljesítményszámláló-adatokat.  

Ha még nem tette meg, [engedélyezze az Application Insights funkciót a függvényalkalmazásban.](functions-monitoring.md#enable-application-insights-integration) Ha ez az integráció engedélyezve van, [lekérdezheti ezt a telemetriai adatot a portálon.](functions-monitoring.md#query-telemetry-data)  

A **Figyelés**csoportban válassza **a Naplók (Analytics)** lehetőséget, majd másolja a következő telemetriai lekérdezést, majd illessze be a lekérdezési ablakba, és válassza a Futtatás **lehetőséget.** Ez a lekérdezés a teljes memóriahasználatot adja vissza minden mintavételezett időpontban.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Az eredmények a következő példához hasonlóan néznek ki:

| időbélyeg \[UTC\]          | név          | érték       |
|----------------------------|---------------|-------------|
| 2019.12.9., 1:05:14\.947 AM | Privát bájtok | 209,932,288 |
| 2019.12.9., 1:06:14\.994 AM | Privát bájtok | 212,189,184 |
| 2019.12.9., 1:06:30\.010 AM | Privát bájtok | 231,714,816 |
| 2019.12.9., 1:07:15\.040 AM | Privát bájtok | 210,591,744 |
| 2019.09.12., 1:12:16\.285 AM | Privát bájtok | 216,285,184 |
| 2019.09.12., 1:12:31\.376 AM | Privát bájtok | 235,806,720 |

## <a name="function-level-metrics"></a>Funkciószintű mérőszámok

Az Azure Monitor nyomon követi a metrikák at az erőforrás szintjén, amely a Functions a függvényalkalmazás. Az Application Insights-integráció függvényenként i metrikákat bocsát ki. Íme egy példa elemzési lekérdezés egy függvény átlagos időtartamának lehívásához:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| név                       | átlagos IdőtartamEzseundum |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationM | 8\.522                      |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Figyelési funkcióalkalmazásokról](functions-monitoring.md)

[árképzési oldal]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portál]: https://portal.azure.com
