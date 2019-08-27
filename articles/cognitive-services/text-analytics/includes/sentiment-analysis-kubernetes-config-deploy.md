---
title: Hangulatelemzés Kubernetes-konfiguráció és a lépések telepítése
titleSuffix: Azure Cognitive Services
description: Hangulatelemzés Kubernetes-konfiguráció és a lépések telepítése
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 5d16500b45cbc6190186835d0c793a48fd121bd0
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051875"
---
## <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Az Hangulatelemzés tároló üzembe helyezése egy AK-fürtön

1. Nyissa meg az Azure CLI-t, és jelentkezzen be az Azure-ba.

    ```azurecli
    az login
    ```

1. Jelentkezzen be az AK-fürtbe. Cserélje `your-cluster-name` le `your-resource-group` a és a értéket a megfelelő értékekre.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    A parancs futtatása után a következőhöz hasonló üzenetet küld:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Ha több előfizetése is elérhető az Azure-fiókjában, és `az aks get-credentials` a parancs hibával tér vissza, egy gyakori probléma, hogy nem megfelelő előfizetést használ. Állítsa be az Azure CLI-munkamenet kontextusát úgy, hogy ugyanazt az előfizetést használja, mint amelyet az erőforrásokhoz hozott létre, és próbálkozzon újra.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Nyissa meg a választható szövegszerkesztőt. Ez a példa a Visual Studio Code-ot használja.

    ```azurecli
    code .
    ```

1. A szövegszerkesztőben hozzon létre egy " *YAML*" nevű új fájlt, és illessze be az alábbi YAML. Ügyeljen arra, hogy `billing/value` a `apikey/value` és a saját adatait cserélje le.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Mentse a fájlt, és zárjuk be a szövegszerkesztőt.
1. Futtassa a Kubernetes `apply` parancsot a *hangulat. YAML* fájllal a célként megadott módon:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Miután a parancs sikeresen alkalmazta a központi telepítési konfigurációt, a következő kimenethez hasonló üzenet jelenik meg:

    ```console
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ellenőrizze, hogy telepítve van-e a pod:

    ```console
    kubectl get pods
    ```

    A pod futási állapotának kimenete:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Győződjön meg arról, hogy a szolgáltatás elérhető, és kérje le az IP-címet.

    ```console
    kubectl get services
    ```

    Az hangulati szolgáltatás a pod-ban futó állapotának kimenete:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
