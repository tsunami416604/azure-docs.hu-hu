---
title: A Computer Vision tároló használata Kubernetes és Helm segítségével
titleSuffix: Azure Cognitive Services
description: Telepítse a Computer Vision tárolót egy Azure Container Instance, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458018"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>A Computer Vision tároló használata Kubernetes és Helm segítségével

A számítógép-látás tárolók helyszíni kezelésére a Kubernetes és a Helm használata az egyik lehetőség. A Kubernetes és a Helm használatával definiálunk egy Computer Vision tárolórendszerképet, létrehozunk egy Kubernetes-csomagot. Ez a csomag a helyszíni Kubernetes-fürtbe lesz telepítve. Végül megvizsgáljuk, hogyan tesztelheti az üzembe helyezett szolgáltatásokat. A Kubernetes vezénylés nélküli Docker-tárolók futtatásáról a [Computer Vision-tárolók telepítése és futtatása](computer-vision-how-to-install-containers.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A számítógép-vision-tárolók helyszíni használata előtt az alábbi előfeltételek:

| Kötelező | Cél |
|----------|---------|
| Azure-fiók | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,][free-azure-account] mielőtt elkezdené. |
| Kubernetes CLI | A [Kubernetes CLI][kubernetes-cli] szükséges a megosztott hitelesítő adatok kezeléséhez a tároló beállításjegyzékből. Kubernetes előtt is szükség van helm, amely a Kubernetes csomagkezelő. |
| Kormányrúd CLI | Telepítse a [Helm CLI-t,][helm-install]amely egy helm diagram (konténercsomag-definíció) telepítésére szolgál. |
| Computer Vision forrás |A tároló használatához a következőkre van szüksége:<br><br>Egy Azure Computer Vision erőforrás és a kapcsolódó API-kulcs a végpont URI.An Azure **Computer Vision** resource and the associated API key the endpoint URI. Mindkét érték elérhető az áttekintés és a kulcsok oldalon az erőforrás, és szükséges a tároló elindításához.<br><br>**{API_KEY}**: A **Kulcsok** lapon elérhető két erőforráskulcs egyike<br><br>**{ENDPOINT_URI}**: Az **Áttekintés** lapon megadott végpont|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Csatlakozás a Kubernetes-fürthöz

A gazdaszámítógépnek rendelkeznie kell egy elérhető Kubernetes-fürttel. Tekintse meg ezt az [oktatóanyagot a Kubernetes-fürt központi telepítéséről,](../../aks/tutorial-kubernetes-deploy-cluster.md) amely ből megtudhatja, hogyan telepíthet kubernetes-fürtöt egy gazdaszámítógépre.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-hitelesítő adatok megosztása a Kubernetes-fürttel

Ahhoz, hogy `docker pull` a Kubernetes-fürt a `containerpreview.azurecr.io` konfigurált rendszerkép(ek) a tároló beállításjegyzékből, át kell vinnie a docker hitelesítő adatokat a fürtbe. Az [`kubectl create`][kubectl-create] alábbi parancs végrehajtásával hozzon létre egy *docker-registry titkos* kulcsot a tároló beállításjegyzék-hozzáférési előfeltétele alapján megadott hitelesítő adatok alapján.

A választott parancssori felületről futtassa a következő parancsot. Ügyeljen arra, `<username>`hogy `<password>`cserélje `<email-address>` le a , és a tároló rendszerleíró hitelesítő adatait.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Ha már rendelkezik `containerpreview.azurecr.io` hozzáféréssel a tároló beállításjegyzékhez, létrehozhat egy Kubernetes-titkos kulcsot az általános jelző használatával. Vegye figyelembe a következő parancsot, amely a Docker-konfiguráció JSON-on hajt végre.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A következő kimenet a konzolra kerül, ha a titkos kulcsot sikeresen létrehozták.

```console
secret "containerpreview" created
```

Annak ellenőrzéséhez, hogy a titkos [`kubectl get`][kubectl-get] kulcsot `secrets` létrehozták-e, hajtsa végre a jelzővel.

```console
kubectl get secrets
```

A nyomtatás `kubectl get secrets` az összes konfigurált titok.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Helm-diagram értékek konfigurálása a telepítéshez

Először hozzon létre egy *olvasott*nevű mappát, majd illessze be a következő YAML-tartalmat egy *Chart.yml*nevű új fájlba.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

A Helm-diagram alapértelmezett értékeinek konfigurálásához másolja és illessze `values.yaml`be a következő YAML-t egy fájlba. Cserélje `# {ENDPOINT_URI}` le `# {API_KEY}` a megjegyzéseket a saját értékeire.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Ha `billing` a `apikey` és az értékek nem állnak rendelkezésre, a szolgáltatások 15 min után lejárnak. Hasonlóképpen az ellenőrzés sikertelen lesz, mivel a szolgáltatások nem lesznek elérhetők.

Hozzon létre egy *sablonmappát* az *olvasási* könyvtár alatt. Másolja és illessze be a következő `deployment.yaml`YAML fájlt egy fájlba. A `deployment.yaml` fájl Helm sablonként fog szolgálni.

> A sablonok jegyzékfájlokat hoznak létre, amelyek a Kubernetes által érthető YAML-formátumú erőforrás-leírások. [- Helm Chart sablon útmutató][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

A sablon meghatározza a terheléselosztó szolgáltatást, és a tároló/lemezkép olvasásra való üzembe helyezése.

### <a name="the-kubernetes-package-helm-chart"></a>A Kubernetes csomag (Helm chart)

A *Helm diagram* tartalmazza a konfigurációt, amely docker `containerpreview.azurecr.io` rendszerkép(ek) lekérése a tároló rendszerleíró adatbázisból.

> A [Helm-diagram][helm-charts] olyan fájlok gyűjteménye, amelyek a Kubernetes-erőforrások kapcsolódó készletét írják le. Egyetlen diagram használható egy egyszerű, például egy memcached pod vagy valami összetett, például egy teljes webalkalmazás-verem http-kiszolgálókkal, adatbázisokkal, gyorsítótárakkal és így tovább.

A megadott *Helm-diagramok* lekéri a docker-rendszerképeket a `containerpreview.azurecr.io` Computer Vision Service, és a megfelelő szolgáltatást a tároló beállításjegyzékből.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>A Helm-diagram telepítése a Kubernetes-fürtre

A *kormányrúd*telepítéséhez végre kell hajtanunk a [`helm install`][helm-install-cmd] parancsot. Győződjön meg arról, hogy `read` a telepítési parancsot a mappa feletti könyvtárból hajtja végre.

```console
helm install read ./read
```

Íme egy példa kimenet, amely et egy sikeres telepítés végrehajtásától vár:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

A Kubernetes-telepítés több percet is igénybe vehet. Annak ellenőrzéséhez, hogy a podok és a szolgáltatások megfelelően vannak-e telepítve és elérhetők-e, hajtsa végre a következő parancsot:

```console
kubectl get all
```

A következő kimenethez hasonló eredményre számíthat:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>További lépések

Az Azure Kubernetes-szolgáltatás (AKS) helmesi alkalmazásaival történő alkalmazások telepítésével kapcsolatos további részletekért [látogasson el ide.][installing-helm-apps-in-aks]

> [!div class="nextstepaction"]
> [Kognitív szolgáltatások tárolói][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
