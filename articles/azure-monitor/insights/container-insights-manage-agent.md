---
title: Az Azure Monitor tárolók ügynök kezelése |} A Microsoft Docs
description: Ez a cikk ismerteti a leggyakoribb karbantartási feladatok kezelése a tárolók az Azure Monitor által használt tárolóalapú Log Analytics-ügynököket.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385468"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Az Azure Monitor tárolók ügynök kezelése

Tárolókhoz az Azure Monitor egy tárolóalapú verzióját használja, a Log Analytics-ügynök Linux rendszeren. Kezdeti telepítés után nincsenek rutin vagy választható feladatok végrehajtásához életciklusa során szükség lehet. Ez a cikk részletesen manuálisan frissítse az ügynököt, és tiltsa le az adott tároló környezeti változóit. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Az Azure Monitor tárolók ügynök frissítése

Tárolókhoz az Azure Monitor egy tárolóalapú verzióját használja, a Log Analytics-ügynök Linux rendszeren. Az ügynök új verziójának felszabadításakor az ügynök automatikusan frissül az Azure Kubernetes szolgáltatásban (ak) és az Azure Red Hat OpenShift üzemeltetett felügyelt Kubernetes-fürtökön. [Hibrid Kubernetes-fürtök](container-insights-hybrid-setup.md) esetében az ügynököt nem felügyeli a rendszer, és manuálisan kell frissítenie az ügynököt.

Ha az ügynök frissítése nem sikerül egy AK-on üzemeltetett fürtön, a cikk az ügynök manuális frissítésének folyamatát is leírja. A kiadott verziók követéséhez tekintse meg az [ügynök kiadási hirdetményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)című témakört.

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Ügynök frissítése a figyelt Kubernetes-fürtön

Az ügynök az Azure Red Hat OpenShift eltérő fürtökön való frissítésének folyamata két egyenes továbbítási lépésből áll. Az első lépés, hogy tiltsa le az Azure CLI használatával az Azure Monitor szolgáltatással figyelést. Kövesse a [figyelés letiltása](container-insights-optout.md?#azure-cli) című cikkben ismertetett lépéseket. Azure CLI használatával lehetővé teszi számunkra, hogy az ügynök eltávolítása a fürt csomópontjainak a megoldás és a vonatkozó adatok a munkaterületen tárolt befolyásolása nélkül. 

>[!NOTE]
>Ezt a karbantartási tevékenységet hajt végre, gyűjtött adatokat nem továbbítja a fürt csomópontjainak, majd azt teljesítménynézetet nem jelenik meg az adatok közötti idő eltávolíthatja az ügynököt, és az új verzió telepítése. 
>

Az ügynök új verziójának telepítéséhez kövesse a [figyelés engedélyezése az Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)-vel című cikkben ismertetett lépéseket a folyamat befejezéséhez.  

A figyelés ismételt engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt frissített állapotának mérőszámait. Az ügynök sikeres frissítésének ellenőrzéséhez futtassa a következő parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

Az állapotnak az alábbi példához hasonlónak kell lennie, ahol a *omsagent* értékének meg kell egyeznie az [ügynök kiadási előzményeiben](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)megadott legújabb verzióval.  

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Környezeti változó gyűjtemény egy tároló letiltása

A tárolók Azure Monitor környezeti változókat gyűjtenek a hüvelyben futó tárolókban, és **a tárolók nézetben a** kiválasztott tároló tulajdonságok ablaktábláján jelenítik meg azokat. Ezt a viselkedést úgy szabályozhatja, ha letilt egy adott tárolóhoz tartozó gyűjteményt a Kubernetes-fürt telepítése során vagy a *AZMON_COLLECT_ENV*környezeti változó beállításával. Ez a funkció érhető el az ügynök verziója – ciprod11292018 és újabb verzióit.  

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

A konfiguráció módosításának ellenőrzéséhez jelöljön ki egy tárolót **Azure monitor tárolók nézetben,** és a Tulajdonságok panelen bontsa ki a **környezeti változók**elemet.  A szakasznak csak a korábban létrehozott változót kell megjelenítenie – **AZMON_COLLECT_ENV = false**. Minden más tárolók a környezeti változók szakaszban kell listázza az összes felderített a környezeti változókat.

A környezeti változók felderítésének újbóli engedélyezéséhez alkalmazza ugyanezt a folyamatot, és módosítsa a **hamis** értéket **true**értékre, majd futtassa újra a `kubectl` parancsot a tároló frissítéséhez.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Következő lépések

Ha problémák merülnek fel az ügynök frissítésekor, tekintse át a támogatási [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .
