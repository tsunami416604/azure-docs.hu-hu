---
title: Beszédszolgáltatás-tárolók használata Kubernetes és Helm használatával
titleSuffix: Azure Cognitive Services
description: A Kubernetes és a Helm használatával definiáljuk a szöveggé és a szövegfelolvasó tárolórendszerképeket, létrehozunk egy Kubernetes-csomagot. Ez a csomag a helyszíni Kubernetes-fürtbe lesz telepítve.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 6ad5a843c8cc287834305e09b48cd3fafe09ca51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474764"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Beszédszolgáltatás-tárolók használata Kubernetes és Helm használatával

A beszédtárolók helyszíni kezelésére a Kubernetes és a Helm használata az egyik lehetőség. A Kubernetes és a Helm használatával definiáljuk a szöveggé és a szövegfelolvasó tárolórendszerképeket, létrehozunk egy Kubernetes-csomagot. Ez a csomag a helyszíni Kubernetes-fürtbe lesz telepítve. Végül bemutatjuk, hogyan tesztelheti az üzembe helyezett szolgáltatásokat és a különböző konfigurációs lehetőségeket. A Docker-tárolók Kubernetes vezénylés nélküli futtatásáról további információt a [Beszédfelismerési szolgáltatástárolók telepítése és futtatása](speech-container-howto.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek a beszédtárolók használata előtt a helyszínen:

| Kötelező | Cél |
|----------|---------|
| Azure-fiók | Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,][free-azure-account] mielőtt elkezdené. |
| Tárolóbeállítás-bejegyzés hez való hozzáférés | Annak érdekében, hogy a Kubernetes lekéri a docker-rendszerképeket a fürtbe, hozzá kell férnem a tároló beállításjegyzékéhez. |
| Kubernetes CLI | A [Kubernetes CLI][kubernetes-cli] szükséges a megosztott hitelesítő adatok kezeléséhez a tároló beállításjegyzékből. Kubernetes előtt is szükség van helm, amely a Kubernetes csomagkezelő. |
| Kormányrúd CLI | Telepítse a [Helm CLI-t,][helm-install]amely egy helm diagram (konténercsomag-definíció) telepítésére szolgál. |
|Beszéd forrás |A tárolók használatához a következőkre van szüksége:<br><br>A _beszédbeli_ Azure-erőforrás a társított számlázási kulcs és a számlázási végpont URI beolvasásához. Mindkét érték elérhető az Azure Portal **beszédfelismerési** áttekintése és a kulcsok lapok, és a tároló elindításához szükséges.<br><br>**{API_KEY}**: erőforráskulcs<br><br>**{ENDPOINT_URI}**: a végpont URI-példája:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Az ajánlott állomásszámítógép-konfiguráció

Tekintse meg a [beszédszolgáltatás tárolószámítógépének][speech-container-host-computer] adatait referenciaként. Ez *a helm diagram* automatikusan kiszámítja a processzor- és memóriaigényeket a felhasználó által megadott dekódolások (egyidejű kérelmek) alapján. Ezenkívül attól függően, hogy a hang- és `enabled`szövegbevitel optimalizálása be van-e állítva. A helm diagram alapértelmezett, két egyidejű kérelmek és letiltása optimalizálás.

| Szolgáltatás | CPU / konténer | Memória / Konténer |
|--|--|--|
| **Beszéd-szöveg** | egy dekóder hez legalább 1150 millimag szükséges. Ha `optimizedForAudioFile` a engedélyezve van, akkor 1950 millicore szükséges. (alapértelmezett: két dekóder) | Kötelező: 2 GB<br>Korlátozott: 4 GB |
| **Szövegfelolvasás** | egy egyidejű kérelem hez legalább 500 millimag szükséges. Ha `optimizeForTurboMode` a engedélyezve van, akkor 1000 millicore szükséges. (alapértelmezett: két egyidejű kérelem) | Kötelező: 1 GB<br> Korlátozott: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Csatlakozás a Kubernetes-fürthöz

A gazdaszámítógépnek rendelkeznie kell egy elérhető Kubernetes-fürttel. Tekintse meg ezt az [oktatóanyagot a Kubernetes-fürt központi telepítéséről,](../../aks/tutorial-kubernetes-deploy-cluster.md) amely ből megtudhatja, hogyan telepíthet kubernetes-fürtöt egy gazdaszámítógépre.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-hitelesítő adatok megosztása a Kubernetes-fürttel

Ahhoz, hogy `docker pull` a Kubernetes-fürt a `containerpreview.azurecr.io` konfigurált rendszerkép(ek) a tároló beállításjegyzékből, át kell vinnie a docker hitelesítő adatokat a fürtbe. Az [`kubectl create`][kubectl-create] alábbi parancs végrehajtásával hozzon létre egy *docker-registry titkos* kulcsot a tároló beállításjegyzék-hozzáférési előfeltétele alapján megadott hitelesítő adatok alapján.

A választott parancssori felületről futtassa a következő parancsot. Ügyeljen arra, `<username>`hogy `<password>`cserélje `<email-address>` le a , és a tároló rendszerleíró hitelesítő adatait.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Ha már rendelkezik `containerpreview.azurecr.io` hozzáféréssel a tároló beállításjegyzékhez, létrehozhat egy Kubernetes-titkos kulcsot az általános jelző használatával. Vegye figyelembe a következő parancsot, amely a Docker-konfiguráció JSON-on hajt végre.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A következő kimenet a konzolra kerül, ha a titkos kulcsot sikeresen létrehozták.

```console
secret "mcr" created
```

Annak ellenőrzéséhez, hogy a titkos [`kubectl get`][kubectl-get] kulcsot `secrets` létrehozták-e, hajtsa végre a jelzővel.

```console
kubectl get secrets
```

A nyomtatás `kubectl get secrets` az összes konfigurált titok.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Helm-diagram értékek konfigurálása a telepítéshez

Látogasson el a [Microsoft Helm Hub][ms-helm-hub] az összes nyilvánosan elérhető helm térképek által kínált Microsoft. A Microsoft Helm Hub ban található a **Cognitive Services helyszíni beszédfelismerési diagramja.** A **Cognitive Services helyszíni beszédfelismerése** a telepítendő diagram, de `config-values.yaml` először létre kell hoznunk egy explicit konfigurációkkal rendelkező fájlt. Kezdjük azzal, hogy hozzáadja a Microsoft-tárházat a Helm-példányhoz.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Ezután konfiguráljuk a Helm diagram értékeit. Másolja és illessze be a következő `config-values.yaml`YAML fájlt egy fájlba. A **Cognitive Services helyszíni helmdiagramjának**testreszabásáról a [helmdiagramok testreszabása](#customize-helm-charts)című témakörben olvashat bővebben. Cserélje `# {ENDPOINT_URI}` le `# {API_KEY}` a megjegyzéseket a saját értékeire.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Ha `billing` a `apikey` és az értékek nem állnak rendelkezésre, a szolgáltatások 15 min után lejárnak. Hasonlóképpen az ellenőrzés sikertelen lesz, mivel a szolgáltatások nem lesznek elérhetők.

### <a name="the-kubernetes-package-helm-chart"></a>A Kubernetes csomag (Helm chart)

A *Helm diagram* tartalmazza a konfigurációt, amely docker `containerpreview.azurecr.io` rendszerkép(ek) lekérése a tároló rendszerleíró adatbázisból.

> A [Helm-diagram][helm-charts] olyan fájlok gyűjteménye, amelyek a Kubernetes-erőforrások kapcsolódó készletét írják le. Egyetlen diagram használható egy egyszerű, például egy memcached pod vagy valami összetett, például egy teljes webalkalmazás-verem http-kiszolgálókkal, adatbázisokkal, gyorsítótárakkal és így tovább.

A megadott *Helm-diagramok* lekéri a docker-rendszerképeket a beszédszolgáltatás, mind a `containerpreview.azurecr.io` szöveg-beszéd és a beszéd-szöveg szolgáltatások a tároló beállításjegyzékből.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>A Helm-diagram telepítése a Kubernetes-fürtre

A *helm diagram* telepítéséhez végre kell [`helm install`][helm-install-cmd] hajtanunk `<config-values.yaml>` a parancsot, és le kell cserélni a megfelelő elérési utat és fájlnév argumentumot. Az `microsoft/cognitive-services-speech-onpremise` alábbiakban hivatkozott Helm diagram itt érhető el a [Microsoft Helm Hub-on.][ms-helm-hub-speech-chart]

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Íme egy példa kimenet, amely et egy sikeres telepítés végrehajtásától vár:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

A Kubernetes-telepítés több percet is igénybe vehet. Annak ellenőrzéséhez, hogy a podok és a szolgáltatások megfelelően vannak-e telepítve és elérhetők-e, hajtsa végre a következő parancsot:

```console
kubectl get all
```

A következő kimenethez hasonló eredményre számíthat:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Helm telepítésének ellenőrzése helm tesztekkel

A telepített Helm diagramok határozzák meg *a Helm teszteket,* amelyek kényelmét szolgálják az ellenőrzéshez. Ezek a tesztek ellenőrzik a szolgáltatás készenlétét. A **beszéd-szöveg** és **a szövegfelolvasási** szolgáltatások ellenőrzéséhez végrehajtjuk a [Helm tesztparancsot.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Ezek a tesztek sikertelenek lesznek, ha a POD állapota nem, `Running` vagy ha a központi telepítés nem szerepel az `AVAILABLE` oszlopban. Legyen türelemmel, mert ez több mint tíz percet vesz igénybe.

Ezek a tesztek különböző állapoteredményeket eredményeznek:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

A *helm tesztek*végrehajtásának alternatívájaként a *külső IP-címeket* és `kubectl get all` a megfelelő portokat a parancsból gyűjtheti. Az IP és a port használatával nyisson meg egy webböngészőt, és keresse meg `http://<external-ip>:<port>:/swagger/index.html` az API swagger lap(ok) megtekintéséhez.

## <a name="customize-helm-charts"></a>Helm-diagramok testreszabása

A kormánydiagramok hierarchikusak. Mivel a hierarchikus lehetővé teszi a diagram öröklése, azt is előírja, hogy a fogalom a specificitás, ahol a beállításokat, amelyek specifikusabbak felülbírálják örökölt szabályok.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
