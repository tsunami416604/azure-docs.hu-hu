---
title: Fejlesztés az Azure Kubernetes szolgáltatásban (ak) a Drafttal
description: A draft használata AK-val és Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 07e267af307fedd9b896e08919b7026a3a1c2bac
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292962"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Gyors útmutató: fejlesztés az Azure Kubernetes Service-ben (ak) a Drafttal

A draft egy nyílt forráskódú eszköz, amely megkönnyíti a Kubernetes-fürtben lévő alkalmazás-tárolók előkészítését és futtatását. A draft használatával gyorsan újratelepítheti az alkalmazásokat, hogy Kubernetes, és ne véglegesítse a módosításokat a verziókövetés előtt. A vázlattal kapcsolatos további információkért tekintse meg a következő témakört: a [githubon található dokumentáció vázlata][draft-documentation].

Ebből a cikkből megtudhatja, hogyan használhatja a piszkozatot a csomaghoz, és hogyan futtathat alkalmazásokat az AK-ban.


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
* A Docker telepítése és konfigurálása megtörtént. A Docker olyan csomagokat biztosít, amelyek a Docker-t egy [Mac][docker-for-mac], [Windows][docker-for-windows]vagy [Linux][docker-for-linux] rendszeren konfigurálják.
* A [Helm v2 telepítve van][helm-install].
* A [Piszkozat telepítve van][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Hozzon létre egy AK-fürtöt. Az alábbi parancsok létrehoznak egy MyResourceGroup nevű erőforráscsoportot és egy MyAKS nevű AK-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Ha a Piszkozat használatával szeretné futtatni az alkalmazást az AK-fürtben, szüksége lesz egy Azure Container Registry a tároló lemezképének tárolására. Az alábbi példa az [az ACR Create][az-acr-create] paranccsal hoz létre egy *MyDraftACR* nevű ACR-t a *MyResourceGroup* -erőforráscsoporthoz az *alapszintű* SKU használatával. Adja meg a saját egyedi regisztrációs nevét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

A kimenet a következő példához hasonló. Jegyezze fel az ACR *lekéréséhez* értékét, mivel azt egy későbbi lépésben fogja használni. Az alábbi példában a *mydraftacr.azurecr.IO* a *mydraftacr* *lekéréséhez* .

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


Az ACR-példány használatára vonatkozó tervezethez először be kell jelentkeznie. A bejelentkezéshez használja az az [ACR login][az-acr-login] parancsot. Az alábbi példa egy *MyDraftACR*nevű ACR-be fog bejelentkezni.

```azurecli
az acr login --name MyDraftACR
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Megbízhatósági kapcsolat létrehozása az AK-fürt és az ACR között

Az AK-fürtnek hozzá kell férnie az ACR-hez a tároló lemezképének lekéréséhez és futtatásához. Az ACR-hez való hozzáférést megbízhatósági kapcsolat létrehozásával engedélyezheti. Ha szeretne megbízhatósági kapcsolatot létesíteni egy AK-fürt és egy ACR-beállításjegyzék között, adjon meg engedélyeket az AK-fürt által használt Azure Active Directory egyszerű szolgáltatáshoz az ACR-beállításjegyzék eléréséhez. A következő parancsok engedélyeket biztosítanak a *MyResourceGroup* lévő *MyAKS* -fürt szolgáltatásnév számára a *MyResourceGroup* *MyDraftACR* ACR-ben.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Kapcsolódás az AK-fürthöz

Ha a helyi számítógépről kíván csatlakozni a Kubernetes-fürthöz, a [kubectl][kubectl], a Kubernetes parancssori ügyfelet kell használnia.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

Ha `kubectl` szeretne konfigurálni a Kubernetes-fürthöz való kapcsolódáshoz, használja az az [AK Get-hitelesítőadats][] parancsot. A következő példa a *MyAKS* nevű AK-fürt hitelesítő adatait kéri le a *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Hozzon létre egy szolgáltatásfiókot a Helm számára

Mielőtt üzembe helyezi a Helm-t egy RBAC-kompatibilis AK-fürtön, szüksége lesz egy szolgáltatásfiók és egy szerepkör-kötésre a kormányrúd szolgáltatáshoz. További információ a Helm/Tiller RBAC-kompatibilis fürtön való biztonságossá tételéről: a [kormányrúd, a névterek és a RBAC][tiller-rbac]. Ha az AK-fürt nem RBAC engedélyezve, ugorja át ezt a lépést.

Hozzon létre egy `helm-rbac.yaml` nevű fájlt, és másolja a következő YAML:

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

Hozzon létre egy szolgáltatásfiókot és egy szerepkör-kötést a `kubectl apply` paranccsal:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>A Helm konfigurálása
Alapszintű kormányrúd egy AK-fürtbe történő üzembe helyezéséhez használja a [Helm init][helm-init] parancsot. Ha a fürt nincs engedélyezve a RBAC, távolítsa el a `--service-account` argumentumot és az értéket.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Piszkozat konfigurálása

Ha nem konfigurálta a piszkozatot a helyi gépen, futtassa `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Az ACR *lekéréséhez* használatához is konfigurálnia kell a vázlatot. A következő parancs a `draft config set`t használja a `mydraftacr.azurecr.io` beállításjegyzékként való használatára.

```console
draft config set registry mydraftacr.azurecr.io
```

Az ACR használatára konfigurálta a piszkozatot, a draft pedig leküldheti a tároló lemezképeit az ACR-nek. Amikor a Piszkozat futtatja az alkalmazást az AK-fürtben, nincs szükség jelszóra vagy titkos kulcsra az ACR-beállításjegyzékből való leküldéshez vagy lekéréshez. Mivel a megbízhatóság létrejött az AK-fürt és az ACR között, a hitelesítés Azure Resource Manager szinten történik, Azure Active Directory használatával.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ez a rövid útmutató [egy példa Java-alkalmazást használ a GitHub-tárházból][example-java]. Klónozott az alkalmazást a GitHubról, és navigáljon a `draft/examples/example-java/` könyvtárba.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>A minta alkalmazás futtatása vázlattal

Az alkalmazás előkészítéséhez használja a `draft create` parancsot.

```console
draft create
```

Ez a parancs létrehozza az alkalmazás Kubernetes-fürtben való futtatásához használt összetevőket. Ezek az elemek egy Docker, egy Helm-diagramot és egy *draft. toml* fájlt tartalmaznak, amely a vázlat konfigurációs fájl.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

A minta alkalmazás az AK-fürtön való futtatásához használja a `draft up` parancsot.

```console
draft up
```

Ez a parancs létrehozza a Docker egy tároló-rendszerkép létrehozásához, leküldi a rendszerképet az ACR-nek, és telepíti a Helm diagramot, hogy elindítsa az alkalmazást az AK-ban. Amikor először futtatja ezt a parancsot, a tároló rendszerképének lekérése és húzása hosszabb időt is igénybe vehet. Az alaprétegek gyorsítótárazása után a rendszer jelentősen csökkenti az alkalmazás üzembe helyezéséhez szükséges időt.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Kapcsolódás a futó minta alkalmazáshoz a helyi gépről

Az alkalmazás teszteléséhez használja a `draft connect` parancsot.

```console
draft connect
```

Ez a parancs biztonságos kapcsolódást biztosít a Kubernetes Pod-hoz. Ha elkészült, az alkalmazás a megadott URL-címen érhető el.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Nyissa meg az alkalmazást egy böngészőben a `localhost` URL-cím használatával, és tekintse meg a minta alkalmazást. A fenti példában az URL-cím `http://localhost:49804`. A `Ctrl+c`használatával állítsa le a kapcsolatokat.

## <a name="access-the-application-on-the-internet"></a>Az alkalmazás elérése az interneten

Az előző lépésben létrehozott egy proxy-kapcsolódást az Application Pod-hoz az AK-fürtben. Az alkalmazás fejlesztése és tesztelése során előfordulhat, hogy az alkalmazást elérhetővé szeretné tenni az interneten. Ahhoz, hogy egy alkalmazás elérhető legyen az interneten, létrehozhat egy Kubernetes szolgáltatást [terheléselosztó][kubernetes-service-loadbalancer].

`charts/example-java/values.yaml` frissítése *terheléselosztó* -szolgáltatás létrehozásához. Módosítsa a *Service. Type* értéket a *ClusterIP* és a *terheléselosztó*érték között.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Mentse a módosításokat, majd a fájl bezárásához és a `draft up` futtatásához futtassa újból az alkalmazást.

```console
draft up
```

Néhány percet vesz igénybe, hogy a szolgáltatás egy nyilvános IP-címet ad vissza. A folyamat figyeléséhez használja a `kubectl get service` parancsot a *Watch* paraméterrel:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Nyissa meg az alkalmazás Load balancerét egy böngészőben a *külső IP* használatával a minta alkalmazás megtekintéséhez. A fenti példában az IP `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Iteráció az alkalmazásban

Az alkalmazást úgy is megismételheti, hogy helyi módosításokat végez, és újra futtatja `draft up`.

Frissítse a visszaadott üzenetet az [src/Main/Java/HelloWorld/Hello. Java 7. sorban][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Futtassa az `draft up` parancsot az alkalmazás újbóli üzembe helyezéséhez:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

A frissített alkalmazás megtekintéséhez navigáljon újra a terheléselosztó IP-címéhez, és ellenőrizze, hogy a módosítások megjelennek-e.

## <a name="delete-the-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, az az [Group delete][az-group-delete] paranccsal távolítsa el az erőforráscsoportot, az AK-fürtöt, a tároló-beállításjegyzéket, az ott tárolt tároló-lemezképeket és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. Az egyszerű szolgáltatás eltávolításának lépéseiért lásd: az [AK szolgáltatással kapcsolatos főbb megfontolások és törlés][sp-delete].

## <a name="next-steps"></a>Következő lépések

A draft használatával kapcsolatos további információkért tekintse meg a következő témakört: a GitHubon található dokumentáció vázlata.

> [!div class="nextstepaction"]
> [A vázlat dokumentációja][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[AK Get-hitelesítőadats]: /cli/azure/aks#az-aks-get-credentials
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
