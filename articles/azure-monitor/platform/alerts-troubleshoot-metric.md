---
title: Azure metric-riasztások hibaelhárítása
description: Azure Monitor metrikus riasztásokkal és lehetséges megoldásokkal kapcsolatos gyakori problémák.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 04/28/2020
ms.subservice: alerts
ms.openlocfilehash: 605d1f550335417a26340b6ee54736321ad69f80
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300761"
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

2. **Kilőtt, de nincs értesítés** – tekintse át a [kilőtt](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) riasztások listáját, és ellenőrizze, hogy megtalálja-e a kilőtt riasztást. Ha a riasztást a listában látja, de a műveleteivel vagy értesítéseivel kapcsolatban probléma merül fel, további információt [itt](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected)talál.

3. **Már aktív** – ellenőrizze, hogy van-e már riasztás a következő metrikai idősorozaton:. A metrikus riasztások állapota állapotú, ami azt jelenti, hogy ha egy adott metrikai idősorozatra riasztást küld, az adott idősorozatra vonatkozó további riasztásokat a rendszer nem hajtja végre, amíg a probléma már nem észlelhető. Ez a kialakítási lehetőség csökkenti a zajt. A riasztás automatikusan megoldódik, ha a riasztási feltétel három egymást követő értékelés esetében nem teljesül.

4. **Felhasznált méretek** – ha egyes [dimenzió értékeket jelölt ki egy metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)esetében, a riasztási szabály a küszöbértékek megszegése érdekében figyeli az egyes metrikai idősorozatokat (a dimenzió értékek kombinációja határozza meg). Az összesített metrikai idősorozatok figyeléséhez (a dimenziók nélkül) további riasztási szabályt is beállíthat a metrikán a dimenziók kiválasztása nélkül.

5. **Összesítés és idő részletessége** – ha a metrikát [metrikai diagramokkal](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)jeleníti meg, ügyeljen a következőkre:
    * A metrikai diagram kiválasztott **összesítése** megegyezik a riasztási szabályban szereplő **összesítési típussal**
    * A kiválasztott **időrészletesség** megegyezik a riasztási szabály **összesítési részletességével (időszakával)** (és nem az "automatikus" értékre van állítva)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Metrikus riasztás kilőtt, ha nem lenne

Ha úgy gondolja, hogy a mérőszám riasztása nem lett elindítva, de a következő lépések segíthetnek a probléma megoldásában.

1. Tekintse át a kilőtt riasztások [listáját](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) , és kattintson ide a részletek megtekintéséhez. Tekintse át a riasztás miatti **figyelmeztetést?** című témakörben található információkat. a metrikai diagram, a **metrika**és a **küszöbérték** megtekinthető a riasztás indításakor.

    > [!NOTE] 
    > Ha dinamikus küszöbértékek feltételtípust használ, és úgy véli, hogy a használt küszöbértékek helytelenek voltak, küldjön visszajelzést a szomorú arcot ábrázoló ikonnal. Ez a visszajelzés hatással lesz a gépi tanulási algoritmusok kutatására, és segít a jövőbeli észlelések fejlesztésében.

2. Ha több dimenzió értéket jelölt ki egy metrika esetében, a riasztás akkor aktiválódik, ha a metrikus idősorozat **bármelyike** (a dimenzió értékek kombinációja alapján) megsérti a küszöbértéket. A méretek metrikariasztásokban való használatáról [itt](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions) talál további információt.

3. Tekintse át a riasztási szabály konfigurációját, és győződjön meg arról, hogy megfelelően van-e konfigurálva:
    - Győződjön meg arról, hogy az **összesítési típus**, az **összesítési részletesség (időszak)** és a **küszöbérték** vagy az **érzékenység** a várt módon van konfigurálva.
    - A dinamikus küszöbértékeket használó riasztási szabályok esetében ellenőrizze, hogy a speciális beállítások konfigurálva vannak-e, mivel a **szabálysértések száma** kiszűrheti a riasztásokat, és **figyelmen kívül hagyhatja az adatvesztést, mielőtt** hatással lehet a küszöbértékek kiszámítására.

   > [!NOTE]
   > A dinamikus küszöbértékekhez legalább 3 nap és 30 metrikus minta szükséges, mielőtt aktív lenne.

4. Ha [metrikai diagrammal](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)jeleníti meg a metrikát, ügyeljen arra, hogy:
    - A metrikai diagram kiválasztott **összesítése** megegyezik a riasztási szabályban szereplő **összesítési típussal**
    - A kiválasztott **időrészletesség** megegyezik a riasztási szabály **összesítési részletességével (időszakával)** (és nem az "automatikus" értékre van állítva)

5. Ha a riasztás elakadt, miközben már vannak olyan riasztások, amelyek ugyanazt a feltételt figyelik (ez nem oldható fel), ellenőrizze, hogy a riasztási szabály konfigurálva van-e az *automérséklés* tulajdonság értéke **false** értékre (ez a tulajdonság csak Rest/PowerShell/CLI használatával konfigurálható, ezért ellenőrizze a riasztási szabály központi telepítéséhez használt parancsfájlt). Ilyen esetben a riasztási szabály nem oldja fel a kilőtt riasztásokat, és nem igényli, hogy a rendszer feloldja a riasztást a tüzelés előtt.


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>Nem található metrika a riasztáshoz a virtuális gépeken 

Ha riasztást szeretne kapni a virtuális gépeken lévő vendég metrikákkal (memória, lemezterület), győződjön meg arról, hogy beállította a diagnosztikai beállításokat, hogy adatokat küldjön egy Azure Monitor fogadónak:
    * [Windows rendszerű virtuális gépek esetén](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [Linux rendszerű virtuális gépek esetén](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> Ha a vendég metrikákat úgy konfigurálta, hogy egy Log Analytics munkaterületre legyenek küldve, a metrikák a Log Analytics munkaterület-erőforrás alatt jelennek meg, és **csak** az azokat figyelő riasztási szabály létrehozása után kezdenek adatokat megjeleníteni. Ehhez kövesse a [naplók metrikariasztásának konfigurálására](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs) szolgáló lépéseket.

## <a name="cant-find-the-metric-to-alert-on"></a>Nem található a riasztáshoz használandó metrika

Ha egy adott metrika esetében szeretne riasztást kapni, de nem látja a mérőszámokat az erőforráshoz, [ellenőrizze, hogy az erőforrás típusa támogatott-e a metrikus riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time)esetén.
Ha megtekintheti az erőforráshoz tartozó mérőszámokat, de nem talál egy adott mérőszámot, [ellenőrizze, hogy a metrika elérhető-e](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), és ha igen, tekintse meg a metrika leírását, hogy az csak az erőforrás adott verzióiban vagy kiadásaiban legyen elérhető.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nem található a riasztásra vonatkozó metrikai dimenzió.

Ha [egy metrika adott dimenzió értékeire](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)szeretne riasztást kapni, de nem találja ezeket az értékeket, vegye figyelembe a következőket:

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

A metrikai riasztások alapértelmezés szerint állapottal rendelkeznek, ezért a rendszer nem indít el további riasztásokat, ha már van egy kilőtt riasztás egy adott idősorozatban. Ha szeretné, hogy egy adott metrikai riasztási szabály állapot nélküli legyen, és a riasztási feltétel teljesülése esetén riasztást kapjon, a riasztási szabályt programozott módon hozza létre (például a [Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates), a [PowerShell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1), a [Rest](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate), a [CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)használatával), és állítsa a "false" értékre az *autokockázatcsökkentő* tulajdonságot.

> [!NOTE] 
> A metrikai riasztási szabály állapotának megakadályozása megakadályozza a kilőtt riasztások feloldását, tehát még azt követően is, hogy a feltétel már nem teljesült, a kilőtt riasztások a 30 napos adatmegőrzési időszakig megmaradnak.


## <a name="metric-alert-rules-quota-too-small"></a>A metrika riasztási szabályainak kvótája túl kicsi

Az előfizetésre vonatkozó metrikus riasztási szabályok megengedett számának [korlátozásai a kvóták](https://docs.microsoft.com/azure/azure-monitor/service-limits).

Ha elérte a kvótakorlátot, az alábbi lépések segíthetnek a probléma megoldásában:
1. Próbálja meg törölni vagy letiltani a már nem használt mérőszám-riasztási szabályokat.

2. Váltson olyan metrikariasztási szabályok használatára, amelyek több erőforrást monitoroznak. Ezzel a képességgel egyetlen riasztási szabály több erőforrást is képes figyelni, csak egy riasztási szabályt használva, amely a kvóta alapján van megszámolva. További információ erről a képességről és a támogatott erőforrás-típusokról: [több](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

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
6. A metrika riasztási szabályainak teljes száma a szabályok lista felett jelenik meg

### <a name="from-api"></a>Küldő API

- PowerShell – [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API – [List by subscription](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Riasztási szabályok kezelése Resource Manager-sablonok, REST API, PowerShell vagy Azure CLI használatával

Ha a rendszer a metrikus riasztások Resource Manager-sablonok, REST API, PowerShell vagy az Azure parancssori felület (CLI) használatával történő létrehozásával, frissítésével, beolvasásával vagy törlésével kapcsolatos problémákba ütközik, a következő lépések segíthetnek a probléma megoldásában.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

- Tekintse át az [Azure-beli üzemelő példányok gyakori hibáinak](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) listáját, és ez alapján végezze el a hibaelhárítást
- Tekintse meg a [metrikai riasztások Azure Resource Manager a sablonokra vonatkozó példákat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates) , amelyekkel biztosíthatja, hogy az összes paraméter megfelelően legyen átadva

### <a name="rest-api"></a>REST API

Tekintse át a [REST API útmutatóját](https://docs.microsoft.com/rest/api/monitor/metricalerts/), és ellenőrizze, hogy minden paramétert megfelelően adott-e meg

### <a name="powershell"></a>PowerShell

Győződjön meg arról, hogy a megfelelő PowerShell-parancsmagokat használja a metrikai riasztásokhoz:

- A metrikariasztások PowerShell-parancsmagjai az [Az.Monitor modulban](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1) érhetők el
- Ügyeljen arra, hogy a "v2" végződésű parancsmagokat használja az új (nem klasszikus) metrikai riasztásokhoz (például [Add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1)).

### <a name="azure-cli"></a>Azure CLI

Győződjön meg arról, hogy a megfelelő CLI-parancsokat használja a metrikai riasztásokhoz:

- A metrikaalapú riasztásokra vonatkozó parancssori felületi parancsok a következőképpen kezdődnek: `az monitor metrics alert` Az [Azure CLI-referenciából](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest) többet tudhat meg a szintaxisról.
- Itt talál egy [példát a metrikaalapú riasztások parancssori felülettel történő használatára](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)
- Ha egyéni metrikához szeretne riasztást beállítani, ügyeljen arra, hogy a metrika nevéhez a megfelelő metrikanévtér előtagját adja meg: NÉVTÉR.METRIKA

### <a name="general"></a>Általános kérdések

- Ha a rendszer `Metric not found` hibát jelez:

   - Platform metrika esetén: Ügyeljen arra, hogy a **metrika** nevét [a Azure monitor támogatott metrikák lapról](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)használja, nem a **metrika megjelenítendő nevét** .

   - Egyéni metrika esetén: Győződjön meg arról, hogy a metrika már el van bocsátva (nem hozható létre riasztási szabály egy olyan egyéni metrika esetében, amely még nem létezik), és hogy Ön megadja az egyéni metrika névterét (lásd az ARM- [sablon példáját](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric))

- Ha [naplókhoz hoz létre metrikaalapú riasztásokat](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs), ügyeljen a megfelelő függőségek megadására. Itt találhat egy [mintasablont](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs).

- Ha több feltételt tartalmazó riasztási szabályt hoz létre, vegye figyelembe a következő korlátozásokat:

   - Dimenziónként csak egy érték választható ki minden feltételben
   - A(z) „\*” nem használható dimenzióértékként
   - Ha a különböző feltételekben konfigurált mérőszámok ugyanazt a dimenziót támogatják, akkor a konfigurált dimenzió értékét explicit módon kell beállítani az összes metrika esetében (lásd a Resource Manager- [sablonok példáját](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria))


## <a name="no-permissions-to-create-metric-alert-rules"></a>Nincs engedélye metrikus riasztási szabályok létrehozására

Metrikus riasztási szabály létrehozásához a következő engedélyekkel kell rendelkeznie:

- Olvasási engedély a riasztási szabály cél erőforrásához
- Írási engedély a riasztási szabályt létrehozó erőforráscsoport számára (ha a riasztási szabályt a Azure Portal hozza létre, a riasztási szabály ugyanabban az erőforráscsoportban jön létre, amelyben a cél-erőforrás található)
- Olvasási engedély a riasztási szabályhoz társított összes műveleti csoporthoz (ha van ilyen)

## <a name="next-steps"></a>További lépések

- A riasztásokkal és értesítésekkel kapcsolatos általános hibaelhárítási információkért lásd: [Azure monitor riasztások hibaelhárítási problémái](alerts-troubleshoot.md).
