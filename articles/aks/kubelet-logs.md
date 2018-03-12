---
title: "Kubelet naplók lekérése az Azure tároló szolgáltatás (AKS)"
description: "Azure tároló szolgáltatás (AKS) fürtcsomópontok kubelet naplók beolvasása"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 56e20a9f9d17eac01e6f85007db41dcc417f83e4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="get-kubelet-logs-from-azure-container-service-aks-cluster-nodes"></a>Azure tároló szolgáltatás (AKS) fürtcsomópontok kubelet naplók beolvasása

Alkalmanként szükség lehet egy Azure tároló szolgáltatás (AKS) csomópont hibaelhárítási célból kubelet naplók beolvasása. Ez a dokumentum részletesen megoldás lehet, ha ezek a naplók húzza.

## <a name="create-an-ssh-connection"></a>Az SSH-kapcsolat létrehozása

Először hozzon létre egy SSH-kapcsolat a csomóponthoz, amelyre kubelet naplók lekéréses kell. Ez a művelet részleteit a a [SSH-ból Azure tároló szolgáltatás (AKS) fürtcsomópontok] [ aks-ssh] dokumentum.

## <a name="get-kubelet-logs"></a>Kubelet naplófájlok

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