---
title: Vázlat használata AKS és Azure Container Registrybe
description: Vázlat használata AKS és Azure Container Registrybe
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: d03324b82d056fc33114f1daa8961158c0a739f6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837647"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Vázlat használata az Azure Kubernetes Service (AKS)

Vázlat egy nyílt forráskódú eszköz, amely segít a csomag és a egy Kubernetes-fürtöt, így szabadon a fejlesztési ciklus – a "belső ciklus" koncentrált fejlesztési az alábbiakra koncentráljon az alkalmazástárolók üzembe helyezése. Vázlat működik, a kód fejlesztése folyamatban van, de a verziókövetés véglegesítése előtt. A Draft akkor is gyorsan alkalmazás újbóli üzembe helyezése egy kubernetes kód változás. A Draft további információkért lásd: a [vázlatszintű a Githubon található dokumentációt][draft-documentation].

Ez a cikk bemutatja, hogyan a Draft használata az AKS egy Kubernetes-fürt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben ismertetett lépések feltételezik, hogy már létrehozott egy AKS-fürtöt, és létrehozott egy `kubectl` kapcsolatot a fürttel. Ha ezek az elemek van szüksége, tekintse meg a [AKS gyors][aks-quickstart].

Privát Docker-tárolójegyzék az Azure Container Registry (ACR) van szüksége. Hozzon létre egy ACR-példányba való lépéseiért lásd: a [Azure Container Registry a rövid útmutató][acr-quickstart].

Az AKS-fürt a Helm is telepíteni kell. Hogyan telepítheti és konfigurálhatja a Helm további információkért lásd: [Azure Kubernetes Service (AKS) használata Helm][aks-helm].

Végül telepítenie kell a [Docker](https://www.docker.com).

## <a name="install-draft"></a>Vázlat telepítése

A Draft CLI egy ügyfél, amely a fejlesztői rendszeren fut, és lehetővé teszi, hogy a kód üzembe helyezhető egy Kubernetes-fürtöt. A Draft CLI telepítéséhez a Mac számítógépen, használjon `brew`. A további telepítési lehetőségekről a [Draft telepítése útmutató][draft-documentation].

> [!NOTE]
> Ha telepítette a Draft verziónál régebbi 0.12, először törölje Draft a fürt használatával `helm delete --purge draft` és futtatásával távolítsa el a helyi konfigurációs `rm -rf ~/.draft`. Ha MacOS-gépeken, majd futtassa `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

Most már a piszkozat a inicializálása a `draft init` parancsot:

```console
draft init
```

## <a name="configure-draft"></a>A Draft konfigurálásához

DRAFT létrehozása helyileg a tárolórendszerképeket, és ezután vagy őket a helyi beállításjegyzékből (például a Minikube), vagy egy Ön által megadott lemezkép beállításjegyzék használ. Ebben a cikkben az Azure Container Registry (ACR), így az AKS-fürt és az ACR-beállításjegyzék között megbízhatósági kapcsolatot kell létrehoznia, majd konfigurálja a tárolórendszerképek leküldése az ACR-be.

### <a name="create-trust-between-aks-cluster-and-acr"></a>AKS-fürt és az ACR közötti megbízhatósági kapcsolat létrehozása

AKS-fürt és a egy ACR-beállításjegyzékbe közötti bizalmi kapcsolat létrehozásához, az ACR-beállításjegyzék eléréséhez az AKS-fürt által használt az Azure Active Directory szolgáltatás egyszerű jogosultság megadása. A következő parancsokat, adja meg a saját `<resourceGroupName>`, cserélje le `<aksName>` néven az AKS-fürtöt, és cserélje le a `<acrName>` az ACR-beállításjegyzék nevére:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Az ACR el ezeket a lépéseket további információkért lásd: [hitelesítés az ACR-REL](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Konfigurálja a leküldése és üzembe helyezése ACR-ből

Most, hogy az AKS és ACR közötti megbízhatósági kapcsolat van, az AKS-fürt az ACR használatának engedélyezése.

1. A Draft-konfiguráció beállítása *beállításjegyzék* értéket. Cserélje le a következő parancsokat, `<acrName>` az ACR-beállításjegyzék nevére:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Jelentkezzen be az ACR-beállításjegyzékbe való [az acr bejelentkezési][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

AKS és ACR közötti megbízhatósági kapcsolat hozta létre, mert egy jelszavakat vagy a titkos kulcsok sem szükséges leküldése vagy lekérni az ACR-beállításjegyzékből. Hitelesítés az Azure Resource Manager szintjén, az Azure Active Directoryval történik.

## <a name="run-an-application"></a>Alkalmazás futtatása

Vázlat megtekintéséhez működés közben, a mintaalkalmazás üzembe helyezni a [Draft-tárház][draft-repo]. Első lépésként klónozza az adattárat:

```console
git clone https://github.com/Azure/draft
```

Módosítsa a Java-példák könyvtárában:

```console
cd draft/examples/example-java/
```

Használja a `draft create` parancsot a folyamat elindításához. Ez a parancs létrehoz egy Kubernetes-fürt az alkalmazás futtatásához használt összetevők. Ezek az elemek közé tartozik egy docker-fájlt, egy Helm-diagramot, és a egy *draft.toml* fájlt, amely a Draft konfigurációs fájlt.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Futtassa a mintaalkalmazást az AKS-fürt, használja a `draft up` parancsot. Ez a parancs létrehozza a docker-fájl létrehozása egy tárolórendszerképet, leküldi a rendszerképet az ACR-be, és végül telepíti a Helm-diagram az alkalmazás elindításához az aks-ben.

Ez a parancs első futtatásakor a tároló rendszerképét mozgatásához eltarthat egy ideig. Miután az alap réteg lettek gyorsítótárazva, az alkalmazás telepítéséhez szükséges idő jelentősen csökken.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Ha leküldése a Docker-rendszerkép problémák merülnek fel, győződjön meg arról, hogy sikeresen bejelentkezett az ACR-beállításjegyzékbe való [az acr bejelentkezési][az-acr-login], ismételje meg a `draft up` újra a parancsot.

## <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

Az alkalmazás teszteléséhez használja a `draft connect` parancsot. Ez a parancs proxyt a Kubernetes-podok biztonságos kapcsolatot. Amikor végzett, az alkalmazás elérhető lesz a megadott URL-címen.

> [!NOTE]
> Eltarthat néhány percig, le kell tölteni a tárolórendszerképet, és az alkalmazás elindításához. Ha egy hibaüzenetet kapja, amikor az alkalmazás elérésének, ismételje meg a kapcsolat.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Az alkalmazás megnyitásához nyissa meg egy webböngészőben a címet és a megadott port a `draft connect` kimenet, mint például *http://localhost:49804*. 

![Java-mintaalkalmazás futtatása a Draft használatával](media/kubernetes-draft/sample-app.png)

Használat `Control+C` leállítani a proxykapcsolatot.

> [!NOTE]
> Is használhatja a `draft up --auto-connect` parancs használatával állítsa össze és az alkalmazás üzembe helyezéséhez, majd az első futó tárolót közvetlenül csatlakozhat.

## <a name="access-the-application-on-the-internet"></a>Az alkalmazás internetes eléréséhez

Az előző lépésben létrehozott az alkalmazáspodot létesítenie az AKS-fürt. Fejlesztés és tesztelheti alkalmazását, érdemes az alkalmazás elérhetővé tétele az interneten. Tegye elérhetővé az interneten egy alkalmazást, létrehozhat egy Kubernetes-szolgáltatást típusú [terheléselosztó][kubernetes-service-loadbalancer], vagy hozzon létre egy [bejövőforgalom-vezérlőjéhez] [ kubernetes-ingress]. Hozzunk létre egy *terheléselosztó* szolgáltatás.

Először frissítse a *values.yaml* vázlatszintű csomagot, adja meg, amely egy szolgáltatás típusú *terheléselosztó* kell létrehozni:

```console
vi charts/java/values.yaml
```

Keresse meg a *service.type* tulajdonságot, és frissítse az értéket a *ClusterIP* való *terheléselosztó*, ahogyan az a következő sűrített példához:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Mentse és zárja be a fájlt, majd a `draft up` kattintva futtassa újra az alkalmazást:

```console
draft up
```

A szolgáltatás egy nyilvános IP-címének visszaadásához néhány percet vesz igénybe. A folyamat állapotának monitorozásához használja a `kubectl get service` parancsot a *watch* paramétert:

```console
kubectl get service --watch
```

Kezdetben a *EXTERNAL-IP* számára jelenik meg a szolgáltatás *függőben lévő*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Miután az EXTERNAL-IP cím a *függőben lévő* egy IP-címet használja `Control+C` leállítani a `kubectl` tekintse meg a folyamat:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Az alkalmazás megtekintéséhez tallózással keresse meg a terheléselosztó a külső IP-címét `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Az alkalmazás ismételt futtatásával

Most, hogy a Draft lett konfigurálva, és az alkalmazás fut, a Kubernetes, akkor vannak beállítva a kód iteráció. Minden alkalommal, amikor a vizsgálni kívánt frissített code, és futtassa a `draft up` parancsot a futó alkalmazás frissítésére.

Ebben a példában a Java-mintaalkalmazás a megjelenített szöveg módosításához frissítse. Nyissa meg a *Hello.java* fájlt:

```console
vi src/main/java/helloworld/Hello.java
```

A kimeneti szöveg megjelenítéséhez frissítse *Hello World, vagyok az aks-ben a Java!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

A frissített alkalmazás megtekintéséhez curl újra a terheléselosztó IP-címe:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>További lépések

A Draft használatával kapcsolatos további információkért lásd: a Draft dokumentáció a Githubon.

> [!div class="nextstepaction"]
> [Vázlat dokumentációja][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
