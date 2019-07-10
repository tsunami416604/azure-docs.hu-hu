---
title: Helyszíni Kubernetes használata
titleSuffix: Azure Cognitive Services
description: Kubernetes és a Helm használatával adja meg a hang-szöveg és a szöveg-hang transzformációs tárolórendszerképeket, egy Kubernetes-csomag létrehozása. Ez a csomag telepíti a kubernetes-fürt helyszíni.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786255"
---
# <a name="use-kubernetes-on-premises"></a>Helyszíni Kubernetes használata

Kubernetes és a Helm használatával adja meg a hang-szöveg és a szöveg-hang transzformációs tárolórendszerképeket, egy Kubernetes-csomag létrehozása. Ez a csomag telepíti a kubernetes-fürt helyszíni. Végül azt vizsgáljuk meg a telepített szolgáltatások és a különböző konfigurációs beállítások tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Beszédfelismerési tárolókhoz helyi használata előtt a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
| Azure-fiók | Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][free-azure-account] a virtuális gép létrehozásának megkezdése előtt. |
| Tárolóregisztrációs adatbázis eléréséhez | Ahhoz, Kubernetes, docker-rendszerképek lekérni a fürtbe el kell a tároló-beállításjegyzékbe való hozzáférést. Kell [kérjen hozzáférést a tárolóregisztrációs adatbázisba][speech-preview-access] első. |
| Kubernetes parancssori Felületét | A [Kubernetes parancssori Felületét][kubernetes-cli] szükség a megosztott hitelesítő adatok kezelése a tárolóregisztrációs adatbázisból. Kubernetes Helm, a Kubernetes Csomagkezelő előtt is szükséges. |
| Helm CLI | Részeként a [Helm CLI][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]. |
|Beszéd erőforrás |Ezek a tárolók használatához rendelkeznie kell:<br><br>A _Speech_ Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket érhetők el az Azure Portalon **Speech** áttekintése és a kulcsok lapok és a szükséges a tárolót.<br><br>**{API_KEY}** : erőforrás-kulcs<br><br>**{ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Az ajánlott gazdagépe számítógép-konfiguráció

Tekintse meg a [beszédszolgáltatás tároló gazdaszámítógép][speech-container-host-computer] referenciaként részleteit. Ez *helm-diagram* automatikusan kiszámolja a Processzor- és követelményei alapján hány dekódol (egyidejű kérelmek), amely a felhasználó adja meg. Ezenkívül fogja módosítani e audio/szövegbevitel optimalizálások alkalmazásbeállításait alapján `enabled`. A helm diagram alapértelmezett értéke, két egyidejű kéréssel és letiltását optimalizálását.

| Szolgáltatás | CPU / tároló | Memória / tároló |
|--|--|--|
| **Speech-to-Text** | egy dekóder 1,150 millicore legalább igényel. Ha a `optimizedForAudioFile` engedélyezve van, majd 1,950 millicore szükség. (alapértelmezett: két dekóderek) | Szükséges: 2 GB<br>Korlátozott:  4 GB |
| **Text-to-Speech** | egy párhuzamos kérés legalább 500 millicore igényel. Ha a `optimizeForTurboMode` engedélyezve van, akkor 1000 millicore szükség. (alapértelmezett: két egyidejű kérelmek) | Szükséges: 1 GB<br> Korlátozott: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>A Kubernetes-fürthöz való csatlakozáshoz

A gazdaszámítógép kellene rendelkeznie az elérhető Kubernetes-fürtöt. Ebben az oktatóanyagban található [Kubernetes-fürt üzembe helyezése](../../aks/tutorial-kubernetes-deploy-cluster.md) fogalmi elsajátítása érdekében hogyan helyezhet üzembe egy Kubernetes-fürtöt egy gazdagépen.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker hitelesítő adatokat osztanak meg a Kubernetes-fürt

A Kubernetes-fürthöz való engedélyezéséhez `docker pull` a konfigurált rendszerképeket a a `containerpreview.azurecr.io` tárolójegyzéket, át kell a docker hitelesítő adatokat a fürtbe. Hajtsa végre a [ `kubectl create` ][kubectl-create] létrehozásához az alábbi parancsot egy *docker-registry titkos* a container registry hozzáférés előfeltétel a megadott hitelesítő adatok alapján.

A választott parancssori felületről futtassa a következő parancsot. Ne felejtse el a `<username>`, `<password>`, és `<email-address>` a container registry hitelesítő adatokkal.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Ha már rendelkezik hozzáféréssel a `containerpreview.azurecr.io` tárolójegyzéket, létrehozhat egy Kubernetes titkos inkább az általános jelző használatával. Vegye figyelembe a következő parancsot, amely végrehajtja a Docker-konfiguráció JSON ellen.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A következő kimenetet a konzol nyomtatott, amikor a titkos kulcs sikeresen létrejött.

```console
secret "containerpreview" created
```

Győződjön meg arról, hogy létrejött-e a titkos kulcsot, hajtsa végre a [ `kubectl get` ][kubectl-get] együtt a `secrets` jelzőt.

```console
kuberctl get secrets
```

Végrehajtása a `kubectl get secrets` jelenít meg a beállított titkos kulcsait.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Üzemelő példány Helm-diagram értékeit konfigurálása

Látogasson el a [Microsoft Helm Hub][ms-helm-hub] a Microsoft által kínált összes nyilvánosan elérhető helm-diagramok. A Microsoft Helm hubról megtalálhatja a **Cognitive Services beszédfelismerő helyszíni diagram**. A **Cognitive Services beszédfelismerő helyszíni** a diagram fogjuk telepíteni, de hogy először létre kell hoznia egy `config-values.yaml` explicit konfigurációk fájlt. Először a Microsoft-tárház hozzáadása a Helm-példányhoz.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Ezután konfigurálását végezzük el a Helm-diagram értékeit. Másolja és illessze be a következő yaml-kódot egy fájlt `config-values.yaml`. Testreszabásáról további információkat a **Cognitive Services beszédfelismerő helyszíni Helm-diagram**, lásd: [testre szabhatja a helm-diagramok](#customize-helm-charts). Cserélje le a `billing` és `apikey` a saját értékeit.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Ha a `billing` és `apikey` értékek nincsenek megadva, a szolgáltatások a 15 perc után lejár. Hasonlóképpen ellenőrzés sikertelen lesz, mivel a szolgáltatás nem lesz elérhető.

### <a name="the-kubernetes-package-helm-chart"></a>A Kubernetes-csomag (Helm-diagram)

A *Helm-diagram* melyik lekéréshez a docker-rendszerképeket konfigurációt tartalmazza a `containerpreview.azurecr.io` tárolóregisztrációs adatbázis.

> A [Helm-diagram][helm-charts] gyűjteménye, fájlok, amelyek kapcsolódó Kubernetes-erőforrások készlete ismertetik. Egy diagramon üzembe helyezéséhez egy egyszerű, előfordulhat, hogy használható, például egy memcached-pod, vagy más összetett, például egy teljes web app verem a HTTP-kiszolgálókhoz, adatbázisok, gyorsítótárakhoz és így tovább.

A megadott *Helm-diagramok* lekérése a docker-rendszerképek a beszédfelismerési szolgáltatás, szöveg-hang transzformációs és szolgáltatásai közül a hang-szöveg transzformációs is a `containerpreview.azurecr.io` tárolóregisztrációs adatbázis.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>A Helm-diagramot a Kubernetes-fürt telepítése

Telepítése a *helm-diagram* kell végrehajtani a [ `helm install` ][helm-install-cmd] parancsot, és cserélje le a `<config-values.yaml>` megfelelő elérési útját és nevét argumentummal. A `microsoft/cognitive-services-speech-onpremise` érhető el az alább hivatkozott Helm-diagramot a [Microsoft Helm itt Hub][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Íme egy példa a kimenetre, előfordulhat, hogy a várt a sikeres telepítés végrehajtását:

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

A Kubernetes üzembe helyezés több percet átveszi végrehajtásához. Győződjön meg róla, hogy podok és a szolgáltatások megfelelően rendszerbe állított és elérhető, hajtsa végre a következő parancsot:

```console
kubectl get all
```

Ehhez hasonló a következő kimenetet kell látnia:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>A Helm tesztek Helm-telepítés ellenőrzése

A telepített Helm-diagramok definiálása *tesztek Helm*, amely szolgál az ellenőrzéshez a kényelem. Ezek a tesztek szolgáltatás készültség ellenőrzése. Ellenőrizze mindkét **hang-szöveg transzformációs** és **szöveg-hang transzformációs** szolgáltatásokat, azt fogja végrehajtani a [Helm teszt][helm-test] parancsot.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Ezek a tesztek sikertelen lesz, ha a POD állapot nincs `Running` , vagy ha a telepítés alatt nem szerepel a `AVAILABLE` oszlop. Türelemmel, mivel ez több mint tíz percet vehet igénybe végrehajtásához.

Ezeket a teszteket fogja eredményeket különféle állapota:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Végrehajtása helyett a *tesztek helm*, amelyeket összegyűjthet a *külső IP* címek és a megfelelő portok a `kubectl get all` parancsot. Nyisson meg egy webböngészőt, és keresse meg az IP és port használata esetén `http://<external-ip>:<port>:/swagger/index.html` az API swagger-oldal(ak) nézetre.

## <a name="customize-helm-charts"></a>Testre szabhatja a Helm-diagramok

Helm-diagramok hierarchikusak. Hierarchikus visszatérhetnek lehetővé teszi a diagram öröklődését, azt is caters sajátossága, ahol a beállításokat, amelyeket a rendszer az örökölt szabályok felülbírálása fogalmát.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>További lépések

Alkalmazások telepítése a Helm használatával az Azure Kubernetes Service (AKS), a további részletekért [látogasson el ide][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [A cognitive Services-tárolók][cog-svcs-containers]

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
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
