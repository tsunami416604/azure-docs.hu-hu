---
title: "SSH-ból fürtcsomópontok Azure tároló szolgáltatás (AKS)"
description: "Az SSH-kapcsolat létrehozása az Azure-tároló szolgáltatás (AKS) fürt a csomópontok"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH-ból fürtcsomópontok Azure tároló szolgáltatás (AKS)

Alkalmanként szükség lehet egy Azure tároló szolgáltatás (AKS) csomópont karbantartási, naplógyűjtést vagy egyéb hibaelhárítási műveletek eléréséhez. Azure-tároló szolgáltatás (AKS) csomópontok nem érhetők el az internethez. Az jelen dokumentumban szereplő lépések segítségével hozzon létre egy SSH-kapcsolat egy AKS csomóponttal.

## <a name="configure-ssh-access"></a>SSH-hozzáférés konfigurálása

 SSH-ból egy konkrét csomóponton, hogy egy pod jön létre a `hostNetwork` hozzáférést. A szolgáltatás pod hozzáférés is létrejön. Ez a konfiguráció kiemelt jogosultságokkal rendelkezik, és használat után el kell távolítani.

Hozzon létre egy fájlt `aks-ssh.yaml` , és másolja a jegyzékfájlban. Frissítse a cél AKS csomópont neve a csomópont neve.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

A jegyzékfájl létrehozása a tok- és szolgáltatás fut.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

A külső IP-cím elérhetőségi szolgáltatás beolvasása. IP-címének konfigurációja befejezéséhez percet is igénybe vehet. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Hozzon létre a ssh kapcsolat. 

Az alapértelmezett felhasználónév egy AKS fürt `azureuser`. Ez a fiók megváltozott, a fürt létrehozásának idejét, helyettesítse be a megfelelő rendszergazdai felhasználónév. 

Ha a kulcs jelenleg nem `~/ssh/id_rsa`, adja meg a megfelelő helyen történő a `ssh -i` argumentum.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

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

Ha befejezte, törölje a hozzáférési tok SSH- és service.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```