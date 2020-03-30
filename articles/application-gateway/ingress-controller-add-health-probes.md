---
title: Egészségügyi szondák hozzáadása az AKS-podokhoz
description: Ez a cikk az állapotmintak (készenlét és/vagy liveness) hozzáadása az Alkalmazásátjáróval rendelkező AKS-podokhoz való hozzáadásáról.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795601"
---
# <a name="add-health-probes-to-your-service"></a>Állapotminta hozzáadása a szolgáltatáshoz
Alapértelmezés szerint a bejövő üzenetek vezérlő egy HTTP GET-mintavételt hoz üzembe a kitett podok számára.
A mintavételi tulajdonságok testreszabható egy [készenléti vagy liveness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) hozzáadásával a `deployment` / `pod` specifikációhoz.

## <a name="with-readinessprobe-or-livenessprobe"></a>Vagy `readinessProbe``livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API – referencia:
* [Konténerszondák](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet művelet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`és `livenessProbe` támogatottak, `httpGet`ha a alkalmazással konfigurálva vannak.
> * A podon lévőtől eltérő porton jelenleg nem támogatott a vizsgálat.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` , nem támogatottak.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Nélkül `readinessProbe` vagy`livenessProbe`
Ha a fenti szondák nincsenek megadva, akkor a Bejövő adatkezelő `Path` feltételezi, `backend-path-prefix` hogy a szolgáltatás `path` elérhető a `ingress` jegyzetre megadott vagy a szolgáltatás definíciójában megadott módon.

## <a name="default-values-for-health-probe"></a>Az állapotminta alapértelmezett értékei
Minden olyan tulajdonság, amely nem lehet következtetni a készenléti / liveness mintavétel, alapértelmezett értékek vannak beállítva.

| Alkalmazásátjáró-mintavételtulajdonság | Alapértelmezett érték |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |