---
title: A tárolók Azure Monitor hibáinak megoldása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani és elhárítani a tárolók Azure Monitorével kapcsolatos problémákat.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403379"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>A tárolók Azure Monitor hibaelhárítása

Ha az Azure Kubernetes Service (ak) fürt figyelését konfigurálja Azure Monitor a tárolók számára, előfordulhat, hogy az adatgyűjtés vagy a jelentéskészítés állapotát megakadályozó probléma merülhet fel. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Engedélyezési hiba a bevezetési vagy frissítési művelet közben
A tárolók Azure Monitorának engedélyezése vagy a fürt frissítése a metrikák gyűjtésének támogatásához a következőhöz hasonló hibaüzenetet kaphat: *az ügyfél < felhasználói identitása > a (z) < felhasználó objectId >) nem jogosult a (z) "Microsoft. Authorization/roleAssignments/Write" művelet végrehajtására a hatókörön* kívül

A bevezetési vagy frissítési folyamat során a rendszer a fürt erőforrásán kísérli meg a **figyelési metrikák közzétevő** szerepkör-hozzárendelésének megadását. A felhasználónak a tárolók Azure Monitor engedélyezését kezdeményező vagy a metrikák gyűjtését támogató frissítéshez hozzáféréssel kell rendelkeznie a **Microsoft. Authorization/roleAssignments/Write** engedélyhez az AK fürterőforrás-hatókörén. Ehhez az engedélyhez csak a **tulajdonos** és a **felhasználói hozzáférés rendszergazdai** beépített szerepköreinek tagjai kapnak hozzáférést. Ha a biztonsági szabályzatok részletességi szintű engedélyek hozzárendelését igénylik, javasoljuk, hogy [Egyéni szerepköröket](../../role-based-access-control/custom-roles.md) tekintse meg, és rendelje hozzá azokat a felhasználókat, akiknek szükségük van rá. 

Ezt a szerepkört manuálisan is megadhatja a Azure Portal az alábbi lépések végrehajtásával:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
2. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a következőt: **Kubernetes**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Azure Kubernetes**lehetőséget.
3. A Kubernetes-fürtök listájában válasszon egyet a listából.
2. A bal oldali menüben kattintson a **hozzáférés-vezérlés (iam)** elemre.
3. Válassza a **+ Hozzáadás** lehetőséget egy szerepkör-hozzárendelés hozzáadásához, majd válassza ki a **figyelési metrikák közzétevői** szerepkört, és a **válassza** ki a négyzetet a **következő típussal** : a rendszer az előfizetésben definiált fürtök egyszerű szolgáltatásai alapján szűri az eredményeket. Válassza ki azt a listából, amely az adott fürtre vonatkozik.
4. A szerepkör hozzárendelésének befejezéséhez kattintson a **Mentés** gombra. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>A tárolók Azure Monitor engedélyezve vannak, de nem jelentenek információt
Ha a tárolók Azure Monitor sikeresen engedélyezve és konfigurálva van, de nem tekintheti meg az állapotadatokat, vagy a rendszer nem ad vissza eredményeket egy napló lekérdezésből, a következő lépésekkel diagnosztizálhatja a problémát: 

1. Az ügynök állapotának ellenőrzéséhez futtassa a parancsot: 

    `kubectl get ds omsagent --namespace=kube-system`

    A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. A parancs használatával keresse meg a központi telepítés állapotát az ügynök *06072018* -as vagy újabb verziójával:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A kimenetnek az alábbi példához hasonlónak kell lennie, amely azt jelzi, hogy megfelelően lett telepítve:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Ellenőrizze a pod állapotát annak ellenőrzéséhez, hogy fut-e a parancs használatával: `kubectl get pods --namespace=kube-system`

    A kimenetnek az alábbi példához hasonlónak kell lennie a omsagent *futtatási* állapotával:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Keresse meg az ügynök naplóit. Ha a tároló ügynök üzembe helyezése megtörténik, a rendszer egy gyors ellenőrzés futtatásával a (z) és az ügynök és a szolgáltató verzióját jeleníti meg. 

5. Annak ellenőrzéséhez, hogy az ügynök telepítése sikeresen megtörtént-e, futtassa a következő parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

    Az állapotnak az alábbi példához hasonlónak kell lennie:

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

Az alábbi táblázat összefoglalja a Azure Monitor for containers használata során felmerülő ismert hibákat.

| Hibaüzenetek  | Műveletek |  
| ---- | --- |  
| Hibaüzenet `No data for selected filters`  | Eltarthat egy ideig, hogy az újonnan létrehozott fürtök monitorozási folyamatát is létrehozza. Legalább 10 – 15 percet is igénybe vehet, amíg az adatai megjelennek a fürt számára. |   
| Hibaüzenet `Error retrieving data` | Az Azure Kubernetes Service-fürt állapota és a teljesítmény figyelése mellett kapcsolat jön létre a fürt és az Azure Log Analytics munkaterület között. A fürt összes figyelési adatait egy Log Analytics munkaterület használatával lehet tárolni. Ez a hiba akkor fordulhat elő, ha a Log Analytics munkaterület törölve lett. Ellenőrizze, hogy a munkaterület törölve lett-e, és ha igen, akkor újra engedélyeznie kell a fürt figyelését Azure Monitor a tárolók számára, és meg kell adnia egy meglévőt, vagy létre kell hoznia egy új munkaterületet. Az ismételt engedélyezéshez [le kell tiltania](container-insights-optout.md) a fürt figyelését, és újra [engedélyeznie](container-insights-enable-new-cluster.md) kell a tárolók Azure monitorét. |  
| `Error retrieving data` a tárolók Azure Monitor hozzáadása után az az AK CLI használatával | Ha `az aks cli`használatával engedélyezi a figyelést, előfordulhat, hogy a tárolók Azure Monitor nincsenek megfelelően telepítve. Győződjön meg arról, hogy telepítve van-e a megoldás. Ehhez lépjen a Log Analytics munkaterületre, és ellenőrizze, hogy elérhető-e a megoldás a bal oldali ablaktáblán a **megoldások** lehetőség kiválasztásával. A probléma megoldásához újra kell telepítenie a megoldást a [Azure monitor a tárolók telepítésére](container-insights-onboard.md) vonatkozó utasításokat követve. |  

A probléma diagnosztizálása érdekében az [itt](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)elérhető hibaelhárítási parancsfájlt biztosítunk.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>A containers Agent ReplicaSet-hüvelyek Azure Monitor nem az Azure Kubernetes-fürtön vannak ütemezve

Azure Monitor a containers Agent ReplicaSet-hüvelye a következő csomópont-választókkal rendelkezik a munkavégző (vagy ügynök) csomópontokon az ütemezéshez:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Ha a munkavégző csomópontokhoz nincsenek csatolva csomópont-címkék, akkor az Agent ReplicaSet-hüvelyek nem lesznek ütemezve. A címke csatolására vonatkozó utasításokért tekintse meg a [Kubernetes hozzárendelése címke-választókat](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>A teljesítmény-diagramok nem jelenítik meg a csomópontok és tárolók PROCESSZORát és memóriáját egy nem Azure-fürtön

A containers Agent Azure Monitor a cAdvisor végpontot használja a csomópont-ügynökön a teljesítmény metrikáinak összegyűjtéséhez. Ellenőrizze, hogy a csomóponton lévő tároló ügynök úgy van-e konfigurálva, hogy az `cAdvisor port: 10255` a fürt összes csomópontján meg lehessen nyitni a teljesítmény-metrikák összegyűjtéséhez.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Nem Azure-beli Kubernetes-fürt nem jelenik meg Azure Monitor tárolók számára

Ha a nem Azure-beli Kubernetes-fürtöt tárolók Azure Monitor szeretné megtekinteni, olvasási hozzáférésre van szükség az ezt az elemzést támogató Log Analytics munkaterületen, valamint a Container betekintési megoldás erőforrás- **ContainerInsights (*munkaterület*)** .

## <a name="next-steps"></a>Következő lépések

Ha a figyelés engedélyezve van az AK-fürtcsomópontok és-hüvelyek állapot-metrikáinak rögzítéséhez, ezek az állapot-mérőszámok a Azure Portal érhetők el. Az Azure Monitor for containers használatának megismeréséhez tekintse meg az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md)című témakört.
