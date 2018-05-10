---
title: Kubelet naplók lekérése az Azure Kubernetes szolgáltatás (AKS)
description: Azure Kubernetes szolgáltatás (AKS) fürtcsomópontok kubelet naplók beolvasása
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0467be7e91fdbf4685fc41a375ea86a503e26009
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Azure Kubernetes szolgáltatás (AKS) fürtcsomópontok kubelet naplók beolvasása

Alkalmanként szükség lehet egy Azure Kubernetes szolgáltatás (AKS) csomópont hibaelhárítási célból kubelet naplók beolvasása. Ez a dokumentum részletesen megoldás lehet, ha ezek a naplók húzza.

## <a name="create-an-ssh-connection"></a>Az SSH-kapcsolat létrehozása

Először hozzon létre egy SSH-kapcsolat a csomóponthoz, amelyre kubelet naplók lekéréses kell. Ez a művelet részleteit a a [SSH-ból Azure Kubernetes szolgáltatás (AKS) fürtcsomópontok] [ aks-ssh] dokumentum.

## <a name="get-kubelet-logs"></a>Kubelet-naplók beszerzése

Egyszer csatlakozott a csomópontra, a következő parancsot a kubelet naplók le tudja.

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

Példa a kimenetre:

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md