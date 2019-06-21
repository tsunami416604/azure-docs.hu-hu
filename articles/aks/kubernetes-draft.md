---
title: Fejlesztés az Azure Kubernetes Service (AKS) a Draft használatával
description: Vázlat használata AKS és Azure Container Registrybe
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303545"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Gyors útmutató: Fejlesztés az Azure Kubernetes Service (AKS) a Draft használatával

Vázlat egy nyílt forráskódú eszköz, amely segít a csomagot, és futtathatók alkalmazástárolók a Kubernetes-fürtben. A Draft akkor is gyorsan alkalmazás újbóli üzembe helyezése egy kubernetes változás kódot anélkül, hogy a változtatások véglegesítése a verziókezeléshez kellene. A Draft további információkért lásd: a [vázlatszintű a Githubon található dokumentációt][draft-documentation].

Ez a cikk bemutatja, hogyan használhatja a Draft csomagot, és alkalmazás futtatása az aks-en.


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* A docker telepítve és konfigurálva. A docker csomagokat biztosít, a Docker konfigurálható egy [Mac][docker-for-mac], [Windows][docker-for-windows], vagy [Linux][docker-a-linux] rendszer.
* [Telepített Helm](https://github.com/helm/helm/blob/master/docs/install.md).
* [A draft telepítése][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

AKS-fürt létrehozása. Az alábbi parancsokkal MyResourceGroup és a egy AKS-fürt MyAKS nevű erőforráscsoport létrehozásához.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
A Draft használatával futtassa az alkalmazást a az AKS-fürt, szüksége van egy Azure Container Registry a tárolólemezképek tárolására. Az alábbi példában használja [az acr létrehozása][az-acr-create] hozhat létre egy ACR-t nevű *MyDraftACR* a a *MyResourceGroup* erőforráscsoportot a *alapszintű* TERMÉKVÁLTOZAT. A saját egyedi nevet kell adnia. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

A kimenet a következő példához hasonló. Jegyezze fel a *adatbázis bejelentkezési kiszolgálójának nevével* értéket az ACR, mivel egy későbbi lépésben lesz. Az az alábbi példában, *mydraftacr.azurecr.io* van a *adatbázis bejelentkezési kiszolgálójának nevével* a *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
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


A Draft használata az ACR-példányba, először jelentkezzen be. Használja a [az acr bejelentkezési][az-acr-login] jelentkezzen be a parancsot. Az alábbi példában fog bejelentkezni egy ACR-t nevű *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="create-trust-between-aks-cluster-and-acr"></a>AKS-fürt és az ACR közötti megbízhatósági kapcsolat létrehozása

Az AKS-fürt is el kell érnie az ACR kérni a tárolórendszerképeket, és futtathatja őket. Engedélyezi a hozzáférést az az ACR-be az AKS által egy megbízhatósági kapcsolat létrehozása. AKS-fürt és a egy ACR-beállításjegyzékbe közötti bizalmi kapcsolat létrehozásához, az ACR-beállításjegyzék eléréséhez az AKS-fürt által használt az Azure Active Directory szolgáltatás egyszerű jogosultság megadása. Az alábbi parancsokat a hozzáférési jogot az egyszerű szolgáltatás a *MyAKS* a fürt a *MyResourceGroup* , a *MyDraftACR* az ACR a  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Csatlakozás az AKS-fürt

Csatlakozás a Kubernetes-fürt a helyi számítógépen, használhatja [kubectl][kubectl], a Kubernetes parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

Konfigurálása `kubectl` a Kubernetes-fürt csatlakozni, használja a [az aks get-credentials][] parancsot. Az alábbi példa lekéri az AKS-fürtöt nevű hitelesítő adatok *MyAKS* a a *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>A Helm szolgáltatásfiók létrehozása

Az RBAC-kompatibilis AKS-fürt telepítése Helm, előtt szükség van a szolgáltatásfiók és a szerepkör kötést a tiller valóban szolgáltatás. További információ biztonságossá tenni a Helm / az RBAC a tiller valóban engedélyezve van a fürtöt, tekintse meg [a tiller valóban, a névterek és az RBAC][tiller-rbac]. Ha az AKS-fürt nem RBAC engedélyezve van, hagyja ki ezt a lépést.

Hozzon létre egy fájlt `helm-rbac.yaml` másolja be a következő yaml-kódot:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

A szolgáltatásfiók és a szerepkör-kötés létrehozása a `kubectl apply` parancsot:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Helm konfigurálása
Egy alapszintű tiller valóban az AKS-fürt üzembe helyezéséhez használja a [helm init][helm-init] parancsot. Ha a fürt nem RBAC engedélyezve van, távolítsa el a `--service-account` argumentum és értékét.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>A Draft konfigurálásához

Ha még nem konfigurálta a Draft a helyi gépén, futtassa `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Vázlat használata konfigurálnia kell a *adatbázis bejelentkezési kiszolgálójának nevével* , az ACR. A következő parancsot használja `draft config set` használandó `mydraftacr.azurecr.io` , beállításjegyzék.

```console
draft config set registry mydraftacr.azurecr.io
```

Konfigurálta a Draft használata az ACR, és a Draft is tárolórendszerképek leküldése az ACR. Vázlat az AKS-fürt az alkalmazás fut, amikor egy jelszavakat vagy a titkos kulcsok sem szükséges leküldése vagy lekérni az ACR-beállításjegyzékből. Az AKS-fürt és az ACR közötti megbízhatósági kapcsolat létrehozása, óta hitelesítési szintjén történik az Azure Resource Manager, az Azure Active Directoryval.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ebben a rövid útmutatóban használt [például java-alkalmazás a Draft GitHub-adattárból][example-java]. Klónozza az alkalmazást a Githubról, és keresse meg a `draft/examples/example-java/` könyvtár.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Futtassa a mintaalkalmazást a Draft használatával

Használja a `draft create` paranccsal készítse elő az alkalmazást.

```console
draft create
```

Ez a parancs létrehoz egy Kubernetes-fürt az alkalmazás futtatásához használt összetevők. Ezek az elemek közé tartozik egy docker-fájlt, egy Helm-diagramot, és a egy *draft.toml* fájlt, amely a Draft konfigurációs fájlt.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Futtassa a mintaalkalmazást az AKS-fürt, használja a `draft up` parancsot.

```console
draft up
```

Ez a parancs létrehozza a docker-fájl létrehozása egy tárolórendszerképet, leküldi a rendszerképet az ACR és telepíti a Helm-diagram az alkalmazás elindításához az aks-ben. Ez a parancs első futtatásakor a tároló rendszerképét mozgatásához eltarthat egy ideig. Miután az alap réteg lettek gyorsítótárazva, az alkalmazás telepítéséhez szükséges idő jelentősen csökken.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Csatlakozás a futó mintaalkalmazást a helyi gépen

Az alkalmazás teszteléséhez használja a `draft connect` parancsot.

```console
draft connect
```

Ez a parancs proxyt a Kubernetes-podok biztonságos kapcsolatot. Amikor végzett, az alkalmazás elérhető lesz a megadott URL-címen.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Az alkalmazás egy böngésző használatával keresse meg a `localhost` URL-cím megjelenítéséhez a mintaalkalmazást. A fenti példában az URL-cím van `http://localhost:49804`. A kapcsolat abbahagyja `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Az alkalmazás internetes eléréséhez

Az előző lépésben létrehozott az alkalmazáspodot létesítenie az AKS-fürt. Fejlesztés és tesztelheti alkalmazását, érdemes az alkalmazás elérhetővé tétele az interneten. Tegye elérhetővé az interneten egy alkalmazást, létrehozhat egy Kubernetes-szolgáltatást típusú [terheléselosztó][kubernetes-service-loadbalancer].

Frissítés `charts/example-java/values.yaml` hozhat létre egy *terheléselosztó* szolgáltatás. Módosítsa az értéket a *service.type* a *ClusterIP* való *terheléselosztó*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Mentse a módosításokat, zárja be a fájlt, és futtassa `draft up` kattintva futtassa újra az alkalmazást.

```console
draft up
```

A szolgáltatás egy nyilvános IP-címének visszaadásához néhány percet vesz igénybe. A folyamat állapotának monitorozásához használja a `kubectl get service` parancsot a *watch* paramétert:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Keresse meg a terheléselosztó egy böngészőben az alkalmazás a *EXTERNAL-IP* , tekintse meg a mintaalkalmazást. A fenti példában az IP-címe `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Az alkalmazás ismételt futtatásával

Által helyileg a módosításokat, majd futtassa újra az alkalmazást is ismételt futtatásával `draft up`.

Frissítse az visszaadott üzenet [7 src/main/java/helloworld/Hello.java sor][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Futtassa a `draft up` parancsot az alkalmazás újbóli üzembe helyezése:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

A frissített alkalmazás megtekintéséhez újból nyissa meg a terheléselosztó IP-címet, és ellenőrizze a módosítások megjelennek-e.

## <a name="delete-the-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, használja a [az csoport törlése][az-group-delete] parancs eltávolítja az erőforráscsoport, az AKS-fürtöt, a tároló-beállításjegyzék, a tárolórendszerképeket tárolják, és minden kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításához lépéseiért lásd: [AKS szolgáltatás fő szempontok és a Törlés][sp-delete].

## <a name="next-steps"></a>További lépések

A Draft használatával kapcsolatos további információkért lásd: a Draft dokumentáció a Githubon.

> [!div class="nextstepaction"]
> [Vázlat dokumentációja][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
