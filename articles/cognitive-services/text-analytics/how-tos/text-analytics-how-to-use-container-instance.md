---
title: Kubernetes-szolgáltatás futtatásához
titleSuffix: Text Analytics -  Azure Cognitive Services
description: A nyelv észlelése tároló, egy futó minta segítségével üzembe az Azure Kubernetes Service-ben, és tesztelje a szolgáltatást egy webböngészőben.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 19ac1819c3b098597819ec75adcedeca929e802d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464544"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>A nyelv észlelése tároló üzembe Azure Kubernetes Service-ben

Megtudhatja, hogyan helyezze üzembe a nyelv észlelése tárolót. Ez az eljárás bemutatja, hogyan helyi Docker-tárolót létrehozni, a tárolók leküldése a saját privát tárolóregisztrációs adatbázis, a tároló futtatása a Kubernetes-fürt és tesztelje a szolgáltatást egy webböngészőben. 

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás több eszközt, hogy telepítve legyen, és helyi futtatása szükséges. Ne használja az Azure Cloud shellben. 

* Használja az Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [A Git](https://git-scm.com/downloads) , az operációs rendszernek, így klónozhat a [minta](https://github.com/Azure-Samples/cognitive-services-containers-samples) ebben az eljárásban használt. 
* [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
* [Docker-motor](https://www.docker.com/products/docker-engine) , és ellenőrizze, hogy működik-e a Docker parancssori felületén a konzolablakban.
* [a kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe). 
* Egy Azure-erőforrás a megfelelő tarifacsomagot. Nem minden tarifacsomag használata ebben a tárolóban:
    * **Szövegelemzés** erőforrás F0 vagy Standard díjszabás csak szint esetében.
    * **A cognitive Services** tarifacsomag az S0 erőforrás.

## <a name="running-the-sample"></a>A minta futtatása

Ez az eljárás betölt, és a Cognitive Services-tároló minta nyelvfelismerés futtat. A minta két tárolót, egy, az ügyfélalkalmazás számára, és egy, a Cognitive Services-tároló rendelkezik. Mindkét ezek a lemezképek leküldése a saját Azure Container Registry kell. Saját beállításjegyzék k, hozzon létre egy Azure Kubernetes Service ezeket a lemezképeket eléréséhez, és a tárolókat futtatják. A tárolók futnak, amikor a **kubectl** CLI tekintse meg a tárolók teljesítménye. Az ügyfélalkalmazás egy HTTP-kérelem a hozzáférést, és az eredmények megtekintéséhez. 

![Futó tárolók minta fogalmi képet](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>A minta-tárolók

A mintának két tárolórendszerképek, egyet az előtér-webhely van. A második kép, a nyelv észlelése tároló visszaadása a felismert nyelv (kulturális környezet) szöveget. Mindkét tárolók érhetők el egy külső IP-cím, amikor elkészült. 

### <a name="the-language-frontend-container"></a>A nyelvi-előtér-tároló

Ezen a webhelyen megegyezik a saját ügyféloldali alkalmazás, amely lehetővé teszi a kérelmek, a nyelv észlelési végpontja. Az eljárás befejezésekor a felismert nyelv karakterből álló karakterlánc kap a böngészőben a webhelyet tároló elérésével `http://<external-IP>/<text-to-analyze>`. Az URL-cím például `http://132.12.23.255/helloworld!`. Az eredmény a böngészőben `English`.

### <a name="the-language-container"></a>A nyelvi tároló

Nyelv észlelése a tárolót, az adott eljárás keretében bármilyen külső kérelmet érhető el. A tároló nem változott semmilyen módon, a standard szintű Cognitive Services-tároló-specifikus nyelv észlelése API érhető el. 

A tároló API nyelvfelismerés POST kérelem érkezik. Mivel az összes Cognitive Services-tárolót, további információ az üzemeltetett Swagger-adatokból a tároló `http://<external-IP>:5000/swagger/index.html`. 

5000-es porton a Cognitive Services-tárolók használt alapértelmezett port. 

## <a name="create-azure-container-registry-service"></a>Azure Container Registry szolgáltatás létrehozása

A tároló üzembe helyezése az Azure Kubernetes Service, a tárolórendszerképeket kell elérhetőnek lennie. Hozzon létre a saját Azure Container Registry szolgáltatásba, a képek tárolására. 

1. Jelentkezzen be az Azure CLI 

    ```azurecli
    az login
    ```

1. Hozzon létre egy erőforráscsoportot `cogserv-container-rg` ebben az eljárásban létrehozott minden erőforrás tárolásához.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. A saját Azure Container Registry létrehozása a következő formátumban nevének majd `registry`, mint például `pattyregistry`. Ne használjon kötőjeleket vagy karaktereket a nevében aláhúzás.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    A get-eredményeket menteni a **adatbázis bejelentkezési kiszolgálójának nevével** tulajdonság. Ez lesz szükség, az üzemeltetett tárolók cím része az `language.yml` fájlt.

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. Jelentkezzen be a tárolóregisztrációs adatbázisba. Jelentkezzen be a rendszerképek leküldése a beállításjegyzékbe előtt kell.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Webhely Docker-lemezkép beolvasása 

1. Az ebben a folyamatban használt mintakódot a Cognitive Services-tárolók mintaadattárban szerepel. A helyi másolatot készíteni a minta tárház klónozásához.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Ha a tárház a helyi számítógépen, keresse meg a webhely a [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtár. Ezen a webhelyen az ügyfélalkalmazás, a nyelv észlelése a nyelv észlelése tárolóban lévő üzemeltetett API hívása funkcionál.  

1. Ezen a webhelyen a Docker-rendszerkép létrehozásához. Ellenőrizze, hogy a konzol nem található a [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) könyvtárat, ahol a docker-fájlban található a következő parancs futtatásakor:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 . 
    ```

    Nyomon követheti a verziót a tároló-beállításjegyzék, a címke hozzáadása az verzió formátumú, például `v1`. 

1. Küldje le a rendszerképet a tárolóregisztrációs adatbázisba. Ez eltarthat néhány percig. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Ha egy `unauthorized: authentication required` hiba, jelentkezzen be a `az acr login --name <your-container-registry-name>` parancsot. 

    Ha a folyamat befejeződik, az eredményeket kell kinéznie:

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

## <a name="get-language-detection-docker-image"></a>Nyelv észlelése Docker-rendszerképének lekérése 

1. Kérje le a Docker-rendszerkép legújabb verzióját a helyi gépen. Ez eltarthat néhány percig. Ha ez a tároló egy újabb verziója van, módosítsa az értéket `1.1.006770001-amd64-preview` az újabb verzióra. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Címke rendszerképet a tárolóregisztrációs adatbázisba. Keresse meg a legújabb verzióra, és cserélje le a verzió `1.1.006770001-amd64-preview` egy újabb verziójával rendelkezik. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Küldje le a rendszerképet a tárolóregisztrációs adatbázisba. Ez eltarthat néhány percig. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Container Registry hitelesítő adatainak lekérése

Az alábbi lépéseket kell tenni az a szükséges adatokat a hoz létre, az eljárás későbbi szakaszában a tárolóregisztrációs adatbázis összekapcsolása az Azure Kubernetes Service-ben.

1. Egyszerű szolgáltatás létrehozása.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Az eredmények mentése `appId` 3,. lépésében assignee paraméter értéke `<appId>`. Mentse a `password` client-secret paraméterhez a következő szakaszban `<client-secret>`.

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

1. A container registry azonosító lekérése

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    A hatókör-paraméter értéke kimenetét mentse `<acrId>`, a következő lépésben. Hasonlóan néz ki:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Mentse a 3. lépés teljes értéke ebben a szakaszban. 

1. Az AKS-fürtöt a tárolóregisztrációs adatbázis tárolt rendszerképek használatához megfelelő hozzáférést, a szerepkör-hozzárendelés létrehozása. Cserélje le <appId> és <acrId> az előző két lépést az összegyűjtött értékekkel.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Az Azure Kubernetes-szolgáltatás létrehozása

1. Hozza létre a Kubernetes-fürtöt. Összes paraméter értékét a korábbi szakaszokban, kivéve a name paraméter származnak. Válasszon egy nevet, amely azt jelzi, hogy ki hozta létre, és az egyéb célra felhasználja, mint például `patty-kube`. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Ez a lépés több percet is igénybe vehet. Az eredmény a következő: 

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

    A szolgáltatás létrehozása, de ez még nem rendelkezik a webhelyet tároló vagy nyelv észlelése tároló.  

1. A Kubernetes-fürt hitelesítő adatainak lekérése. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>A Kubernetes üzembe vezénylési definíciójának betöltése

Ez a szakasz a **kubectl** szeretne beszélni az Azure Kubernetes Service-ben a CLI. 

1. Az orchestration-definíció betöltése, előtt ellenőrizze az **kubectl** férhet hozzá a csomópontokat.

    ```console
    kubectl get nodes
    ```

    A válasz a következőhöz hasonló:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Másolja a következő fájlt, és adja neki `language.yml`. A fájl rendelkezik egy `service` szakaszban és a egy `deployment` minden, a két tárolót típusokhoz szakasz a `language-frontend` webhelyet tároló és a `language` észlelési tároló. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Módosítsa a nyelvi-frontend telepítési sornyi `language.yml` saját beállításjegyzék tárolólemezkép-nevek, a titkos Ügyfélkód és a text analytics beállítások hozzáadása az alábbi táblázat alapján.

    Nyelvi-frontend központi telepítési beállítások|Cél|
    |--|--|
    |Sor 32<br> `image` A tulajdonság|Lemezkép helyét a előtéri rendszerképet a Tárolóregisztrációs adatbázis<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Sor 44<br> `name` A tulajdonság|A lemezképet tároló beállításjegyzék titkos kulcsát néven `<client-secret>` az egyik előző szakaszban.|

1. Módosítsa a nyelvi telepítési sornyi `language.yml` saját beállításjegyzék tárolólemezkép-nevek, a titkos Ügyfélkód és a text analytics beállítások hozzáadása az alábbi táblázat alapján.

    |Nyelvi központi telepítési beállítások|Cél|
    |--|--|
    |Sor 78<br> `image` A tulajdonság|Lemezkép helyét a nyelvi rendszerképet a Tárolóregisztrációs adatbázis<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Sor 95<br> `name` A tulajdonság|A lemezképet tároló beállításjegyzék titkos kulcsát néven `<client-secret>` az egyik előző szakaszban.|
    |Sor 91<br> `apiKey` A tulajdonság|A text analytics-erőforrás kulcsa|
    |Sor 92<br> `billing` A tulajdonság|A text analytics-erőforrás számlázási végpontját.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

    Mivel a **apikey tulajdonsággal végzett tesztelése** és **végpont számlázási** vannak beállítva a Kubernetes vezénylési definíciójának részeként a webhelyet tároló nem szükséges tudni ezeket, vagy adja meg azokat a kérés részeként. Az orchestrator név szerint a nyelv észlelése tároló hivatkozik a webhelyet tároló `language`. 

1. Ez a minta az orchestration-definíciós fájljának betölthető a mappát, ahol létrehozott és mentett a `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    A válasz a következő:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Külső IP-címek, a tárolók beolvasása

A két tárolót, ellenőrizze a `language-frontend` és `language` szolgáltatások futnak, és a külső IP-címének lekéréséhez. 

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

Ha a `EXTERNAL-IP` vonatkozó, függőben lévő, futtassa a parancsot újból látható a szolgáltatás, mindaddig, amíg az IP-cím jelenik meg a következő lépéssel áthelyezése előtt. 

## <a name="test-the-language-detection-container"></a>A nyelv észlelése tároló tesztelése

Nyisson meg egy böngészőt, és keresse meg a külső IP-címét a `language` az előző szakaszban tároló: `http://<external-ip>:5000/swagger/index.html`. Használhatja a `Try it` funkció az API tesztelése a nyelvi észlelési végpontja. 

![A tároló swagger-dokumentáció megtekintése](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Az ügyfél alkalmazástárolók tesztelése

Módosítsa az URL-CÍMÉT a böngészőben a külső IP-címét a `language-frontend` tároló, a következő formátumban: `http://<external-ip>/helloworld`. Az angol nyelvű szövege culture `helloworld` összegyűjtése várható `English`.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, a fürttel, törölje az Azure-erőforráscsoportot. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Kapcsolódó információk

* [a kubectl a Docker-felhasználók számára](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>További lépések 

* Több [Cognitive Services-tárolók](../../cognitive-services-container-support.md)
* A csatlakoztatott szolgáltatás használata a Text Analytics] (.. / vs-szöveg-csatlakoztatott-service.md)


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->