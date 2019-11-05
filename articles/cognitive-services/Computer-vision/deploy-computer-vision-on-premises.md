---
title: Computer Vision tároló használata a Kubernetes és a Helm használatával
titleSuffix: Azure Cognitive Services
description: Telepítse az Computer Vision tárolót egy Azure Container Instanceba, és tesztelje egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8d285bf60e356f15caf55271b0791e9adc97ac14
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481745"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Computer Vision tároló használata a Kubernetes és a Helm használatával

Az Computer Vision tárolók helyszíni kezelésének egyik lehetősége a Kubernetes és a Helm használata. Egy Computer Vision-tároló rendszerképének definiálásához a Kubernetes és a Helm használatával hozzunk létre egy Kubernetes-csomagot. Ez a csomag egy helyszíni Kubernetes-fürtön lesz üzembe helyezve. Végezetül megismerheti az üzembe helyezett szolgáltatások tesztelésének módját. További információ a Docker-tárolók Kubernetes-előkészítés nélküli futtatásáról: [Computer Vision tárolók telepítése és futtatása](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Előfeltételek

Computer Vision tárolók helyszíni használata előtt a következő előfeltételek szükségesek:

|Kötelező|Cél|
|--|--|
| Azure-fiók | Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][free-azure-account] a virtuális gép létrehozásának megkezdése előtt. |
| Kubernetes CLI | A megosztott hitelesítő adatok a tároló-beállításjegyzékből való kezeléséhez a [KUBERNETES CLI][kubernetes-cli] szükséges. A Kubernetes a Helm előtt is szükséges, amely a Kubernetes csomagkezelő. |
| Helm parancssori felület | A [Helm CLI][helm-install] telepítésének részeként el kell végeznie a Helm inicializálását is, amely a [kormányrúdat][tiller-install]fogja telepíteni. |
| Erőforrás Computer Vision |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Computer Vision** erőforrás és a hozzá tartozó API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}** : a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : az **Áttekintés** oldalon megadott végpont|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kapcsolódás a Kubernetes-fürthöz

A gazdaszámítógépnek várhatóan rendelkezésre áll egy Kubernetes-fürt. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy [Kubernetes-fürtöt](../../aks/tutorial-kubernetes-deploy-cluster.md) a Kubernetes-fürtök gazdagépre történő központi telepítésének fogalmi megismeréséhez.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-hitelesítő adatok megosztása a Kubernetes-fürttel

Annak engedélyezéséhez, hogy a Kubernetes-fürt `docker pull` a konfigurált rendszerkép (ek) et a `containerpreview.azurecr.io` Container registryből, át kell vinnie a Docker hitelesítő adatait a fürtbe. Az alábbi [`kubectl create`][kubectl-create] -parancs végrehajtásával hozzon létre egy *Docker-beállításjegyzékbeli titkot* a tároló beállításjegyzék-hozzáférésének előfeltétele alapján megadott hitelesítő adatok alapján.

A választható parancssori felületen futtassa a következő parancsot. A tároló beállításjegyzékbeli hitelesítő adataival ne felejtse el lecserélni a `<username>`, `<password>`és `<email-address>`.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Ha már rendelkezik hozzáféréssel a `containerpreview.azurecr.io` Container registryhez, létrehozhat egy Kubernetes-titkot az általános jelző használatával. Vegye figyelembe a következő parancsot, amely a Docker-konfiguráció JSON-fájlján fut.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A rendszer a következő kimenetet nyomtatja ki a konzolra, amikor a titkos kulcs sikeresen létrejött.

```console
secret "containerpreview" created
```

Annak ellenőrzéséhez, hogy létrejött-e a titkos kulcs, hajtsa végre a [`kubectl get`][kubectl-get] a `secrets` jelzővel.

```console
kuberctl get secrets
```

A `kubectl get secrets` végrehajtásával kinyomtatja az összes beállított titkot.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>A Helm-diagram értékeinek konfigurálása üzembe helyezéshez

Először hozzon létre egy *READ*nevű mappát, majd illessze be az alábbi YAML-tartalmat egy *diagram. YML*nevű új fájlba.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

A Helm diagram alapértelmezett értékeinek konfigurálásához másolja és illessze be a következő YAML egy `values.yaml`nevű fájlba. Cserélje le a `# {ENDPOINT_URI}` és `# {API_KEY}` megjegyzéseket a saját értékeire.

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
> Ha a `billing` és a `apikey` értékek nincsenek megadva, a szolgáltatások 15 perc után lejárnak. Hasonlóképpen, az ellenőrzés sikertelen lesz, mert a szolgáltatások nem lesznek elérhetők.

Hozzon létre egy *templates* mappát az *olvasási* könyvtár alatt. Másolja és illessze be a következő YAML egy `deployment.yaml`nevű fájlba. A `deployment.yaml`-fájl Helm-sablonként fog szolgálni.

> A sablonok jegyzékfájlokat hoznak, amelyek a Kubernetes által értelmezhető YAML formázottak. [– Helm diagram sablonjának útmutatója][chart-template-guide]

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

A sablon megadja a terheléselosztó szolgáltatást és a tároló/rendszerkép üzembe helyezését olvasásra.

### <a name="the-kubernetes-package-helm-chart"></a>A Kubernetes-csomag (Helm-diagram)

A *Helm diagram* tartalmazza azt a konfigurációt, amely alapján a Docker-rendszerképek lehívhatók a `containerpreview.azurecr.io` tároló beállításjegyzékből.

> A [Helm diagram][helm-charts] a Kubernetes-erőforrások kapcsolódó készletét leíró fájlok gyűjteménye. Egy diagramot felhasználhat egy egyszerű, például egy Memcached vagy egy összetett Pod üzembe helyezésére, például egy teljes webalkalmazás-verem használatára HTTP-kiszolgálók, adatbázisok, gyorsítótárak és így tovább.

A megadott *Helm-diagramok* lekérik a Computer Vision szolgáltatás Docker-rendszerképeit és a megfelelő szolgáltatást a `containerpreview.azurecr.io` tároló beállításjegyzékből.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>A Helm diagram telepítése a Kubernetes-fürtön

A *Helm diagram*telepítéséhez végre kell hajtania a [`helm install`][helm-install-cmd] parancsot. Győződjön meg arról, hogy az install parancsot a `read` mappa felett található könyvtárból hajtja végre.

```console
helm install read --name read
```

Az alábbi példa egy olyan kimenetet mutat be, amely sikeres telepítés után várható:

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

A Kubernetes üzembe helyezése több percet is igénybe vehet. A következő parancs végrehajtásával ellenőrizheti, hogy a hüvelyek és a szolgáltatások megfelelően vannak-e telepítve és elérhetők:

```console
kubectl get all
```

A következő kimenethez hasonlónak kell megjelennie:

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

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való telepítésével kapcsolatos további információkért [látogasson el ide][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services tárolók][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
