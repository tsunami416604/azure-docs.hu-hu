---
title: Nyelvi észlelési tároló futtatása a Kubernetes szolgáltatásban
titleSuffix: Text Analytics -  Azure Cognitive Services
description: A nyelvészlelési tárolót egy futó mintával telepítheti az Azure Kubernetes-szolgáltatásba, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399169"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>A Text Analytics nyelvfelismerési tároló üzembe helyezése az Azure Kubernetes szolgáltatásban

További információ a nyelvészlelési tároló üzembe helyezéséről. Ez az eljárás bemutatja, hogyan hozza létre a helyi Docker-tárolókat, hogyan tolja le a tárolókat a saját privát tároló beállításjegyzékébe, futtassa a tárolót egy Kubernetes-fürtben, és tesztelje azt egy webböngészőben.

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás több olyan eszközt igényel, amelyeket helyileg kell telepíteni és futtatni. Ne használja az Azure Cloud rendszerhéjat.

* Azure-előfizetés használata. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* [Git](https://git-scm.com/downloads) az operációs rendszerhez, így klónozhatja az ebben az eljárásban használt [mintát.](https://github.com/Azure-Samples/cognitive-services-containers-samples)
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Docker-motort,](https://www.docker.com/products/docker-engine) és ellenőrizze, hogy a Docker CLI működik-e egy konzolablakban.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Egy Azure-erőforrás a megfelelő tarifacsomaggal. Nem minden tarifacsomag működik ezzel a tárolóval:
  * **Szöveges analytics-erőforrás** csak F0 vagy Standard tarifacsomagokkal.
  * **Cognitive Services** erőforrás az S0 tarifacsomaggal.

## <a name="running-the-sample"></a>A minta futtatása

Ez az eljárás betölti és futtatja a Cognitive Services-tároló minta a nyelvfelismerés. A minta két tárolót tartalmaz, egyet az ügyfélalkalmazáshoz és egyet a Cognitive Services-tárolóhoz. Mindkét lemezképet leküldéses az Azure Container Registry. Miután a saját beállításjegyzékben, hozzon létre egy Azure Kubernetes-szolgáltatás ezek a lemezképek eléréséhez és a tárolók futtatásához. Amikor a tárolók futnak, használja a **kubectl** CLI-t a tárolók teljesítményének figyeléséhez. Az ügyfélalkalmazás http-kérelemmel való elérése és az eredmények megtekintése.

![A mintatárolók futtatásának fogalmi ötlete](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>A mintatárolók

A minta két tárolórendszerkép, az egyik a frontend website. A második kép a szöveg észlelt nyelvét (kulturális környezetét) visszaadó nyelvfelismerő tároló. Mindkét tároló külső IP-címről érhető el, ha végzett.

### <a name="the-language-frontend-container"></a>A nyelv-előtér tároló

Ez a webhely egyenértékű a saját ügyféloldali alkalmazás, amely kéri a nyelvfelismerési végpont. Amikor az eljárás befejeződött, egy karaktersorozat észlelt nyelvét kapja meg a webhelytároló val rendelkező böngészőben való elérésével. `http://<external-IP>/<text-to-analyze>` Erre az URL-címre példa a `http://132.12.23.255/helloworld!`. Az eredmény a `English`böngészőben .

### <a name="the-language-container"></a>A nyelvi tároló

A nyelvfelismerési tároló ebben a konkrét eljárásban minden külső kérés számára elérhető. A tároló semmilyen módon nem módosult, így a standard Cognitive Services-tároló-specifikus nyelvészlelési API érhető el.

Ehhez a tárolóhoz az API egy POST-kérelem a nyelvfelismeréshez. Mint minden Cognitive Services-tárolók, a tároló ról a tárolt `http://<external-IP>:5000/swagger/index.html`Swagger-információk, .

Az 5000-es port a Cognitive Services-tárolók alapértelmezett portja.

## <a name="create-azure-container-registry-service"></a>Azure Container Registry szolgáltatás létrehozása

A tároló üzembe helyezéséhez az Azure Kubernetes szolgáltatás, a tárolórendszerképek elérhetőnek kell lennie. Hozzon létre saját Azure Container Registry szolgáltatás a rendszerképek üzemeltetéséhez.

1. Bejelentkezés az Azure CLI-be

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre `cogserv-container-rg` egy nevű erőforráscsoportot az eljárás ban létrehozott összes erőforrás tárolására.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Hozzon létre saját Azure Container Registry `registry`a formátuma a nevét, majd, például. `pattyregistry` Ne használjon kötőjeleket vagy aláhúzáskaraktereket a névben.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Mentse az eredményeket a **loginServer** tulajdonság lehívásához. Ez a tárolt tároló címének része lesz, `language.yml` amelyet a fájl későbbi részében használnak.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
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

1. Jelentkezzen be a tároló beállításjegyzékébe. Be kell jelentkeznie, mielőtt képeket a rendszerleíró adatbázisba.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Webhely Docker-képének beszereznie

1. Az ebben az eljárásban használt mintakód a Cognitive Services-tárolók mintatárban található. Klónozza a tárházat, hogy a minta helyi másolata legyen.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Miután a tárház a helyi számítógépen van, keresse meg a webhelyet a [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárban. Ez a webhely működik, mint az ügyfél alkalmazás hívja meg a nyelvészlelési API-t a nyelvészlelési tárolóban üzemeltetett.  

1. A Docker-rendszerkép létrehozása ehhez a webhelyhez. Győződjön meg arról, hogy a konzol a [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárban található, ahol a Docker-fájl található, amikor futtatja a következő parancsot:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    A tárolóbeállítás-jegyzékben a verzió számának nyomon követéséhez `v1`adja hozzá a címkét egy verzióformátummal, például .

1. A rendszerkép leküldése a tároló beállításjegyzékébe. Ez eltarthat néhány percig.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Ha hibaüzenetet `unauthorized: authentication required` kap, jelentkezzen `az acr login --name <your-container-registry-name>` be a paranccsal. 

    Amikor a folyamat befejeződött, az eredményeknek hasonlónak kell lenniük:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Nyelvfelismerési Docker-rendszerkép beszereznie

1. A Docker-lemezkép legújabb verziójának lekérése a helyi számítógépre. Ez eltarthat néhány percig. Ha a tárolónak van egy újabb verziója, `1.1.006770001-amd64-preview` módosítsa az értéket az újabb verzióra.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Címke kép a tároló rendszerleíró adatbázis. Keresse meg a legújabb `1.1.006770001-amd64-preview` verziót, és cserélje le a verziót, ha van egy újabb verzió. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. A rendszerkép leküldése a tároló beállításjegyzékébe. Ez eltarthat néhány percig.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Tárolójegyzék hitelesítő adatainak beszereznie

A következő lépések szükségesek a szükséges információkat a tároló beállításjegyzék csatlakoztatásához az Azure Kubernetes szolgáltatás hoz létre az eljárás későbbi részében.

1. Egyszerű szolgáltatás létrehozása.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    `appId` A `<appId>`3. Mentse `password` a következő szakasz ügyféltitkos paraméteréhez. `<client-secret>`

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. A tároló beállításazonosítójának beszerezése.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Mentse a hatókör paraméterértékének `<acrId>`kimenetét a következő lépésben. Úgy néz ki, mint:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Ebben a szakaszban a 3.

1. Ha azt szeretné, hogy az AKS-fürt megfelelő hozzáférést biztosítson a tárolóbeállításjegyzékében tárolt lemezképek használatához, hozzon létre egy szerepkör-hozzárendelést. Cserélje `<appId>` `<acrId>` ki és az előző két lépésben összegyűjtött értékeket.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes-szolgáltatás létrehozása

1. Hozza létre a Kubernetes-fürtöt. Az összes paraméterérték az előző szakaszokból származik, kivéve a name paramétert. Válasszon egy nevet, amely jelzi, hogy `patty-kube`ki hozta létre, és annak célja, például .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Ez a lépés eltarthat néhány percig. Az eredmény:

    ```output
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

    A szolgáltatás létrejön, de még nem rendelkezik a webhely-tárolóval vagy a nyelvészlelési tárolóval.  

1. A Kubernetes-fürt hitelesítő adatainak beszereznie.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>A vezénylési definíció betöltése a Kubernetes szolgáltatásba

Ez a szakasz a **kubectl** CLI segítségével beszél az Azure Kubernetes szolgáltatás.

1. A vezénylési definíció betöltése előtt ellenőrizze **kubectl** hozzáfér a csomópontokhoz.

    ```console
    kubectl get nodes
    ```

    A válasz így néz ki:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Másolja a következő fájlt, és nevezze el `language.yml`. A fájl `service` rendelkezik `deployment` egy szakaszt és egy-egy szakaszt a két tárolótípushoz, a `language-frontend` webhelytárolóhoz és az `language` észlelési tárolóhoz.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Módosítsa a nyelvi-előtér-telepítési sorok at az alábbi táblázat `language.yml` alapján, hogy saját tároló beállításjegyzék-lemezképneveket, ügyféltitkos kulcsokat és szövegelemzési beállításokat adjon hozzá.

    Nyelvi előtér-telepítési beállítások|Cél|
    |--|--|
    |32. sor<br> `image`Tulajdonság|Az előtér-lemezkép lemezképe a tárolóbeállítás-jegyzékben<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |44. sor<br> `name`Tulajdonság|A rendszerkép tárolóbeállítási titka, az előző `<client-secret>` szakaszban.|

1. Módosítsa a nyelvi `language.yml` telepítési sorok alapján az alábbi táblázatadja meg a saját tároló beállításjegyzék-lemezkép nevek, ügyféltitkos kulcsok és szöveges elemzési beállításokat.

    |Nyelvi telepítési beállítások|Cél|
    |--|--|
    |78. sor<br> `image`Tulajdonság|A nyelvi lemezkép helye a tárolóbeállítási adatbázisban<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |95. sor<br> `name`Tulajdonság|A rendszerkép tárolóbeállítási titka, az előző `<client-secret>` szakaszban.|
    |91. sor<br> `apiKey`Tulajdonság|A szövegelemzési erőforráskulcs|
    |92. sor<br> `billing`Tulajdonság|A szöveges elemzési erőforrás számlázási végpontja.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Mivel az **apiKey** és a **számlázási végpont** a Kubernetes vezénylési definíciójának részeként van beállítva, a webhelytárolónak nem kell tudnia ezekről, és nem kell átadnia őket a kérés részeként. A webhelytároló az orchestrator neve alapján hivatkozik a nyelvfelismerési tárolóra. `language`

1. A mintához készült vezénylési definíciós `language.yml`fájl betöltése abból a mappából, amelybe létrehozta és mentette a.

    ```console
    kubectl apply -f language.yml
    ```

    A válasz:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Tárolók külső IP-jének beszereznie

A két tároló esetében `language-frontend` `language` ellenőrizze, hogy fut-e a szolgáltatások és a szolgáltatások, és lekéri a külső IP-címet.

```console
kubectl get all
```

```output
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

Ha `EXTERNAL-IP` a szolgáltatás függőben lévőként jelenik meg, futtassa újra a parancsot, amíg az IP-cím meg nem jelenik, mielőtt a következő lépésre lépne.

## <a name="test-the-language-detection-container"></a>A nyelvfelismerő tároló tesztelése

Nyisson meg egy böngészőt, `language` és keresse meg `http://<external-ip>:5000/swagger/index.html`a tároló külső IP-címét az előző szakaszból: . Az API `Try it` szolgáltatás segítségével tesztelheti a nyelvészlelési végpontot.

![A tároló swagger dokumentációjának megtekintése](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Az ügyfélalkalmazás-tároló tesztelése

Módosítsa a böngészőben lévő URL-címet `language-frontend` a tároló külső `http://<external-ip>/helloworld`IP-címére a következő formátumban: . Az angol kultúra `helloworld` szövege `English`az előrejelzések szerint .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a fürt, törölje az Azure erőforráscsoport.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Kapcsolódó információk

* [kubectl docker-felhasználóknak](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kognitív szolgáltatások tárolói](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->