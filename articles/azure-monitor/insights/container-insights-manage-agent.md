---
title: Az Azure-figyelő tárolókügynökhöz szolgáltatásának kezelése | Microsoft dokumentumok
description: Ez a cikk ismerteti a leggyakoribb karbantartási feladatok kezelése az Azure Monitor által használt tárolósnapló-ügynök tárolók.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275320"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Az Azure-figyelő tárolók ügynökének kezelése

Az Azure Monitor a tárolók egy tárolóba adott linuxos ügynök egy tárolóba adott verzióját használja. A kezdeti üzembe helyezés után vannak olyan rutin vagy választható feladatok, amelyeket az életciklusa során végre kell hajtania. Ez a cikk részletezi, hogyan manuálisan frissítheti az ügynök, és tiltsa le a környezeti változók gyűjteményének egy adott tárolóból. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Az Azure-figyelő tárolók ügynökéhez szolgáltatásának frissítése

Az Azure Monitor a tárolók egy tárolóba adott linuxos ügynök egy tárolóba adott verzióját használja. Az ügynök új verziójának megjelenésekor az ügynök automatikusan frissül az Azure Kubernetes-szolgáltatás (AKS) és az Azure Red Hat OpenShift üzemeltetett felügyelt Kubernetes-fürtökön. Egy [hibrid Kubernetes-fürt](container-insights-hybrid-setup.md) esetében az ügynök nem felügyelt, és manuálisan kell frissíteniaz ügynököt.

Ha az ügynök frissítése sikertelen az AKS-en üzemeltetett fürtesetében, ez a cikk az ügynök manuális frissítésének folyamatát is ismerteti. A kiadott verziók követéséről az ügynök kiadási közleményei című [témakörben látható.](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Frissítési ügynök a figyelt Kubernetes-fürtön

Az ügynök fürtökön való frissítésének folyamata, az Azure Red Hat OpenShift-től eltérő fürtökön, két egyenes lépésből áll. Az első lépés az, hogy letiltja a figyelést az Azure Monitor az Azure CLI használatával tárolók. Kövesse a [figyelés letiltása](container-insights-optout.md?#azure-cli) című cikkben ismertetett lépéseket. Az Azure CLI használatával eltávolíthatja az ügynököt a fürt csomópontjairól anélkül, hogy befolyásolna a megoldást és a munkaterületen tárolt megfelelő adatokat. 

>[!NOTE]
>A karbantartási tevékenység végrehajtása közben a fürt csomópontjai nem továbbítják az összegyűjtött adatokat, és a teljesítménynézetek nem jelenítik meg az adatokat az ügynök eltávolítása és az új verzió telepítése között. 
>

Az ügynök új verziójának telepítéséhez kövesse a figyelés engedélyezése az [Azure CLI használatával](container-insights-enable-new-cluster.md#enable-using-azure-cli)című témakörben leírt lépéseket a folyamat befejezéséhez.  

Miután újra engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt frissített állapotmutatóit. Az ügynök sikeres frissítésének ellenőrzéséhez futtassa a következő parancsot:`kubectl logs omsagent-484hw --namespace=kube-system`

Az állapotnak a következő példához kell *hasonlítania,* ahol az *omi* és omsagent értékének meg kell egyeznie az [ügynök kiadási előzményeiben](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)megadott legújabb verzióval.  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Frissítési ügynök hibrid Kubernetes-fürtön

A helyszíni Üzemeltetésű Kubernetes-fürt, az Azure-on és az Azure Stack en található Kubernetes-fürt ügynökének frissítési folyamata a következő parancs futtatásával hajtható végre:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Ha a Log Analytics-munkaterület az Azure China-ban található, futtassa a következő parancsot:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Ha a Log Analytics-munkaterület az Azure US Government szolgáltatásban található, futtassa a következő parancsot:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>A környezeti változók gyűjteményének letiltása tárolón

Az Azure Monitor tárolók gyűjti a környezeti változók a tárolókban futó tárolók, és bemutatja azokat a kiválasztott tároló a **tárolók** nézetben a tulajdonság ablaktáblában. Ezt a viselkedést úgy szabályozhatja, hogy letiltja egy adott tároló gyűjteményét a Kubernetes-fürt telepítése során, vagy azt követően a AZMON_COLLECT_ENV környezeti *változó*beállításával. Ez a funkció az ügynök verziójából érhető el – ciprod11292018 és újabb verziók.  

Környezeti változók gyűjteményének letiltásához egy új vagy meglévő tárolón, állítsa be a változó **AZMON_COLLECT_ENV** a Kubernetes telepítési yaml konfigurációs fájlban **a False** értékkel. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Futtassa a következő parancsot a módosítás nak az Azure Red `kubectl apply -f  <path to yaml file>`Hat OpenShift-től eltérő Kubernetes-fürtökre való alkalmazásához): . A ConfigMap szerkesztéséhez és a módosítás Azure Red Hat OpenShift-fürtökhöz való alkalmazásához futtassa a következő parancsot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Ezzel megnyitja az alapértelmezett szövegszerkesztőt. A változó beállítása után mentse a fájlt a szerkesztőbe.

A konfigurációs módosítás érvénybe léptetésének ellenőrzéséhez jelöljön ki egy tárolót a **tárolók** nézetben az Azure Monitor tárolókhoz, és a tulajdonságpanelen bontsa ki a **Környezeti változók csomópontot.**  A szakaszcsak a korábban létrehozott változót jelenítse meg - **AZMON_COLLECT_ENV=FALSE**. Az összes többi tároló esetében a Környezeti változók szakaszban fel kell sorolnia az összes felderített környezeti változót.

A környezeti változók felderítésének újbóli engedélyezéséhez alkalmazza korábban ugyanazt a folyamatot, és `kubectl` módosítsa az értéket **Hamis** értékről **Igaz**értékre, majd futtassa újra a parancsot a tároló frissítéséhez.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>További lépések

Ha az ügynök frissítése során problémákat tapasztal, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md) a támogatásért.
