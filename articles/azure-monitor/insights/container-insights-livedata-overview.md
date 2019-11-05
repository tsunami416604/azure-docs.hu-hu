---
title: Élő adatértékek (előzetes verzió) megtekintése az Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk a Kubernetes-naplók,-események és a pod-metrikák valós idejű nézetét írja le anélkül, hogy a kubectl-t használja a tárolók Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 9c497c73d42e1fb8851c5293010098d843297250
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514419"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>A Kubernetes-naplók,-események és a pod-metrikák valós idejű megtekintése

A tárolók Azure Monitor tartalmazza az élő adatok (előzetes verzió) szolgáltatást, amely egy fejlett diagnosztikai funkció, amely lehetővé teszi az Azure Kubernetes szolgáltatás (ak) tároló-naplófájljainak (StdOut/stderror), az események és a pod-metrikák közvetlen elérését. Közvetlen hozzáférést tesz elérhetővé `kubectl logs -c`hoz, `kubectl get` eseményekhez és `kubectl top pods`okhoz. A konzol ablaktáblán láthatók a tároló-motor által generált naplók, események és mérőszámok, amelyek a hibák valós idejű hibaelhárítását segítik elő.

Ez a cikk részletes áttekintést nyújt, és segít megérteni a funkció használatát. 

>[!NOTE]
>Ez a funkció nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AK-fürtöket. Ez a funkció arra támaszkodik, hogy közvetlenül a böngészőből egy proxykiszolgálón keresztül éri el a Kubernetes API-t. A hálózati biztonság engedélyezésével letilthatja a Kubernetes API-t ebből a proxyból, és letiltja a forgalmat. 

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure USA kormányzati szerveiben.

Az élő adatszolgáltatások (előzetes verzió) beállításához vagy hibaelhárításához tekintse át a [telepítési útmutatót](container-insights-livedata-setup.md). Ez a funkció közvetlenül hozzáfér a Kubernetes API-hoz, és a hitelesítési modellel kapcsolatos további információ [itt](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)található. 

## <a name="live-data-preview-functionality-overview"></a>Az élő adat (előzetes verzió) funkcióinak áttekintése

### <a name="search"></a>Keresés

![Példa az élő adatkonzol ablaktáblájának szűrésére](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

Az élő adat (előzetes verzió) szolgáltatás keresési funkciókat is tartalmaz. A **keresőmezőbe** az eredmények szűréséhez írja be a kulcs szót vagy kifejezést, és a rendszer kiemeli a megfelelő eredményeket a gyors áttekintés érdekében. Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **szűrő** pirulával is korlátozhatja. Attól függően, hogy melyik erőforrást választotta ki, a pirula egy Pod, Namespace vagy fürtöt listáz ki, amelyről a választott forrás van kiválasztva.  

### <a name="scroll-lock-and-pause"></a>Scroll lock és pause 

Az autogörgetés felfüggesztéséhez és a panel működésének szabályozásához, amely lehetővé teszi az új adatolvasás manuális görgetését, használhatja a **görgetőgomb** lehetőséget. Az autoscroll újbóli engedélyezéséhez egyszerűen válassza a **Scroll (görgetés** ) lehetőséget. A naplózási vagy az esemény adatainak lekérését is szüneteltetheti a **szüneteltetés** lehetőség kiválasztásával, és ha készen áll a folytatásra, egyszerűen válassza a **Lejátszás**lehetőséget.  

![Élő adatkonzol ablaktábla az élő nézet szüneteltetése](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>A probléma elhárítása során javasoljuk, hogy csak rövid idő alatt felfüggessze vagy szüneteltesse az autoscroll-et. Ezek a kérések befolyásolhatják a fürtön a Kubernetes API rendelkezésre állását és szabályozását. 

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárolja véglegesen az adattárolást. A rendszer a munkamenet során rögzített összes információt törli a böngésző bezárásakor, vagy innen navigál. Az adatok csak a metrika funkció öt perces időszakában jelennek meg a vizualizáción belül. az öt percnél régebbi metrikákat is törli a rendszer. Az élő adatok (előzetes verzió) pufferének lekérdezései az ésszerű memóriahasználat korlátain belül (itt pontosabban kell megadni, mi az ésszerű?). 

## <a name="view-logs"></a>Naplók megtekintése

A valós idejű naplózási adatok megtekinthetők úgy, ahogy a tároló motorja létrehozta a **csomópontok**, a **vezérlők**és a **tárolók** nézetből. A naplózási adatok megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.** 

3. Válassza ki a **csomópontok**, **vezérlők**vagy **tárolók** fület.

4. Válasszon ki egy objektumot a teljesítmény rácsból, és a jobb oldalon található Tulajdonságok ablaktáblán válassza az **élő adatok megtekintése (előzetes verzió)** lehetőséget. Ha az AK-fürt egyszeri bejelentkezéssel van konfigurálva az Azure AD-vel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.  

    >[!NOTE]
    >Amikor az Log Analytics munkaterületről tekinti meg az adatok megtekintését a Tulajdonságok ablaktáblán látható **Megtekintés az elemzésben** lehetőség kiválasztásával, a naplók keresési eredményei potenciálisan megjelennek a **csomópontok**, a **démon-készletek**, a **replikák**, a **feladatok**,  **Olyan cron-feladatok**, **hüvelyek**és **tárolók** , amelyek már nem léteznek. Kísérlet a naplók keresésére egy olyan tárolóban, amely nem érhető el a `kubectl`-ben is sikertelen lesz. A korábbi naplók, események és mérőszámok megtekintésével kapcsolatos további információkért tekintse át az [elemzési funkció nézetét](container-insights-log-search.md#search-logs-to-analyze-data) .  

A sikeres hitelesítés után az élő adat (előzetes verzió) konzol ablaktábla a teljesítményadatok rács alatt jelenik meg, ahol megtekintheti a naplózási adataikat egy folyamatos adatfolyamban. Ha a beolvasás állapotjelzője egy zöld pipa jelenik meg, amely a panel jobb szélén található, az azt jelenti, hogy a rendszer lekéri az adatait, és megkezdi a folyamatos átvitelt a konzolra.  

![Csomópont-tulajdonságok ablaktáblájának adatnézete beállítás](./media/container-insights-livedata-overview/node-properties-pane.png)  

A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva.

## <a name="view-events"></a>Események megtekintése

Megtekintheti a tároló motor által a **csomópontok**, **vezérlők**, **tárolók**és **központi telepítések (előzetes verzió)** által generált valós idejű események adatait, ha tároló, Pod, node, ReplicaSet, daemonset elemet, Job, CronJob vagy A központi telepítés van kiválasztva. Az események megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.** 

3. Válassza ki a **csomópontok**, **vezérlők**, **tárolók**vagy **központi telepítések (előzetes verzió)** lapot.

4. Válasszon ki egy objektumot a teljesítmény rácsból, és a jobb oldalon található Tulajdonságok ablaktáblán válassza az **élő adatok megtekintése (előzetes verzió)** lehetőséget. Ha az AK-fürt egyszeri bejelentkezéssel van konfigurálva az Azure AD-vel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.  

    >[!NOTE]
    >Amikor az Log Analytics munkaterületről tekinti meg az adatok megtekintését a Tulajdonságok ablaktáblán látható **Megtekintés az elemzésben** lehetőség kiválasztásával, a naplók keresési eredményei potenciálisan megjelennek a **csomópontok**, a **démon-készletek**, a **replikák**, a **feladatok**,  **Olyan cron-feladatok**, **hüvelyek**és **tárolók** , amelyek már nem léteznek. Kísérlet a naplók keresésére egy olyan tárolóban, amely nem érhető el a `kubectl`-ben is sikertelen lesz. A korábbi naplók, események és mérőszámok megtekintésével kapcsolatos további információkért tekintse át az [elemzési funkció nézetét](container-insights-log-search.md#search-logs-to-analyze-data) .  

A sikeres hitelesítés után az élő adat (előzetes verzió) konzol ablaktábla megjelenik a teljesítményadatok rács alatt. Ha a beolvasás állapotjelzője egy zöld pipa jelenik meg, amely a panel jobb szélén található, az azt jelenti, hogy a rendszer lekéri az adatait, és megkezdi a folyamatos átvitelt a konzolra. 
    
Ha a kiválasztott objektum egy tároló, válassza az **események** lehetőséget a panelen. Ha kiválasztotta a csomópontot, a hüvelyt vagy a vezérlőt, a rendszer automatikusan kijelöli az események megtekintését. 

    ![Controller properties pane view events](./media/container-insights-livedata-overview/controller-properties-live-events.png)  

A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva.

### <a name="filter-events"></a>Események szűrése 

Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **szűrő** pirulával is korlátozhatja. Attól függően, hogy melyik erőforrást választotta ki, a pirula egy Pod, Namespace vagy fürtöt listáz ki, amelyről a választott forrás van kiválasztva.  

## <a name="view-metrics"></a>Metrikák megtekintése 

A valós idejű metrikai adatokat úgy tekintheti meg, ahogy a tároló motorja a **csomópontok** vagy **vezérlők** nézetből csak akkor jelenik meg, ha egy **Pod** van kiválasztva. A metrikák megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.** 

3. Válassza ki a **csomópontok** vagy a **vezérlők** lapot.

4. Válasszon ki egy **Pod** objektumot a teljesítmény rácsból, és a jobb oldalon található Tulajdonságok ablaktáblán válassza az **élő adatok megtekintése (előzetes verzió)** lehetőséget. Ha az AK-fürt egyszeri bejelentkezéssel van konfigurálva az Azure AD-vel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.  

    >[!NOTE]
    >Amikor az Log Analytics munkaterületről tekinti meg az adatok megtekintését a Tulajdonságok ablaktáblán látható **Megtekintés az elemzésben** lehetőség kiválasztásával, a naplók keresési eredményei potenciálisan megjelennek a **csomópontok**, a **démon-készletek**, a **replikák**, a **feladatok**,  **Olyan cron-feladatok**, **hüvelyek**és **tárolók** , amelyek már nem léteznek. Kísérlet a naplók keresésére egy olyan tárolóban, amely nem érhető el a `kubectl`-ben is sikertelen lesz. A korábbi naplók, események és mérőszámok megtekintésével kapcsolatos további információkért tekintse át az [elemzési funkció nézetét](container-insights-log-search.md#search-logs-to-analyze-data) .  

A sikeres hitelesítés után az élő adat (előzetes verzió) konzol ablaktábla megjelenik a teljesítményadatok rács alatt. A rendszer beolvassa a metrikai adatokat, és megkezdi a folyamatos átvitelt a konzolon a két diagramon való megjelenítéshez. A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva.

![Példa a pod mérőszámok megtekintésére](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>További lépések

- A Azure Monitor használatának megismeréséhez és az AK-fürt egyéb szempontjainak figyeléséhez lásd: az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md).

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a riasztások, a vizualizációk vagy a fürtök további elemzéséhez.