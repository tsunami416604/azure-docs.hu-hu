---
title: Language Detection Kubernetes konfigurációs és üzembe helyezési lépések
titleSuffix: Azure Cognitive Services
description: Language Detection Kubernetes konfigurációs és üzembe helyezési lépések
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: addb044d82429a4471e7ecd302351dd7b7eada84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262138"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>A Nyelvészlelési tároló telepítése AKS-fürtre

1. Nyissa meg az Azure CLI-t, és jelentkezzen be az Azure-ba.

    ```azurecli
    az login
    ```

1. Jelentkezzen be az AKS-fürtbe. Cserélje `your-cluster-name` `your-resource-group` ki és a megfelelő értékeket.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    A parancs futtatása után a következőhöz hasonló üzenetet jelent:

    ```output
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

1. A szövegszerkesztőn belül hozzon létre egy *language.yaml*nevű új fájlt, és illessze be a következő YAML-fájlt. Ügyeljen arra, `billing/value` `apikey/value` hogy cserélje ki, és a saját adatait.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
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
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. Mentse a fájlt, és zárja be a szövegszerkesztőt.
1. Futtassa a `apply` Kubernetes parancsot a *language.yaml* fájllal a célként:

    ```console
    kubectl apply -f language.yaml
    ```

    Miután a parancs sikeresen alkalmazza a központi telepítési konfigurációt, egy üzenet jelenik meg a következő kimenethez hasonlóan:

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. Ellenőrizze, hogy a pod telepítve van-e:

    ```console
    kubectl get pods
    ```

    A pod futó állapotának kimenete:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Ellenőrizze, hogy a szolgáltatás elérhető-e, és az IP-cím beszerzése.

    ```console
    kubectl get services
    ```

    A podban lévő *nyelvi* szolgáltatás futó állapotának kimenete:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
