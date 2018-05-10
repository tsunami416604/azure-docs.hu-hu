---
title: AKS és az Azure-tárolót beállításjegyzék vázlat használata
description: AKS és az Azure-tárolót beállításjegyzék vázlat használata
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f474921acbd3a5082afea03dd982bcfa9c52dcda
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Vázlat használata Azure Kubernetes szolgáltatásnál (AKS)

Vázlat egy nyílt forráskódú eszköz, amely segít tartalmazhat, és ezek Kubernetes fürtben, így a fejlesztési ciklus – a "belső hurkot" koncentrált fejlesztési összpontosíthat szabadon tároló üzembe helyezése. Vázlat működik, a kód még fejlesztés alatt, de előtt véglegesítése verziókövetésbe. Vázlat gyorsan központilag telepítheti az alkalmazás Kubernetes kód változás. Kapcsolatos további információkért lásd: a [dokumentációnkat a Githubon vázlatszintű][draft-documentation].

Ez a dokumentum adatokat AKS Kubernetes fürtben, vázlat használata.

## <a name="prerequisites"></a>Előfeltételek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezek az elemek van szüksége, tekintse meg a [AKS gyors üzembe helyezés][aks-quickstart].

Meg kell egy titkos Docker beállításjegyzék Azure tároló beállításjegyzék (ACR) is. ACR példány telepítésével kapcsolatos útmutatásért lásd: a [Azure tároló beállításjegyzék gyors üzembe helyezés][acr-quickstart].

Helm is telepíteni kell a AKS fürtben. Helm telepítésével kapcsolatos további információkért lásd: [használata Helm Azure Kubernetes szolgáltatás (AKS)][aks-helm].

Végül, telepítenie kell a [Docker](https://www.docker.com).

## <a name="install-draft"></a>Vázlat telepítése

A Vázlat CLI egy ügyfelet, amelyben a fejlesztői rendszeren fut, és lehetővé teszi, hogy quicky kód telepítése Kubernetes fürtbe.

> [!NOTE]
> Ha telepítette a vázlatként megjelölt verziót 0.12 előtt, kell először törölnie vázlat a fürt használt `helm delete --purge draft` , és távolítsa el a helyi konfigurációs futtatásával `rm -rf ~/.draft`. Ha MacOS, `brew upgrade draft`.

A Vázlat parancssori felület telepítése egy Mac használata `brew`. További telepítési beállítások talál, a [vázlat telepítése útmutató][install-draft].

```console
brew tap azure/draft
brew install draft
```

Most már a piszkozat a inicializálni a `draft init` parancsot.

```console
draft init
```

## <a name="configure-draft"></a>Vázlat konfigurálása

Vázlat helyileg a tároló lemezképet állít össze, és ezután vagy telepíti őket (esetén Minikube) a helyi beállításjegyzékből, vagy meg kell adnia a kép beállításjegyzék használatára. Ez a példa az Azure tároló beállításjegyzék (ACR), így a AKS fürt és a ACR beállításjegyzék közötti megbízhatósági kapcsolat létrehozása és a tároló leküldése ACR tervezet konfigurálnia kell.

### <a name="create-trust-between-aks-cluster-and-acr"></a>AKS fürt és ACR közötti megbízhatósági kapcsolat létrehozása

Egy AKS fürt és az ACR beállításjegyzék közötti megbízhatósági kapcsolatot létesíteni az Azure Active Directory szolgáltatás egyszerű használt AKS ACR tárház hatókörű hozzáadva a közreműködői szerepkör módosítása. Ehhez futtassa a következő parancsokat, cseréje _&lt;aks-rg-neve&gt;_ és _&lt;aks fürtnév&gt;_ erőforráscsoport és annak nevét a AKS fürt, és _&lt;acr-rg-név&gt;_ és _&lt;acr-tárház-neve&gt;_ az ACR erőforrás csoport és a tárház nevét a tárház kívánt megbízhatósági kapcsolat létrehozása.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(Ezeket a lépéseket és más hitelesítési mechanizmusok ACR eléréséhez [hitelesítése az ACR](../container-registry/container-registry-auth-aks.md).)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Vázlat leküldése és telepítése az ACR konfigurálása

Most, hogy AKS és ACR közötti megbízhatósági kapcsolat áll fenn, akkor a következő lépéseket a AKS fürtről ACR használatának engedélyezése.
1. A tervezett konfiguráció `registry` futtatásával érték `draft config set registry <registry name>.azurecr.io`, ahol _&lt;beállításjegyzék neve&lt;_ a ACR beállításjegyzék neve.
2. Jelentkezzen be a ACR beállításjegyzék futtatásával `az acr login -n <registry name>`.

Mivel most jelentkezett be helyileg történő ACR és AKS és ACR megbízhatósági kapcsolatot létrehozott, nincs jelszó vagy titkos kulcsok szükségesek leküldése vagy lekérés ACR a AKS be. Hitelesítés az Azure Resource Manager szintjén, Azure Active Directory használatával történik.

## <a name="run-an-application"></a>Alkalmazás futtatása

A Vázlat tárház több alkalmazásokra, amelyek segítségével bemutató Vázlat tartalmazza. A tárház klónozott másolatának létrehozása.

```console
git clone https://github.com/Azure/draft
```

Módosítsa a Java-példák könyvtárában.

```console
cd draft/examples/example-java/
```

Használja a `draft create` parancsot a folyamat elindításához. Ezzel a paranccsal létrejön az összetevőket, futtassa az alkalmazást egy Kubernetes fürtben használt. Ezek az elemek közé tartozik egy Dockerfile Helm diagramot, és egy `draft.toml` fájl, amely a Vázlat konfigurációs fájl.

```console
draft create
```

Kimenet:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Az alkalmazás futtatásához Kubernetes fürtön, használja a `draft up` parancsot. A parancs létrehozza a Dockerfile tároló lemezkép létrehozása, leküldi a kép ACR, és végül telepíti a Helm diagram AKS a az alkalmazás indításához.

Az első alkalommal fut, kérdez le, és húzza a tároló kép hosszabb időt vehet igénybe; Ha az alap rétegek gyorsítótárba kerüljenek-e, igénybe vett idő jelentős mértékben csökken.

```console
draft up
```

Kimenet:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás teszteléséhez a `draft connect` parancsot. A parancs proxyk, lehetővé téve a biztonságos helyi kapcsolat Kubernetes fogyasztanak csatlakozni. Amikor végzett, az alkalmazás a megadott URL-címen érhető el.

Bizonyos esetekben is igénybe vehet néhány percet, amíg a tároló kép le és indítsa el az alkalmazást. Ha arra vonatkozó hibaüzenetet kap, ha az alkalmazás elérésének, újból kapcsolódni.

```console
draft connect
```

Kimenet:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Az alkalmazás most teszteléséhez tallózással http://localhost:46143 (az előző példában; az a port eltérő is lehet). Amikor befejeződött az alkalmazás használatát tesztelés `Control+C` leállítja a proxykapcsolatot.

> [!NOTE]
> Használhatja a `draft up --auto-connect` felépítéséhez és az alkalmazás központi telepítése, és azonnal csatlakozni az első futó tárolót, hogy ellenőrizze az iterációs parancs még gyorsabban ciklus.

## <a name="expose-application"></a>Teszi közzé az alkalmazást

Ha egy alkalmazás tesztelése a Kubernetes, érdemes lehet elérhetővé tenni az alkalmazást az interneten. Ezt megteheti egy Kubernetes szolgáltatással típussal rendelkező [terheléselosztó] [ kubernetes-service-loadbalancer] vagy egy [érkező vezérlő][kubernetes-ingress]. Ez a dokumentum adatokat Kubernetes szolgáltatást használ.


Először a Vázlat csomag annak meghatározása, hogy frissíteni kell a szolgáltatás olyan típusú `LoadBalancer` kell létrehozni. Ehhez az szükséges, frissítse a szolgáltatás típusa a `values.yaml` fájlt.

```console
vi charts/java/values.yaml
```

Keresse meg a `service.type` tulajdonság, és frissítse az értéket a `ClusterIP` való `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Futtatás `draft up` újra futtatni az alkalmazást.

```console
draft up
```

A szolgáltatás egy nyilvános IP-cím vissza néhány percet is igénybe vehet. Figyelheti a folyamat használja a `kubectl get service` egy figyelési parancsot.

```console
kubectl get service -w
```

Kezdetben a *külső IP-* megjelenik a szolgáltatás `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Miután az EXTERNAL-IP cím `pending` állapotról `IP address` állapotúra változik, a `Control+C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címhez.

```console
curl 52.175.224.118
```

Kimenet:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Az alkalmazás többször

Most, hogy vázlat van konfigurálva, és az alkalmazás Kubernetes fut, a kód iterációs be. Minden alkalommal, amikor szeretné tesztelni frissített kódját, futtassa a `draft up` parancs frissítjük a futó alkalmazást.

Ebben a példában frissítse a Java hello world alkalmazásról.

```console
vi src/main/java/helloworld/Hello.java
```

Frissítse a Hello World szöveget.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Futtassa a `draft up --auto-connect` parancsot arra, amint egy pod válaszolni készen áll, telepítse újra az alkalmazást.

```console
draft up --auto-connect
```

Kimenet

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Végül tekintse meg az alkalmazás a frissítéseket.

```console
curl 52.175.224.118
```

Kimenet:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>További lépések

Vázlat használatával kapcsolatos további információkért az dokumentációjában vázlat a Githubon.

> [!div class="nextstepaction"]
> [Vázlat dokumentáció][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
