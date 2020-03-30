---
title: (ELAVULT) Az Azure Kubernetes-fürt figyelése – Üzemeltetéskezelés
description: Kubernetes-fürt figyelése az Azure Container Service szolgáltatásban a Log Analytics használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371154"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(ELAVULT) Azure Container Service-fürt figyelése a Log Analytics szolgáltatással

> [!TIP]
> Az Azure Kubernetes-szolgáltatást használó cikk frissített verziójáról az [Azure-figyelő tárolókról](../../azure-monitor/insights/container-insights-overview.md)című témakörben olvashat.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a forgatókönyv feltételezi, hogy [létrehozott egy Kubernetes-fürtöt az Azure Container Service használatával.](container-service-kubernetes-walkthrough.md)

Azt is feltételezi, hogy `az` az Azure `kubectl` cli és eszközök telepítve van.

Tesztelheti, hogy telepítve `az` van-e az eszköz a következő futtatásával:

```azurecli
az --version
```

Ha nincs telepítve az `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)utasítások találhatók.
Azt is megteheti, hogy használhatja `az` az Azure `kubectl` [Cloud Shellt,](https://docs.microsoft.com/azure/cloud-shell/overview)amely rendelkezik az Azure cli és az eszközök már telepítve van.

Tesztelheti, hogy telepítve `kubectl` van-e az eszköz a következő futtatásával:

```console
kubectl version
```

Ha nincs `kubectl` telepítve, futtathatja a következőket:

```azurecli
az acs kubernetes install-cli
```

Annak teszteléséhez, hogy a kubectl eszközben telepítve van-e kubernetes kulcsok, futtathatja a következőket:

```console
kubectl get nodes
```

Ha a fenti parancs hibák at, telepítenie kell kubernetes fürtkulcsokat a kubectl eszközbe. Ezt a következő paranccsal teheti meg:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Tárolók figyelése a Log Analytics szolgáltatással

A Log Analytics a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében.A Container Solution egy megoldás a Log Analytics szolgáltatásban, amely segít a tárolókészlet, a teljesítmény és a naplók egyetlen helyen való megtekintésében. A tárolók naplózása, a tárolók hibaelhárítása a központi helyen lévő naplók megtekintésével, és zajos fogyasztású felesleges tárolót találhat a gazdagépen.

![](media/container-service-monitoring-oms/image1.png)

A Container Solution szolgáltatásról további információt a [Container Solution Log Analytics című dokumentumban talál.](../../azure-monitor/insights/containers.md)

## <a name="installing-log-analytics-on-kubernetes"></a>A Log Analytics telepítése a Kubernetes-re

### <a name="obtain-your-workspace-id-and-key"></a>A munkaterület-azonosító és a kulcs beszerzése
Ahhoz, hogy a Log Analytics-ügynök beszéljen a szolgáltatással, egy munkaterület-azonosítóval és egy munkaterületi kulccsal kell konfigurálni. A munkaterület-azonosító és a kulcs lekérti <https://mms.microsoft.com>a fiókját a(z) helyen kell létrehoznia.
Fiók létrehozásához kövesse a lépéseket. Miután befejezte a fiók létrehozását, beszerezheti az azonosítóját és a kulcsot a **Log Analytics** panelre, majd a munkaterület nevére kattintva. Ezután a **Speciális beállítások**, **Csatlakoztatott források**, majd **linuxos kiszolgálók**alatt megtalálja a szükséges információkat, az alábbiak szerint.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>A Log Analytics-ügynök telepítése DaemonSet használatával
A DaemonSets-et a Kubernetes arra használja, hogy a fürt minden állomásán egyetlen példányt futtasson.
Tökéletesek a megfigyelő ügynökök működtetéséhez.

Itt van a [DaemonSet YAML fájl](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Mentse egy elnevezett `oms-daemonset.yaml` fájlba, és cserélje `WSID` `KEY` le a munkaterület-azonosító és a fájlban lévő kulcs helyőrző értékeit.

Miután hozzáadta a munkaterület-azonosítót és a kulcsot a DaemonSet konfigurációhoz, telepítheti a Log Analytics-ügynököt a fürtre a `kubectl` parancssori eszközzel:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>A Log Analytics-ügynök telepítése Kubernetes titkos adathasználatával
A Log Analytics-munkaterület-azonosító és a kulcs védelme érdekében használhatja a Kubernetes Secret-et a DaemonSet YAML-fájl részeként.

- Másolja a parancsfájlt, a titkos sablonfájlt és a DaemonSet YAML fájlt (a [tárházból),](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)és győződjön meg arról, hogy ugyanazon a könyvtáron vannak.
  - titkos generáló parancsfájl - secret-gen.sh
  - titkos sablon - secret-template.yaml
    - DaemonSet YAML fájl - omsagent-ds-secrets.yaml
- Futtassa a szkriptet. A parancsfájl kérni fogja a Log Analytics-munkaterület azonosítóját és elsődleges kulcsát. Helyezze be, és a parancsfájl létrehoz egy titkos yaml fájlt, így futtathatja.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Hozza létre a titkos kulcsokpodat a következő futtatásával:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Az ellenőrzéshez futtassa a következőket:

  ```console
  kubectl get secrets
  ```

  ```output
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

  - Hozza létre az omsagent démonkészletet a következő futtatásával:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Összegzés
Ennyi az egész! Néhány perc múlva látnia kell a Log Analytics irányítópultjára áramló adatokat.
