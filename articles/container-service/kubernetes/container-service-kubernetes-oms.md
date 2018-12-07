---
title: (ELAVULT) Azure-beli Kubernetes-fürt – az Operations Management figyelése
description: Figyelés a Log Analytics szolgáltatást az Azure Container Service Kubernetes-fürtön
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 8010d7cbf960c3e2f6528687be97a47d31270696
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997202"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(ELAVULT) A figyelő az Azure Container Service-fürt, a Log Analytics használatával

> [!TIP]
> Ez a cikk, amely a frissített verziót használja Azure Kubernetes Service-ben, lásd: [-tárolókhoz az Azure Monitor](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Az útmutató feltételezi, hogy [egy Kubernetes-fürtöt az Azure Container Service használatával létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a `az` az Azure cli és `kubectl` telepített eszközök.

Ha rendelkezik tesztelheti a `az` futtatásával telepített eszköz:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszközt telepítette, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).
Másik lehetőségként használhatja [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), amelynek a `az` az Azure cli és `kubectl` eszközök már telepítve van az Ön számára.

Ha rendelkezik tesztelheti a `kubectl` futtatásával telepített eszköz:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:
```console
$ az acs kubernetes install-cli
```

Ha van telepítve a kubectl eszköz futtatása a kubernetes-kulcsok teszteléséhez:
```console
$ kubectl get nodes
```

Ha a fenti parancs hibát ki, telepítenie kell kubernetes-fürt kulcsok a kubectl eszközbe. Megteheti, hogy a következő parancsot:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>A tárolók Log Analytics szolgáltatással

A log Analytics a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúrára. Tároló megoldás egy olyan megoldás a Log Analyticsben, így a segítségével egyetlen helyen, a tároló készlet, a teljesítmény és a naplók megtekintéséhez. Naplózási, tárolók hibaelhárítása központi helyen a naplóinak megtekintésével és zajos felhasználása felesleges tároló-gazdagépen található.

![](media/container-service-monitoring-oms/image1.png)

Tároló megoldásról további információkért tekintse meg a [tároló megoldás a Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>A Kubernetes a Log Analytics telepítése

### <a name="obtain-your-workspace-id-and-key"></a>A munkaterület-Azonosítót és a kulcs beszerzése
A Log Analytics ügynök kommunikáljon a szolgáltatásnak kell konfigurálni a munkaterület Azonosítójára és a egy munkaterület kulcsa. A munkaterület Azonosítóját és kulcsát szeretne létrehozni egy fiókot a <https://mms.microsoft.com>.
Kövesse a lépéseket egy fiók létrehozásához. Miután végzett, hozza létre a fiókot is az azonosító és kattintson a kulcs a **Log Analytics** panelen, majd a munkaterület nevét. Ezután a **speciális beállítások**, **csatlakoztatott források**, majd **Linux-kiszolgálók**, a szükséges információkat, láthatja, ahogy az alábbi.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>A DaemonSet használata a Log Analytics-ügynök telepítése
Kubernetes DaemonSets használják egy tárolót egyetlen példányát futtatni a fürt minden gazdagépen.
Azok a futó monitorozási ügynökök ideális.

Íme a [DaemonSet YAML-fájl](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Mentse a fájlt `oms-daemonset.yaml` , és cserélje le a helyőrző értékei `WSID` és `KEY` a munkaterület Azonosítójára és a fájlban található kulcsot.

Miután hozzáadta a munkaterület Azonosítóját és kulcsát a DaemonSet konfigurációhoz, telepítheti a Log Analytics-ügynököket a fürt a `kubectl` parancssori eszköz:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>A Kubernetes titkos kulcs használata a Log Analytics-ügynök telepítése
A Log Analytics-munkaterület Azonosítójára és kulcsára védelméhez használhatja Kubernetes titkos DaemonSet YAML-fájl részeként.

 - Másolja a szkriptet, titkos sablonfájl és a DaemonSet YAML-fájlt (a [tárház](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)), és ellenőrizze, hogy ugyanabban a könyvtárban legyenek.
      - Titkos kulcs generálása szkriptet - titkos kód – gen.sh
      - titkos kód sablon - secret-template.yaml
   - DaemonSet YAML-fájlt - omsagent-ds-secrets.yaml
 - Futtassa a szkriptet. A parancsprogram kéri a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcsot. Helyezze be, amely, és a parancsfájl létrehoz egy titkos yaml-fájlt, így is futtatható legyen.
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - Hozza létre a titkos kulcsok pod a következő futtatásával: ``` kubectl create -f omsagentsecret.yaml ```

   - Ellenőrizze, hogy futtassa a következőt:

   ```
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes
   ```

  - A omsagent futtatásával démon-készlet létrehozása ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Összegzés
Ennyi az egész! Néhány perc elteltével megtekintheti a Log Analytics-irányítópult adatforgalmát kell lennie.
