---
title: Kubernetes szolgáltatás futtatása – Text Analytics
titleSuffix: Azure Cognitive Services
description: Üzembe helyezheti a nyelvfelismerés tárolót egy futó mintával az Azure Kubernetes szolgáltatásban, és tesztelheti egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 7b6621273b04d019791f604ca0db26a73c289f2b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562614"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Nyelvfelismerés-tároló üzembe helyezése az Azure Kubernetes Service-ben

Megtudhatja, hogyan helyezheti üzembe a nyelvfelismerés-tárolót. Ez az eljárás bemutatja, hogyan hozhat létre helyi Docker-tárolókat, leküldheti a tárolókat a saját privát tároló-beállításjegyzékbe, futtathatja a tárolót egy Kubernetes-fürtben, és tesztelheti egy böngészőben. A tárolók használatával átirányíthatja a figyelmet az infrastruktúra kezelése helyett, hogy az alkalmazások fejlesztésére összpontosítsanak.

## <a name="prerequisites"></a>Előfeltételek

Ennek az eljárásnak számos olyan eszközre van szüksége, amelyet helyileg kell telepíteni és futtatni. Ne használja a Azure Cloud Shell. A következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Git](https://git-scm.com/downloads) az operációs rendszerhez, így az eljárásban használt [minta](https://github.com/Azure-Samples/cognitive-services-containers-samples) klónozása is megtörténik.
* A [az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Egy [Docker](https://www.docker.com/products/docker-engine)-motort, és ellenőrizze, hogy a Docker CLI működik-e a konzol ablakban.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Egy megfelelő árképzési szintű Azure-erőforrás. Nem minden díjszabási csomag működik ezzel a tárolóval:
    * Az **Azure Text Analytics** erőforrás csak a F0 vagy a standard szintű árképzési szinttel rendelkezik.
    * **Azure Cognitive Services** erőforrás a S0 díjszabási szintjével.

## <a name="run-the-sample"></a>Minta futtatása

Ez az eljárás betölti és futtatja a Cognitive Services tároló mintát a nyelvfelismerés számára. A mintának két tárolója van, egyet az ügyfélalkalmazás és egyet a Cognitive Services tárolóhoz. Mindkét lemezképet a Azure Container Registry saját példányára kell leküldeni. Miután saját beállításjegyzékbe kerültek, hozzon létre egy Azure Kubernetes Service-példányt (ak) a lemezképek eléréséhez és a tárolók futtatásához. Ha a tárolók futnak, a **kubectl** CLI használatával tekintheti meg a tárolók teljesítményét. Nyissa meg az ügyfélalkalmazás HTTP-kérelemmel, és tekintse meg az eredményeket.

![A minta tárolók futtatásának fogalmi ötlete](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>A minta tárolók

A mintának két tároló képe van. Az egyik az előtér-webhely webhelye. A második rendszerkép a nyelvfelismerés tároló, amely a szöveg észlelt nyelvét (kultúráját) adja vissza. Ha elkészült, mindkét tároló elérhető külső IP-címről.

### <a name="the-language-front-end-container"></a>A nyelv előtér-tárolója

Ez a webhely egyenértékű a saját ügyféloldali alkalmazásával, amely a nyelvfelismerés-végpontra vonatkozó kéréseket tesz lehetővé. Ha az eljárás elkészült, egy karakterlánc felderített nyelvét kapja meg egy böngészőben `http://<external-IP>/<text-to-analyze>`a alkalmazásban a webhely tárolóban. Példa erre az URL-címre `http://132.12.23.255/helloworld!`. Az eredmény a böngészőben `English`.

### <a name="the-language-container"></a>A nyelvi tároló

Ebben a konkrét eljárásban a nyelvfelismerés tároló bármely külső kérelem számára elérhető. A tároló semmilyen módon nem változik, így a szabványos Cognitive Services a Container-specifikus nyelvi észlelési API érhető el.

Ebben a tárolóban ez az API egy, a nyelvfelismerés utáni kérelem. Ahogy az összes Cognitive Services tárolóhoz hasonlóan, további információkat tudhat meg a tárolóról a kihelyezett `http://<external-IP>:5000/swagger/index.html`hencegő adatairól.

Az 5000-es port a Cognitive Services tárolók által használt alapértelmezett port.

## <a name="create-an-azure-container-registry-service"></a>Azure Container Registry szolgáltatás létrehozása

A tárolónak az Azure Kubernetes szolgáltatásban való üzembe helyezéséhez a tároló lemezképének elérhetőnek kell lennie. Hozzon létre saját Azure Container Registry szolgáltatást a lemezképek üzemeltetéséhez.

1. Jelentkezzen be az Azure CLI-be.

    ```azurecli
    az login
    ```

1. Hozzon létre egy nevű `cogserv-container-rg` erőforráscsoportot az ebben az eljárásban létrehozott összes erőforrás tárolásához.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Hozza létre saját Azure Container Registry saját példányát, és adja meg a nevét, `registry`amelyet a neve követ. Például: `pattyregistry`. Ne használjon kötőjelet vagy aláhúzás karaktert a névben.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Mentse az eredményeket a **lekéréséhez** tulajdonság beszerzéséhez. Ez a tulajdonság a tárolt tároló címe, amely a `language.yml` fájl későbbi részében használatos.

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Jelentkezzen be a tároló-beállításjegyzékbe. A rendszerképek beállításjegyzékbe való leküldéséhez be kell jelentkeznie.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-the-website-docker-image"></a>A webhely Docker-rendszerképének beszerzése

1. Az eljárásban használt mintakód a Cognitive Services containers Samples repositoryban található. A tárház klónozásával a minta helyi másolatát is elvégezheti.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Miután a tárház a helyi számítógépen található, keresse meg a webhelyet a [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárban. Ez a webhely olyan ügyfélalkalmazás, amely meghívja a nyelvfelismerés-tárolóban üzemeltetett nyelvi észlelési API-t. 

1. Hozzon létre egy Docker-rendszerképet ehhez a webhelyhez. Győződjön meg arról, hogy a konzol abban a [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárban van, ahol a Docker-fájl található, amikor a következő parancsot futtatja:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    A tároló-beállításjegyzék verziójának nyomon követéséhez adja hozzá a címkét a verzió formátumához, `v1`például:. 

1. Küldje le a rendszerképet a tároló-beállításjegyzékbe. Ez a lépés néhány percet is igénybe vehet. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Ha `unauthorized: authentication required` hibaüzenetet kap, jelentkezzen be a `az acr login --name <your-container-registry-name>` paranccsal. 

    A folyamat végeztével az eredmények a következőhöz hasonlóak:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-the-language-detection-docker-image"></a>A nyelvfelismerés Docker-rendszerképének beolvasása 

1. A Docker-rendszerkép legújabb verziójának lekérése a helyi gépre. Ez a lépés néhány percet is igénybe vehet. Ha a tároló újabb verziója van, módosítsa az értéket `1.1.006770001-amd64-preview` az újabb verzióra. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Címkézze fel a rendszerképet a tároló beállításjegyzékével. Keresse meg a legújabb verziót, és cserélje le `1.1.006770001-amd64-preview` a verziót, ha újabb verzióval rendelkezik. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Küldje le a rendszerképet a tároló-beállításjegyzékbe. Ez a lépés néhány percet is igénybe vehet. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Container Registry hitelesítő adatok beolvasása

A következő lépések szükségesek ahhoz, hogy a tároló-beállításjegyzéket az eljárás későbbi részében létrehozott Azure Kubernetes Service-példányhoz lehessen kapcsolni a szükséges információk beszerzéséhez.

1. Egyszerű szolgáltatásnév létrehozása.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Mentse a befoglaló paraméter eredmény `appId` értékét a `<appId>`3. lépésben. Mentse a következő szakasz Client-Secret paraméterének `<client-secret>`jelszavát.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Szerezze be a tároló beállításjegyzék-AZONOSÍTÓját.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Mentse a hatókör-paraméter értékének `<acrId>`kimenetét a következő lépésben. A következőképpen néz ki:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Mentse a szakasz 3. lépésének teljes értékét. 

1. Hozzon létre egy szerepkör-hozzárendelést ahhoz, hogy megfelelő hozzáférést biztosítson az AK-fürt számára a tároló beállításjegyzékében tárolt rendszerképek használatához. Cserélje `<appId>` le `<acrId>` az és az értéket az előző két lépésben összegyűjtött értékekre.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-the-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

1. Hozza létre a Kubernetes-fürtöt. Az összes paraméter értéke az előző szakaszban szerepel, kivéve a name paramétert. Válasszon egy nevet, amely azt jelzi, hogy ki hozta létre, és `patty-kube`a célja, például:. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Ez a lépés néhány percet is igénybe vehet. Az eredmény a következőket eredményezi: 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    A szolgáltatás létrejött, de még nem rendelkezik a webhely tárolóval vagy a nyelvfelismerés tárolóval. 

1. A Kubernetes-fürt hitelesítő adatainak beolvasása. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>A Kubernetes szolgáltatásba betöltheti a hangszerelési definíciót

Ez a szakasz a **kubectl** CLI használatával kommunikál az Azure Kubernetes szolgáltatás példányával. 

1. Mielőtt betölti a hangelőkészítési definíciót, ellenőrizze, hogy a **kubectl** rendelkezik-e hozzáféréssel a csomópontokhoz.

    ```console
    kubectl get nodes
    ```

    A válasz a következőre hasonlít:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Másolja a következő fájlt, és nevezze el `language.yml`. A fájl tartalmaz egy `service` szakaszt és egy `deployment` szakaszt a két típusú tárolóhoz, a `language-frontend` webhely-tárolóhoz és `language` az észlelési tárolóhoz. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. A következő táblázat `language.yml` alapján módosítsa az előtér-telepítési sorokat, és adja hozzá a saját tároló beállításjegyzék-rendszerképének neveit, az ügyfél titkos kulcsát és a Text Analytics beállításait.

    Nyelvi előtér-telepítési beállítások|Cél|
    |--|--|
    |32. sor<br> `image`tulajdonság|Rendszerkép helye a tároló beállításjegyzékének előtér-rendszerképéhez<br>`<container-registry-name>.azurecr.io/language-frontend:v1`.|
    |44. sor<br> `name`tulajdonság|A rendszerképhez tartozó tároló beállításjegyzékbeli titka, `<client-secret>` amelyet egy korábbi szakaszban is említett.|

1. Módosítsa a nyelvi telepítési sorokat `language.yml` a következő táblázat alapján, és adja hozzá a saját tároló beállításjegyzék-rendszerképének nevét, az ügyfél titkos kulcsát és a Text Analytics beállításait.

    |Nyelvi telepítési beállítások|Cél|
    |--|--|
    |78. sor<br> `image`tulajdonság|Rendszerkép helye a tároló beállításjegyzékében található nyelvi képhez<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`.|
    |95. sor<br> `name`tulajdonság|A rendszerképhez tartozó tároló beállításjegyzékbeli titka, `<client-secret>` amelyet egy korábbi szakaszban is említett.|
    |91. sor<br> `apiKey`tulajdonság|Az Text Analytics-erőforrás kulcsa.|
    |92. sor<br> `billing`tulajdonság|A Text Analytics erőforrás számlázási végpontja.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`.|

    Mivel a **apiKey** és a **Számlázási végpont** tulajdonságai a Kubernetes-előkészítési definíció részeként vannak beállítva, a webhely-tárolónak nem kell tudnia ezeket, vagy át kell adnia őket a kérelem részeként. A webhely-tároló a Orchestrator neve `language`alapján a nyelvfelismerés-tárolóra hivatkozik. 

1. Töltse be a minta előkészítési definíciós fájlját abban a mappában, ahová a fájlt létrehozta és mentette `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    A válasz:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Külső IP-címek beolvasása tárolók számára

A két tároló esetében ellenőrizze, hogy a `language-frontend` és `language` a szolgáltatások futnak-e, és hogy a külső IP-címet kéri-e. 

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

`EXTERNAL-IP` Ha a szolgáltatás a függőben állapotú, futtassa újra a parancsot, amíg az IP-cím meg nem jelenik a következő lépésre való áttérés előtt. 

## <a name="test-the-language-detection-container"></a>Nyelvfelismerés-tároló tesztelése

Nyisson meg egy böngészőt, és lépjen a `language` tároló külső IP-címére az előző szakaszban:. `http://<external-ip>:5000/swagger/index.html` Az API `Try it` funkciójának használatával tesztelheti a nyelvfelismerés végpontját. 

![A tároló felvágási dokumentációjának megtekintése](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Az ügyfélalkalmazás tárolójának tesztelése

Módosítsa a böngészőben található URL-címet a `language-frontend` tároló külső IP-címére a következő formátum használatával:. `http://<external-ip>/helloworld` Az angol kulturális szöveg a `helloworld` következőképpen `English`van megjósolva:.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a fürttel, törölje az Azure-erőforráscsoportot. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Kapcsolódó információk

* [kubectl a Docker-felhasználók számára](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>További lépések 

* Használjon további [Cognitive Services tárolókat](../../cognitive-services-container-support.md).
* Használja a [text Analytics csatlakoztatott szolgáltatásokat](../vs-text-connected-service.md).


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->