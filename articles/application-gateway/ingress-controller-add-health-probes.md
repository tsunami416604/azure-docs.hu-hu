---
title: Állapot-mintavétel hozzáadása az AK-hüvelyekhez
description: Ez a cikk azt ismerteti, hogyan adhatók hozzá az egészségügyi vizsgálatok (készültség és/vagy élettartam) az AK-hüvelyekhez egy Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795601"
---
# <a name="add-health-probes-to-your-service"></a>Állapot-mintavétel hozzáadása a szolgáltatáshoz
Alapértelmezés szerint a bejövő forgalomhoz tartozó vezérlő kiépít egy HTTP GET mintavételt a kitett hüvelyek számára.
A mintavételi tulajdonságok testreszabhatók úgy, hogy [készültségi vagy élettartam](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) -vizsgálatot adnak a `deployment` / `pod` specifikációhoz.

## <a name="with-readinessprobe-or-livenessprobe"></a>`readinessProbe` Vagy`livenessProbe`
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
> * `readinessProbe`és `livenessProbe` támogatottak, ha a `httpGet`-vel van konfigurálva.
> * A nem a pod-on keresztül kitett porton lévő szondázás jelenleg nem támogatott.
> * `HttpHeaders``SuccessThreshold` , `InitialDelaySeconds`nem támogatottak.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Nélkül `readinessProbe` vagy`livenessProbe`
Ha a fenti mintavételek nincsenek megadva, akkor a bejövő vezérlő feltételezi, hogy a `Path` szolgáltatás elérhető a `backend-path-prefix` jegyzethez vagy a szolgáltatás `path` `ingress` definíciójában megadott módon.

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