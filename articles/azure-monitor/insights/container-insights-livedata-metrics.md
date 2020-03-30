---
title: A mérőszámok valós idejű megtekintése az Azure Monitor tárolókhoz szolgáltatásával | Microsoft dokumentumok
description: Ez a cikk ismerteti a metrikák valós idejű nézetét kubectl használata nélkül az Azure Monitor tárolók.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216592"
---
# <a name="how-to-view-metrics-in-real-time"></a>A mérőszámok valós idejű megtekintése

Az Azure Monitor a tárolók Live Data (előzetes verzió) funkció lehetővé teszi, hogy valós időben vizualizálja a csomópontok és a pod állapota a fürtben metrikákat. A program a , `kubectl top nodes` `kubectl get pods –all-namespaces`és `kubectl get nodes` a parancsait emulálja, hogy hívja, elemezze és vizualizálja az adatokat az Insight ban szereplő teljesítménydiagramokban. 

Ez a cikk részletes áttekintést nyújt, és segít megérteni, hogyan használhatja ezt a funkciót.  

>[!NOTE]
>Ez a szolgáltatás nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AKS-fürtöket. Ez a szolgáltatás a Kubernetes API közvetlen elérésére támaszkodik a böngészőből származó proxykiszolgálón keresztül. Ha engedélyezi a hálózati biztonságot a Kubernetes API letiltásának a proxyról, az blokkolja ezt a forgalmat. 

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure US Government.

A Live Data (előzetes verzió) funkció beállításával vagy hibaelhárításával kapcsolatos segítségért olvassa el [a beállítási útmutatót.](container-insights-livedata-setup.md)

## <a name="how-it-works"></a>Működési elv 

A Live Data (előzetes verzió) funkció közvetlenül hozzáfér a Kubernetes API-hoz, és további információk a hitelesítési modellről [itt](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)találhatók. 

Ez a szolgáltatás lekérdezési műveletet hajt végre `/api/v1/nodes` `/apis/metrics.k8s.io/v1beta1/nodes`a `/api/v1/pods`metrikák végpontjai (beleértve , és ), amely alapértelmezés szerint öt másodpercenként történik. Ezeket az adatokat a böngésző gyorsítótárazza, és a **Fürt** lapon lévő tárolók azure-figyelőjében található négy teljesítménydiagram ábrázolja a **Go Live (előzetes verzió)** lehetőség kiválasztásával. Minden ezt követő szavazás egy gördülő ötperces vizualizációs ablakba kerül. 

![Élő adás a Fürt nézetben](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

A lekérdezési időköz a **Set interval** legördülő menüből van konfigurálva, így 1, 5, 15 és 30 másodpercenként állíthatja be az új adatok lekérdezését. 

![Ugrás élő legördülő lekérdezési időköz](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Azt javasoljuk, hogy a lekérdezési időközt egy másodpercre, míg a hibaelhárítás egy rövid ideig. Ezek a kérések hatással lehetnek a Kubernetes API rendelkezésre állására és szabályozására a fürtön. Ezt követően konfigurálja újra hosszabb lekérdezési időközre. 

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárol véglegesen adatokat. A munkamenet során rögzített összes információ azonnal törlődik, amikor bezárja a böngészőt, vagy elnavigál a funkciótól. Az adatok csak az ötperces ablakon belüli vizualizációhoz maradnak meg; az öt percnél régebbi mérőszámok is véglegesen törlődnek.

Ezek a diagramok nem rögzíthetők az élő módban megtekintett utolsó Azure-irányítópulthoz.

## <a name="metrics-captured"></a>Rögzített mérőszámok

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Csomópont cpu-kihasználtsága % / Csomópontmemória kihasználtsága % 

Ez a két teljesítménydiagram a `kubectl top nodes` **CPU%** és a **MEMORY%** oszlopok megfelelőjének felel meg a megfelelő diagramon. 

![Kubectl felső csomópontok példa eredmények](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Csomópontok CPU-kihasználtsági százalékdiagramja](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Csomópontmemória kihasználtsági százaléka diagram](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

A percentilis számítások nagyobb fürtökben fognak működni, hogy könnyebben azonosíthassák a fürt kiugró csomópontjait. Például annak megértéséhez, hogy a csomópontok alul-kihasznált leskálázási célokra. A **Min** összesítés tkihasználva láthatja, hogy mely csomópontok nak van alacsony kihasználtsága a fürtben. További vizsgálathoz válassza ki a **Csomópontok** lapot, és rendezze a rácsot processzor vagy memóriakihasználtság szerint.

Ez is segít megérteni, hogy mely csomópontok vannak leküldéses a határértékeket, és ha horizontális felskálázás ra lehet szükség. A **Max** és a **P95** összesítések segítségével ellenőrizheti, hogy vannak-e magas erőforrás-kihasználtsú csomópontok a fürtben. További vizsgálathoz ismét át kell **váltania** a Csomópontok lapra.

### <a name="node-count"></a>Csomópontszám

Ez a teljesítménydiagram az **ÁLLAPOT** `kubectl get nodes` oszlop meghívásával és hozzárendelésével egyenértékű állapotot rendel egy állapottípusok szerint csoportosított diagramhoz.

![Kubectl kap csomópontok példa eredmények](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Csomópontok száma diagram](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

A csomópontok at **kész** vagy **nem kész** állapotban jelentik. A (megszámlálják a számlálást) és létrejön a teljes szám), és a két összesítés eredménye megjelenik.
Például annak megértéséhez, hogy a csomópontok sikertelen állapotokba esnek-e. A Nem **kész** összesítés tkihasználva gyorsan megtekintheti a fürt csomópontjainak számát jelenleg **nem kész** állapotban.

### <a name="active-pod-count"></a>Aktív podok száma

Ez a teljesítménydiagram a diagram `kubectl get pods –all-namespaces` állapottípusai szerint csoportosított **ÁLLAPOT** oszlopának megfelelő jére van leképezve.

![Kubectl kap podok példa eredmények](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Csomópontok podszámláló-diagramja](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Előfordulhat, hogy az `kubectl` állapot oka nem egyezik meg pontosan a diagramon. 

## <a name="next-steps"></a>További lépések

Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez riasztások, vizualizációk létrehozásához vagy a fürtök további elemzéséhez.
