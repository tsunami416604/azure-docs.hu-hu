---
title: Hangulatelemzés Kubernetes konfigurációs és üzembe helyezési lépések
titleSuffix: Azure Cognitive Services
description: Hangulatelemzés Kubernetes konfigurációs és üzembe helyezési lépések
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877827"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>A Hangulatelemzés tároló telepítése AKS-fürtre

1. Nyissa meg az Azure CLI-t, és jelentkezzen be az Azure-ba.

    ```azurecli
    az login
    ```

1. Jelentkezzen be az AKS-fürtbe. Cserélje `your-cluster-name` `your-resource-group` ki és a megfelelő értékeket.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    A parancs futtatása után a következőhöz hasonló üzenetet jelent:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Ha több előfizetés érhető el az Azure-fiókjában, és a `az aks get-credentials` parancs hiba esetén ad vissza, gyakori probléma, hogy nem a megfelelő előfizetést használja. Állítsa be az Azure CLI-munkamenet környezetét, hogy ugyanazt az előfizetést használja, amelyhez az erőforrásokat hozta létre, és próbálkozzon újra.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Nyissa meg a választott szövegszerkesztőt. Ez a példa a Visual Studio-kódot használja.

    ```console
    code .
    ```

1. A szövegszerkesztőn belül hozzon létre egy *új, sentiment.yaml*nevű fájlt, és illessze be a következő YAML-fájlt. Ügyeljen arra, `billing/value` `apikey/value` hogy cserélje ki, és a saját adatait.

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
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
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

1. Mentse a fájlt, és zárja be a szövegszerkesztőt.
1. Futtassa a `apply` Kubernetes parancsot a *sentiment.yaml* fájllal a célként:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Miután a parancs sikeresen alkalmazza a központi telepítési konfigurációt, egy üzenet jelenik meg a következő kimenethez hasonlóan:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Ellenőrizze, hogy a pod telepítve van-e:

    ```console
    kubectl get pods
    ```

    A pod futó állapotának kimenete:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ellenőrizze, hogy a szolgáltatás elérhető-e, és az IP-cím beszerzése.

    ```console
    kubectl get services
    ```

    A kimenet a szolgáltatás a *podban* futó állapot:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
