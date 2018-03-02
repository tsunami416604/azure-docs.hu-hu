---
title: "Azure-on futó Kubernetes oktatóanyag – A Kubernetes monitorozása"
description: "AKS oktatóanyag – A Kubernetes monitorozása a Microsoft Operations Management Suite (OMS) használatával"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2fedd615733e3bf51469d3b69d5fe51e3e99087e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="monitor-azure-container-service-aks"></a>Az Azure Container Service (AKS) monitorozása

A Kubernetes-fürt és -tárolók monitorozása kritikus fontosságú, különösen, ha egy éles fürtöt futtat skálázható módon, több alkalmazással.

Ebben az oktatóanyagban az AKS-fürt monitorozását a [Log Analytics tárolószolgáltatásával][log-analytics-containers] konfigurálja.

Ez az oktatóanyag, amely egy nyolcrészes sorozat hetedik része, a következő feladatokon vezet végig:

> [!div class="checklist"]
> * A tároló monitorozására szolgáló megoldás konfigurálása
> * A monitorozási ügynökök konfigurálása
> * Hozzáférés a monitorozási információkhoz az Azure Portalon

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk tárolórendszerképekbe, a rendszerképeket feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt.

Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="configure-the-monitoring-solution"></a>A monitorozási megoldás konfigurálása

Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget, és keresse meg a `Container Monitoring Solution` elemet. Ha megtalálta, kattintson a **Létrehozás** elemre.

![Megoldás hozzáadása](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Hozzon létre új OMS-munkaterületet, vagy válasszon ki egy meglévőt. Az OMS-munkaterület űrlapja végigvezeti a folyamaton.

A könnyebb elérhetőség érdekében a munkaterület létrehozásakor jelölje be a **Rögzítés az irányítópulton** beállítást.

![OMS-munkaterület](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Ha elkészült, kattintson az **OK** gombra. Az ellenőrzés után kattintson a **Létrehozás** gombra a tárolómonitorozó megoldás létrehozásához.

Amint a munkaterület létrejött, megjelenik az Azure Portalon.

## <a name="get-workspace-settings"></a>Munkaterület beállításainak lekérése

A megoldásügynök Kubernetes-csomópontokon való konfigurálásához szükség lesz a Log Analytics-munkaterület azonosítójára és kulcsára.

Az értékek lekéréséhez válassza az **OMS-munkaterület** lehetőséget a tárolómegoldásokat tartalmazó bal oldali menüből. Válassza a **Speciális beállítások** lehetőséget, és jegyezze fel a **MUNKATERÜLET AZONOSÍTÓJÁT** és az **ELSŐDLEGES KULCSOT**.

## <a name="configure-monitoring-agents"></a>Monitorozási ügynökök konfigurálása

A következő Kubernetes-jegyzékfájl a tárolómonitorozási ügynökök konfigurálásához használható a Kubernetes-fürtön. Létrehoz egy Kubernetes [DaemonSet][kubernetes-daemonset] elemet, amely minden fürtcsomóponton egy podot futtat.

Mentse az alábbi szöveget egy `oms-daemonset.yaml` nevű fájlban, és a `WSID` és a `KEY` helyőrzőértékeket cserélje le a Log Analytics-munkaterület azonosítójára és kulcsára.

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
        - mountPath: /var/lib/docker/containers/
          name: container-log
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
    - name: container-log
      hostPath:
       path: /var/lib/docker/containers/
```

A DaemonSet létrehozásához használja a következő parancsot:

```azurecli
kubectl create -f oms-daemonset.yaml
```

A DaemonSet létrejöttének ellenőrzéséhez futtassa a következőt:

```azurecli
kubectl get daemonset
```

A kimenet a következőkhöz hasonló:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Ha az ügynökök futnak, az OMS számára az adatok betöltése és feldolgozása több percet vesz igénybe.

## <a name="access-monitoring-data"></a>Monitorozási adatok elérése

Az Azure Portalon válassza ki a Log Analytics-munkaterületet, amely a portál irányítópultján van rögzítve. Kattintson a **Tárolómonitorozási megoldás** csempére. Itt információkat találhat az AKS-fürtről és a fürtön található tárolókról.

![Irányítópult](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

A monitorozási adatok lekérdezésére és elemzésére vonatkozó részletes útmutatásért tekintse meg az [Azure Log Analytics-dokumentációját][log-analytics-docs].

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az OMS használatával monitorozta a Kubernetes-fürtöt. A következők feladatokat hajtottuk végre:

> [!div class="checklist"]
> * A tároló monitorozására szolgáló megoldás konfigurálása
> * A monitorozási ügynökök konfigurálása
> * Hozzáférés a monitorozási információkhoz az Azure Portalon

Folytassa a következő oktatóanyaggal, amely a Kurbernetes új verzióra történő frissítését ismerteti.

> [!div class="nextstepaction"]
> [Kubernetes frissítése][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
