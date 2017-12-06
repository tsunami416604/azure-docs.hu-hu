---
title: "Azure Kubernetes fürt - felügyeleti műveletek figyelése"
description: "Használatával a Microsoft Operations Management Suite Azure Tárolószolgáltatás-fürt Kubernetes figyelése"
services: container-service
author: bburns
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: e8a68896c923d785fea84cef60f8d2015906f342
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>A figyelő az Azure Tárolószolgáltatás-fürtöt a Microsoft Operations Management Suite (OMS)

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy rendelkezik [a Kubernetes Azure Tárolószolgáltatási fürt létrehozott](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy a `az` Azure cli és `kubectl` eszközök vannak telepítve.

Ha tesztelheti a `az` eszköz futtatásával telepítve:

```console
$ az --version
```

Ha nem rendelkezik a `az` eszköz telepítve, az e-mail utasításokat is [Itt](https://github.com/azure/azure-cli#installation).  
Használhatja azt is megteheti, [Azure Cloud rendszerhéj](https://docs.microsoft.com/azure/cloud-shell/overview), amelynek a `az` Azure cli és `kubectl` már telepítette a eszközök.  

Ha tesztelheti a `kubectl` eszköz futtatásával telepítve:

```console
$ kubectl version
```

Ha nem rendelkezik `kubectl` telepítve, futtatható:
```console
$ az acs kubernetes install-cli
```

Ha telepítette a kubectl eszköz futtatása kubernetes kulcsok tesztelése:
```console
$ kubectl get nodes
```

Ha a fenti parancs hibák kimenő, telepítendő kubernetes fürt kulcsok a kubectl eszközt. Azt a következő paranccsal teheti meg:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Az Operations Management Suite (OMS) tárolók figyelése

A Microsoft Operations Management (OMS) a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. Tároló egy olyan megoldás az OMS Naplóelemzési, amely segít a tároló szoftverleltár, a teljesítmény és a naplók megtekintéséhez egyetlen helyen. Naplózási, tárolók hibaelhárítás a naplók megtekintése központi helyen, és zajos fel felesleges tároló egy gazdagépen található.

![](media/container-service-monitoring-oms/image1.png)

A tároló megoldásról további információkért tekintse meg a [tároló megoldás Naplóelemzési](../../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Kubernetes OMS telepítése

### <a name="obtain-your-workspace-id-and-key"></a>Munkaterületének Azonosítóját és kulcs beszerzése
Az OMS felvegye a szolgáltatás az ügynök kell konfigurálni a munkaterület azonosítóját és kulcsát egy. A munkaterület azonosítója és kulcsa OMS fiók létrehozásához szükséges <https://mms.microsoft.com>. Kérjük, kövesse a lépéseket egy fiók létrehozásához. Ha elkészült a fiók létrehozását, be kell szereznie a azonosítója és kulcsa kattintva **beállítások**, majd **csatlakoztatott források**, majd **Linux kiszolgálók**lent látható módon.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Egy DaemonSet használatával OMS-ügynök telepítése
A tároló egyetlen példány futhat az a fürt minden egyes állomás DaemonSets Kubernetes használják.
Fontosságúak tökéletes figyelőügynökök futtatásához.

Itt a [DaemonSet YAM fájl](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Mentse a fájlt `oms-daemonset.yaml` , és cserélje le a helyőrző értékeinek `WSID` és `KEY` a munkaterület azonosítója és a fájlban kulccsal.

Miután hozzáadta a munkaterület azonosítója és kulcsa az DaemonSet konfigurációhoz, telepítheti az OMS-ügynököt a fürt a `kubectl` parancssori eszköz:

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-oms-agent-using-a-kubernetes-secret"></a>Kubernetes titkos kulcs használata az OMS-ügynök telepítése
Az OMS-munkaterület azonosítója és kulcsára használhatja Kubernetes titkos DaemonSet YAM-fájl részeként.

 - Másolja a parancsfájlt, titkos sablon és a DaemonSet YAM fájlt (a [tárház](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)), és győződjön meg arról, hogy az ugyanabban a könyvtárban. 
      - Titkos kulcs parancsprogram - secret-gen.sh létrehozása
      - titkos sablon - secret-template.yaml
   - DaemonSet YAM fájl - omsagent – ds-secrets.yaml
 - Futtassa a szkriptet. A parancsprogram kéri az OMS-munkaterület azonosítója és az elsődleges kulcs. Helyezze be, és a parancsfájl létrehoz egy titkos yam fájlt, ezért is futtathatja.   
   ```
   #> sudo bash ./secret-gen.sh 
   ```

   - Hozza létre a titkos kulcsok fogyasztanak a következő futtatásával:``` kubectl create -f omsagentsecret.yaml ```
 
   - Ellenőrzéséhez futtassa az alábbi parancsot: 

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
 
  - A omsagent futó démon-készlet létrehozása``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Összegzés
Ennyi az egész! Néhány perc múlva megtekintheti az OMS-irányítópultra áramló adatokat kell lennie.
