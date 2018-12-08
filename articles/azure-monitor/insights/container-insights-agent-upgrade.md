---
title: Tárolók (előzetes verzió) ügynök frissítése a Azure Monitor |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használja az Azure Monitor tárolók Log Analytics-ügynököket frissít.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 026bdd6a59dc84220e7e52707cee3c1971fba838
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104314"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Az Azure Monitor tárolók (előzetes verzió) ügynök frissítése
Tárolókhoz az Azure Monitor egy tárolóalapú verzióját használja, a Log Analytics-ügynök Linux rendszeren. Az ügynök egy új verziója jelent meg, amikor a rendszer automatikusan frissíti az ügynök a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes fürtökön.  

Ha az ügynök frissítése sikertelen, ez a cikk ismerteti a folyamatot, az ügynök manuális frissítése. Tekintse kiadott verziói követéséhez [ügynök közleményekért](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Felügyelt Kubernetes-fürtön az ügynök frissítése
Az ügynök frissítési folyamata két egyszerű lépésből áll. Az első lépés, hogy tiltsa le az Azure CLI használatával az Azure Monitor szolgáltatással figyelést.  Az ismertetett lépéseket követve a [letiltani a figyelést](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) cikk. Azure CLI használatával lehetővé teszi számunkra, hogy az ügynök eltávolítása a fürt csomópontjainak a megoldás és a vonatkozó adatok a munkaterületen tárolt befolyásolása nélkül. 

>[!NOTE]
>Ezt a karbantartási tevékenységet hajt végre, gyűjtött adatokat nem továbbítja a fürt csomópontjainak, majd azt teljesítménynézetet nem jelenik meg az adatok közötti idő eltávolíthatja az ügynököt, és az új verzió telepítése. 
>

Az ügynök az új verzió telepítéséhez kövesse a leírt lépéseket követve a [előkészítése figyelési](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) című cikket, a folyamat befejezéséhez az Azure CLI használatával.  

Miután a figyelés újbóli engedélyezését, körülbelül 15 percet, mielőtt a fürt frissített mérőszámok megtekintéséhez vehet igénybe. Az ügynök frissítése sikerült ellenőrzéséhez futtassa a parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

Az állapot a következő példában kell hasonlítania ahol értéke *omi* és *omsagent* egyeznie kell a legújabb verzióra a megadott a [ügynök verziókiadások előzményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>További lépések
Ha az ügynök frissítése közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutató](container-insights-troubleshoot.md) támogatást.