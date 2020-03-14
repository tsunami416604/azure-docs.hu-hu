---
title: ELAVULT Azure Kubernetes-fürt – Operations Management figyelése
description: Kubernetes-fürt figyelése Azure Container Service a Log Analytics használatával
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371154"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>ELAVULT Azure Container Service-fürt figyelése Log Analytics

> [!TIP]
> Az Azure Kubernetes Service-t használó cikk frissített verziója: [Azure monitor for containers](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez a bemutató azt feltételezi, hogy [Azure Container Service használatával hozott létre egy Kubernetes-fürtöt](container-service-kubernetes-walkthrough.md).

Azt is feltételezi, hogy telepítve van a `az` Azure CLI és `kubectl` eszközök.

A futtatásával tesztelheti, hogy telepítve van-e a `az` eszköz:

```azurecli
az --version
```

Ha nincs telepítve a `az` eszköz, [itt](https://github.com/azure/azure-cli#installation)talál útmutatást.
Azt is megteheti, hogy a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)is használhatja, amely rendelkezik a `az` Azure CLI-vel és az `kubectl`-eszközökkel.

A futtatásával tesztelheti, hogy telepítve van-e a `kubectl` eszköz:

```console
kubectl version
```

Ha nincs `kubectl` telepítve, akkor a következőket futtathatja:

```azurecli
az acs kubernetes install-cli
```

Ha a kubectl-eszközön telepített kubernetes-kulcsokkal szeretne tesztelni, futtassa a következőt:

```console
kubectl get nodes
```

Ha a fenti parancs hibába ütközik, telepítenie kell a kubernetes-fürt kulcsait a kubectl eszközre. Ezt a következő paranccsal teheti meg:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Tárolók figyelése Log Analytics

A Log Analytics a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a Felhőbeli infrastruktúra kezelésében és biztonságában. A Container Solution Log Analytics megoldás, amely segít megtekinteni a tárolók leltározását, teljesítményét és naplóit egyetlen helyen. Naplózhatja a tárolók hibakeresését, ha megtekinti a naplókat a központi helyen, és a gazdagépen megkeresi a felesleges tárolót.

![](media/container-service-monitoring-oms/image1.png)

A tároló megoldással kapcsolatos további információkért tekintse meg a [Container solution log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Log Analytics telepítése a Kubernetes-on

### <a name="obtain-your-workspace-id-and-key"></a>A munkaterület-azonosító és-kulcs beszerzése
Ahhoz, hogy a Log Analytics-ügynök kommunikáljon a szolgáltatással, meg kell adni egy munkaterület-azonosítót és egy munkaterület-kulcsot. A munkaterület-azonosító és-kulcs beszerzéséhez létre kell hoznia egy fiókot a következő helyen: <https://mms.microsoft.com>.
Kövesse a fiók létrehozásához szükséges lépéseket. Ha elkészült a fiók létrehozásával, az azonosítót és a kulcsot a **log Analytics** panelre, majd a munkaterület nevére kattintva kérheti le. Ezután a **Speciális beállítások**, a **csatlakoztatott források**, majd a **Linux-kiszolgálók**területen megtalálja a szükséges információkat az alább látható módon.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>A Log Analytics-ügynök telepítése Daemonset elemet használatával
A Kubernetes a DaemonSets használja a tároló egyetlen példányának futtatására a fürt minden gazdagépén.
Tökéletesek a figyelési ügynökök futtatásához.

Itt látható a [DAEMONSET elemet YAML-fájlja](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Mentse a fájlt egy `oms-daemonset.yaml` nevű fájlba, és cserélje le a `WSID` és a `KEY` helyére az adott munkaterület AZONOSÍTÓját és kulcsát.

Miután hozzáadta a munkaterület AZONOSÍTÓját és kulcsát a Daemonset elemet-konfigurációhoz, telepítheti a Log Analytics ügynököt a fürtön a `kubectl` parancssori eszköz használatával:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>A Log Analytics-ügynök telepítése Kubernetes-titok használatával
A Log Analytics-munkaterület AZONOSÍTÓjának és kulcsának védetté tétele érdekében a Kubernetes titkos kulcsot használhatja a Daemonset elemet YAML fájljának részeként.

- Másolja a parancsfájlt, a titkos sablonfájl és a Daemonset elemet YAML-fájlját (a [tárházból](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)), és győződjön meg arról, hogy ugyanazon a címtáron vannak.
  - titkos kód generálása – secret-gen.sh
  - titkos kód sablon - secret-template.yaml
    - Daemonset elemet YAML fájl-omsagent-DS-Secrets. YAML
- Futtassa a szkriptet. A parancsfájl kérni fogja a Log Analytics munkaterület AZONOSÍTÓját és elsődleges kulcsát. Szúrja be, és a szkript létrehoz egy titkos YAML-fájlt, amellyel futtathatja.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Hozza létre a titkos kulcsok pod a következő futtatásával:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Ellenőrizze, hogy futtassa a következőt:

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

  - Hozza létre a omsagent Daemon-set utasításait a következő futtatásával:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Összegzés
Ennyi az egész! Néhány perc elteltével láthatja, hogy a Log Analytics-irányítópultra áramló adatfolyamok láthatók.
