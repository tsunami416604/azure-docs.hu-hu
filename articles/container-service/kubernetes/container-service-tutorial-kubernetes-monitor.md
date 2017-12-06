---
title: "Azure Tárolószolgáltatás útmutató - figyelő Kubernetes"
description: "Azure Tárolószolgáltatás útmutató - figyelő Kubernetes a Microsoft Operations Management Suite (OMS)"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 07/25/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 948e3aeea34a0355c3d958f29008c26499e19ba4
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>A figyelő az Operations Management Suite Kubernetes fürt

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A Kubernetes fürt és a tárolók figyelése fontos, különösen akkor, ha több alkalmazáshoz léptékű termelési fürt felügyeletére. 

Kihasználhatja a több Kubernetes figyelési megoldás, a Microsoft vagy más szolgáltatók. Ebben az oktatóanyagban a tárolók megoldás használatával figyelheti a Kubernetes fürt [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), a Microsoft felhőalapú informatikai felügyeleti megoldás. (A képen van az OMS-tárolók megoldás.)

Ebben az oktatóanyagban hét részét hét, a következő feladatokat tartalmazza:

> [!div class="checklist"]
> * OMS-munkaterület beállításainak beolvasása
> * Állítsa be a Kubernetes csomópontok OMS-ügynökök
> * Elérni a figyelési adatait az OMS-portálon vagy az Azure-portálon

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagok tároló lemezképek, a feltöltött Azure tároló beállításjegyzék ezeket a lemezképeket és a létrehozott Kubernetes fürt alkalmazás lett csomagolva. 

Ha nem volna ezeket a lépéseket, és szeretné követéséhez, vissza [oktatóanyag 1 – létrehozás tároló képek](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Munkaterület beállításainak beolvasása

Amikor megnyitja a [OMS-portálon](https://mms.microsoft.com), keresse fel **beállítások** > **csatlakoztatott források** > **Linux kiszolgálók**. Itt megtalálhatja a *munkaterület azonosítója* és elsődleges vagy másodlagos *Munkaterületkulcsot*. Jegyezze fel ezeket az értékeket, amely OMS-ügynököt a fürt beállításához szükséges.

## <a name="set-up-oms-agents"></a>Állítsa be az OMS-ügynökök

Itt egy olyan YAM fájl OMS-ügynököt a Linux fürtcsomópontokon beállítása. Létrehoz egy Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), amely egy egyetlen azonos pod fut a fürt minden csomópontján. A DaemonSet erőforrás figyelési ügynök telepítéséhez ideális. 

A következő szöveg nevű fájlba mentése `oms-daemonset.yaml`, és cserélje le a helyőrző értékeket az *myWorkspaceID* és *myWorkspaceKey* az OMS-munkaterület azonosítója és kulcsa. (A termelési, is kódol ezeket az értékeket, a titkos kulcsok.)

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

A DaemonSet létrehozása a következő parancsot:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

A DaemonSet létrehozott megtekintéséhez futtassa:

```azurecli-interactive
kubectl get daemonset
```

A kimenet a következőkhöz hasonló:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Miután az ügynökök futnak, OMS betöltési és feldolgozni az adatokat több percet vesz igénybe.

## <a name="access-monitoring-data"></a>Figyelési adatok hozzáférés

Megtekintése és elemzése a figyelési adatok az OMS-tároló a [tároló megoldás](../../log-analytics/log-analytics-containers.md) az OMS-portálon vagy az Azure-portálon. 

A tároló megoldás használatával telepítéséhez a [OMS-portálon](https://mms.microsoft.com), keresse fel **megoldások gyűjteménye**. Majd adja hozzá **tároló megoldás**. Másik lehetőségként adja hozzá a tárolók megoldást a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Az OMS-portálon keresse meg a **tárolók** összefoglalás csempéje az OMS-irányítópultot. Kattintson a csempére vonatkozó információt talál többek között: tároló események, a hibák, a állapota, a kép leltározás és a Processzor- és memóriahasználatról. Részletesebb információkért kattintson egy sorra a bármely csempére, vagy hajtsa végre a [naplófájl-keresési](../../log-analytics/log-analytics-log-searches.md).

![Az OMS-portálon tárolók irányítópult](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Ehhez hasonlóan az Azure-portálon lépjen **Naplóelemzési** , és válassza ki a munkaterület nevét. Hogy a **tárolók** összefoglalás csempére, kattintson a **megoldások** > **tárolók**. Részletek megtekintéséhez kattintson a csempére.

Tekintse meg a [Azure Log Analytics-dokumentáció](../../log-analytics/index.yml) kérdez le, és a figyelési adatok elemzése részletes útmutatást.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az OMS Kubernetes fürt figyeli. Feladatok kezelt tartalmazza:

> [!div class="checklist"]
> * OMS-munkaterület beállításainak beolvasása
> * Állítsa be a Kubernetes csomópontok OMS-ügynökök
> * Elérni a figyelési adatait az OMS-portálon vagy az Azure-portálon


Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített parancsfájl minták számára.

> [!div class="nextstepaction"]
> [Azure Tárolószolgáltatás-parancsfájl minták](cli-samples.md)
