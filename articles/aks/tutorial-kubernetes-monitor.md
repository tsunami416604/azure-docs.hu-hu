---
title: "Az Azure útmutató - figyelő Kubernetes Kubernetes"
description: "AKS oktatóanyag - figyelő Kubernetes a Microsoft Operations Management Suite (OMS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d10f9ce965e832c826e2b27b4746231b47be83d0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-azure-container-service-aks"></a>A figyelő az Azure Tárolószolgáltatás (AKS)

A Kubernetes fürt és a tárolók figyelése fontos, különösen akkor, ha egy éles fürt futtató több alkalmazással rendelkező léptékű.

Ebben az oktatóanyagban a AKS fürt használatával figyelés konfigurálása a [Log Analytics-tárolók megoldást][log-analytics-containers].

Ebben az oktatóanyagban nyolc, hét részét tartalmazza a következő feladatokat:

> [!div class="checklist"]
> * A felügyeleti megoldás tároló konfigurálása
> * A figyelő az ügynökök konfigurálása
> * Elérni az Azure-portálon figyelési adatait.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagok tároló lemezképek, a feltöltött Azure tároló beállításjegyzék ezeket a lemezképeket és a létrehozott Kubernetes fürt alkalmazás lett csomagolva.

Ha nem volna ezeket a lépéseket, és szeretné követéséhez, vissza [oktatóanyag 1 – létrehozás tároló képek][aks-tutorial-prepare-app].

## <a name="configure-the-monitoring-solution"></a>A figyelési megoldás konfigurálása

Válassza ki az Azure-portálon **új** keresse meg a `Container Monitoring Solution`. Ha található, válassza ki a **létrehozása**.

![Megoldás hozzáadása](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Hozzon létre egy új OMS-munkaterület, vagy válasszon egy meglévőt. Az OMS-munkaterület űrlap végigvezeti a folyamat.

A munkaterület létrehozása esetén **rögzítés az irányítópulton** partnerek.

![OMS-munkaterület](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Ha elkészült, válassza ki a **OK**. Ellenőrzés befejezése után válassza ki a **létrehozása** létrehozni a tárolót, felügyeleti megoldás.

Ha a munkaterületet létrejött, jelölőnégyzetként jelenik meg az Azure portálon.

## <a name="get-workspace-settings"></a>Munkaterület beállításainak beolvasása

A naplóelemzési munkaterület azonosítója és kulcsa van szükség a Kubernetes csomópontokon a megoldás ügynök konfigurálása.

Válassza ki az értékek lekérésére, **OMS-munkaterület** a tároló-megoldásokkal bal oldali menüből. Válassza ki **speciális beállítások** és tekintse meg a **MUNKATERÜLET azonosítója** és a **elsődleges kulcs**.

## <a name="configure-monitoring-agents"></a>Figyelés ügynökök konfigurálása

A következő Kubernetes jegyzékfájl a tárolófigyelő ügynökök Kubernetes fürt konfigurálásához használható. Létrehoz egy Kubernetes [DaemonSet][kubernetes-daemonset], amely egyetlen pod fut a fürt minden csomópontján.

A következő szöveg nevű fájlba mentése `oms-daemonset.yaml`, és cserélje le a helyőrző értékeket az `WSID` és `KEY` a napló Analytics munkaterületének Azonosítóját és kulcsát.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
```

A DaemonSet létrehozása a következő parancsot:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

A DaemonSet létrehozott megtekintéséhez futtassa:

```azurecli-interactive
kubectl get daemonset
```

A kimenet a következőkhöz hasonló:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Miután az ügynökök futnak, OMS betöltési és feldolgozni az adatokat több percet vesz igénybe.

## <a name="access-monitoring-data"></a>Figyelési adatok hozzáférés

Az Azure-portálon válassza ki a Naplóelemzési munkaterület sikeresen rögzítve a portál Irányítópultjára. Kattintson a **tároló figyelésére szolgáló megoldás** csempére. Itt található információk a AKS fürt és a tárolók a fürtből.

![Irányítópult](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Tekintse meg a [Azure Log Analytics-dokumentáció] [ log-analytics-docs] kérdez le, és a figyelési adatok elemzése részletes útmutatást.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az OMS Kubernetes fürt figyeli. Feladatok kezelt tartalmazza:

> [!div class="checklist"]
> * A felügyeleti megoldás tároló konfigurálása
> * A figyelő az ügynökök konfigurálása
> * Elérni az Azure-portálon figyelési adatait.

A következő oktatóanyag tájékozódhat az új verzióra frissíti Kubernetes továbblépés.

> [!div class="nextstepaction"]
> [Frissítési Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml