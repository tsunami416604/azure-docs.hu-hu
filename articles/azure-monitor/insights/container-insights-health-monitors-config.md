---
title: Azure Monitor tárolók állapotfigyelőinek konfigurációjához | Microsoft dokumentumok
description: Ez a cikk az Azure Monitor tárolók állapotfigyelőinek részletes konfigurációját ismertető tartalmat tartalmaz.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055706"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Azure Monitor tárolók állapotfigyelőkonfigurációs útmutatójához

A figyelők az elsődleges elemek az állapot méréséhez és a hibák észleléséhez az Azure Monitor tárolókhoz. Ez a cikk segít megérteni az állapot mérésének módját, valamint azokat az elemeket, amelyek a Kubernetes-fürt állapotát az [Állapot (előzetes verzió)](container-insights-health.md) funkcióval figyelik és jelentik.

>[!NOTE]
>Az Állapot funkció jelenleg nyilvános előzetes verzióban érhető el.
>

## <a name="monitors"></a>Monitorozások

A monitor egy felügyelt objektum bizonyos aspektusának állapotát méri. A figyelők mindegyike két vagy három állapotot. A monitor lesz az egyik és csak az egyik potenciális államok egy adott időpontban. Amikor a tárolóügynök által betöltött figyelő, azt egy kifogástalan állapotba inicializálódik. Az állapot csak akkor változik, ha a megadott feltételeket egy másik állapotra észleli.

Egy adott objektum általános állapotát az egyes monitorok állapota határozza meg. Ezt a hierarchiát az Azure-figyelő állapothierarchia ablaktáblája mutatja be. Az állapot összesítésének házirendje az összesítő figyelők konfigurációjának része.

## <a name="types-of-monitors"></a>A monitorok típusai

|Figyelés | Leírás | 
|--------|-------------|
| Egység monitor |Az egységfigyelő egy erőforrás vagy alkalmazás bizonyos aspektusait méri. Ez lehet, hogy ellenőrzi a teljesítményszámláló teljesítményét az erőforrás teljesítményét, vagy annak elérhetőségét. |
|Összesítő figyelő | Összesítve monitorok csoport több figyelők egyetlen állapot összesített állapot biztosítása érdekében. Az egységfigyelők általában egy adott összesítő figyelő alatt vannak konfigurálva. Egy csomópontösszesítő figyelő például összegzi a csomóponti processzor kihasználtságának, a memóriakihasználtságnak és a csomópontállapotnak az állapotát.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Összesített figyelő állapotösszesítési szabályzata

Minden összesítő figyelő egy állapotösszesítési szabályzatot határoz meg, amely az összesített figyelő állapotának meghatározására használt logika az alatta lévő figyelők állapota alapján. Az összesítő figyelő lehetséges állapotösszesítési házirendjei a következők:

#### <a name="worst-state-policy"></a>Legrosszabb állami politika

Az összesített figyelő állapota a legrosszabb állapottal rendelkező gyermekfigyelő állapotának felel meg. Ez az összesítő figyelők által használt leggyakoribb házirend.

![Példa az összesített monitor összesítő legrosszabb állapotára](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Százalékos házirend

A forrásobjektum a célobjektumok adott százalékának egyetlen tagjának legrosszabb állapotának felel meg a legjobb állapotban. Ez a házirend akkor használatos, ha a célobjektumok egy bizonyos százalékának kifogástalannak kell lennie ahhoz, hogy a célobjektum kifogástalannak minősüljön. A százalékos házirend a figyelőket az állapot súlyosságának csökkenő sorrendjében rendezi, és az összesített figyelő állapotát a rendszer az N% legrosszabb állapotaként számítja ki (az N-t a *StateThresholdPercentage*konfigurációs paraméter határozza meg).

Tegyük fel például, hogy egy tárolórendszerkép öt tárolópéldánya van, és az egyes állapotok **kritikusak**, **figyelmeztetés,** **kifogástalan,** **kifogástalan,** **kifogástalan.**  A tároló CPU-kihasználtsági figyelőjének állapota **kritikus**lesz, mivel a tárolók legrosszabb 90%-a **kritikus,** ha csökkenő súlyossági sorrendben van rendezve.

## <a name="understand-the-monitoring-configuration"></a>A figyelési konfiguráció megismerése

Az Azure Monitor tárolók hoz számos kulcsfontosságú figyelési forgatókönyvek, amelyek a következőképpen vannak konfigurálva.

### <a name="unit-monitors"></a>Egységmonitorok

|**Figyelő neve** | Figyelő típusa | **Leírás** | **Paraméter** | **Érték** |
|-----------------|--------------|-----------------|---------------|-----------|
|Csomópontmemória kihasználtsága |Egység monitor |Ez a figyelő percenként kiértékeli egy csomópont memóriakihasználtságát a cadvisor által jelentett adatok alapján. |Egymást követőmintákAzátra<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Csomópont processzorának kihasználtsága |Egységfigyelő |Ez a figyelő percenként ellenőrzi a csomópont CPU-kihasználtságát a cadvisor által jelentett adatok használatával. | Egymást követőmintákAzátra<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Csomópont állapota |Egység monitor |Ez a figyelő a Kubernetes által jelentett csomópontfeltételeket ellenőrzi.<br> Jelenleg a következő csomópontfeltételek vannak bejelölve: Lemeznyomás, Memórianyomás, PID-nyomás, Lemezen kívül, A hálózat nem érhető el, A csomópont készenléti állapota.<br> A fenti feltételek közül, ha a *Lemezen kívül* vagy a *Hálózat nem érhető el* **érték,** a figyelő **kritikus** állapotra változik.<br> Ha bármely más feltétel **igaz**, a **Kész** állapoton kívül a figyelő **figyelmeztetési** állapotra változik. | NodeConditionTypeForFailedState | outofdisk,hálózat nem érhető el ||
|Tárolómemória-kihasználtság |Egység monitor |Ez a figyelő a tároló példányainak memóriakihasználtságának (RSS) együttes állapotát jelenti.<br> Egyszerű összehasonlítást hajt végre, amely az egyes mintákat egyetlen küszöbértékhez hasonlítja, és a **ConsecutiveSamplesForStateTransition**konfigurációs paraméterrel adja meg.<br> Az állapot kiszámítása a legrosszabb 90%-a tároló (StateThresholdPercentage) példányok, csökkenő sorrendben a tároló állapota állapota (azaz kritikus, figyelmeztetés, kifogástalan).<br> Ha nem kap rekordot egy tárolópéldány, majd a tárolópéldány állapota ismeretlen ként lesz **jelentve,** és magasabb prioritást élvez a **kritikus** állapottal szemben a rendezési sorrendben.<br> Minden egyes tárolópéldány állapota a konfigurációban megadott küszöbértékek alapján kerül kiszámításra. Ha a használat túllépi a kritikus küszöbértéket (90%), akkor a példány **kritikus** állapotban van, ha az kevesebb, mint a kritikus küszöbérték (90%) de nagyobb, mint a figyelmeztetési küszöbérték (80%), akkor a példány **figyelmeztetési** állapotban van. Ellenkező esetben **kifogástalan állapotban** van. |Egymást követőmintákAzátra<br> FailIflessThanpercentage<br> StateThresholdSzázalék<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Tároló CPU-kihasználtsága |Egység monitor |Ez a figyelő a tároló példányainak processzorkihasználtságának együttes állapotát jelenti.<br> Egyszerű összehasonlítást hajt végre, amely az egyes mintákat egyetlen küszöbértékhez hasonlítja, és a **ConsecutiveSamplesForStateTransition**konfigurációs paraméterrel adja meg.<br> Az állapot kiszámítása a legrosszabb 90%-a tároló (StateThresholdPercentage) példányok, csökkenő sorrendben a tároló állapota állapota (azaz kritikus, figyelmeztetés, kifogástalan).<br> Ha nem kap rekordot egy tárolópéldány, majd a tárolópéldány állapota ismeretlen ként lesz **jelentve,** és magasabb prioritást élvez a **kritikus** állapottal szemben a rendezési sorrendben.<br> Minden egyes tárolópéldány állapota a konfigurációban megadott küszöbértékek alapján kerül kiszámításra. Ha a használat túllépi a kritikus küszöbértéket (90%), akkor a példány **kritikus** állapotban van, ha az kevesebb, mint a kritikus küszöbérték (90%) de nagyobb, mint a figyelmeztetési küszöbérték (80%), akkor a példány **figyelmeztetési** állapotban van. Ellenkező esetben **kifogástalan állapotban** van. |Egymást követőmintákAzátra<br> FailIflessThanpercentage<br> StateThresholdSzázalék<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|A rendszer munkaterhelési podjai készen állnak |Egység monitor |Ez a figyelő egy adott számítási feladatban a készenléti állapotban lévő podok százalékos aránya alapján jelenti az állapotot. Állapota **kritikus,** ha a podok kevesebb mint 100%-a **kifogástalan állapotban van.** |Egymást követőmintákAzátra<br> FailIflessThanpercentage |2<br> 100 ||
|Kube API állapota |Egység monitor |Ez a figyelő a Kube API-szolgáltatás állapotát jelenti. A figyelő kritikus állapotban van abban az esetben, ha a Kube API-végpont nem érhető el. Ebben a figyelőben az állapot határozza meg a kube-api-kiszolgáló "csomópontok" végpontjának lekérdezését. Az OK válaszkódon kívül bármi más **kritikus** állapotba módosítja a figyelőt. | Nincsenek konfigurációs tulajdonságok |||

### <a name="aggregate-monitors"></a>Összesített figyelők

|**Figyelő neve** | **Leírás** | **Algoritmus** |
|-----------------|-----------------|---------------|
|Csomópont |Ez a figyelő az összes csomópontfigyelő összesítése. A legrosszabb állapottal rendelkező gyermekfigyelő állapotának felel meg:<br> Csomópont processzorának kihasználtsága<br> Csomópontmemória kihasználtsága<br> Csomópont állapota | Legrosszabb|
|Csomópontkészlet |Ez a figyelő a csomópontkészlet *ügynökkészletében*lévő összes csomópont összesített állapotát jelenti. Ez egy három állapotfigyelő, amelynek állapota a csomópontkészlet csomópontjainak legrosszabb 80%-án alapul, csökkenő sorrendben a csomópontállapotok (azaz kritikus, figyelmeztetés, kifogástalan).|Százalék |
|Csomópontok (a csomópontkészlet szülője) |Ez az összes csomópontkészlet összesített figyelője. Állapota a gyermekfigyelők legrosszabb állapotán alapul (azaz a fürtben lévő csomópontkészleteken). |Legrosszabb |
|Fürt (csomópontok szülője/<br> Kubernetes infrastruktúra) |Ez a szülőfigyelő, amely megfelel a gyermekfigyelő állapotának a legrosszabb állapotú, azaz a kubernetes infrastruktúrával és a csomópontokkal. |Legrosszabb |
|Kubernetes-infrastruktúra |Ez a figyelő a fürt felügyelt infrastruktúra-összetevőinek együttes állapotát jelenti. állapota a gyermekfigyelő állapotának "legrosszabbjaként" kerül kiszámításra, azaz kube-system munkaterhelésként és API Server-állapotként. |Legrosszabb|
|Rendszer terhelése |Ez a figyelő a kube-system munkaterhelés állapotáról számol be. Ez a figyelő megfelel a gyermekfigyelő állapotának a legrosszabb állapotú, azaz a **podok kész állapotban** (figyelő és a tárolók a munkaterhelés). |Legrosszabb |
|Tároló |Ez a figyelő egy adott számítási feladatban lévő tároló általános egészségi állapotát jelenti. Ez a figyelő megfelel a gyermekfigyelő állapotának a legrosszabb állapottal, azaz a **processzorkihasználtság** és a **memóriakihasználtság** figyelőivel. |Legrosszabb |

## <a name="next-steps"></a>További lépések

Tekintse [meg a fürt állapotának figyelése,](container-insights-health.md) hogy megtudjon a Kubernetes-fürt állapotának megtekintéséről.
