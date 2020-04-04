---
title: Fejlesztés az Azure Kubernetes szolgáltatáson (AKS) piszkozattal
description: A Piszkozat az AKS-sel és az Azure Container Registry használatával csomag, és futtatja az alkalmazástárolók egy fürtben.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 820af2d8ddf03997eea559fbc5270e84f30a805a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632880"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Rövid útmutató: Fejlesztés az Azure Kubernetes szolgáltatáson (AKS) a vázlattal

A Draft egy nyílt forráskódú eszköz, amely segít az alkalmazástárolók kubernetes-fürtben történő csomagolásában és futtatásában. A Piszkozat segítségével gyorsan újratelepítheti az alkalmazásokat a Kubernetes-re, mivel kódmódosítások történnek anélkül, hogy a módosításokat a verziókövetésre kellene véglegesíteni. A Vázlat ról további információt a [GitHub dokumentációtervezetcímű][draft-documentation]témaköre tartalmaz.

Ez a cikk bemutatja, hogyan használhatja a Piszkó az AKS-en lévő alkalmazások csomagolásához és futtatásához.


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Az Azure CLI telepítve van.](/cli/azure/install-azure-cli?view=azure-cli-latest)
* A Docker telepítve és konfigurálva. A Docker csomagokat biztosít, amelyekkel a Docker [Mac][docker-for-mac], [Windows][docker-for-windows] vagy [Linux][docker-for-linux] rendszereken konfigurálható.
* [Helm v2 telepítve][helm-install].
* [A vázlat telepítve][draft-documentation]van.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

Hozzon létre egy AKS-fürtöt. Az alábbi parancsok létrehoznak egy MyResourceGroup nevű erőforráscsoportot és egy MyAKS nevű AKS-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
A Piszka az alkalmazás AKS-fürtben való futtatásához egy Azure Container Registry kell a tárolórendszerképek tárolásához. Az alábbi példa az [acr create][az-acr-create] segítségével hozzon létre egy *MyDraftACR* nevű ACR-t a *MyResourceGroup* erőforráscsoportban az *alapszintű* termékváltozattal. Meg kell adnia a saját egyedi rendszerleíró nevét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

A kimenet a következő példához hasonló. Jegyezze fel az ACR *loginServer* értékét, mivel egy későbbi lépésben fogja használni. Az alábbi példában *mydraftacr.azurecr.io* a *MyDraftACR* *loginServer kiszolgálója.*

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


Ahhoz, hogy a Piszkó az ACR-példányt használhassa, először be kell jelentkeznie. A bejelentkezéshez használja az [acr bejelentkezési][az-acr-login] parancsot. Az alábbi példa egy *MyDraftACR*nevű ACR-be jelentkezik be.

```azurecli
az acr login --name MyDraftACR
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Az AKS-fürt és az ACR közötti bizalom létrehozása

Az AKS-fürthöz is hozzá kell férnie az ACR-hez a tárolórendszerképek lekérése és futtatása érdekében. Az AKS-ből hozzáférést biztosít az ACR-hez egy megbízhatósági kapcsolat létrehozásával. Az AKS-fürt és az ACR-beállításjegyzék közötti bizalmi kapcsolat létrehozásához adjon engedélyeket az AKS-fürt által az ACR-beállításjegyzék eléréséhez használt Azure Active Directory egyszerű szolgáltatáshoz. A következő parancsok engedélyeket adnak a *MyResourceGroup* *MyAKS* fürtjének egyszerű szolgáltatásának a *MyResourceGroup* *MyDraftACR* ACR szolgáltatásához.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Csatlakozás az AKS-fürthöz

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

Az [aks get-credentials][] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. A következő példa a *MyResourceGroup*ban a MyResourceGroup ban *myaks* nevű AKS-fürt hitelesítő adatait kapja meg:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Szolgáltatásfiók létrehozása a Helm számára

Mielőtt üzembe helyezheti a Helm-et egy RBAC-kompatibilis AKS-fürtben, szüksége van egy szolgáltatásfiókra és szerepkör-kötésre a Tiller szolgáltatáshoz. A Helm / Tiller RBAC-kompatibilis fürtben való védelméről további információt a [Tiller, a Namespaces és az RBAC című][tiller-rbac]témakörben talál. Ha az AKS-fürt nincs engedélyezve az RBAC- értéken, hagyja ki ezt a lépést.

Hozzon létre `helm-rbac.yaml` egy elnevezett fájlt, és másolja a következő YAML-fájlba:

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

Hozza létre a szolgáltatásfiókot `kubectl apply` és a szerepkörkötést a következő paranccsal:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Helm konfigurálása
Egy alap-tiller üzembe helyezéséhez egy AKS-fürt, használja a [helm init][helm-init] parancsot. Ha a fürt nincs engedélyezve az `--service-account` RBAC- kód, távolítsa el az argumentumot és az értéket.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Vázlat konfigurálása

Ha még nem konfigurálta a Piszkozatot a helyi számítógépen, futtassa a következőket: `draft init`

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Az ACR *loginServer* használatához a Piszkozat beállítást is be kell állítania. A következő `draft config set` parancs `mydraftacr.azurecr.io` a rendszerleíró adatbázisként való használatra szolgál.

```console
draft config set registry mydraftacr.azurecr.io
```

A Draft ot úgy állította be, hogy az ACR-t használja, és a Vázlat leküldéses tárolórendszerképeket az ACR-be. Amikor a Piszkozat az alkalmazást az AKS-fürtben futtatja, nincs szükség jelszavakra vagy titkos kulcsokra az ACR-beállításjegyzékbe való leküldése vagy lekérése esetén. Mivel az AKS-fürt és az ACR között bizalmi kapcsolat jött létre, a hitelesítés az Azure Resource Manager szintjén történik az Azure Active Directory használatával.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ez a rövid útmutató [egy Példa Java-alkalmazást használ a Draft GitHub tárházból.][example-java] Klónozza az alkalmazást a GitHubról, és keresse meg a `draft/examples/example-java/` könyvtárat.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>A mintaalkalmazás futtatása a Vázlat segítségével

Használja `draft create` a parancsot az alkalmazás előkészítéséhez.

```console
draft create
```

Ez a parancs létrehozza azokat az összetevőket, amelyek az alkalmazás Kubernetes-fürtben való futtatásához használatosak. Ezek közé tartozik a Dockerfile, a Helm-diagram és a *draft.toml* fájl, amely a Draft konfigurációs fájl.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

A mintaalkalmazás AKS-fürtben való `draft up` futtatásához használja a parancsot.

```console
draft up
```

Ez a parancs létrehozza a Dockerfile egy tárolórendszerképet, leküldése a rendszerképet az ACR-be, és telepíti a Helm diagramot az alkalmazás AKS-ben való elindításához. A parancs első futtatásakor a tárolókép lenyomása és húzása eltarthat egy ideig. Az alaprétegek gyorsítótárba helyezése után az alkalmazás üzembe helyezéséhez szükséges idő jelentősen csökken.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Csatlakozás a futó mintaalkalmazáshoz a helyi számítógépről

Az alkalmazás teszteléséhez `draft connect` használja a parancsot.

```console
draft connect
```

Ez a parancs biztonságos kapcsolatot biztosít a Kubernetes-podhoz. Ha elkészült, az alkalmazás elérhető a megadott URL-címen.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Keresse meg az alkalmazást egy `localhost` böngészőben az URL-címet használva a mintaalkalmazás megtekintéséhez. A fenti példában az `http://localhost:49804`URL-cím . Állítsa le `Ctrl+c`a kapcsolatot a használatával.

## <a name="access-the-application-on-the-internet"></a>Az alkalmazás elérése az interneten

Az előző lépés egy proxykapcsolatot hozott létre az Alkalmazáspodhoz az AKS-fürtben. Az alkalmazás fejlesztése és tesztelése során érdemes lehet elérhetővé tenni az alkalmazást az interneten. Ahhoz, hogy egy alkalmazást az interneten, létrehozhat egy Kubernetes szolgáltatást egy fajta [LoadBalancer][kubernetes-service-loadbalancer].

Frissítse `charts/example-java/values.yaml` ni a *LoadBalancer* szolgáltatás létrehozásához. Módosítsa a *service.type* értékét *ClusterIP-ről* *LoadBalancer*értékre.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Mentse a módosításokat, zárja `draft up` be a fájlt, és futtassa az alkalmazás ismételt futtatásához.

```console
draft up
```

A szolgáltatás néhány percet vesz igénybe a nyilvános IP-cím visszaadása. A folyamat figyeléséhez `kubectl get service` használja a parancsot a *figyelési* paraméterrel:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Keresse meg az alkalmazás terheléselosztóját egy böngészőben az *EXTERNAL-IP használatával* a mintaalkalmazás megtekintéséhez. A fenti példában az `52.175.224.118`IP .

## <a name="iterate-on-the-application"></a>Az alkalmazás iterate

Az alkalmazást helyi legvégrehajtott módosításokkal és újrafuttatásával terejítheti. `draft up`

Frissítse az üzenetet vissza [a 7-es vonalon src / main / java / helloworld / Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Futtassa a `draft up` parancsot az alkalmazás újratelepítéséhez:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

A frissített alkalmazás megtekintéséhez keresse meg újra a terheléselosztó IP-címét, és ellenőrizze, hogy megjelennek-e a módosítások.

## <a name="delete-the-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, az [az csoport törlési][az-group-delete] parancsával távolítsa el az erőforráscsoportot, az AKS-fürtöt, a tároló beállításjegyzékét, az ott tárolt tárolólemezképeket és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="next-steps"></a>További lépések

A Piszkozat használatáról további információt a GitHubon található Vázlat dokumentációban talál.

> [!div class="nextstepaction"]
> [Dokumentációtervezet][draft-documentation]


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
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
