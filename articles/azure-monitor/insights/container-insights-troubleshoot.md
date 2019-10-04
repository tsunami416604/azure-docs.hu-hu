---
title: Hogyan hibaelhárítása az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hibaelhárítása és tárolók az Azure Monitor szolgáltatással kapcsolatos problémák megoldásához.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: b6c245142eea12bcec5ed642ec9bd91a58e10eb0
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813768"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Hibaelhárítás az Azure Monitor for containers szolgáltatásban

Az Azure Kubernetes Service (AKS)-fürt figyelése az Azure Monitor szolgáltatással tárolók konfigurálásakor előfordulhat, hogy hibába ütközik megakadályozza az adatok gyűjtése vagy állapotát. Ez a cikk részletesen néhány gyakori hibák és hibaelhárítási lépéseket.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Engedélyezési hiba a bevezetési vagy frissítési művelet közben
A tárolók Azure Monitorának engedélyezése vagy a fürt frissítése a metrikák gyűjtésének támogatásához a következőhöz hasonló hibaüzenetet kaphat: *az ügyfél < felhasználói identitása > a (z) < felhasználó objectId >) nem rendelkezik engedéllyel a "Microsoft. Authorization/roleAssignments/Write" művelet végrehajtása a hatókörön keresztül*

A bevezetési vagy frissítési folyamat során a rendszer a fürt erőforrásán kísérli meg a **figyelési metrikák közzétevő** szerepkör-hozzárendelésének megadását. A felhasználónak a tárolók Azure Monitor engedélyezését kezdeményező vagy a metrikák gyűjtését támogató frissítéshez hozzáféréssel kell rendelkeznie a **Microsoft. Authorization/roleAssignments/Write** engedélyhez az AK fürterőforrás-hatókörén. Ehhez az engedélyhez csak a **tulajdonos** és a **felhasználói hozzáférés rendszergazdai** beépített szerepköreinek tagjai kapnak hozzáférést. Ha a biztonsági szabályzatok részletességi szintű engedélyek hozzárendelését igénylik, javasoljuk, hogy [Egyéni szerepköröket](../../role-based-access-control/custom-roles.md) tekintse meg, és rendelje hozzá azokat a felhasználókat, akiknek szükségük van rá. 

Ezt a szerepkört manuálisan is megadhatja a Azure Portal az alábbi lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a következőt: **Kubernetes**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Azure Kubernetes**lehetőséget.
3. A Kubernetes-fürtök listájában válasszon egyet a listából.
2. A bal oldali menüben kattintson a **hozzáférés-vezérlés (iam)** elemre.
3. Válassza a **+ Hozzáadás** lehetőséget egy szerepkör-hozzárendelés hozzáadásához, majd válassza ki a **figyelési metrikák közzétevői** szerepkört, és a **válassza** ki a négyzetet a következő típussal: a rendszer az előfizetésben definiált fürtök egyszerű szolgáltatásai alapján szűri az eredményeket. Válassza ki azt a listából, amely az adott fürtre vonatkozik.
4. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Az Azure Monitor for containers szolgáltatásban engedélyezve van, de semmilyen információt nem küld
Ha a tárolók Azure Monitor sikeresen engedélyezve és konfigurálva van, de nem tekintheti meg az állapotadatokat, vagy a rendszer nem ad vissza eredményeket egy napló lekérdezésből, a következő lépésekkel diagnosztizálhatja a problémát: 

1. Ellenőrizze az ügynök állapotát a parancs futtatásával: 

    `kubectl get ds omsagent --namespace=kube-system`

    A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. A központi telepítési állapotának az ügynök verziója *06072018* vagy később a parancs segítségével:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A kimenet a következő példának, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Ellenőrizze, hogy a parancs fut-e pod állapotának ellenőrzéséhez: `kubectl get pods --namespace=kube-system`

    A kimenet az alábbi példa állapottal kell hasonlítania *futó* a omsagent számára:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Ellenőrizze az ügynök bejegyzéseit. A tárolóalapú ügynök telepítésekor lekérdezi az OMI a következő parancsok futtatásával egy gyors ellenőrzés fut, és az ügynök és a szolgáltató verzióját jeleníti meg. 

5. Annak ellenőrzéséhez, hogy az ügynök telepítése sikeresen megtörtént-e, futtassa a következő parancsot:`kubectl logs omsagent-484hw --namespace=kube-system`

    Az állapot a következő példa kell hasonlítania:

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

Az alábbi táblázat összefoglalja az ismert hibák-tárolókhoz az Azure Monitor használata során találkozhat.

| Hibaüzenetek  | Műveletek |  
| ---- | --- |  
| Hibaüzenet `No data for selected filters`  | Ez eltarthat egy ideig, újonnan létrehozott fürtök esetében a figyelési adatok folyamat létrehozásához. Legalább 10 – 15 percet is igénybe vehet, amíg az adatai megjelennek a fürt számára. |   
| Hibaüzenet `Error retrieving data` | Azure Kubenetes Service-fürt beállítását állapotának és teljesítményének figyeléséhez, amíg a kapcsolat jön létre a fürt és az Azure Log Analytics-munkaterület között. Log Analytics-munkaterületet a fürt számára az összes monitorozási adat tárolására szolgál. Ez a hiba akkor fordulhat elő, ha a Log Analytics munkaterület törölve lett. Ellenőrizze, hogy a munkaterület törölve lett-e, és ha igen, akkor újra engedélyeznie kell a fürt figyelését Azure Monitor a tárolók számára, és meg kell adnia egy meglévőt, vagy létre kell hoznia egy új munkaterületet. Az ismételt engedélyezéshez [le](container-insights-optout.md) kell tiltania a fürt figyelését, és újra [engedélyeznie](container-insights-enable-new-cluster.md) kell a tárolók Azure monitorét. |  
| `Error retrieving data` az aks parancssori felületén keresztül-tárolókhoz az Azure Monitor hozzáadása után | Ha a használatával `az aks cli`engedélyezi a figyelést, akkor előfordulhat, hogy a tárolók Azure monitor nem megfelelően vannak telepítve. Győződjön meg arról, hogy telepítve van-e a megoldás. Ehhez nyissa meg a Log Analytics-munkaterületet, és a megoldás-e elérhető kiválasztásával **megoldások** a a bal oldali panelen. A probléma megoldásához, szüksége lesz a megoldás újbóli üzembe helyezéséhez utasításokat követve [üzembe helyezése az Azure Monitor for containers szolgáltatásban](container-insights-onboard.md) |  

A probléma diagnosztizálása érdekében adtunk meg a rendelkezésre álló hibaelhárítási parancsfájl [Itt](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>További lépések

A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).