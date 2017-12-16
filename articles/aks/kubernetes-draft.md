---
title: "AKS és az Azure-tárolót beállításjegyzék vázlat használata"
description: "AKS és az Azure-tárolót beállításjegyzék vázlat használata"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a77e214c1138ce936b2ec6c521950704e5beb3ff
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Az Azure Tárolószolgáltatás (AKS) vázlat használata

Vázlat egy nyílt forráskódú eszköz, amely segít a csomag és a kódra Kubernetes fürtben. Vázlat célja a fejlesztési iterációs ciklus; mivel a kód még fejlesztés alatt, de előtt véglegesítése verziókövetésbe. Vázlat gyorsan központilag telepítheti az alkalmazás Kubernetes kód változás. Kapcsolatos további információkért lásd: a [dokumentációnkat a Githubon vázlatszintű][draft-documentation].

Ez a dokumentum adatokat AKS Kubernetes fürtben, vázlat használata.

## <a name="prerequisites"></a>Előfeltételek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezek az elemek van szüksége, tekintse meg a [AKS gyors üzembe helyezés][aks-quickstart].

Meg kell egy titkos Docker beállításjegyzék Azure tároló beállításjegyzék (ACR) is. ACR példány telepítésével kapcsolatos útmutatásért lásd: a [Azure tároló beállításjegyzék gyors üzembe helyezés][acr-quickstart].

## <a name="install-helm"></a>Helm telepítése

A Helm CLI ügyfél, amely a fejlesztői rendszeren fut, és lehetővé teszi indítása, leállítása és kezelheti az alkalmazásokat az Helm diagramok.

Telepítse a Helm parancssori felület Mac, használja a `brew`. További telepítési lehetőségekről, tekintse meg [telepítése Helm][install-helm].

```console
brew install kubernetes-helm
```

Kimenet:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Vázlat telepítése

A Vázlat CLI egy ügyfelet, amelyben a fejlesztői rendszeren fut, és lehetővé teszi, hogy quicky kód telepítése Kubernetes fürtbe.

A Vázlat parancssori felület telepítése egy Mac használata `brew`. További telepítési beállítások talál, a [vázlat telepítése útmutató][install-draft].

```console
brew install draft
```

Kimenet:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Vázlat konfigurálása

Vázlat konfigurálásakor tároló beállításjegyzékbeli kell megadni. Ebben a példában az Azure-tároló beállításjegyzék szolgál.

A következő parancsot a neve és a ACR példány bejelentkezési kiszolgáló nevét. A parancs frissíti a ACR példányát tartalmazó erőforráscsoport nevét.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

A ACR példány jelszó is szükséges.

A következő parancsot a ACR jelszó vissza. A parancs frissíti az ACR-példány nevét.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

A Vázlat inicializálni a `draft init` parancsot.

```console
draft init
```

A folyamat során kér a tároló beállításjegyzék hitelesítő adatokat. Egy Azure-tároló beállításjegyzék használata esetén a beállításjegyzék URL ACR bejelentkezési kiszolgálónév, a felhasználónév az ACR példány nevét, és a jelszó pedig a ACR jelszót.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

Művelet befejeződése után a vázlat a Kubernetes fürtben lett konfigurálva, és használatra kész.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Alkalmazás futtatása

A Vázlat tárház több alkalmazásokra, amelyek segítségével bemutató Vázlat tartalmazza. A tárház klónozott másolatának létrehozása.

```console
git clone https://github.com/Azure/draft
```

Módosítsa a Java-példák könyvtárában.

```console
cd draft/examples/java/
```

Használja a `draft create` parancsot a folyamat elindításához. Ezzel a paranccsal létrejön az összetevőket, futtassa az alkalmazást egy Kubernetes fürtben használt. Ezek az elemek közé tartozik egy Dockerfile Helm diagramot, és egy `draft.toml` fájl, amely a Vázlat konfigurációs fájl.

```console
draft create
```

Kimenet:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Az alkalmazás futtatásához Kubernetes fürtön, használja a `draft up` parancsot. Ez a parancs az alkalmazás kódja és konfigurációs fájljait a Kubernetes fürt feltölt. Azután tároló lemezkép létrehozása Dockerfile fut, leküldéses értesítések a lemezképet a tároló beállításjegyzék, és végül futtatja a Helm diagram az alkalmazás indításához.

```console
draft up
```

Kimenet:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás teszteléséhez a `draft connect` parancsot. A parancs proxyk, lehetővé téve a biztonságos helyi kapcsolat Kubernetes fogyasztanak csatlakozni. Amikor végzett, az alkalmazás a megadott URL-címen érhető el.

Bizonyos esetekben is igénybe vehet néhány percet, amíg a tároló kép le és indítsa el az alkalmazást. Ha arra vonatkozó hibaüzenetet kap, ha az alkalmazás elérésének, újból kapcsolódni.

```console
draft connect
```

Kimenet:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Amikor befejeződött az alkalmazás használatát tesztelés `Control+C` leállítja a proxykapcsolatot.

## <a name="expose-application"></a>Teszi közzé az alkalmazást

Ha egy alkalmazás tesztelése a Kubernetes, érdemes lehet elérhetővé tenni az alkalmazást az interneten. Ezt megteheti egy Kubernetes szolgáltatással típussal rendelkező [terheléselosztó] [ kubernetes-service-loadbalancer] vagy egy [érkező vezérlő][kubernetes-ingress]. Ez a dokumentum adatokat Kubernetes szolgáltatást használ.


Először a Vázlat csomag annak meghatározása, hogy frissíteni kell a szolgáltatás olyan típusú `LoadBalancer` kell létrehozni. Ehhez az szükséges, frissítse a szolgáltatás típusa a `values.yaml` fájlt.

```console
vi chart/java/values.yaml
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
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Miután a külső IP-cím megváltozott `pending` való egy `IP address`, használja `Control+C` kubectl figyelési megszakításához.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címet.

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
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Futtassa a `draft up` parancs futtatásával telepítse újra az alkalmazást.

```console
draft up
```

Kimenet

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Végül tekintse meg az alkalmazás a frissítéseket.

```console
curl 52.175.224.118
```

Kimenet:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>Következő lépések

Vázlat használatával kapcsolatos további információkért az dokumentációjában vázlat a Githubon.

> [!div class="nextstepaction"]
> [Vázlat dokumentáció][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[install-helm]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[kubernetes-ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md