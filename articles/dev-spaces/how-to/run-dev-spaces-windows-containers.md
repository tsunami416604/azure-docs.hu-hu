---
title: Windows-tárolók használata
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Az Azure Dev Spaces futtatása meglévő fürtön Windows-tárolókkal
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Windows-tárolók
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240486"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Windows-tárolók használata az Azure Dev Spaces használatával

Az Azure Dev Spaces új és meglévő Kubernetes-névtereken is engedélyezhető. Az Azure Dev Spaces linuxos tárolókon futó és műszeres szolgáltatásokat fog futtatni. Ezek a szolgáltatások az azonos névtérben windowsos tárolókon futó alkalmazásokkal is kommunikálhatnak. Ez a cikk bemutatja, hogyan használhatja a fejlesztői tárolók szolgáltatások meglévő Windows-tárolókkal rendelkező névtérben való futtatásához. Jelenleg nem lehet hibakeresést vagy windowsos tárolókhoz csatolni az Azure Dev Spaces használatával.

## <a name="set-up-your-cluster"></a>A fürt beállítása

Ez a cikk feltételezi, hogy már rendelkezik egy fürtlinuxos és Windows-csomópontkészletekkel. Ha Linux és Windows csomópontkészletekkel rendelkező fürtöt kell létrehoznia, kövesse az [itt][windows-container-cli]található utasításokat.

Csatlakozzon a fürthöz [a kubectl][kubectl], a Kubernetes parancssori ügyfél használatával. Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példa kimeneti egy Windows- és Linux-csomóddal rendelkező fürtöt mutat be. A folytatás előtt győződjön meg arról, hogy az állapot *készen áll* az egyes csomópontokhoz.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Alkalmazzon [egy romlottat][using-taints] a Windows-csomópontokra. A Windows-csomópontok on a windowsos csomópontok on-val megakadályozza, hogy a fejlesztői tárolók linuxos tárolók at a Windows-csomópontokon való futtatásra ütemezze. A következő parancspélda parancs az előző példából származó *aksnpwin987654* Windows-csomópontra alkalmaz egy romlottat.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Ha egy csomópontra alkalmaz egy romlottat, konfigurálnia kell egy megfelelő tűrést a szolgáltatás központi telepítési sablonjában a szolgáltatás futtatásához az adott csomóponton. A mintaalkalmazás már konfigurálva van az előző parancsban konfigurált értékhez [tartozó tűréssel.][sample-application-toleration-example]

## <a name="run-your-windows-service"></a>A Windows-szolgáltatás futtatása

Futtassa a Windows-szolgáltatást az AKS-fürtön, és ellenőrizze, hogy *futó* állapotban van-e. Ez a cikk egy [mintaalkalmazás][sample-application] segítségével mutatja be a fürtön futó Windows- és Linux-szolgáltatást.

Klónozza a mintaalkalmazást a GitHubról, és navigáljon a `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` könyvtárba:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A mintaalkalmazás a [Helm 3][helm-installed] segítségével futtatja a Windows-szolgáltatást a fürtön. Keresse meg `charts` a könyvtárat, és használja a Helm-szolgáltatást a Windows-szolgáltatás futtatásával:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

A fenti parancs a Helm segítségével futtatja a Windows-szolgáltatást a *fejlesztési* névtérben. Ha nem rendelkezik *fejlesztői*nevű névtérrel, akkor létrejön.

A `kubectl get pods` paranccsal ellenőrizze, hogy a Windows-szolgáltatás fut-e a fürtben. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure dev-szóközök engedélyezése

Engedélyezze a fejlesztői szóközöket a Windows-szolgáltatás futtatásához használt névtérben. A következő parancs engedélyezi a fejlesztői szóközöket a *fejlesztői* névtérben:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>A Windows-szolgáltatás frissítése a fejlesztői szóközökhez

Ha engedélyezi a fejlesztői szóközöket egy meglévő névtérben, amely nek már futnak, alapértelmezés szerint a fejlesztői tárolók megpróbálnak az adott névtérben futó új tárolókat. A fejlesztői tárolók is megpróbálnak instrument minden új tárolók létre a szolgáltatás már fut a névtérben. Ha meg szeretné akadályozni, hogy a fejlesztői szóközök a névtérben futó tárolót instrumentingezzenek, adja hozzá a *proxy nélküli* fejlécet a `deployment.yaml`hoz.

Hozzáadás `azds.io/no-proxy: "true"` `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` a fájlhoz:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

A `helm list` Windows-szolgáltatás központi telepítésének felsorolására használható:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

A fenti példában a központi telepítés neve *windows-service.* Frissítse a Windows-szolgáltatást az `helm upgrade`új konfigurációval a következő használatával:

```cmd
helm upgrade windows-service . --namespace dev
```

A frissítés `deployment.yaml`óta a fejlesztői tárolóhelyek nem próbálja meg a szolgáltatás eszközét.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Linux-alkalmazás futtatása az Azure Dev Spaces segítségével

Keresse meg `webfrontend` a könyvtárat, és használja a és `azds prep` `azds up` a parancsokat a Linux-alkalmazás futtatásához a fürtön.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

A `azds prep --enable-ingress` parancs létrehozza a Helm-diagramot és a Dockerfiles-fájlokat az alkalmazáshoz.

> [!TIP]
> A [Dockerfile és helm diagram](../how-dev-spaces-works-prep.md#prepare-your-code) a projekt azure dev spaces a kód létrehozásához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné, hogyan a projekt épül, és fut.

A `azds up` parancs a szolgáltatást a névtérben futtatja.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Az azds up parancs kimenetében megjelenő nyilvános URL-cím megnyitásával láthatja a futó szolgáltatást. Ebben a példában a `http://dev.webfrontend.abcdef0123.eus.azds.io/`nyilvános URL-cím . Keresse meg a szolgáltatást a böngészőben, és kattintson a *Körülbelül* a tetején. Ellenőrizze, hogy megjelenik-e egy üzenet a *mywebapi* szolgáltatásból, amely a tároló által használt Windows-verziót tartalmazza.

![Mintaalkalmazás a mywebapi Windows-verzióját megjelenítő alkalmazásból](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Csapatfejlesztés az Azure Dev Spaces-ben][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
