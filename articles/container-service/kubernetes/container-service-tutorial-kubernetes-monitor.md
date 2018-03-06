---
title: "Azure Container Service-oktatóanyag – A Kubernetes monitorozása"
description: "Azure Container Service-oktatóanyag – A Kubernetes monitorozása a Microsoft Operations Management Suite (OMS) használatával"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 965ce4b7e154684fc1d171c90f17498afc828a66
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Kubernetes-fürt monitorozása az Operations Management Suite használatával

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A Kubernetes-fürt és -tárolók monitorozása kritikus fontosságú, különösen, ha egy éles fürtöt kezel skálázható módon, több alkalmazással. 

Több Kubernetes-monitorozási megoldás közül választhat a Microsofttól vagy más szolgáltatóktól. Ebben az oktatóanyagban a Kubernetes-fürt monitorozásához az [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), a Microsoft felhőalapú informatikai felügyeleti megoldása tárolási megoldását használja. (Az OMS tárolási megoldása előzetes verzióként érhető el.)

Ez az oktatóanyag, amely egy hétrészes sorozat hetedik része, a következő feladatokon vezet végig:

> [!div class="checklist"]
> * OMS-munkaterület beállításainak lekérése
> * OMS-ügynökök beállítása a Kubernetes-csomópontokon
> * Hozzáférés a monitorozási információkhoz az OMS-portálon vagy az Azure Portalon

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk tárolórendszerképekbe, a rendszerképeket feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. 

Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez. 

## <a name="get-workspace-settings"></a>Munkaterület beállításainak lekérése

Az [OMS-portál](https://mms.microsoft.com) megnyitásakor lépjen a **Beállítások** > **Csatlakoztatott források** > **Linuxos kiszolgálók** elemre. Itt megtalálhatja a *munkaterület-azonosítót* és egy elsődleges vagy másodlagos *munkaterületkulcsot*. Jegyezze fel ezeket az értékeket, mert szüksége lesz rájuk az OMS-ügynökök a fürtön történő beállításához.

## <a name="set-up-oms-agents"></a>OMS-ügynökök beállítása

Itt talál egy YAML-fájlt az OMS-ügynökök a Linux-fürtcsomópontokon történő beállításához. Létrehoz egy Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) elemet, amely minden fürtcsomóponton egy azonos podot futtat. A DaemonSet erőforrás ideális figyelőügynökök üzembe helyezéséhez. 

Mentse az alábbi szöveget egy `oms-daemonset.yaml` nevű fájlban, és a *myWorkspaceID* és a *myWorkspaceKey* helyőrzőértékeket cserélje le az OMS-munkaterület azonosítójára és kulcsára. (Az éles környezetben titkos kódokként kódolhatja ezeket az értékeket.)

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
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
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
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
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

Ha az ügynökök futnak, az OMS számára az adatok betöltése és feldolgozása több percet vesz igénybe.

## <a name="access-monitoring-data"></a>Monitorozási adatok elérése

Megtekintheti és elemezheti az OMS-tároló monitorozási adatait a [tárolómegoldással](../../log-analytics/log-analytics-containers.md) az OMS-portálon vagy az Azure Portalon. 

Ha a tárolómegoldást az [OMS-portál](https://mms.microsoft.com) segítségével szeretné telepíteni, lépjen a **megoldástárba**. Ezután adja hozzá a **tárolómegoldást**. Másik megoldásként adja hozzá a tárolómegoldást az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Az OMS-portálon keresse meg a **Tárolók** összefoglaló-csempét az OMS-irányítópulton. Kattintson a csempére többek között a következő részletekért: tárolóesemények, hibák, állapot, rendszerképek leltára, valamint processzor- és memóriahasználat. Részletesebb információkért kattintson egy sorra bármely csempén, vagy végezzen [naplókeresést](../../log-analytics/log-analytics-log-searches.md).

![Tárolók irányítópultja az OMS-portálon](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Hasonlóképpen az Azure Portalon lépjen a **Log Analytics** területre, és válassza ki a munkaterület nevét. A **Tárolók** összefoglaló-csempe megtekintéséhez kattintson a **Megoldások** > **Tárolók** elemre. A részletek megtekintéséhez kattintson a csempére.

A monitorozási adatok lekérdezésére és elemzésére vonatkozó részletes útmutatásért tekintse meg az [Azure Log Analytics dokumentációját](../../log-analytics/index.yml).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az OMS használatával monitorozta a Kubernetes-fürtöt. A következők feladatokat hajtottuk végre:

> [!div class="checklist"]
> * OMS-munkaterület beállításainak lekérése
> * OMS-ügynökök beállítása a Kubernetes-csomópontokon
> * Hozzáférés a monitorozási információkhoz az OMS-portálon vagy az Azure Portalon


Kövesse ezt a hivatkozást a Container Service-hez előre felépített szkriptminták megtekintéséhez.

> [!div class="nextstepaction"]
> [Az Azure Container Service szkriptmintái](cli-samples.md)
