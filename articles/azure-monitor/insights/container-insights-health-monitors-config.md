---
title: Azure Monitor a containers Health Monitors konfigurációjában | Microsoft Docs
description: Ez a cikk az Azure Monitor for containers állapot-figyelők részletes konfigurációját ismertető tartalmat tartalmaz.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80055706"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Azure Monitor a containers Health Monitor konfigurációs útmutatója

A figyelők a tárolók állapotának méréséhez és Azure Monitor hibák észleléséhez használt elsődleges elemek. Ebből a cikkből megtudhatja, hogyan méri a rendszer az állapotot, valamint az állapot modelljét a Kubernetes-fürt állapotának figyelésére és jelentésére a [Health (előzetes verzió)](container-insights-health.md) szolgáltatással.

>[!NOTE]
>Az állapot szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
>

## <a name="monitors"></a>Monitorozások

A figyelő egy felügyelt objektum bizonyos aspektusának állapotát méri. A figyelők kettő vagy három állapottal rendelkeznek. Egy figyelő egy adott időpontban csak egy lehetséges állapotában lesz. A tároló ügynök által betöltött figyelő esetén a rendszer Kifogástalan állapotba inicializálja azt. Az állapot csak akkor változik, ha egy másik állapothoz megadott feltételek észlelhetők.

Egy adott objektum általános állapota az egyes figyelők állapota alapján van meghatározva. Ez a hierarchia a tárolók Azure Monitor állapot-hierarchia paneljén látható. Az állapot összesítésének házirendje az összesítő figyelők konfigurációjának része.

## <a name="types-of-monitors"></a>Figyelők típusai

|Figyelés | Leírás | 
|--------|-------------|
| Egység figyelője |Az egység-figyelő egy erőforrás vagy alkalmazás bizonyos aspektusait méri. Lehetséges, hogy az erőforrás teljesítményének és rendelkezésre állásának megállapításához a teljesítményszámláló ellenőrzése szükséges. |
|Összesítő figyelő | Az összesített figyelők csoport több figyelőt biztosít egyetlen állapot összesített állapotának biztosításához. Az egység figyelők általában egy adott összesítő figyelő alatt vannak konfigurálva. A Node összesítő figyelő például összesíti a csomópont CPU-kihasználtságát, a memória kihasználtságát és a csomópont állapotát.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Összesítő figyelő állapotának összesítő szabályzata

Mindegyik összesített figyelő egy állapot-összesítési szabályzatot határoz meg, amely az összesített figyelő állapotának meghatározására szolgál az alatta lévő figyelők állapota alapján. Az összesített figyelő lehetséges állapot-összesítési házirendjei a következők:

#### <a name="worst-state-policy"></a>Legrosszabb állapot házirendje

Az összesítő figyelő állapota megegyezik a gyermek figyelő állapotával, a legrosszabb állapottal. Ez az összesített figyelők által használt leggyakoribb házirend.

![Példa az összesített figyelő kumulatív legrosszabb állapotára](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Százalékos szabályzat

A forrásoldali objektum a lehető legrosszabbnak felel meg a célként megadott objektumok egy adott százalékában a legjobb állapotban. Ezt a házirendet akkor kell használni, ha a célobjektum bizonyos hányadának kifogástalannak kell lennie ahhoz, hogy a cél objektum kifogástalannak minősül. A százalékos házirend az állapot súlyosságának csökkenő sorrendjében rendezi a figyelőket, az összesített figyelő állapotát pedig az N% legrosszabb állapotának számítja ki (N), amelyet a *StateThresholdPercentage*konfigurációs paraméter diktál.

Tegyük fel például, hogy egy tároló rendszerképének öt tároló példánya van, és az egyes állapotuk **kritikus**, **Figyelmeztetés**, kifogástalan **, kifogástalan, kifogástalan**állapotú **.** **Healthy**  A tároló CPU-kihasználtsági figyelő állapota **kritikus**lesz, mivel a tárolók 90%-ának legrosszabb állapota **kritikus** , ha csökkenő súlyossági sorrendben rendezi a rendszer.

## <a name="understand-the-monitoring-configuration"></a>A figyelési konfiguráció ismertetése

A tárolók Azure Monitor számos kulcsfontosságú figyelési forgatókönyvet tartalmaz, amelyek az alábbiak szerint vannak konfigurálva.

### <a name="unit-monitors"></a>Egység figyelők

|**Figyelő neve** | Figyelő típusa | **Leírás** | **Paraméter** | **Érték** |
|-----------------|--------------|-----------------|---------------|-----------|
|Node memória kihasználtsága |Egység figyelője |Ez a figyelő percenként kiértékeli a csomópontok memóriájának kihasználtságát, a cadvisor jelentett adat használatával. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Csomópont CPU-kihasználtsága |Egység figyelője |Ez a figyelő percenként ellenőrzi a csomópont CPU-kihasználtságát, a cadvisor jelentett adat használatával. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Csomópont állapota |Egység figyelője |Ez a figyelő ellenőrzi a Kubernetes által jelentett csomóponti feltételeket.<br> Jelenleg a következő csomópont-feltételek vannak bejelölve: lemezes nyomás, memória-nyomás, PID-nyomás, a lemez, a hálózat nem érhető el, üzemkész állapot a csomóponthoz.<br> A fenti feltételektől **függetlenül, ha**a *lemez vagy a* *hálózat nem érhető* el, a figyelő **kritikus** állapotra vált.<br> Ha bármilyen más feltétel **igaz**, a **kész** állapottól eltérő, a figyelő **Figyelmeztetési** állapotba vált. | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Tároló memóriájának kihasználtsága |Egység figyelője |Ez a figyelő a tároló példányainak memória-kihasználtsága (RSS) összesített állapotát jelenti.<br> Egy egyszerű összehasonlítást végez, amely összehasonlítja az egyes mintákat egyetlen küszöbértékkel, és a **ConsecutiveSamplesForStateTransition**konfigurációs paraméterrel van megadva.<br> Az állapota a tároló (StateThresholdPercentage) példányainak legrosszabb 90%-ában van kiszámítva, a tároló állapotának súlyossága csökkenő sorrendbe rendezve (azaz kritikus, figyelmeztetés, kifogástalan).<br> Ha egy tároló-példány nem kap rekordot, akkor a rendszer **ismeretlenként**jelenti a tároló példányának állapotát, és a rendezési sorrend magasabb prioritással rendelkezik a **kritikus** állapotra vonatkozóan.<br> A rendszer minden egyes tároló-példány állapotát a konfigurációban megadott küszöbértékek alapján számítja ki. Ha a használat meghaladja a kritikus küszöbértéket (90%), akkor a példány **kritikus** állapotban van, ha az kisebb, mint a kritikus küszöbérték (90%) de nagyobb a figyelmeztetési küszöbértéknél (80%), a példány **Figyelmeztetési** állapotban van. Ellenkező esetben **kifogástalan** állapotban van. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Tároló CPU-kihasználtsága |Egység figyelője |Ez a figyelő a tároló példányainak CPU-kihasználtságának összesített állapotát jelenti.<br> Egy egyszerű összehasonlítást végez, amely összehasonlítja az egyes mintákat egyetlen küszöbértékkel, és a **ConsecutiveSamplesForStateTransition**konfigurációs paraméterrel van megadva.<br> Az állapota a tároló (StateThresholdPercentage) példányainak legrosszabb 90%-ában van kiszámítva, a tároló állapotának súlyossága csökkenő sorrendbe rendezve (azaz kritikus, figyelmeztetés, kifogástalan).<br> Ha egy tároló-példány nem kap rekordot, akkor a rendszer **ismeretlenként**jelenti a tároló példányának állapotát, és a rendezési sorrend magasabb prioritással rendelkezik a **kritikus** állapotra vonatkozóan.<br> A rendszer minden egyes tároló-példány állapotát a konfigurációban megadott küszöbértékek alapján számítja ki. Ha a használat meghaladja a kritikus küszöbértéket (90%), akkor a példány **kritikus** állapotban van, ha az kisebb, mint a kritikus küszöbérték (90%) de nagyobb a figyelmeztetési küszöbértéknél (80%), a példány **Figyelmeztetési** állapotban van. Ellenkező esetben **kifogástalan** állapotban van. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|A rendszerterhelési hüvelyek készen állnak |Egység figyelője |Ez a figyelő a hüvelyek százalékos arányán alapuló, üzemkész állapotban lévő állapotot jeleníti meg egy adott munkaterhelés esetében. Az állapota **kritikus** értékre van állítva, ha a hüvelyek kevesebb mint 100%-a **kifogástalan** állapotban van |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Kube API-állapot |Egység figyelője |Ez a figyelő a Kube API szolgáltatás állapotát jelzi. A figyelő kritikus állapotban van abban az esetben, ha a Kube API-végpont nem érhető el. Ennél a Figyelőnél az állapotot a Kube-API-kiszolgáló "csomópontok" végpontjának lekérdezésével határozzák meg. Minden más, mint egy OK válasz kódja **kritikus** állapotra változtatja a figyelőt. | Nincsenek konfigurációs tulajdonságok |||

### <a name="aggregate-monitors"></a>Összesített figyelők

|**Figyelő neve** | **Leírás** | **Algoritmus** |
|-----------------|-----------------|---------------|
|Csomópont |Ez a figyelő az összes csomópont-figyelő összesítése. Megegyezik a gyermek figyelő állapotával a legrosszabb állapottal:<br> Csomópont CPU-kihasználtsága<br> Node memória kihasználtsága<br> Csomópont állapota | Legrosszabb|
|Csomópont-készlet |Ez a figyelő a csomópont-készlet *agentpool*lévő összes csomópont összevont állapotát jelenti. Ez egy három állapotú figyelő, amelynek állapota a csomópont-készlet csomópontjainak 80%-a legrosszabb állapotán alapul, a csomóponti állapotok súlyosságának csökkenő sorrendbe rendezve (azaz kritikus, figyelmeztetés, kifogástalan).|Százalék |
|Csomópontok (csomópont-készlet szülőjének) |Ez az összes csomópont-készlet összesített figyelője. Az állapota a gyermek figyelők (azaz a fürtben lévő csomópont-készletek) legrosszabb állapotán alapul. |Legrosszabb |
|Fürt (csomópontok szülőjének/<br> Kubernetes-infrastruktúra) |Ez az a szülő-figyelő, amely megfelel a gyermek figyelőnek a legrosszabb állapottal, amely az infrastruktúra és a csomópontok kubernetes. |Legrosszabb |
|Kubernetes-infrastruktúra |Ez a figyelő a fürt felügyelt infrastruktúra összetevőinek összevont állapotát jelenti. az állapota a "legrosszabb" a gyermek figyelő állapotának számít, azaz a Kube-rendszerterhelések és az API-kiszolgáló állapota. |Legrosszabb|
|Rendszerterhelés |Ez a figyelő a Kube számítási feladatok állapotát jelzi. Ez a figyelő megfelel a gyermek figyelő állapotának a legrosszabb állapottal, azaz a **hüvelyek üzemkész állapotban** vannak (a figyelő és a számítási feladatok tárolói). |Legrosszabb |
|Tároló |Ez a figyelő egy adott munkaterhelés egy tárolójának általános állapotát jelenti. Ez a figyelő megegyezik a gyermek figyelő azon állapotával, amely a legrosszabb állapotú, azaz a **CPU-kihasználtság** és a **memóriahasználat** figyelése. |Legrosszabb |

## <a name="next-steps"></a>További lépések

A Kubernetes-fürt állapotának megtekintéséhez tekintse meg a [fürt állapotának figyelése](container-insights-health.md) című témakört.
