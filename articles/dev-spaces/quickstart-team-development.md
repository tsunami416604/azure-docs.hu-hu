---
title: Csoportmunka-fejlesztés a Kubernetes-on
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan végezheti el a Team Kubernetes-fejlesztést tárolókkal és szolgáltatásokkal az Azure dev Spaces használatával
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: d2b31cce6604cef31de6f034566ebd46a4e92750
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721591"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Gyors útmutató: csoportmunka-fejlesztés a Kubernetes-ben – Azure dev Spaces

Ebből az útmutatóból a következőket tudhatja meg:

- Azure fejlesztői tárhelyek beállítása egy felügyelt Kubernetes-fürtön az Azure-ban.
- Helyezzen üzembe egy nagyméretű alkalmazást több szolgáltatással egy fejlesztői tárhelyen.
- Egyetlen, a teljes alkalmazás kontextusában lévő, elkülönített fejlesztési területen tesztelheti a szolgáltatást.

![Az Azure dev Spaces csapatának fejlesztése](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- A [Helm 3 telepítve van][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

A [támogatott régiókban][supported-regions]létre kell hoznia egy AK-fürtöt. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. Az alábbi parancs lehetővé teszi a dev Spaces használatát a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *dev*nevű fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure dev Spaces CLI nem telepíthető a Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Minta alkalmazás kódjának beolvasása

Ebben a cikkben az [Azure dev Spaces Bike Sharing Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használatával mutatjuk be az Azure dev Spaces szolgáltatást.

Az alkalmazás klónozása a GitHubról, majd a címtárba való navigálása:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>A *fejlesztői* HostSuffix beolvasása

A `azds show-context` parancs használatával jelenítse meg a HostSuffix a *fejlesztői*számára.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>A Helm-diagram frissítése a HostSuffix

Nyissa meg a [diagramok/értékek. YAML](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) , és cserélje le a `<REPLACE_ME_WITH_HOST_SUFFIX>` összes példányát a korábban lekért HostSuffix értékre. Mentse a módosításokat, és zárjuk be a fájlt.

## <a name="run-the-sample-application-in-kubernetes"></a>A minta alkalmazás futtatása a Kubernetes-ben

A Kubernetes alkalmazáshoz való futtatására szolgáló parancsok egy meglévő folyamat részét képezik, és nincs függőségük az Azure dev Spaces-eszközökön. Ebben az esetben a Helm a minta alkalmazás futtatásához használt eszköz, de más eszközök is használhatók a teljes alkalmazás futtatására egy fürtön belüli névtérben. A Helm-parancsok célja a korábban létrehozott *dev* -terület, de ez a fejlesztői terület is Kubernetes névtér. Ennek eredményeképpen a dev Spaces más eszközökkel is megcélozható, mint a többi névtér.

Az Azure dev Spaces for Team-fejlesztést akkor használhatja, ha egy alkalmazás egy fürtön fut, függetlenül az üzembe helyezéséhez használt eszköztől.

A `helm install` parancs használatával állítsa be és telepítse a minta alkalmazást a fürtön.

```cmd
cd charts/
helm install bikesharing . --dependency-update --namespace dev --atomic
```

A `helm install` parancs végrehajtása több percet is igénybe vehet. Miután a minta alkalmazást telepítette a fürtön, és mivel a fürtön engedélyezve vannak a dev Spaces, a `azds list-uris` parancs használatával jelenítse meg a jelenleg kiválasztott *fejlesztői* alkalmazás URL-címeit.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a `azds list-uris` parancs nyilvános URL-címének megnyitásával. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget a felhasználóként. Ellenőrizze, hogy megjelenik-e a *Hi Aurelia Briggs szövege | Kijelentkezés* a csúcsra.

![Az Azure dev Spaces Bike Sharing minta alkalmazása](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Gyermek fejlesztői szóközök létrehozása

A `azds space select` parancs használatával hozzon létre két alárendelt szóközt a *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

A fenti parancsok két gyermek szóközt hoznak létre a *azureuser1* és a *azureuser2*nevű *dev* alatt. Ez a két gyermekobjektum különböző fejlesztői helyeket képvisel a fejlesztők *azureuser1* és *azureuser2* , amelyek a minta alkalmazás módosítására használhatók.

Az `azds space list` parancs használatával jelölje ki az összes fejlesztői helyet, és erősítse meg a *dev/azureuser2* elemet.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

A `azds list-uris` használatával jelenítse meg a minta alkalmazás URL-címeit a jelenleg kijelölt, a *dev/azureuser2*területen.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Győződjön meg arról, hogy az `azds list-uris` parancs által megjelenített URL-címek rendelkeznek a *azureuser2. s. dev* előtaggal. Ez az előtag megerősíti, hogy a kiválasztott aktuális terület a *azureuser2*, amely a *fejlesztési*gyermek.

Nyissa meg a *bikesharingweb* szolgáltatást a *dev/azureuser2* fejlesztői területhez a `azds list-uris` parancs nyilvános URL-címének megnyitásával. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget a felhasználóként. Ellenőrizze, hogy megjelenik-e a *Hi Aurelia Briggs szövege | Kijelentkezés* a csúcsra.

## <a name="update-code"></a>Kód frissítése

Nyissa meg a *BikeSharingWeb/Components/header. js fájlt* egy szövegszerkesztőben, és módosítsa a [span elem szövegét az `userSignOut` osztálynév](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16)használatával.

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Mentse a módosításokat, és zárjuk be a fájlt.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>A frissített bikesharingweb szolgáltatás létrehozása és futtatása a *dev/azureuser2* fejlesztői térben

Navigáljon a *BikeSharingWeb/* könyvtárhoz, és futtassa az `azds up` parancsot.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Ez a parancs létrehozza és futtatja a *bikesharingweb* szolgáltatást a *dev/azureuser2* fejlesztői térben. Ez a szolgáltatás a *dev* -ben futó *bikesharingweb* szolgáltatás mellett fut, és csak a *azureuser2. s* URL előtaggal rendelkező kérelmek esetében használatos. További információ arról, hogyan működik az Útválasztás a szülő és gyermek fejlesztési területek között: [Hogyan működik az Azure dev Spaces, és hogyan van konfigurálva](how-dev-spaces-works.md).

Az `azds up` parancs kimenetében megjelenő nyilvános URL-cím megnyitásával nyissa meg a *bikesharingweb* szolgáltatást a *dev/azureuser2* fejlesztői területhez. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget a felhasználóként. Ellenőrizze, hogy a jobb felső sarokban látható-e a frissített szöveg. Előfordulhat, hogy frissítenie kell a lapot, vagy törölnie kell a böngésző gyorsítótárát, ha nem látja azonnal ezt a változást.

![Azure dev Spaces Bike-megosztási minta alkalmazás frissítve](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Amikor `azds up`futtatása közben navigál a szolgáltatáshoz, a rendszer a HTTP-kérelmek nyomkövetését is megjeleníti a `azds up` parancs kimenetében. Ezek a Nyomkövetések segítenek a szolgáltatás hibaelhárításában és hibakeresésében. A nyomkövetéseket letilthatja a `--disable-http-traces` használatával `azds up`futtatásakor.

## <a name="verify-other-dev-spaces-are-unchanged"></a>Más fejlesztői területek ellenőrzése nem változik

Ha a `azds up` parancs még fut, nyomja le a *CTRL + c*billentyűkombinációt.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Keresse meg a *bikesharingweb* *fejlesztői* verzióját a böngészőben, válassza az *Aurelia Briggs (ügyfél)* lehetőséget a felhasználóként, és ellenőrizze, hogy az eredeti szöveg jelenik-e meg a jobb felső sarokban. Ismételje meg ezeket a lépéseket a *dev/azureuser1* URL-címmel. Figyelje meg, hogy a módosítások csak a *bikesharingweb* *dev/azureuser2* verziójára vonatkoznak. A *dev/azureuser2* változásainak elkülönítése lehetővé teszi, hogy a *azureuser2* a *azureuser1*befolyásolása nélkül végezze el a módosításokat.

Ahhoz, hogy ezek a változások megjelenjenek a *fejlesztői* és *fejlesztői/azureuser1*, követnie kell a csapat meglévő munkafolyamat-vagy CI/CD-folyamatát. Előfordulhat például, hogy ez a munkafolyamat magában foglalja a verzió-ellenőrzési rendszer módosításának véglegesítését és a frissítés üzembe helyezését CI/CD-folyamat vagy eszközök, például a Helm használatával.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan segít az Azure Dev Spaces az összetettebb alkalmazások fejlesztésében több tároló között, és hogyan egyszerűsítheti le az együttműködésen alapuló fejlesztést a kód különböző verzióinak vagy ágainak különböző terekben való kezelésével.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
