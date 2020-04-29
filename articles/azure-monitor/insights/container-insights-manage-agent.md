---
title: A containers Agent Azure Monitor kezelése | Microsoft Docs
description: Ez a cikk ismerteti a leggyakoribb karbantartási feladatok kezelését a Azure Monitor által a tárolók számára használt Log Analytics ügynökkel.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275320"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>A containers Agent Azure Monitor kezelése

A tárolók Azure Monitor a Linux Log Analytics ügynökének egy tárolós verzióját használja. A kezdeti üzembe helyezést követően a rendszer rutin vagy opcionális feladatokat hajt végre, amelyekre szükség lehet az életciklusa során. Ez a cikk azt ismerteti, hogyan lehet manuálisan frissíteni az ügynököt, és letiltani a környezeti változók gyűjteményét egy adott tárolóból. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>A Azure Monitor a tárolók ügynökének frissítése

A tárolók Azure Monitor a Linux Log Analytics ügynökének egy tárolós verzióját használja. Az ügynök új verziójának felszabadításakor az ügynök automatikusan frissül az Azure Kubernetes szolgáltatásban (ak) és az Azure Red Hat OpenShift üzemeltetett felügyelt Kubernetes-fürtökön. [Hibrid Kubernetes-fürtök](container-insights-hybrid-setup.md) esetében az ügynököt nem felügyeli a rendszer, és manuálisan kell frissítenie az ügynököt.

Ha az ügynök frissítése nem sikerül egy AK-on üzemeltetett fürtön, a cikk az ügynök manuális frissítésének folyamatát is leírja. A kiadott verziók követéséhez tekintse meg az [ügynök kiadási hirdetményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)című témakört.

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Ügynök frissítése a figyelt Kubernetes-fürtön

Az ügynök az Azure Red Hat OpenShift eltérő fürtökön való frissítésének folyamata két egyenes továbbítási lépésből áll. Első lépésként le kell tiltania a figyelést Azure Monitor az Azure CLI-vel rendelkező tárolók esetében. Kövesse a [figyelés letiltása](container-insights-optout.md?#azure-cli) című cikkben ismertetett lépéseket. Az Azure CLI használata lehetővé teszi, hogy az ügynököt a fürt csomópontjairól távolítsa el anélkül, hogy ez hatással lenne a megoldásra és a munkaterületen tárolt megfelelő adatokra. 

>[!NOTE]
>A karbantartási tevékenység végrehajtása közben a fürt csomópontjai nem továbbítják az összegyűjtött adatokat, és a teljesítmény nézetek nem jelenítik meg az ügynök eltávolítása és az új verzió telepítésének időpontja közötti adatokat. 
>

Az ügynök új verziójának telepítéséhez kövesse a [figyelés engedélyezése az Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)-vel című cikkben ismertetett lépéseket a folyamat befejezéséhez.  

A figyelés ismételt engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt frissített állapotának mérőszámait. Az ügynök sikeres frissítésének ellenőrzéséhez futtassa a következő parancsot:`kubectl logs omsagent-484hw --namespace=kube-system`

Az állapotnak az alábbi példához hasonlónak kell lennie, *omi* ahol a *omsagent* értékének meg kell egyeznie az [ügynök kiadási előzményeiben](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)megadott legújabb verzióval.  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>A hibrid Kubernetes-fürtön futó ügynök frissítése

Az ügynököt a helyszínen üzemeltetett Kubernetes-fürtön, az Azure-on és az Azure Stack-ban található, az alábbi parancs futtatásával lehet elvégezni:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Ha a Log Analytics munkaterület az Azure China-ban van, futtassa a következő parancsot:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Ha az Log Analytics munkaterület az Azure US Governmentben van, futtassa a következő parancsot:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Környezeti változók gyűjteményének letiltása tárolón

A tárolók Azure Monitor környezeti változókat gyűjtenek a hüvelyben futó tárolókban, és **a tárolók nézetben a** kiválasztott tároló tulajdonságok ablaktábláján jelenítik meg azokat. Ezt a viselkedést úgy szabályozhatja, ha letilt egy adott tárolóhoz tartozó gyűjteményt a Kubernetes-fürt telepítése során vagy a *AZMON_COLLECT_ENV*környezeti változó beállításával. Ez a funkció az ügynök verziójában érhető el – ciprod11292018 és újabb.  

Ha le szeretné tiltani a környezeti változók új vagy meglévő tárolón való gyűjtését, állítsa be az **AZMON_COLLECT_ENV** változó értékét **false** értékre a Kubernetes telepítési YAML konfigurációs fájljában. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Futtassa a következő parancsot, hogy alkalmazza a módosítást az Azure Red Hat OpenShift eltérő Kubernetes-fürtökre: `kubectl apply -f  <path to yaml file>`. A ConfigMap szerkesztéséhez és az Azure Red Hat OpenShift-fürtökre vonatkozó módosítás alkalmazásához futtassa a következő parancsot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Ekkor megnyílik az alapértelmezett szövegszerkesztő. A változó beállítása után mentse a fájlt a szerkesztőbe.

A konfiguráció módosításának ellenőrzéséhez jelöljön ki egy tárolót **Azure monitor tárolók nézetben,** és a Tulajdonságok panelen bontsa ki a **környezeti változók**elemet.  A szakasznak csak a korábban létrehozott változót kell megjelenítenie – **AZMON_COLLECT_ENV = false**. Az összes többi tároló esetében a környezeti változók szakaszban fel kell sorolni az összes felderített környezeti változót.

A környezeti változók felderítésének újbóli engedélyezéséhez alkalmazza ugyanezt a folyamatot, és módosítsa a **hamis** értéket **true**értékre, majd futtassa újra a `kubectl` parancsot a tároló frissítéséhez.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>További lépések

Ha problémák merülnek fel az ügynök frissítésekor, tekintse át a támogatási [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .
