---
title: Csapatfejlesztés a Kubernetes-en
services: azure-dev-spaces
ms.date: 01/22/2020
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan lehet a Kubernetes csapatfejlesztését tárolókkal és mikroszolgáltatásokkal az Azure Dev Spaces segítségével elvégezni
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 0fe177db420913e5d68807dd803df791653c0914
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244938"
---
# <a name="quickstart-team-development-on-kubernetes---azure-dev-spaces"></a>Rövid útmutató: Csapatfejlesztés a Kubernetesen – Azure Dev Spaces

Ebből az útmutatóból a következőket tudhatja meg:

- Állítsa be az Azure Dev Spaces egy felügyelt Kubernetes-fürtön az Azure-ban.
- Üzembe helyezhet egy több mikroszolgáltatásokat tartalmazó nagy alkalmazást egy fejlesztői térbe.
- Tesztelje egyetlen mikroszolgáltatás egy elszigetelt fejlesztési térben a teljes alkalmazás környezetében.

![Az Azure Dev Spaces csapatfejlesztése](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Az Azure CLI telepítve van.](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3 telepítve][helm-installed].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

AKS-fürtöt támogatott régióban kell [létrehoznia.][supported-regions] Az alábbi parancsok létrehoznak egy *SamiresourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AKS-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev spaces engedélyezése az AKS-fürtön

A `use-dev-spaces` paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat. Az alábbi parancs engedélyezi a Fejlesztői szóközöket a *MyResourceGroup* csoport *MyAKS-fürtén,* és létrehoz egy *fejlesztői területet.*

> [!NOTE]
> A `use-dev-spaces` parancs az Azure Dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure Dev Spaces CLI nem telepíthető az Azure Cloud Shellben.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Mintaalkalmazás kódjának beszereznie

Ebben a cikkben az [Azure Dev Spaces-ek kerékpármegosztásminta-alkalmazást](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használja az Azure Dev Spaces használatával történő bemutatáshoz.

Klónozza az alkalmazást a GitHubról, és navigáljon a könyvtárába:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>A gazdagéputótag *beolvasása*

A `azds show-context` parancs segítségével jelenítse meg a *gazdagép hez*való állomásutótagot.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>A Helm-diagram frissítése az HostSuffix-el

Nyissa [meg a diagramokat/values.yaml fájlt,](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) és cserélje le az `<REPLACE_ME_WITH_HOST_SUFFIX>` összes példányt a korábban beolvasott HostSutótag értékre. Mentse a módosításokat, és zárja be a fájlt.

## <a name="run-the-sample-application-in-kubernetes"></a>A mintaalkalmazás futtatása Kubernetes ben

A kubernetes-alapú mintaalkalmazás futtatásához szükséges parancsok egy meglévő folyamat részét képezik, és nem függenek az Azure Dev Spaces eszközelemtől. Ebben az esetben a Helm a mintaalkalmazás futtatásához használt eszköz, de más eszközök kelthetők a teljes alkalmazás futtatásához egy fürt névterén. A Helm parancsok a korábban létrehozott *dev* nevű fejlesztői területet célozzák meg, de ez a fejlesztői terület is Kubernetes-névtér. Ennek eredményeképpen a fejlesztői tereket más eszközök is megcélozhatják, mint más névterek.

Az Azure Dev Spaces segítségével csapatfejlesztés után egy alkalmazás fut a fürtben, függetlenül attól, hogy az üzembe helyezéshez használt.

A `helm install` paranccsal beállíthatja és telepítheti a mintaalkalmazást a fürtön.

```cmd
cd charts/
helm install bikesharingsampleappsampleapp . --dependency-update --namespace dev --atomic
```

A `helm install` parancs több percig is eltarthat. Miután a mintaalkalmazás telepítve van a fürtön, és mivel `azds list-uris` a fejlesztői terek engedélyezve van a fürtön, a paranccsal jelenítse meg a mintaalkalmazás URL-címeit a jelenleg kiválasztott *fejlesztői környezetben.*

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Keresse meg a *bikesharingweb* szolgáltatást a `azds list-uris` nyilvános URL megnyitásával a parancsból. A fenti példában a *bikesharingweb* szolgáltatás `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`nyilvános URL-címe . Válassza ki *az Aurelia Briggs (ügyfél)* lehetőséget felhasználóként. Ellenőrizze, hogy látja-e a *Hi Aurelia Briggs szöveget | Jelentkezz ki* a csúcson.

![Azure Dev Spaces kerékpármegosztási mintaalkalmazás](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Gyermekfejlesztői terek létrehozása

A `azds space select` parancs segítségével két gyermektereket hozhat létre a *fejlesztési területen:*

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

A fenti parancsok két gyermektereket hoznak létre *az azureuser1* és az *azureuser2*nevű *fejlesztői terület* alatt. Ez a két gyermekter külön fejlesztői téraz *azureuser1* és *az azureuser2* a mintaalkalmazás módosításaihoz.

A `azds space list` parancs segítségével sorolja fel az összes fejlesztői tereket, és erősítse meg a *fejlesztési/azureuser2* ki van jelölve.

```cmd
$ azds space list
   Name            DevSpacesEnabled
-  --------------  ----------------
   default         False
   dev             True
   dev/azureuser1  True
*  dev/azureuser2  True
```

A `azds list-uris` segítségével a mintaalkalmazás URL-címeit jelenítheti meg a jelenleg kijelölt területen, amely *a fejlesztési/azureuser2.*

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Győződjön meg arról, `azds list-uris` hogy a parancs által megjelenített URL-címek rendelkeznek az *azureuser2.s.dev* előtaggal. Ez az előtag megerősíti, hogy a kijelölt aktuális terület az *azureuser2*, amely a *fejlesztési*terület gyermeke.

Keresse meg a *bikesharingweb* szolgáltatás a *fejlesztési/azureuser2* fejlesztői `azds list-uris` terület megnyitásával a nyilvános URL-t a parancsból. A fenti példában a *bikesharingweb* szolgáltatás `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`nyilvános URL-címe . Válassza ki *az Aurelia Briggs (ügyfél)* lehetőséget felhasználóként. Ellenőrizze, hogy látja-e a *Hi Aurelia Briggs szöveget | Jelentkezz ki* a csúcson.

## <a name="update-code"></a>Kód frissítése

Nyissa meg *a BikeSharingWeb/components/Header.js fájlt* egy szövegszerkesztővel, és módosítsa a [span elem szövegét a `userSignOut` className .](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16)

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Mentse a módosításokat, és zárja be a fájlt.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>A frissített bikesharingweb szolgáltatás létrehozása és futtatása a *fejlesztési/azureuser2* fejlesztési területen

Keresse meg a *BikeSharingWeb/* `azds up` könyvtárat, és futtassa a parancsot.

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

Ez a parancs létrehozza és futtatja a *bikesharingweb* szolgáltatást a *fejlesztési/azureuser2* fejlesztési területen. Ez a szolgáltatás a *fejlesztési területen* futó *bikesharingweb* szolgáltatás mellett fut, és csak az *azureuser2.s* URL-előtaggal rendelkező kérelmekhez használható. A szülő- és gyermekfejlesztési terek közötti útválasztás működéséről további információt az [Azure dev spaces működésének és konfigurálásának című témakörben talál.](how-dev-spaces-works.md)

Keresse meg a *bikesharingweb* szolgáltatás a *fejlesztési/azureuser2* fejlesztési terület megnyitásával `azds up` a nyilvános URL-t jelenik meg a kimeneta a parancs. Válassza ki *az Aurelia Briggs (ügyfél)* lehetőséget felhasználóként. Ellenőrizze, hogy a frissített szöveg a jobb felső sarokban látható-e. Előfordulhat, hogy frissítenie kell az oldalt, vagy törölnie kell a böngésző gyorsítótárát, ha nem látja azonnal ezt a változást.

![Frissítve az Azure Dev Spaces kerékpármegosztási mintaalkalmazása](media/quickstart-team-development/bikeshare-update.png)

> [!NOTE]
> Amikor futás `azds up`közben a szolgáltatáshoz navigál, a HTTP-kérelem nyomkövetései is megjelennek a `azds up` parancs kimenetén. Ezek a nyomok segíthetnek a szolgáltatás hibaelhárításában és hibakeresésében. Ezeket a nyomkövetéseket a futás során `--disable-http-traces` letilthatja. `azds up`

## <a name="verify-other-dev-spaces-are-unchanged"></a>A többi fejlesztői ter változatlanségének ellenőrzése

Ha `azds up` a parancs még fut, nyomja *le a Ctrl+c billentyűkombinációt.*

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

Keresse meg a *bikesharingweb* *fejlesztői* verzióját a böngészőjében, válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, és ellenőrizze, hogy az eredeti szöveg a jobb felső sarokban látható-e. Ismételje meg ezeket a lépéseket a *fejlesztési/azureuser1* URL-címhasználatával. Figyelje meg, hogy a módosítások csak a *bikesharingweb* *fejlesztői/azureuser2* verziójára vonatkoznak. A *fejlesztési/azureuser2* módosításainak elkülönítése lehetővé teszi *az azureuser2* számára a módosításokat az *azureuser1*befolyásolása nélkül.

Ha ezeket a változásokat a *fejlesztési* és *fejlesztési/azureuser1*tükröződéséhez a csapat meglévő munkafolyamatát vagy CI/CD-folyamatát kell követnie. Ez a munkafolyamat például magában foglalhatja a verziókövető rendszer rekedésének véglegesítését és a frissítés ci/CD-folyamat vagy eszközkezelés, például a Helm használatával történő telepítését.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan segít az Azure Dev Spaces az összetettebb alkalmazások fejlesztésében több tároló között, és hogyan egyszerűsítheti le az együttműködésen alapuló fejlesztést a kód különböző verzióinak vagy ágainak különböző terekben való kezelésével.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-nodejs.md)

[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
