---
title: Hozzon létre egy Azure Kubernetes szolgáltatás (AKS) belső terheléselosztó
description: A belső terheléselosztók Azure Kubernetes szolgáltatás (AKS) használja.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6a657df82e1670f7a9d604dd5166ab53bb38bf74
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>A belső terheléselosztók használata Azure Kubernetes szolgáltatás (AKS)

Belső terheléselosztás Kubernetes szolgáltatás számára elérhető lesz a Kubernetes fürt azonos virtuális hálózaton futó alkalmazások. A dokumentum részletek a belső terheléselosztók Azure Kubernetes szolgáltatás (AKS) létrehozása.

## <a name="create-internal-load-balancer"></a>Belső terheléselosztó létrehozása

A belső terheléselosztók létrehozásához összeállítása a szolgáltatástípushoz szolgáltatás jegyzékfájl `LoadBalancer` és a `azure-load-balancer-internal` Megjegyzés az alábbi mintában látható módon.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Amennyiben telepített, az Azure terheléselosztó létrehozása, és szeretné elérhetővé tenni a AKS fürttel azonos virtuális hálózaton.

![Belső terheléselosztó AKS képe](media/internal-lb/internal-lb.png)

Ha a szolgáltatás beolvasása részleteit, az IP-cím a `EXTERNAL-IP` oszlop a belső terheléselosztó IP-címét.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>IP-cím

Ha szeretné használni a belső terheléselosztó egy adott IP-cím, vegye fel a `loadBalancerIP` a load balancer spec tulajdonságot. A megadott IP-cím a AKS fürt azonos alhálózaton kell lennie, és nem már kell rendelni egy erőforrást.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Ha a szolgáltatás adatainak beolvasása, az IP-cím a a `EXTERNAL-IP` egyeznie kell a megadott IP-cím.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>A load balancer törlése

Ha törölték az összes olyan szolgáltatás, a belső terheléselosztó használatával, a terheléselosztó maga is törlődik.

## <a name="next-steps"></a>További lépések

További tudnivalók Kubernetes szolgáltatásának a [Kubernetes szolgáltatások dokumentációja][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/