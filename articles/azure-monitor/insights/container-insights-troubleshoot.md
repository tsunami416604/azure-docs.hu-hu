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
ms.openlocfilehash: db4b468c03d93b073067083f4fae1ec86c70dde8
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577041"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Hibaelhárítás az Azure Monitor for containers szolgáltatásban

Az Azure Kubernetes Service (AKS)-fürt figyelése az Azure Monitor szolgáltatással tárolók konfigurálásakor előfordulhat, hogy hibába ütközik megakadályozza az adatok gyűjtése vagy állapotát. Ez a cikk részletesen néhány gyakori hibák és hibaelhárítási lépéseket.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Engedélyezési hiba történt a bevezetési vagy frissítési művelet közben
Engedélyezése az Azure Monitor-tárolókhoz, és támogatja a mérőszámok gyűjtését. a fürt frissítése, akkor előfordulhat, hogy megjelenik egy hibaüzenet hasonlítanak a következő - *az ügyfél < felhasználó identitása > "objektum azonosítója: < objectId > felhasználó" nem rendelkezik hatókörben 'Microsoft.Authorization/roleAssignments/write' művelet végrehajtására*

A bevezetési vagy a frissítési folyamat során biztosítása a **figyelési metrikákat közzétevő** szerepkör-hozzárendelés fürterőforrás próbálta meg végrehajtani. A folyamatot kezdeményező felhasználó számára az Azure Monitor tárolók vagy a frissítés támogatja a mérőszámok gyűjtését hozzáféréssel kell rendelkeznie a **Microsoft.Authorization/roleAssignments/write** engedélyt az AKS-fürtöt erőforrás-hatókör. Csak a tagjai a **tulajdonosa** és **felhasználói hozzáférés rendszergazdája** beépített szerepkörök, amelyekhez hozzáférést ezt az engedélyt. Ha a biztonsági házirendek részletes szintű engedélyek hozzárendelése a, javasoljuk, hogy megtekinti [egyéni szerepkörök](../../role-based-access-control/custom-roles.md) és rendelje hozzá a felhasználókat, akiknek nincs szükségük. 

Manuálisan is biztosíthat a ezt a szerepkört az Azure Portalon a következő lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Kubernetes**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Azure Kubernetes**.
3. Kubernetes-fürtök listájában válassza ki azt a listából.
2. Kattintson a bal oldali menüből **hozzáférés-vezérlés (IAM)**.
3. Válassza ki **+ Hozzáadás** szerepkör-hozzárendelés hozzáadása, és válassza ki a **figyelési metrikákat közzétevő** szerepkör és a **válassza ki** mezőbe írja be **AKS** , a szűrő csak a fürtöket az eredményeket engedélyezéseihez definiálva az előfizetésben. Válassza ki azt a listából a létrehozott fürtre jellemző.
4. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Az Azure Monitor for containers szolgáltatásban engedélyezve van, de semmilyen információt nem küld
Ha-tárolókhoz az Azure Monitor sikeresen engedélyezve és konfigurálva, de állapotinformációi nem jelennek, vagy nem jár eredménnyel log lekérdezésből, a probléma diagnosztizálása az alábbi lépéseket: 

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

5. Győződjön meg arról, hogy az ügynök sikeresen lett előkészítve, futtassa a parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

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
| Hibaüzenet `No data for selected filters`  | Ez eltarthat egy ideig, újonnan létrehozott fürtök esetében a figyelési adatok folyamat létrehozásához. Kérjük, várjon legalább 10-15 percet adatok jelenjenek meg a fürt számára. |   
| Hibaüzenet `Error retrieving data` | Azure Kubenetes Service-fürt beállítását állapotának és teljesítményének figyeléséhez, amíg a kapcsolat jön létre a fürt és az Azure Log Analytics-munkaterület között. Log Analytics-munkaterületet a fürt számára az összes monitorozási adat tárolására szolgál. Ez a hiba akkor fordulhat elő, ha a Log Analytics-munkaterületet törölték, vagy megszakadt. Ellenőrizze, hogy a munkaterület elérhető áttekintésével [hozzáférés kezelése](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#view-workspace-details). Ha a munkaterület nem található, szüksége lesz ismételt üzembe helyezni a fürt és az Azure Monitor tárolók. Helyreállítási környezet – üzembe helyezni, kell [letiltása](container-insights-optout.md) a fürt figyelése és [engedélyezése](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) újra-tárolókhoz az Azure Monitor. |  
| `Error retrieving data` az aks parancssori felületén keresztül-tárolókhoz az Azure Monitor hozzáadása után | Ha a bevezetési használatával `az aks cli`, nagyon ritkán, az Azure Monitor for containers szolgáltatásban nem lehet megfelelően előkészítve. Ellenőrizze, hogy a megoldás előkészítve. Ehhez nyissa meg a Log Analytics-munkaterületet, és a megoldás-e elérhető kiválasztásával **megoldások** a a bal oldali panelen. A probléma megoldásához, szüksége lesz a megoldás újbóli üzembe helyezéséhez utasításokat követve [üzembe helyezése az Azure Monitor for containers szolgáltatásban](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

A probléma diagnosztizálása érdekében adtunk meg a rendelkezésre álló hibaelhárítási parancsfájl [Itt](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>További lépések
A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).