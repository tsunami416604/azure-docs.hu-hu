---
title: Futtassa az Azure Kubernetes-szolgáltatás
titleSuffix: Text Analytics - Azure Cognitive Services
description: Helyezze üzembe a text analytics tárolókat a vélemények elemzése lemezképpel, az Azure Kubernetes-szolgáltatás, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 4d5e1da01be531550915a38bed17dd8e57be907a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454949"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Hangulatelemzés a tároló üzembe helyezése az Azure Kubernetes-szolgáltatások (AKS)

Ismerje meg, hogyan helyezhet üzembe a Cognitive Services [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) a Hangulatelemzés lemezképet az Azure Kubernetes-szolgáltatások (AKS)-tárolóban. Ez az eljárás exemplifies a egy erőforrás létrehozását a Text Analytics, a társított Hangulatelemzés lemezkép és az azon képessége, hogy a két közvetlenül a böngészőből a vezénylési létrehozását. Tárolók használatával is shift figyelmét a fejlesztők számára az alkalmazásfejlesztés inkább összpontosító infrastruktúra kezelése.

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás több eszközt, hogy telepítve legyen, és helyi futtatása szükséges. Ne használja az Azure Cloud shellben.

* Használja az Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Szövegszerkesztőben, például: [Visual Studio Code](https://code.visualstudio.com/download).
* Telepítse az [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Telepítse a [Kubernetes parancssori Felületét](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Egy Azure-erőforrás a megfelelő tarifacsomagot. Nem minden tarifacsomag használata ebben a tárolóban:
    * **Szövegelemzés** erőforrás F0 vagy Standard díjszabás csak szint esetében.
    * **A cognitive Services** tarifacsomag az S0 erőforrás.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Text Analytics tároló üzembe AKS-fürt

1. Nyissa meg az Azure parancssori felület és a bejelentkezés az Azure-bA

    ```azurecli
    az login
    ```

1. Jelentkezzen be az AKS-fürtöt (cserélje le a `your-cluster-name` és `your-resource-group` a megfelelő értékekkel)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Ez a parancs végrehajtása után jelentést készít a következőhöz hasonló üzenetet:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Ha Ön számára elérhető Azure-fiók több előfizetéssel rendelkezik, és a `az aks get-credentials` parancs hibát ad vissza, a gyakori probléma, hogy a megfelelő előfizetést használ. Egyszerűen állítsa be az Azure CLI-munkamenetet, amely az erőforrásokat a létrehozott ugyanahhoz az előfizetéshez használandó keretében, és próbálkozzon újra.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Nyissa meg a tetszőleges szövegszerkesztőben (Ez a példa __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. Belül a szövegszerkesztőben hozzon létre egy új fájlt _sentiment.yaml_ és illessze be a következő yaml-kódot:

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
              value: "https://westus2.api.cognitive.microsoft.com/"
            - name: apikey
              value: "16c12e3419f54ba49a3222177cef781d"
     
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

1. Mentse a fájlt, és zárja be a szövegszerkesztőben.
1. Hajtsa végre a Kubernetes `apply` parancsot a _sentiment.yaml_ és a cél:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Miután a parancs sikeresen telepítve van a telepítési konfigurációt, a következőhöz hasonló üzenetet egy kimenet:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Győződjön meg arról, hogy telepítve lett-e a POD:

    ```console
    kubectl get pods
    ```

    Ez a POD futó állapotának kimenete:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Győződjön meg arról, hogy a szolgáltatás elérhető, és IP-címének lekéréséhez:

    ```console
    kubectl get services
    ```

    Ez futó állapotának kimenete a _vélemények_ a POD szolgáltatással:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>További lépések

* Több [Cognitive Services-tárolók](../../cognitive-services-container-support.md)
* Használja a [Szövegelemzés csatlakoztatott szolgáltatás](../vs-text-connected-service.md)