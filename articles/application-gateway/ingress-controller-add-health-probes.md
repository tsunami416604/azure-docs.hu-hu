---
title: Állapot-mintavétel hozzáadása az AK-hüvelyekhez
description: Ez a cikk azt ismerteti, hogyan adhatók hozzá az egészségügyi vizsgálatok (készültség és/vagy élettartam) az AK-hüvelyekhez egy Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 073babf407509c9bbf05340edd828c895fa376e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513626"
---
# <a name="add-health-probes-to-your-service"></a>Állapot-mintavétel hozzáadása a szolgáltatáshoz
Alapértelmezés szerint a bejövő forgalomhoz tartozó vezérlő kiépít egy HTTP GET mintavételt a kitett hüvelyek számára.
A mintavételi tulajdonságok testreszabhatók úgy, hogy [készültségi vagy élettartam](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) -mintavételt adnak hozzá a `deployment`/`pod` spec.

## <a name="with-readinessprobe-or-livenessprobe"></a>`readinessProbe` vagy `livenessProbe`
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

Kubernetes API-referenciája:
* [Tároló-mintavételek](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet művelet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * a `readinessProbe` és a `livenessProbe` a `httpGet`-vel való konfigurálás esetén támogatott.
> * A nem a pod-on keresztül kitett porton lévő szondázás jelenleg nem támogatott.
> * a `HttpHeaders`, `InitialDelaySeconds`, `SuccessThreshold` nem támogatottak.

##  <a name="without-readinessprobe-or-livenessprobe"></a>`readinessProbe` vagy `livenessProbe` nélkül
Ha a fenti mintavételek nincsenek megadva, akkor a bejövő vezérlő feltételezi, hogy a szolgáltatás elérhető `Path` `backend-path-prefix` jegyzethez vagy a szolgáltatás `ingress` definíciójában megadott `path`hoz.

## <a name="default-values-for-health-probe"></a>Az állapot mintavételének alapértelmezett értékei
Minden olyan tulajdonság esetében, amely nem következtethető ki a készültség/élő mintavétel alapján, az alapértelmezett értékek vannak megadva.

| Application Gateway mintavételi tulajdonság | Alapértelmezett érték |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |