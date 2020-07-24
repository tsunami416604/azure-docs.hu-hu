---
title: Azure metric-riasztások hibaelhárítása
description: Azure Monitor metrikus riasztásokkal és lehetséges megoldásokkal kapcsolatos gyakori problémák.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 07/21/2020
ms.subservice: alerts
ms.openlocfilehash: 98cd7a4d31f4d7053426f44dd02a876759688cc7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045234"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Azure Monitor metrikai riasztásokkal kapcsolatos problémák elhárítása 

Ez a cikk a Azure Monitor [metrikus riasztásokkal](alerts-metric-overview.md) kapcsolatos gyakori problémákat és azok hibaelhárítását ismerteti.

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. A riasztással kapcsolatos további információkért lásd: [a Microsoft Azure riasztások áttekintése](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Metrikus riasztást kellett volna kilőtt, de nem 

Ha úgy gondolja, hogy egy metrikai riasztásnak kilőtte, de nem volt tűz, és nem található a Azure Portalban, próbálkozzon a következő lépésekkel:

1. **Konfiguráció** – tekintse át a metrika riasztási szabályának konfigurációját, és győződjön meg arról, hogy megfelelően van-e konfigurálva:
    - Győződjön meg arról, hogy az **összesítési típus**, az **összesítési részletesség (időszak)** és a **küszöbérték** vagy az **érzékenység** a várt módon van konfigurálva.
    - A dinamikus küszöbértékeket használó riasztási szabályok esetében ellenőrizze, hogy a speciális beállítások konfigurálva vannak-e, mivel a **szabálysértések száma** kiszűrheti a riasztásokat, és **figyelmen kívül hagyhatja az adatvesztést, mielőtt** hatással lehet a küszöbértékek kiszámítására.

       > [!NOTE] 
       > A dinamikus küszöbértékekhez legalább 3 nap és 30 metrikus minta szükséges, mielőtt aktív lenne.

2. **Kilőtt, de nincs értesítés** – tekintse át a [kilőtt](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) riasztások listáját, és ellenőrizze, hogy megtalálja-e a kilőtt riasztást. Ha a riasztást a listában látja, de a műveleteivel vagy értesítéseivel kapcsolatban probléma merül fel, további információt [itt](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)talál.

3. **Már aktív** – ellenőrizze, hogy van-e már riasztás a következő metrikai idősorozaton:. A metrikariasztások állapotalapúak, ami azt jelenti, hogy a riasztások egy adott metrika-idősorozatra való aktiválását követően az adott idősorozatra vonatkozó további riasztások addig nem lesznek aktiválva, amíg a probléma meg nem szűnik. Ez a kialakítási lehetőség csökkenti a zajt. A riasztás automatikusan megoldódik, ha a riasztási feltétel három egymást követő értékelés esetében nem teljesül.

4. **Felhasznált méretek** – ha egyes [dimenzió értékeket jelölt ki egy metrika](./alerts-metric-overview.md#using-dimensions)esetében, a riasztási szabály a küszöbértékek megszegése érdekében figyeli az egyes metrikai idősorozatokat (a dimenzió értékek kombinációja határozza meg). Az összesített metrikai idősorozatok figyeléséhez (a dimenziók nélkül) további riasztási szabályt is beállíthat a metrikán a dimenziók kiválasztása nélkül.

5. **Összesítés és idő részletessége** – ha a metrikát [metrikai diagramokkal](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)jeleníti meg, ügyeljen arra, hogy:
    * A metrikai diagram kiválasztott **összesítése** megegyezik a riasztási szabályban szereplő **összesítési típussal**
    * A kiválasztott **időrészletesség** megegyezik a riasztási szabály **összesítési részletességével (időszakával)** (és nem az "automatikus" értékre van állítva)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Metrikus riasztás kilőtt, ha nem lenne

Ha úgy gondolja, hogy a mérőszám riasztása nem lett elindítva, de a következő lépések segíthetnek a probléma megoldásában.

1. Tekintse át a kilőtt riasztások [listáját](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , és kattintson ide a részletek megtekintéséhez. Tekintse át a riasztás miatti **figyelmeztetést?** című témakörben található információkat. a metrikai diagram, a **metrika**és a **küszöbérték** megtekinthető a riasztás indításakor.

    > [!NOTE] 
    > Ha dinamikus küszöbértékeket használ, és úgy gondolja, hogy a használt küszöbértékek nem megfelelőek, kérjük, küldje el a visszajelzést a komor ikon használatával. Ez a visszajelzés hatással lesz a gépi tanulási algoritmusok kutatására, és segít a jövőbeli észlelések fejlesztésében.

2. Ha több dimenzió értéket jelölt ki egy metrika esetében, a riasztás akkor aktiválódik, ha a metrikus idősorozat **bármelyike** (a dimenzió értékek kombinációja alapján) megsérti a küszöbértéket. A méretek metrikariasztásokban való használatáról [itt](./alerts-metric-overview.md#using-dimensions) talál további információt.

3. Tekintse át a riasztási szabály konfigurációját, és győződjön meg arról, hogy megfelelően van-e konfigurálva:
    - Győződjön meg arról, hogy az **összesítési típus**, az **összesítési részletesség (időszak)** és a **küszöbérték** vagy az **érzékenység** a várt módon van konfigurálva.
    - A dinamikus küszöbértékeket használó riasztási szabályok esetében ellenőrizze, hogy a speciális beállítások konfigurálva vannak-e, mivel a **szabálysértések száma** kiszűrheti a riasztásokat, és **figyelmen kívül hagyhatja az adatvesztést, mielőtt** hatással lehet a küszöbértékek kiszámítására.

   > [!NOTE]
   > A dinamikus küszöbértékekhez legalább 3 nap és 30 metrikus minta szükséges, mielőtt aktív lenne.

4. Ha [mérőszámok diagram](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)használatával jeleníti meg a metrikát, ügyeljen arra, hogy:
    - A metrikai diagram kiválasztott **összesítése** megegyezik a riasztási szabályban szereplő **összesítési típussal**
    - A kiválasztott **időrészletesség** megegyezik a riasztási szabály **összesítési részletességével (időszakával)** (és nem az "automatikus" értékre van állítva)

5. Ha a riasztás elakadt, miközben már vannak olyan riasztások, amelyek ugyanazt a feltételt figyelik (ez nem oldható fel), ellenőrizze, hogy a riasztási szabály konfigurálva van-e az *automérséklés* tulajdonság értéke **false** értékre (ez a tulajdonság csak Rest/PowerShell/CLI használatával konfigurálható, ezért ellenőrizze a riasztási szabály központi telepítéséhez használt parancsfájlt). Ilyen esetben a riasztási szabály nem oldja fel a kilőtt riasztásokat, és nem igényli, hogy a rendszer feloldja a riasztást a tüzelés előtt.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Nem található a riasztást a virtuális gépek vendég metrikáit jelző metrika

Ha riasztást szeretne kapni a virtuális gépek vendég operációs rendszerére vonatkozó metrikáról (például memória, lemezterület), győződjön meg arról, hogy telepítette a szükséges ügynököt az adatok Azure Monitor metrikához való összegyűjtéséhez:
- [Windows rendszerű virtuális gépek esetén](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux rendszerű virtuális gépek esetén](./collect-custom-metrics-linux-telegraf.md)

További információ az adatoknak a virtuális gép vendég operációs rendszerből való összegyűjtéséről: [itt](../insights/monitor-vm-azure.md#guest-operating-system).
    
> [!NOTE] 
> Ha a vendég metrikákat úgy konfigurálta, hogy egy Log Analytics munkaterületre legyenek küldve, a metrikák a Log Analytics munkaterület-erőforrás alatt jelennek meg, és **csak** az azokat figyelő riasztási szabály létrehozása után kezdenek adatokat megjeleníteni. Ehhez kövesse a [naplók metrikariasztásának konfigurálására](./alerts-metric-logs.md#configuring-metric-alert-for-logs) szolgáló lépéseket.

## <a name="cant-find-the-metric-to-alert-on"></a>Nem található a riasztáshoz használandó metrika

Ha egy adott metrika esetében szeretne riasztást kapni, de nem látja a mérőszámokat az erőforráshoz, [ellenőrizze, hogy az erőforrás típusa támogatott-e a metrikus riasztások](./alerts-metric-near-real-time.md)esetén.
Ha megtekintheti az erőforráshoz tartozó mérőszámokat, de nem talál egy adott mérőszámot, [ellenőrizze, hogy a metrika elérhető-e](./metrics-supported.md), és ha igen, tekintse meg a metrika leírását, hogy az csak az erőforrás adott verzióiban vagy kiadásaiban legyen elérhető.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nem található a riasztásra vonatkozó metrikai dimenzió.

Ha [egy metrika adott dimenzió értékeire](./alerts-metric-overview.md#using-dimensions)szeretne riasztást kapni, de nem találja ezeket az értékeket, vegye figyelembe a következőket:

1. Eltarthat egy pár percig, amíg a dimenzióértékek megjelennek a **Dimenzióértékek** listában
1. A megjelenített dimenzióértékek az elmúlt három napban gyűjtött metrikaadatokon alapulnak
1. Ha a dimenzió értéke még nincs kibocsátva, kattintson a "+" jelre egyéni érték hozzáadásához.
1. Ha szeretne riasztást kapni egy dimenzió összes lehetséges értékéről (beleértve a jövőbeli értékeket is), jelölje be a "Select *" jelölőnégyzetet.

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>A metrika riasztási szabályai még definiálva vannak egy törölt erőforráson 

Amikor töröl egy Azure-erőforrást, az ahhoz társított metrikariasztási szabályok nem törlődnek automatikusan. Egy törölt erőforráshoz társított riasztási szabályok törlése:

1. Nyissa meg az erőforráscsoportot, amelyben a törölt erőforrás volt
1. Az erőforrások listájában jelölje be a **Rejtett típusok megjelenítése** jelölőnégyzetet
1. Szűrje a listát típus szerint = **microsoft.insights/metricalerts**
1. Válassza ki a megfelelő riasztási szabályokat, és válassza a **Törlés** lehetőséget.

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Metrikus riasztások elkészítése minden alkalommal, amikor a feltétel teljesült

A metrikai riasztások alapértelmezés szerint állapottal rendelkeznek, ezért a rendszer nem indít el további riasztásokat, ha már van egy kilőtt riasztás egy adott idősorozatban. Ha szeretné, hogy egy adott metrikai riasztási szabály állapot nélküli legyen, és a riasztási feltétel teljesülése esetén riasztást kapjon, a riasztási szabályt programozott módon hozza létre (például a [Resource Manager](./alerts-metric-create-templates.md), a [PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1), a [Rest](/rest/api/monitor/metricalerts/createorupdate), a [CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)használatával), és állítsa a "false" értékre az *autokockázatcsökkentő* tulajdonságot.

> [!NOTE] 
> A metrikai riasztási szabály állapotának megakadályozása megakadályozza a kilőtt riasztások feloldását, tehát még azt követően is, hogy a feltétel már nem teljesült, a kilőtt riasztások a 30 napos adatmegőrzési időszakig megmaradnak.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Riasztási szabály definiálása egy olyan egyéni metrika esetében, amely még nincs kibocsátva

Metrika-riasztási szabály létrehozásakor a metrika neve a [metrika-definíciók API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions/list) -val lesz érvényesítve, hogy biztosan létezik. Bizonyos esetekben riasztási szabályt kell létrehoznia egy egyéni metrika esetében még a kibocsátása előtt is. Ha például egy ARM-sablonnal hoz létre egy Application Insights-erőforrást, amely egy egyéni metrikát fog kibocsátani, valamint egy riasztási szabályt, amely figyeli a metrikát.

Annak elkerülése érdekében, hogy a telepítés meghiúsuljon az egyéni metrika definícióinak érvényesítése során, használhatja a *skipMetricValidation* paramétert a riasztási szabály feltételek szakaszában, ami miatt a metrika érvényesítése ki lesz hagyva. Tekintse meg az alábbi példát, amely bemutatja, hogyan használhatja ezt a paramétert egy ARM-sablonban (teljes ARM-sablon mintákhoz metrikus riasztási szabályok létrehozásához [).]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                    "name" : "condition1",
                        "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                        "dimensions":[],
                        "operator": "GreaterThan",
                        "threshold" : 10,
                        "timeAggregation": "Average",
                    "skipMetricValidation": true
        }
              ]
        }
```

## <a name="metric-alert-rules-quota-too-small"></a>A metrika riasztási szabályainak kvótája túl kicsi

Az előfizetésre vonatkozó metrikus riasztási szabályok megengedett számának [korlátozásai a kvóták](../service-limits.md).

Ha elérte a kvóta korlátját, a következő lépések segíthetnek a probléma megoldásában:
1. Próbálja meg törölni vagy letiltani a már nem használt mérőszám-riasztási szabályokat.

2. Váltson olyan metrikariasztási szabályok használatára, amelyek több erőforrást monitoroznak. Ezzel a képességgel egyetlen riasztási szabály több erőforrást is képes figyelni, csak egy riasztási szabályt használva, amely a kvóta alapján van megszámolva. A képességről és a támogatott erőforrástípusokról [itt](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) talál további információt.

3. Ha növelni szeretné a kvóta korlátját, nyisson meg egy támogatási kérést, és adja meg a következő információkat:

    - Az előfizetés-azonosító (k), amelyhez a kvóta korlátját növelni kell
    - A kvóta növelésének erőforrás-típusa: **metrikai riasztások** vagy **metrikai riasztások (klasszikus)**
    - Kért kvótakorlát

## <a name="check-total-number-of-metric-alert-rules"></a>Metrikai riasztási szabályok teljes számának keresése

A metrikus riasztási szabályok aktuális használatának vizsgálatához kövesse az alábbi lépéseket.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Nyissa meg a **Riasztások** képernyőt, és kattintson a **Riasztási szabályok kezelése** elemre
2. Szűrés a megfelelő előfizetésre az **előfizetés** legördülő menüjének használatával
3. Ügyeljen arra, hogy ne szűrje egy adott erőforráscsoportot, erőforrástípust vagy erőforrást
4. A **jel típusa** legördülő menüben válassza a **metrikák** lehetőséget.
5. Annak ellenőrzése, hogy az **állapot** legördülő menüje **engedélyezve** értékre van-e állítva
6. A metrika riasztási szabályainak teljes száma a riasztási szabályok listáján felül jelenik meg

### <a name="from-api"></a>Küldő API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API – [List by subscription](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Riasztási szabályok kezelése Resource Manager-sablonok, REST API, PowerShell vagy Azure CLI használatával

Ha a metrikus riasztások Resource Manager-sablonokkal, REST API, PowerShell-lel vagy az Azure parancssori felületével (CLI) történő létrehozásával, frissítésével, beolvasásával vagy törlésével kapcsolatos problémákba ütközik, a következő lépések segíthetnek a probléma megoldásában.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

- Tekintse át az [Azure-beli üzemelő példányok gyakori hibáinak](../../azure-resource-manager/templates/common-deployment-errors.md) listáját, és ez alapján végezze el a hibaelhárítást
- Tekintse meg a [metrikai riasztások Azure Resource Manager a sablonokra vonatkozó példákat](./alerts-metric-create-templates.md) , és győződjön meg róla, hogy az összes paramétert helyesen adta át

### <a name="rest-api"></a>REST API

Tekintse át a [REST API útmutatót](/rest/api/monitor/metricalerts/) , és ellenőrizze, hogy helyesen adta-e meg az összes paramétert

### <a name="powershell"></a>PowerShell

Győződjön meg arról, hogy a megfelelő PowerShell-parancsmagokat használja a metrikai riasztásokhoz:

- A metrikariasztások PowerShell-parancsmagjai az [Az.Monitor modulban](/powershell/module/az.monitor/?view=azps-3.6.1) érhetők el
- Ügyeljen arra, hogy a "v2" végződésű parancsmagokat használja az új (nem klasszikus) metrikai riasztásokhoz (például [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1)).

### <a name="azure-cli"></a>Azure CLI

Győződjön meg arról, hogy a megfelelő CLI-parancsokat használja a metrikai riasztásokhoz:

- A metrikaalapú riasztásokra vonatkozó parancssori felületi parancsok a következőképpen kezdődnek: `az monitor metrics alert` Az [Azure CLI-referenciából](/cli/azure/monitor/metrics/alert?view=azure-cli-latest) többet tudhat meg a szintaxisról.
- Itt talál egy [példát a metrikaalapú riasztások parancssori felülettel történő használatára](./alerts-metric.md#with-azure-cli)
- Ha egyéni metrikához szeretne riasztást beállítani, ügyeljen arra, hogy a metrika nevéhez a megfelelő metrikanévtér előtagját adja meg: NÉVTÉR.METRIKA

### <a name="general"></a>Általános kérdések

- Ha a `Metric not found` következő hibaüzenetet kapja:

   - Platform metrika esetén: Ügyeljen arra, hogy a **metrika** nevét [a Azure monitor támogatott metrikák lapról](./metrics-supported.md)használja, nem a **metrika megjelenítendő nevét** .

   - Egyéni metrika esetén: Győződjön meg arról, hogy a metrika már el van bocsátva (nem hozható létre riasztási szabály egy olyan egyéni metrika esetében, amely még nem létezik), és hogy Ön megadja az egyéni metrika névterét (lásd az ARM- [sablon példáját](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Ha [metrikus riasztásokat hoz létre a naplókon](./alerts-metric-logs.md), győződjön meg arról, hogy a megfelelő függőségek szerepelnek benne. Itt találhat egy [mintasablont](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Ha több feltételt tartalmazó riasztási szabályt hoz létre, vegye figyelembe a következő korlátozásokat:

   - Dimenziónként csak egy érték választható ki minden feltételben
   - A(z) „\*” nem használható dimenzióértékként
   - Ha a különböző feltételekben konfigurált mérőszámok ugyanazt a dimenziót támogatják, akkor a konfigurált dimenzió értékét explicit módon kell beállítani az összes metrika esetében (lásd a Resource Manager- [sablonok példáját](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Nincs engedélye metrikus riasztási szabályok létrehozására

Metrikus riasztási szabály létrehozásához a következő engedélyekkel kell rendelkeznie:

- Olvasási engedély a riasztási szabály cél erőforrásához
- Írási engedély a riasztási szabályt létrehozó erőforráscsoport számára (ha a riasztási szabályt a Azure Portal hozza létre, a riasztási szabály ugyanabban az erőforráscsoportban jön létre, amelyben a cél-erőforrás található)
- Olvasási engedély a riasztási szabályhoz társított összes műveleti csoporthoz (ha van ilyen)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Metrikus riasztási szabályok elnevezési korlátozásai

Vegye figyelembe a következő korlátozásokat a metrikus riasztási szabályok neveinél:

- A metrika riasztási szabályainak neve nem módosítható (átnevezve) a létrehozás után
- A metrikus riasztási szabályok neveinek egyedinek kell lenniük egy erőforráscsoporthoz
- A metrikai riasztási szabályok nevei nem tartalmazhatják a következő karaktereket: * # & +:  < > ? @ % { } \ / 
- A metrika riasztási szabályainak nevei nem végződhet a következő karakterrel:.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Korlátozások a metrikus riasztási szabályokban több feltételt tartalmazó dimenziók használata esetén

A metrikus riasztások támogatják a többdimenziós metrikák riasztásait, valamint a több feltétel meghatározásának támogatását (a riasztási szabály legfeljebb 5 feltételét).

Ha több feltételt tartalmazó riasztási szabályban dimenziókat használ, vegye figyelembe a következő korlátozásokat:
- Minden feltételben csak egy értéket lehet kijelölni dimenzión belül.
- Nem használhatja a "minden aktuális és jövőbeli érték kijelölése" lehetőséget (válassza ki \* ).
- Ha a különböző feltételekben konfigurált mérőszámok ugyanazt a dimenziót támogatják, akkor a konfigurált dimenzió értékét explicit módon kell beállítani az összes metrika esetében (a vonatkozó feltételek között).
Például:
    - Vegyünk egy olyan metrikai riasztási szabályt, amely egy Storage-fiókban van definiálva, és két feltételt figyel:
        * **Tranzakciók** összesen > 5
        * Átlagos **SuccessE2ELatency** > 250 MS
    - Szeretném frissíteni az első feltételt, és csak olyan tranzakciókat figyelni, amelyekben az **ApiName** dimenzió *"GetBlob"* értékkel rendelkezik
    - Mivel mind a **tranzakciók** , mind a **SuccessE2ELatency** metrikája támogatja a **ApiName** -dimenziót, mindkét feltételt frissíteni kell, és mindkettőnek meg kell adnia a **ApiName** dimenziót *"GetBlob"* értékkel.


## <a name="next-steps"></a>További lépések

- A riasztásokkal és értesítésekkel kapcsolatos általános hibaelhárítási információkért lásd: [Azure monitor riasztások hibaelhárítási problémái](alerts-troubleshoot.md).
