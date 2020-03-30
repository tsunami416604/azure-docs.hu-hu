---
title: Az Azure automatikus skálázásának hibáiról
description: A Service Fabricben, a virtuális gépekben, a webalkalmazásokban és a felhőszolgáltatásokban használt Azure-automatikus skálázással kapcsolatos problémák nyomon követése.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751338"
---
# <a name="troubleshooting-azure-autoscale"></a>Az Azure automatikus skálázásának hibáiról
 
Az Azure Monitor automatikus skálázása segít, hogy a megfelelő mennyiségű erőforrás fut az alkalmazás terhelésének kezeléséhez. Lehetővé teszi, hogy erőforrásokat adjon hozzá a terhelés növekedésének kezeléséhez, és pénzt takarítson meg az olyan erőforrások eltávolításával, amelyek tétlenül ülnek. Skálázhatja ütemezés, rögzített dátum-idő vagy erőforrás-metrika által kiválasztott. További információt az [Automatikus skálázás – áttekintés című témakörben talál.](autoscale-overview.md)

Az automatikus skálázási szolgáltatás metrikákat és naplókat biztosít annak megértéséhez, hogy milyen méretezési műveletek történtek, és a műveletekhez vezető feltételek kiértékelése. Az alábbiakra a következő kérdésekre találhat választ:

- Miért horizontális feladták a szolgáltatásomat, vagy miért?
- Miért nem méretezett a szolgáltatásom?
- Miért nem sikerült egy automatikus skálázási művelet?
- Miért van az automatikus skálázási művelet méretezése időt vesz igénybe?
  
## <a name="autoscale-metrics"></a>Automatikus skálázási mutatók

Az automatikus skálázás [négy metrikát](metrics-supported.md#microsoftinsightsautoscalesettings) biztosít a működésének megértéséhez. 

- **Megfigyelt metrikaérték** – Az automatikus skálázási motor által látott vagy számított méretezési művelethez kiválasztott metrika értéke. Mivel egyetlen automatikus skálázási beállítás több szabályt, és így több metrikaforrást is használhat, szűrhet a "metrikaforrás" dimenzióként használatával.
- **Metrika küszöbérték** – A méretezési művelet hez beállított küszöbérték. Mivel egyetlen automatikus skálázási beállítás több szabályt, és így több metrikaforrást is használhat, a "metrikaszabály" dimenzióként való szűrése.
- **Megfigyelt kapacitás** – A célerőforrás automatikus skálázási motor által látható példányainak aktív száma.
- **Kezdeményezett skálázási műveletek** – Az automatikus skálázási motor által kezdeményezett horizontális fel- és leskálázási műveletek száma. A műveletek horizontális felskálázása és léptéke szerint szűrhető.

A [Metrika-kezelő](metrics-getting-started.md) segítségével egy helyen ábrázolhatja a fenti mutatókat. A diagramnak a következőket kell mutatnia:

  - a tényleges metrika
  - az automatikus skálázású motor által látott/számított mérőszám
  - a skálaművelet küszöbértéke
  - a kapacitásváltozás 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>1. példa – Egyszerű automatikus skálázási szabály elemzése 

Van egy egyszerű automatikus skálázási beállítás egy virtuális gép méretezési készlet, amely:

- akkor kicsinyedik, ha egy készlet átlagos CPU-százaléka 10 percig meghaladja a 70%-ot 
- akkor, ha a készlet CPU-százaléka 10 percnél tovább 5%-nál kisebb. 

Tekintse át az automatikus skálázási szolgáltatás metrikáit.
 
![Virtuálisgép-méretezési halmaz százalékos CPU-beállítása példa](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Virtuálisgép-méretezési halmaz százalékos CPU-beállítása példa](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***1a. ábra – Százalékos CPU-metrika a virtuálisgép-méretezési csoporthoz és a Megfigyelt metrikaérték-metrika metrika-metrika-mérőszám az automatikus skálázási beállításhoz***

![Metrikaküszöbérték és megfigyelt kapacitás](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***1b. ábra – Metrikus küszöbérték és megfigyelt kapacitás***

Az 1b. **Metric Threshold**  A **megfigyelt kapacitás** (sötétkék vonal) az aktív példányok számát mutatja, amely jelenleg 3. 

> [!NOTE]
> A **metrikaküszöbértéket** a metrikaeseményi szabály dimenzióhorizontális (növekmény) szabály a horizontális felskálázási küszöbérték és a szabály (csökkenés) léptéke szerint kell szűrnie. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>2. példa – Speciális automatikus skálázás egy virtuálisgép-méretezési csoporthoz

Van egy automatikus skálázási beállítás, amely lehetővé teszi a virtuális gép méretezési erőforrás horizontális felskálázása alapján a saját metrika **kimenő folyamatok.** Figyelje meg, hogy a **metrikaküszöbértékek felosztási metrikaszámláló** beállításának felosztása. 

A léptékműveletszabály a következő: 

Ha a **példányonkénti kimenő folyamat** értéke nagyobb, mint 10, akkor az automatikus skálázási szolgáltatásnak 1 példánysal kell horizontálisan skáláznia. 

Ebben az esetben az automatikus skálázási motor megfigyelt metrikaértéke a tényleges metrikaérték és a példányok számának hányadosaként kerül kiszámításra. Ha a megfigyelt metrikaértéke kisebb, mint a küszöbérték, nem kezdeményez horizontális felskálázási műveletet. 
 
![Virtuálisgép-méretezési készlet automatikus skálázási metrikák diagramok példa](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Virtuálisgép-méretezési készlet automatikus skálázási metrikák diagramok példa](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***2. ábra – Virtuálisgép-méretezési készlet automatikus skálázási mérőszámok diagramok példa***

Ábra 2, láthatjuk a két metrikus diagramok. 

A diagram tetején a kimenő **folyamatok** mutató tényleges értékét mutatja. A tényleges érték 6. 

Az alsó diagram néhány értéket mutat. 
 - A **megfigyelt metrika értéke** (világoskék) 3, mert 2 aktív példány és 6 osztva 2 3. 
 - A **megfigyelt kapacitás** (lila) az automatikus skálázási motor által látott példányszámot jeleníti meg. 
 - A **metrikaküszöbérték** (világoszöld) 10-re van állítva. 

Ha több méretezési műveletszabály létezik, a Hasítás vagy a **Szűrő hozzáadása** beállítás sal a Metrika-kezelő diagramon egy adott forrás vagy szabály szerint vizsgálhatja meg a metrikát. A metrikadiagramok felosztásáról további információt a [Metrikadiagramok speciális jellemzői – felosztás](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>3. példa – Az automatikus skálázási események ismertetése

Az automatikus skálázási beállítás képernyőn nyissa meg az **Előzmények futtatása** lapot a legutóbbi méretezési műveletek megtekintéséhez. A lapon a megfigyelt kapacitás idővel bekövetkező **változása** is látható. Ha további részleteket szeretne megtudni az összes automatikus skálázási műveletről, beleértve az olyan műveleteket is, mint például az automatikus skálázási beállítások frissítése/törlése, tekintse meg a tevékenységnaplót, és szűrje az automatikus skálázási műveletek szerint.

![Az automatikus skálázási beállítások előzményeket futtatnak](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Erőforrásnaplók automatikus skálázása

Ugyanúgy, mint bármely más Azure-erőforrás, az automatikus skálázási szolgáltatás [erőforrásnaplókat](platform-logs-overview.md)biztosít. A naplóknak két kategóriája van.

- **Automatikus skálázási kiértékelések** – Az automatikus skálázási motor minden egyes feltételkiértékelés naplóbejegyzéseit rögzíti minden alkalommal, amikor ellenőrzést végez.  A bejegyzés részletesen ismerteti a metrikák megfigyelt értékeit, az értékelt szabályokat, és ha az értékelés méretezési műveletet eredményezett-e vagy sem.

- **Automatikus skálázási skálázási műveletek** – A motor rögzíti az automatikus skálázási szolgáltatás által kezdeményezett műveletesemények skálázását, és ezek a skálázási műveletek eredményeit (sikeres, sikertelen és mennyi skálázás történt az automatikus skálázási szolgáltatás által látott).

Mint minden Azure Monitor által támogatott szolgáltatás, [a diagnosztikai beállítások](diagnostic-settings.md) segítségével a naplók at irányíthatja:

- a Log Analytics munkaterületére a részletes elemzéshez
- az Event Hubs-hoz, majd a nem Azure-eszközökhöz
- az Azure storage-fiókjához archiváláshoz  

![Automatikus skálázásdiagnosztikai beállítások](media/autoscale-troubleshoot/diagnostic-settings.png)

Az előző képen az Azure Portal automatikus skálázási diagnosztikai beállításai láthatók. Itt kiválaszthatja a Diagnosztikai/erőforrásnaplók lapot, és engedélyezheti a naplógyűjtést és -művelettervet. Ugyanezt a műveletet a REST API, CLI, PowerShell, Resource Manager-sablonokkal is végrehajthatja a diagnosztikai beállításokhoz, ha a *Microsoft.Insights/AutoscaleSettings*erőforrástípust választja. 

## <a name="troubleshooting-using-autoscale-logs"></a>Hibaelhárítás automatikus skálázási naplók használatával 

A legjobb hibaelhárítási élmény érdekében azt javasoljuk, hogy a naplókat az Azure Monitor Logs (Log Analytics) egy munkaterületen keresztül, amikor létrehozza az automatikus skálázási beállítást. Ez a folyamat látható a képen az előző szakaszban. A Kiértékelések és a méretezési műveletek jobban a Log Analytics használatával.

Miután konfigurálta az automatikus skálázási naplók at a Log Analytics-munkaterületre való küldésre, a következő lekérdezéseket hajthatja végre a naplók ellenőrzéséhez. 

Első lépésként próbálja meg ezt a lekérdezést a legutóbbi automatikus skálázási kiértékelési naplók megtekintéséhez:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Vagy próbálja meg a következő lekérdezést a legújabb méretezési műveletnaplók megtekintéséhez:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

A következő szakaszokat használja ezekhez a kérdésekhez. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Olyan léptékskálázás történt, amire nem számítottam

Először hajtsa végre a lekérdezést a méretezési művelet, hogy megtalálja a méretezési művelet érdekli. Ha ez a legújabb méretezési művelet, használja a következő lekérdezést:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Válassza ki a Korrelációs azonosító mezőt a méretezési műveletek naplójában. A Korrelációs azonosító segítségével keresse meg a megfelelő kiértékelési naplót. Az alábbi lekérdezés végrehajtása megjeleníti az összes kiértékelt szabályt és feltételt, amely az adott léptékművelethez vezet.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Milyen profil okozott méretezési műveletet?

Méretezett művelet történt, de átfedésben vannak a szabályok és profilok, és le kell nyomoznia, hogy melyik okozta a műveletet. 

Keresse meg a léptékművelet korrelációs azonosítóját (az 1. példában leírtak szerint), majd hajtsa végre a lekérdezést a kiértékelési naplókban, hogy többet tudjon meg a profilról.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

A teljes profilkiértékelés a következő lekérdezéssel is jobban érthető:

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Nem történt léptékművelet

Vártam egy skála fellépés, és ez nem történt meg. Előfordulhat, hogy nincsenek méretezési műveletesemények vagy naplók.

Tekintse át az automatikus skálázási metrikákat, ha metrikaalapú méretezési szabályt használ. Lehetséges, hogy a **megfigyelt metrika érték** vagy a megfigyelt **kapacitás** nem az, amit várt, és ezért a léptékszabály nem tűz. Akkor is látni értékelések, de nem egy horizontális felskálázás szabály. Az is lehetséges, hogy a lehűlési idő megakadályozta, hogy a skála akció bekövetkezzen. 

 Tekintse át az automatikus skálázási kiértékelési naplókat abban az időszakban, amikor a méretezési művelet várhatóan bekövetkezik. Tekintse át az összes értékelést, és miért döntött úgy, hogy nem indít el léptékskálát.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>A méretezési művelet nem sikerült

Előfordulhat, hogy az automatikus skálázási szolgáltatás végrehajtotta a skálázási műveletet, de a rendszer úgy döntött, hogy nem méretezi, vagy nem tudta végrehajtani a skálázási műveletet. Ezzel a lekérdezéssel megkeresheti a sikertelen méretezési műveleteket.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Hozzon létre riasztási szabályokat, hogy értesítést kapjon az automatikus skálázási műveletekről vagy hibákról. Az automatikus skálázási eseményekről értesítést kaphat riasztási szabályokat is.

## <a name="schema-of-autoscale-resource-logs"></a>Automatikus skálázási erőforrásnaplók sémája

További információ: [automatikus skálázási erőforrásnaplók](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>További lépések
Olvassa el az [automatikus skálázással kapcsolatos gyakorlati tanácsokat.](autoscale-best-practices.md) 
