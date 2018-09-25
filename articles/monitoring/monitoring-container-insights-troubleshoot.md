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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: de7ae5788224b83105e4dc9a24aea35c8b841c88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986727"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Hibaelhárítás az Azure Monitor for containers szolgáltatásban

Az Azure Kubernetes Service (AKS)-fürt figyelése az Azure Monitor szolgáltatással tárolók konfigurálásakor előfordulhat, hogy hibába ütközik megakadályozza az adatok gyűjtése vagy állapotát. Ez a cikk részletesen néhány gyakori hibák és hibaelhárítási lépéseket.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Az Azure Monitor for containers szolgáltatásban engedélyezve van, de semmilyen információt nem küld
Ha-tárolókhoz az Azure Monitor sikeresen engedélyezve és konfigurálva, azonban állapotinformációi nem jelennek, vagy nem jár eredménnyel a napló Log Analytics-lekérdezés, a probléma diagnosztizálása az alábbi lépéseket: 

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

## <a name="next-steps"></a>További lépések
A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](monitoring-container-insights-analyze.md).