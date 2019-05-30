---
title: A Kubernetes Azure-fejlesztési szóközzel csoportos fejlesztése
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Tárolók és mikroszolgáltatások az Azure-csapat a Kubernetes-fejlesztés
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
manager: jeconnoc
ms.openlocfilehash: 74ed1981ba6bf62413d7d7bfd51282eb04afd403
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393405"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Gyors útmutató: A Kubernetes Azure-fejlesztési szóközzel csoportos fejlesztése

Ebből az útmutatóból a következőket tudhatja meg:

- Állítsa be a Azure fejlesztési szóközt, egy felügyelt Kubernetes-fürtöt az Azure-ban.
- A mikroszolgáltatásokat több nagy alkalmazás telepítése egy fejlesztési területre.
- Egyetlen mikroszolgáltatást tesztelje az alkalmazás teljes környezetében egy elkülönített fejlesztési területen.

![Az Azure fejlesztői, szóközök csoportos fejlesztése](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Helm 2.13 vagy nagyobb telepített](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

Az AKS-fürt létre kell hoznia egy [támogatott régió][supported-regions]. Az alábbi parancsokkal nevű erőforráscsoport létrehozása *MyResourceGroup* és a egy AKS-fürt nevű *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

A *MyAKS* fürt is létrejön, egy csomópont használatával a *Standard_DS2_v2* méretétől, és az RBAC le van tiltva.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az AKS-fürt az Azure fejlesztési tárolóhelyek engedélyezése

Használja a `use-dev-spaces` parancs fejlesztési szóközt engedélyezni az AKS-fürt, és kövesse az utasításokat. Az alábbi parancs segítségével fejlesztési tárolóhelyek a *MyAKS* a fürt a *MyResourceGroup* csoportból, és létrehoz egy fejlesztési tárolóhelyet nevű *fejlesztési*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Minta az alkalmazáskód letöltése

Ebben a cikkben fogja használni a [mintaalkalmazás Azure fejlesztési tárolóhelyek kerékpárt megosztása](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) bemutatása, Azure-fejlesztési szóközzel.

Klónozza az alkalmazást a Githubról, és lépjen abba a könyvtárba:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>A HostSuffix a lekéréséhez *dev*

Használja a `azds show-context` megjelenítéséhez a HostSuffix a parancs *fejlesztési*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Frissítse a HostSuffix a Helm-diagram

Nyissa meg [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) , és cserélje le az összes példányát `<REPLACE_ME_WITH_HOST_SUFFIX>` a korábban kapott HostSuffix értékkel. Mentse a módosításokat, és zárja be a fájlt.

## <a name="run-the-sample-application-in-kubernetes"></a>A mintaalkalmazás futtatása a Kubernetesben

A mintaalkalmazás futtatása a Kubernetes-parancsokat egy létező folyamat részét képezik, és nincs függőség van az Azure fejlesztési tárolóhelyek azokat az eszközöket. Helm ebben az esetben ez a mintaalkalmazás futtatásához használt eszközökkel, de más eszközök a teljes alkalmazás futtatása egy névtérben a fürtön belül használható. A Helm parancsok céloz meg a fejlesztői terület nevű *fejlesztési* korábban létrehozott, de ez a fejlesztési terület is egy Kubernetes-névtér. Ennek eredményeképpen a fejlesztői, szóközök célpontja is lehet más eszközök, ugyanaz, mint más névterek.

Azure fejlesztői tárolóhelyek használhatják csoportos fejlesztéshez, miután egy alkalmazás fut egy fürtben, függetlenül az üzembe helyezéshez használt eszközökkel.

Használja a `helm init` és `helm install` parancsok és a mintaalkalmazás telepíthetők a fürtön.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic --wait
```

A `helm install` parancs több percet is igénybe vehet. A parancs kimenete a fürthöz, amikor elkészült, telepített minden szolgáltatás állapotát jeleníti meg:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic --wait

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

A minta a alkalmazás telepítve van a fürtön, és rendelkezésére fejlesztési tárolóhelyek engedélyezve van a fürtön, mivel a `azds list-uris` a mintaalkalmazáshoz az URL-címeinek megjelenítése a parancs *fejlesztési* jelenleg ki van jelölve.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Keresse meg a *bikesharingweb* nyissa meg a nyilvános URL-CÍMÉT a szolgáltatás a `azds list-uris` parancsot. A fenti példában, nyilvános URL-CÍMÉT a *bikesharingweb* szolgáltatás `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza ki *Aurelia Briggs (ügyfél)* felhasználóként. Ellenőrizze, hogy a szöveg megtekintéséhez *Üdvözöljük Aurelia Briggs |} Kijelentkezés* tetején.

![Az Azure fejlesztési tárolóhelyek kerékpárt megosztása mintaalkalmazás](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Gyermek-fejlesztési tárolóhelyek létrehozása

Használja a `azds space select` paranccsal hozzon létre két gyermek tárolóhelyek alatt *fejlesztési*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

A fenti parancsokat, hozzon létre két gyermek tárolóhelyek alatt *fejlesztési* nevű *azureuser1* és *azureuser2*. E két alárendelt tárolóhelyek különböző fejlesztési szóközöket jelzik a fejlesztők számára a *azureuser1* és *azureuser2* módosítása a mintaalkalmazás használandó.

Használja a `azds space list` parancs listázza az összes fejlesztői címterét, és győződjön meg róla *dev/azureuser2* van kiválasztva.

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Használja a `azds list-uris` meg az URL-címeket, a mintaalkalmazás, amely a jelenleg kijelölt terület *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Győződjön meg arról, hogy az URL-címek szerint jelenik meg a `azds list-uris` parancs van a *azureuser2.s.dev* előtag. Ez az előtag megerősíti, hogy van-e az aktuális hely kiválasztott *azureuser2*, azaz gyermeke *fejlesztési*.

Keresse meg a *bikesharingweb* szolgáltatásba a *dev/azureuser2* fejlesztési terület nyissa meg a nyilvános URL-CÍMÉT a `azds list-uris` parancsot. A fenti példában, nyilvános URL-CÍMÉT a *bikesharingweb* szolgáltatás `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza ki *Aurelia Briggs (ügyfél)* felhasználóként. Ellenőrizze, hogy a szöveg megtekintéséhez *Üdvözöljük Aurelia Briggs |} Kijelentkezés* tetején.

## <a name="update-code"></a>Kód frissítése

Nyissa meg *BikeSharingWeb/components/Header.js* egy egyszerű szövegszerkesztőben, és módosítsa a szöveget a [span elemet a `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Mentse a módosításokat, és zárja be a fájlt.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Hozza létre és futtassa a frissített bikesharingweb szolgáltatás a *dev/azureuser2* fejlesztési terület

Keresse meg a *BikeSharingWeb /* könyvtárhoz, majd futtassa a `azds up` parancsot.

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

Ez a parancs létrehozza, és futtatja a *bikesharingweb* szolgáltatásának a *dev/azureuser2* fejlesztési terület. A szolgáltatás fut mellett a *bikesharingweb* -ban futó service *fejlesztési* használó kérelmek esetén használható a *azureuser2.s* URL-előtagot. Útválasztás működéséről közötti szülő és gyermek fejlesztési tárolóhelyek további információkért lásd: [hogyan Azure fejlesztési tárolóhelyek működik, és konfigurált](how-dev-spaces-works.md).

Keresse meg a *bikesharingweb* szolgáltatásba a *dev/azureuser2* fejlesztési terület, a kimenetben megjelenített nyilvános URL-cím megnyitásával az `azds up` parancsot. Válassza ki *Aurelia Briggs (ügyfél)* felhasználóként. Ellenőrizze, hogy a frissített szöveget a jobb felső sarkában láthatja. Szükség lehet, frissítse az oldalt, vagy törölje a böngésző gyorsítótárát, ha meg nem jelenik meg rögtön Ez a változás.

![Az Azure fejlesztési tárolóhelyek kerékpárt megosztása mintaalkalmazás frissítve](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Ellenőrizze, hogy más fejlesztői szóközt nem változnak

Ha a `azds up` parancsot továbbra is fut, nyomja meg az *Ctrl + c*.

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

Keresse meg a *fejlesztési* verziója *bikesharingweb* a böngészőben, válassza ki a *Aurelia Briggs (ügyfél)* felhasználóként, és ellenőrizze, hogy az eredeti szöveg jobb felső sarkában láthatja sarokban. Ismételje meg ezeket a lépéseket a *dev/azureuser1* URL-CÍMÉT. Figyelje meg a rendszer csak alkalmazza a módosításokat a *dev/azureuser2* verziója *bikesharingweb*. Ez az elkülönítés módosításainak *dev/azureuser2* lehetővé teszi, hogy *azureuser2* módosításokat működésének megzavarása nélkül megtesztelheti *azureuser1*.

Szeretné, hogy ezek a módosítások megjelennek *fejlesztési* és *dev/azureuser1*, kövesse a csapata meglévő munkafolyamat vagy CI/CD-folyamat. Ez a munkafolyamat például magában foglalhatja a módosítás véglegesítését a verziókövetési rendszerét, és a egy CI/CD-folyamat használatával, vagy eszközök, például a Helm a frissítés telepítése.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan segít az Azure Dev Spaces az összetettebb alkalmazások fejlesztésében több tároló között, és hogyan egyszerűsítheti le az együttműködésen alapuló fejlesztést a kód különböző verzióinak vagy ágainak különböző terekben való kezelésével.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations
