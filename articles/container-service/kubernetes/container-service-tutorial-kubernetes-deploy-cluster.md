---
title: "Azure Tárolószolgáltatás útmutató - fürt központi telepítése |} Microsoft Docs"
description: "Azure Tárolószolgáltatás útmutató - fürt központi telepítése"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dcced32925ecd109be8dde6324cca4899923679b
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Az Azure Tárolószolgáltatásban Kubernetes fürt központi telepítése

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Azure Tárolószolgáltatás készen áll a Kubernetes éles fürt üzembe esetén egyszerűen és gyorsan. Ez az oktatóanyag, 7, 3. rész egy Azure-tároló szolgáltatás Kubernetes fürtre van telepítve. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * Egy Kubernetes ACS-fürt telepítése
> * A Kubernetes CLI (kubectl) telepítése
> * Kubectl konfigurálása

A következő útmutatókból az Azure szavazattal alkalmazás központi telepítése a fürt, méretezhető, frissítése, és az Operations Management Suite a Kubernetes fürt figyelésére van beállítva.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokat a tároló-lemezkép létrejött, de feltöltött egy Azure-tároló beállításjegyzék-példányon. Ha nem volna ezeket a lépéseket, és szeretné követéséhez, vissza [oktatóanyag 1 – létrehozás tároló képek](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Hozzon létre egy Kubernetes-fürtöt az Azure Container Service-ben az [az acs create](/cli/azure/acs#create) paranccsal. 

Az alábbi példakód létrehozza a fürt nevű `myK8sCluster` nevű erőforráscsoportban `myResourceGroup`. Ez az erőforráscsoport jött létre a [az oktatóanyag előző](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

Egyes esetekben – például korlátozott próbaverziónál – az Azure-előfizetés korlátozott hozzáféréssel rendelkezik az Azure-erőforrásokhoz. Ha az üzembe helyezés az elérhető magok korlátozott száma miatt hiúsul meg, csökkentse az alapértelmezett ügynökök számát az `--agent-count 1` az [az acs create](/cli/azure/acs#create) parancshoz történő hozzáadásával. 

Pár perc múlva a telepítés befejeződik, és értéket ad vissza json formátumú az ACS telepítési információkat.

## <a name="install-the-kubectl-cli"></a>A kubectl parancssori felület telepítése

Az Kubernetes fürthöz csatlakoztatja az ügyfélszámítógépről, használja a [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), a Kubernetes parancssori ügyfél. 

Ha az Azure CloudShellt használja, a kubectl már telepítve van. Ha helyileg telepíteni szeretne, használja a [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) parancsot.

Ha a Linux vagy macOS fut, szükség lehet a sudo futtatásához. A Windows győződjön meg arról, a rendszerhéj futtatása rendszergazdaként.

```azurecli-interactive 
az acs kubernetes install-cli 
```

A Windows, az alapértelmezett telepítési van *c:\program files (x86)\kubectl.exe*. Szükség lehet a fájl hozzáadása a Windows útvonalhoz. 

## <a name="connect-with-kubectl"></a>Kapcsolódás a kubectl parancssori ügyfélhez

A kubectl a Kubernetes-fürthöz való csatlakozásra konfigurálásához futtassa az [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) parancsot.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Ellenőrizze a kapcsolatot a fürthöz, futtassa a [kubectl beolvasása csomópontok](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) parancsot.

```azurecli-interactive
kubectl get nodes
```

Kimenet:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Oktatóanyag befejezése után következő, hogy az ACS Kubernetes fürt készen áll a munkaterhelések. A következő útmutatókból egy több tároló alkalmazás üzemel a fürthöz, horizontálisan, frissítése, és figyeli.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure-tároló szolgáltatás Kubernetes fürt tették elérhetővé telepítésre. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Egy Kubernetes ACS-fürt telepítése
> * A Kubernetes CLI (kubectl) telepítése
> * Konfigurált kubectl

Továbblépés a következő oktatóanyag fürtben futó alkalmazás megismeréséhez.

> [!div class="nextstepaction"]
> [Kubernetes az alkalmazás központi telepítése](./container-service-tutorial-kubernetes-deploy-application.md)
