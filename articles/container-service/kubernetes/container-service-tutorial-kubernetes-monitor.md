---
title: (ELAVULT) Az Azure Container Service-oktatóanyag – a Kubernetes monitorozása
description: Azure Container Service-oktatóanyag – A Kubernetes monitorozása a Log Analytics használatával
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6f95aa701228730682c0122dc1fd46d8a2537ce1
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001617"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>(ELAVULT) A Log Analytics használatával a Kubernetes-fürt monitorozása

> [!TIP]
> Ebben az oktatóanyagban, amely használja az Azure Kubernetes Service-ben, lásd: a frissített verzió [tárolók (előzetes verzió) – áttekintés az Azure Monitor](../../azure-monitor/insights/container-insights-overview.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

A Kubernetes-fürt és -tárolók monitorozása kritikus fontosságú, különösen, ha egy éles fürtöt kezel skálázható módon, több alkalmazással.

Több Kubernetes-monitorozási megoldás közül választhat a Microsofttól vagy más szolgáltatóktól. Ebben az oktatóanyagban a Kubernetes-fürt monitorozásához a Microsoft felhőalapú informatikai felügyeleti megoldásának, a [Log Analyticsnek](../../operations-management-suite/operations-management-suite-overview.md) a tárolási megoldását használja. (A tárolási megoldás előzetes verzióként érhető el.)

Ez az oktatóanyag, amely egy hétrészes sorozat hetedik része, a következő feladatokon vezet végig:

> [!div class="checklist"]
> * A Log Analytics-munkaterület beállításainak lekérése
> * Log Analytics-ügynökök beállítása a Kubernetes-csomópontokon
> * Hozzáférés a monitorozási információkhoz a Log Analytics-portálon vagy az Azure Portalon

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk tárolórendszerképekbe, a rendszerképeket feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt.

Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez.

## <a name="get-workspace-settings"></a>Munkaterület beállításainak lekérése

A [Log Analytics-portál](https://mms.microsoft.com) megnyitásakor lépjen a **Beállítások** > **Csatlakoztatott források** > **Linuxos kiszolgálók** elemre. Itt megtalálhatja a *munkaterület-azonosítót* és egy elsődleges vagy másodlagos *munkaterületkulcsot*. Jegyezze fel ezeket az értékeket, mert szüksége lesz rájuk a Log Analytics-ügynökök a fürtön való beállításához.

## <a name="create-kubernetes-secret"></a>Kubernetes titkos kódjának létrehozása

Tárolja a Log Analytics-munkaterület beállításait egy `omsagent-secret` nevű Kubernetes titkos kulcsban a [kubectl create secret][kubectl-create-secret] paranccsal. A `WORKSPACE_ID` helyére írja be a Log Analytics-munkaterület azonosítóját, a `WORKSPACE_KEY` helyére pedig a munkaterület kulcsát.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Log Analytics-ügynökök beállítása

A következő Kubernetes-jegyzékfájl a tárolómonitorozási ügynökök konfigurálásához használható a Kubernetes-fürtön. Létrehoz egy Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) elemet, amely minden fürtcsomóponton egy azonos podot futtat.

Mentse az alábbi szöveget egy `oms-daemonset.yaml` nevű fájlba.

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

A DaemonSet létrehozásához használja a következő parancsot:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

A DaemonSet létrejöttének ellenőrzéséhez futtassa a következőt:

```azurecli-interactive
kubectl get daemonset
```

A kimenet a következőkhöz hasonló:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Ha az ügynökök futnak, a Log Analytics számára az adatok betöltése és feldolgozása néhány percet vesz igénybe.

## <a name="access-monitoring-data"></a>Monitorozási adatok elérése

A tároló monitorozási adatait a Log Analytics-portálon vagy az Azure Portalon a [tárolómegoldással](../../azure-monitor/insights/containers.md) tekintheti meg és elemezheti.

Ha a tárolómegoldást a [Log Analytics-portál](https://mms.microsoft.com) segítségével szeretné telepíteni, lépjen a **megoldástárba**. Ezután adja hozzá a **tárolómegoldást**. Másik megoldásként adja hozzá a tárolómegoldást az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

A Log Analytics-portálon keresse meg a **Tárolók** összefoglaló csempét az irányítópulton. Kattintson a csempére többek között a következő részletekért: tárolóesemények, hibák, állapot, rendszerképek leltára, valamint processzor- és memóriahasználat. Részletesebb információkért kattintson egy sorra bármely csempén, vagy végezzen [naplókeresést](../../log-analytics/log-analytics-log-searches.md).

![Tárolók irányítópult az Azure Portalon](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Hasonlóképpen az Azure Portalon lépjen a **Log Analytics** területre, és válassza ki a munkaterület nevét. A **Tárolók** összefoglaló-csempe megtekintéséhez kattintson a **Megoldások** > **Tárolók** elemre. A részletek megtekintéséhez kattintson a csempére.

A monitorozási adatok lekérdezésére és elemzésére vonatkozó részletes útmutatásért tekintse meg az [Azure Log Analytics dokumentációját](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Log Analytics használatával monitoroztuk a Kubernetes-fürtöt. A következők feladatokat hajtottuk végre:

> [!div class="checklist"]
> * A Log Analytics-munkaterület beállításainak lekérése
> * Log Analytics-ügynökök beállítása a Kubernetes-csomópontokon
> * Hozzáférés a monitorozási információkhoz a Log Analytics-portálon vagy az Azure Portalon


Kövesse ezt a hivatkozást a Container Service-hez előre felépített szkriptminták megtekintéséhez.

> [!div class="nextstepaction"]
> [Az Azure Container Service szkriptmintái](cli-samples.md)
