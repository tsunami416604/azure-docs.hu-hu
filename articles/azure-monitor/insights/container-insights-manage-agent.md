---
title: Az Azure Monitor tárolók ügynök kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti a leggyakoribb karbantartási feladatok kezelése a tárolók az Azure Monitor által használt tárolóalapú Log Analytics-ügynököket.
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 6bfcb17d0a81c6e23c4bfa3dd4f4dd8cc3079379
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331470"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Az Azure Monitor tárolók ügynök kezelése
Tárolókhoz az Azure Monitor egy tárolóalapú verzióját használja, a Log Analytics-ügynök Linux rendszeren. Kezdeti telepítés után nincsenek rutin vagy választható feladatok végrehajtásához életciklusa során szükség lehet. Ez a cikk részletesen manuálisan frissítse az ügynököt, és tiltsa le az adott tároló környezeti változóit. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Az Azure Monitor tárolók ügynök frissítése
Tárolókhoz az Azure Monitor egy tárolóalapú verzióját használja, a Log Analytics-ügynök Linux rendszeren. Az ügynök egy új verziója jelent meg, amikor a rendszer automatikusan frissíti az ügynök a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes fürtökön.  

Ha az ügynök frissítése sikertelen, ez a cikk ismerteti a folyamatot, az ügynök manuális frissítése. Tekintse kiadott verziói követéséhez [ügynök közleményekért](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Felügyelt Kubernetes-fürtön az ügynök frissítése
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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Környezeti változó gyűjtemény egy tároló letiltása
Környezeti változók gyűjti össze a tárolók fut egy pod-tárolókhoz az Azure Monitor és a jeleníti meg őket a tulajdonság panelen a kiválasztott tároló a **tárolók** megtekintése. Ez a viselkedés szabályozhatja az AKS-fürtöt, vagy után üzembe helyezés során vagy egy adott tároló gyűjtés letiltása a környezeti változó beállításával *AZMON_COLLECT_ENV*. Ez a funkció érhető el az ügynök verziója – ciprod11292018 és újabb verzióit.  

Egy új vagy meglévő tárolón környezeti változók gyűjteményét letiltásához állítsa be a változó **AZMON_COLLECT_ENV** értékkel **hamis** a Kubernetes üzembe helyezési yaml konfigurációs fájlban.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

A következő parancsot a módosítás alkalmazásához az AKS-tároló: `kubectl apply -f  <path to yaml file>`.

Annak ellenőrzéséhez, hogy a konfiguráció módosításának érvénybe tartott, válassza ki a tároló a **tárolók** megtekintheti az Azure monitorban tárolókhoz, valamint a tulajdonság panelen, bontsa ki a **környezeti változók**.  A szakasz csak a korábban - létrehozott változó megjelennie **AZMON_COLLECT_ENV = FALSE**. Minden más tárolók a környezeti változók szakaszban kell listázza az összes felderített a környezeti változókat.   

Kívánja újból engedélyezni a környezeti változók felderítési, korábban alkalmazza ugyanazt a folyamatot, és módosítsa az értéket **false (hamis)** való **igaz**, majd futtassa újra a `kubectl` parancsot a tároló frissítésére.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>További lépések
Ha az ügynök frissítése közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutató](container-insights-troubleshoot.md) támogatást.