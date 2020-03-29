---
title: Az Azure Monitor hibáinak elhárítása a tárolókhoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan háríthatja el és oldhatja meg a tárolók azure-figyelővel kapcsolatos problémákat.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403379"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Az Azure Monitor hibáinak elhárítása a tárolókhoz

Amikor konfigurálja az Azure Kubernetes-szolgáltatás (AKS) fürtjének figyelését az Azure Monitor tárolókhoz, előfordulhat, hogy az adatgyűjtés vagy a jelentéskészítés iményének megakadályozása miatt fordulhat elő. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Engedélyezési hiba a bevezetési vagy frissítési művelet során
Miközben engedélyezi az Azure Monitor tárolók vagy frissítése a fürt, hogy támogassa a mérőszámok gyűjtése, előfordulhat, hogy a következőhöz hasonló hibaüzenet jelenik meg – *Az ügyfél <a felhasználó identitás->" objektumazonosító "<felhasználó objectId>" nem rendelkezik engedéllyel a "Microsoft.Authorization/roleAssignments/write" művelet hatókörön keresztüli végrehajtásához*

A bevezetési vagy frissítési folyamat során a **figyelési metrikák** közzétevőszerepkör-hozzárendelést a fürterőforráson kísérelmeg megadni. A folyamatot kezdeményező felhasználó, hogy engedélyezze az Azure Monitor tárolók vagy a frissítés, hogy támogassa a metrikák gyűjteményének hozzáféréssel kell rendelkeznie a **Microsoft.Authorization/roleAssignments/write** engedélyt az AKS-fürt erőforrás-hatókör. Ehhez az engedélyhez csak a **Tulajdonos** és **a Felhasználói hozzáférés rendszergazdájának** beépített szerepkörei kapnak hozzáférést. Ha a biztonsági házirendek részletes szintű engedélyek hozzárendelését igénylik, javasoljuk, hogy tekintse meg [az egyéni szerepköröket,](../../role-based-access-control/custom-roles.md) és rendelje hozzá azokat a felhasználókhoz, akiknek szükségük van rá. 

Ezt a szerepkört manuálisan is megadhatja az Azure Portalon a következő lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Kubernetes ( Kubernetes**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Azure Kubernetes lehetőséget.**
3. A Kubernetes-fürtök listájában válasszon egyet a listából.
2. A bal oldali menüben kattintson a **Hozzáférés-vezérlés (IAM) parancsra.**
3. Válassza a **+ Add** szerepkör-hozzárendelés hozzáadásához válassza ki a **Monitoring Metrics Publisher** szerepkört, és a **Select** mező ben **az AKS** mező ben szűrheti az eredményeket csak az előfizetésben definiált fürtök szolgáltatásnévi tagokon. Válassza ki azt a fürthöz tartozó elemet a listából.
4. A szerepkör hozzárendelésének befejezéséhez válassza a **Mentés** lehetőséget. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Az Azure Monitor a tárolók engedélyezve van, de nem jelent semmilyen információt
Ha az Azure Monitor tárolókhoz sikeresen engedélyezve van, és konfigurálva van, de nem tekintheti meg az állapotadatokat, vagy nem ad vissza eredményt egy naplólekérdezésből, az alábbi lépésekkel diagnosztizálhatja a problémát: 

1. Ellenőrizze az ügynök állapotát a parancs futtatásával: 

    `kubectl get ds omsagent --namespace=kube-system`

    A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Ellenőrizze a központi telepítési állapotot a *06072018-as* vagy újabb ügynökverzióval a következő paranccsal:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A kimenetnek a következő példához kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Ellenőrizze a pod állapotát, és ellenőrizze, hogy fut-e a paranccsal:`kubectl get pods --namespace=kube-system`

    A kimenetnek a következő példához kell hasonlítania, *amelynek állapota Futás* az omsagenthez:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Ellenőrizze az ügynök naplóit. Amikor a tárolóügynök üzembe kerül, az OMI-parancsok futtatásával gyors ellenőrzést futtat, és megjeleníti az ügynök és a szolgáltató verzióját. 

5. Az ügynök sikeres telepítésének ellenőrzéséhez futtassa a következő parancsot:`kubectl logs omsagent-484hw --namespace=kube-system`

    Az állapotnak a következő példához kell hasonlítania:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Hibaüzenetek

Az alábbi táblázat összefoglalja az okat, amelyek az Azure Monitor tárolókhoz való használata során találkozhatnak.

| Hibaüzenetek  | Műveletek |  
| ---- | --- |  
| Hibaüzenet`No data for selected filters`  | Az újonnan létrehozott fürtök figyelési adatfolyamának beállítása időt vehet idénybe. Legalább 10–15 percet várjon, amíg az adatok megjelennek a fürtben. |   
| Hibaüzenet`Error retrieving data` | Míg az Azure Kubernetes Service cluster állapot- és teljesítményfigyelésre van beállítva, a fürt és az Azure Log Analytics-munkaterület között létrejön a kapcsolat. A Log Analytics-munkaterület a fürt összes figyelési adatának tárolására szolgál. Ez a hiba akkor fordulhat elő, ha a Log Analytics munkaterületet törölték. Ellenőrizze, hogy törölték-e a munkaterületet, és ha az volt, újra engedélyeznie kell a fürt figyelését az Azure Monitor tárolókhoz, és meg kell adnia egy meglévőt, vagy új munkaterületet kell létrehoznia. Az újbóli engedélyezéséhez le kell [tiltania](container-insights-optout.md) a fürt figyelését, és újra [engedélyeznie](container-insights-enable-new-cluster.md) kell az Azure Monitort a tárolókhoz. |  
| `Error retrieving data`az aks cli-n keresztüli tárolók Azure Monitor-figyelőjének hozzáadása után | Ha engedélyezi `az aks cli`a figyelést a használatával, előfordulhat, hogy a tárolók Azure Monitor szolgáltatása nincs megfelelően telepítve. Ellenőrizze, hogy a megoldás telepítve van-e. Ehhez nyissa meg a Log Analytics-munkaterületet, és nézze meg, hogy a megoldás elérhető-e a **megoldások** kiválasztásával a bal oldali ablaktáblából. A probléma megoldásához újra kell telepítenie a megoldást az [Azure Monitor tárolókhoz való üzembe helyezésére](container-insights-onboard.md) vonatkozó utasításokat követve. |  

A probléma diagnosztizálása érdekében itt [egy](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)hibaelhárító parancsfájlt biztosítottunk.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>A Replikácsika-podok tárolóügynök Azure-figyelője nincs ütemezve nem Azure-kubernetes-fürtön

A Replikáni-podok tárolóügynök-ügynök Azure-figyelője a következő csomópontválasztóktól függ az ütemezés hez tartozó feldolgozó (vagy ügynök) csomópontokon:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Ha a munkavégző csomópontok nem rendelkeznek csomópontcímkék csatolt, majd ügynök ReplicaSet Pods nem lesz ütemezve. A címke csatolásáról a [Kubernetes címkeválasztók hozzárendelése](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) című dokumentumban talál útmutatást.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>A teljesítménydiagramok nem jelenítik meg a PROCESSZORt vagy a csomópontok és tárolók memóriáját egy nem Azure-fürtön

Az Azure Monitor tárolók ügynök podok használja a cAdvisor-végpont a csomópont ügynök a teljesítménymetrikák összegyűjtése. Ellenőrizze, hogy a csomóponton lévő tárolóügynök úgy van-e beállítva, hogy lehetővé tegye `cAdvisor port: 10255` a fürt összes csomópontján való megnyitást a teljesítménymutatók összegyűjtéséhez.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>A nem Azure-kubernetes-fürt nem jelenik meg az Azure Monitor tárolókban

A nem Azure-kubernetes-fürt megtekintéséhez az Azure Monitor tárolók, olvasási hozzáférés szükséges a Log Analytics munkaterület támogatja ezt az Insight és a Container Insights megoldás erőforrás **ContainerInsights (*munkaterület*).**

## <a name="next-steps"></a>További lépések

Ha a figyelés engedélyezve van az AKS-fürtcsomópontok és a podok állapotmetrikának rögzítéséhez, ezek az állapotmetrikák elérhetők az Azure Portalon. Az Azure [Kubernetes-szolgáltatás állapotának megtekintése.](container-insights-analyze.md)
