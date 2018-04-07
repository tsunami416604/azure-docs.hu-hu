---
title: SSH-ból fürtcsomópontok Azure tároló szolgáltatás (AKS)
description: Az SSH-kapcsolat létrehozása az Azure-tároló szolgáltatás (AKS) fürt a csomópontok
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH-ból fürtcsomópontok Azure tároló szolgáltatás (AKS)

Alkalmanként szükség lehet egy Azure tároló szolgáltatás (AKS) csomópont karbantartási, naplógyűjtést vagy egyéb hibaelhárítási műveletek eléréséhez. Azure-tároló szolgáltatás (AKS) csomópontok nem érhetők el az internethez. Az jelen dokumentumban szereplő lépések segítségével hozzon létre egy SSH-kapcsolat egy AKS csomóponttal.

## <a name="get-aks-node-address"></a>AKS címe beolvasása

Az IP-cím használatával AKS fürt csomópont beolvasása a `az vm list-ip-addresses` parancsot. Az erőforráscsoport neve cserélje le a AKS erőforráscsoport nevét.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>SSH-kapcsolat létrehozása

Futtassa a `debian` tároló rendszerképet, csatlakoztassa azt egy terminál-munkamenetet. A tároló majd segítségével hozzon létre egy SSH-munkamenetet a AKS fürt bármely csomópontjára.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Egy SSH-ügyfél telepítése a tárolóban.

```console
apt-get update && apt-get install openssh-client -y
```

Nyissa meg a második terminált, és minden három munkaállomás-csoporttal az újonnan létrehozott pod nevét listája.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Az SSH-kulcs másolása fogyasztanak, pod neve cserélje le a megfelelő értéket.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Frissítés a `id_rsa` fájlt úgy, hogy a felhasználó csak olvasható legyen.

```console
chmod 0600 id_rsa
```

Most hozzon létre egy SSH-kapcsolat a AKS csomópontra. Az alapértelmezett felhasználónév egy AKS fürt `azureuser`. Ez a fiók megváltozott, a fürt létrehozásának idejét, helyettesítse be a megfelelő rendszergazdai felhasználónév.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>SSH-hozzáférés

Ha befejezte, lépjen ki az SSH-munkamenetet, és majd a tároló interaktív munkamenet. Ez a művelet törli a AKS fürtről SSH hozzáféréshez használt fogyasztanak.