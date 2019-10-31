---
title: Azure dev Spaces-kapcsolat
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Ismerje meg, hogyan használhatja az Azure dev Spaces-kapcsolatot
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, csatlakoztatás
ms.openlocfilehash: 2e1984b838e961239e0533179c34eccb118abd8e
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065872"
---
# <a name="azure-dev-spaces-connect-preview"></a>Azure dev Spaces-kapcsolat (előzetes verzió)

Az Azure dev Spaces Connect lehetővé teszi, hogy kódot futtasson vagy anélkül a fejlesztői gépen található tárolóval, miközben a Kubernetes-fürthöz még a többi alkalmazásával vagy szolgáltatásával csatlakozik. A fejlesztői gép fürthöz csatlakoztatásával gyorsan fejlesztheti alkalmazását, és teljes körű tesztelést hajthat végre anélkül, hogy Docker-vagy Kubernetes-konfigurációt kellene létrehoznia. Az AK-fürthöz úgy is csatlakozhat, hogy nem érinti a többi munkaterhelést, vagy olyan felhasználókat, akik ugyanazt a fürtöt használják.

Az Azure dev Spaces Connect átirányítja a forgalmat a csatlakoztatott AK-fürt és a fejlesztői számítógép között. Ez a forgalom átirányítása lehetővé teszi, hogy az AK-fürtön futó fejlesztési gépen és szolgáltatásain a kód ugyanúgy kommunikáljon, mintha ugyanabban az AK-fürtben vannak. Mivel a kód a fejlesztői gépen fut, rugalmassággal rendelkezik a kód futtatásához és hibakereséséhez használt fejlesztői eszközökhöz is. Az Azure dev Spaces összekapcsolási szolgáltatása lehetővé teszi a környezeti változók és a csatlakoztatott fájlok a hüvelyek számára elérhetővé tételét a fejlesztői gépen.

Ebből az útmutatóból a következőket tudhatja meg:

- Azure fejlesztői tárhelyek beállítása egy felügyelt Kubernetes-fürtön az Azure-ban.
- Helyezzen üzembe egy nagyméretű alkalmazást több szolgáltatással egy fejlesztői tárhelyen.
- Az Azure dev Spaces-kapcsolat használatával átirányíthatja a forgalmat az AK-fürt és a fejlesztői gépen futó kód között.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előzetes teendők

Ez az útmutató az Azure [dev Spaces Bike Sharing Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használatával mutatja be az Azure dev Spaces kapcsolódását. Ha van egy saját alkalmazása az AK-fürtön, amelyet Ehelyett használni szeretne, elkezdheti [itt](#use-azure-dev-spaces-connect).

### <a name="limitations"></a>Korlátozások

* Az UDP nem támogatott az Azure dev Spaces-kapcsolattal.

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Telepített Azure CLI][azure-cli].
* A [Helm 2,13 vagy újabb rendszer van telepítve][helm-installed].
* A [Visual Studio Code][vs-code] az [Azure dev Spaces][azds-vs-code] bővítménnyel van telepítve, és MacOS vagy Windows 10 rendszeren fut.

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

A fejlesztéshez az Azure dev Spaces szolgáltatást használhatja, ha az alkalmazás egy fürtön fut, függetlenül az üzembe helyezéséhez használt eszköztől.

### <a name="use-helm-to-install-the-sample-application"></a>A minta alkalmazás telepítése a Helm használatával

A `helm init` és `helm install` paranccsal állíthatja be és telepítheti a minta alkalmazást a fürtön.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic 
```

> [!Note]
> **Ha RBAC-kompatibilis fürtöt használ**, mindenképpen állítson be [egy szolgáltatásfiókot a kormányrúdhoz](https://helm.sh/docs/using_helm/#role-based-access-control). Ellenkező esetben `helm` parancsok sikertelenek lesznek.

A `helm install` parancs végrehajtása több percet is igénybe vehet. A parancs kimenete a fürtön üzembe helyezett összes szolgáltatás állapotát jeleníti meg, ha befejeződött:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

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

### <a name="navigate-to-your-sample-application"></a>Navigáljon a minta alkalmazáshoz

Miután a minta alkalmazást telepítette a fürtön, és mivel a fürtön engedélyezve vannak a dev Spaces, a `azds list-uris` parancs használatával jelenítse meg a jelenleg kiválasztott *fejlesztői* alkalmazás URL-címeit.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Nyissa meg a *bikesharingweb* szolgáltatást a `azds list-uris` parancs nyilvános URL-címének megnyitásával. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Ellenőrizze, hogy megjelenik-e a bike helyőrző képe.

## <a name="use-azure-dev-spaces-connect"></a>Az Azure dev Spaces-kapcsolat használata

Nyissa meg a *dev-Spaces/Samples/BikeSharingApp/Bikes* -t a Visual Studio Code-ban, és használja az Azure dev Spaces összekapcsolási bővítményt a fejlesztői gép és az AK-fürt

Az Azure dev Spaces kapcsolódási bővítményének használatához nyissa meg a parancssort a Visual Studio Code-ban a *nézet* , majd a *parancs paletta*elemre kattintva. Kezdje el begépelni `Azure Dev Spaces: Redirect` és kattintson a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, a `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`vagy a `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`elemre.

![Csatlakozási parancsok](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Átirányítási lehetőség kiválasztása

Ha `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`futtatja, a rendszer arra kéri, hogy válasszon ki egy meglévő Kubernetes-szolgáltatást:

![Kapcsolat kiválasztása szolgáltatás](../media/how-to-connect/connect-choose-service.png)

Ez a beállítás a szolgáltatáshoz tartozó AK-fürt összes forgalmát átirányítja a fejlesztői gépen futó alkalmazás verziójára. Ha a szolgáltatásban több hüvely fut az AK-fürtben, akkor a szolgáltatáshoz tartozó összes forgalom csak a fejlesztői géphez lesz irányítva. Az Azure dev Spaces szolgáltatás az alkalmazásból érkező összes kimenő forgalmat az AK-fürtre irányítja vissza.

Ha `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`futtatja, a rendszer arra kéri, hogy válasszon ki egy adott Pod-t:

![Kapcsolat kiválasztása Pod](../media/how-to-connect/connect-choose-pod.png)

Ez a beállítás egy adott Pod-hoz csatlakozik. Ez a lehetőség olyan hüvelyek esetében hasznos, amelyek nem küldenek vagy fogadnak forgalmat, és leállított hüvelyeket replikálnak. Ha a pod elküldi és fogadja a forgalmat, ez a beállítás hasonló módon viselkedik `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` és átirányítja az AK-fürt összes forgalmát a kiválasztott Pod szolgáltatáshoz kapcsolódó összes hüvelyre.

Ha `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`futtatja, a rendszer nem kéri egy meglévő Pod vagy szolgáltatás kiválasztását. Ez a beállítás átirányítja az összes kimenő forgalmat a fejlesztői gépen futó alkalmazásból az AK-fürtre.

Ebben a példában válassza a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` lehetőséget, és válassza ki a *Bikes* szolgáltatást.

### <a name="select-a-connection-mode"></a>Válassza ki a kapcsolatok módját

Miután kiválasztotta az átirányítási lehetőséget, a rendszer kéri, hogy válassza ki a *Csere* vagy a *klónozás* kapcsolódási módját.

![Kapcsolat cseréje vagy klónozása](../media/how-to-connect/connect-replace-clone.png)

A *replace (csere* ) lehetőség lecseréli az aktuális Pod vagy szolgáltatást az AK-fürtben, és átirányítja az adott szolgáltatáshoz tartozó összes forgalmat a fejlesztői gépre. Ez a beállítás zavaró lehet az AK-fürt más, az átirányított szolgáltatással kommunikáló szolgáltatásai esetében, amíg el nem indítja az alkalmazást a fejlesztői gépen. A *klónozott* lehetőség lehetővé teszi, hogy kiválasszon egy meglévő gyermek fejlesztési területet, vagy új gyermek-fejlesztési helyet hozzon létre a pod vagy szolgáltatáshoz tartozó forgalom átirányításához a fejlesztői gépre. Ez a beállítás lehetővé teszi, hogy elszigetelten működjön, és ne zavarja meg a többi szolgáltatást, mivel csak a gyermek-fejlesztési terület felé irányuló forgalom lesz átirányítva a fejlesztői gépre. A *klónozási* beállításhoz az AK-fürtnek engedélyezve kell lennie az Azure dev Spaces használatához.

Ehhez a példához válassza a *replace (csere*) lehetőséget.

> [!NOTE]
> Ha a meglévő szolgáltatás Pod több tárolóval rendelkezik, a rendszer felszólítja az alkalmazás tárolójának kiválasztására is.

### <a name="select-a-port-for-your-application"></a>Válasszon portot az alkalmazáshoz

A kapcsolódási mód kiválasztása után a rendszer felszólítja, hogy adja meg a helyi alkalmazás TCP-portját. Ha az alkalmazás több portot nyit meg, vesszővel válassza el őket, például *80, 81*. Ha az alkalmazás nem fogad el hálózati kérelmeket, adja meg a *0*értéket. Ehhez a példához adja meg a *8080*értéket.

![Csatlakozás a porthoz](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Erősítse meg, hogy csatlakozik

Az alkalmazás TCP-portjának kiválasztása után az Azure dev Spaces kapcsolódási szolgáltatás kapcsolatot létesít az AK-fürttel. Az Azure dev Spaces összekapcsolja az ügynököt az AK-fürtbe, hogy átirányítsa a forgalmat az AK-fürt és a fejlesztői gép között. A kapcsolatok létrehozása eltarthat néhány percig. Az Azure dev Spaces szolgáltatás a rendszergazdai hozzáférést is kérni fogja, hogy módosítsa a *gazdagépek* fájlját a fejlesztői gépen.

> [!IMPORTANT]
> Ha az Azure dev Spaces-kapcsolat kapcsolatot létesít az AK-fürttel, előfordulhat, hogy az AK-fürt többi szolgáltatása nem működik megfelelően, amíg el nem indítja a szolgáltatást a fejlesztői gépen. Továbbá, ha a szolgáltatás olyan függőséggel rendelkezik, amely nem érhető el a fejlesztői gépen, előfordulhat, hogy módosítania kell az alkalmazást, vagy [további konfigurációt](#additional-configuration) kell megadnia.

Az Azure dev Spaces-kapcsolat megnyit egy, a *AZDS csatlakoztatása-Bikes* nevű terminál ablakot, miután kapcsolatot LÉTESÍT az AK-fürttel. Ez a terminálablak az AK-fürtből konfigurált összes környezeti változót és DNS-bejegyzést tartalmazza. Az ebben a terminál ablakban vagy a Visual Studio Code Debugger használatával futtatott kód az AK-fürthöz csatlakozik.

![Terminál összekötése](../media/how-to-connect/connect-terminal.png)

Emellett az Azure dev Spaces-kapcsolatok nevű ablakban a *dev Spaces összekapcsolását* is létrehozhatja az összes kimenetével.

![Kimenet összekötése](../media/how-to-connect/connect-output.png)

Az Azure dev Spaces-kapcsolatnak van egy állapotsor-eleme is, amely a kapcsolat állapotát mutatja.

![Kapcsolat állapota](../media/how-to-connect/connect-status.png)

Győződjön meg arról, hogy az állapotsoron a *dev Spaces: Connected to dev/Bikes on local port 8080*.

### <a name="configure-your-application-on-your-development-machine"></a>Az alkalmazás konfigurálása a fejlesztői gépen

Nyissa meg a *AZDS-összekötő-Bikes* terminál ablakot, és futtassa a `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Nyissa meg a [Server. js fájlt](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L352) , majd az 352-es frissítési *portot* *8080*-re:

```javascript
var port = 8080;
var server = null;
```

Kattintson a *hibakeresés* , majd a *konfigurációk megnyitása*lehetőségre. Ezzel létrehoz egy `.vscode/launch.json` fájlt. Cserélje le a fájl tartalmát a következőre:

```json
{
    "configurations": [
        ...
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Nyissa meg a [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) fájlt, és adjon hozzá egy hibakeresési parancsfájlt:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Az alkalmazás elindítása a fejlesztői gépen

Kattintson a bal oldalon található *hibakeresés* ikonra, és kattintson a Start gombra, majd a *NPM* -on felüli Indítás gombra.

![Indítás a NPM-on keresztül](../media/how-to-connect/launch-npm.png)

Az alkalmazás elindul, és az Azure dev Spaces-kapcsolat átirányítja a forgalmat az AK-fürt és a fejlesztői számítógép között. Az alábbihoz hasonló üzenetek jelennek meg a *hibakeresési konzolon*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 8080
```

Navigáljon a *bikesharingweb* szolgáltatáshoz az Azure dev Spaces kapcsolódási állapotsorra kattintva, és válassza ki az alkalmazás nyilvános URL-címét. A nyilvános URL-címet a korábban futtatott `azds list-uris` parancsból is megtalálhatja. Ha nem használja az Azure dev Spaces szolgáltatást a fürtön, használja az alkalmazás IP-címét vagy URL-címét a használt névtérhez. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt.

### <a name="set-a-break-point"></a>Töréspont beállítása

Nyissa meg a [Server. js fájlt](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) , és kattintson a 233. sorban a kurzorra. Állítsa be a töréspontot az *F9 billentyű* lenyomásával, vagy kattintson a *hibakeresés* elemre, majd a *töréspontot*

Nyissa meg a *bikesharingweb* szolgáltatást a nyilvános URL-cím megnyitásával. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Figyelje meg, hogy a kerékpár képe nem töltődik be. Térjen vissza a Visual Studio Code-ba, és figyelje meg, hogy ki van emelve a 233. sor. A megadott töréspont szüneteltette a szolgáltatást a 233. sorban. A szolgáltatás folytatásához nyomja meg az *F5 billentyűt* , vagy kattintson a *hibakeresés* gombra, és *folytassa a művelettel*. Térjen vissza a böngészőhöz, és ellenőrizze, hogy megjelenik-e a bike helyőrző képe.

Távolítsa el a töréspontot úgy, hogy a kurzort az 233-es sorra helyezi a `server.js` és az *F9 billentyűt*

### <a name="update-your-application"></a>Az alkalmazás frissítése

`server.js` szerkesztése a 232 és 233 sorok eltávolításához:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A szakasznak így kell kinéznie:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Mentse a módosításokat, majd *kattintson a hibakeresés gombra* , majd *indítsa újra a hibakeresést*. Frissítse a böngészőt, és győződjön meg arról, hogy már nem jelenik meg a bike helyőrző képe.

A hibakereső leállításához kattintson a *hibakeresés* gombra, majd *állítsa le a hibakeresést* . Kattintson az Azure dev Spaces csatlakozás állapotsorára az AK-fürtről való leválasztáshoz.

## <a name="additional-configuration"></a>További konfiguráció

Az Azure dev Spaces-kapcsolat további konfiguráció nélkül képes kezelni az útválasztási forgalmat és a környezeti változók replikálását. Ha olyan fájlokat kell letöltenie, amelyek az AK-fürtben lévő tárolóhoz vannak csatlakoztatva, például egy ConfigMap-fájlt, létrehozhat egy `azds-local.env`, amellyel letöltheti ezeket a fájlokat a fejlesztői gépére.

Íme egy példa `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használható az Azure dev Spaces és a GitHub-műveletek a lekéréses kérelemben szereplő módosítások tesztelésére közvetlenül az AK-ban, mielőtt a lekéréses kérelem beolvad a tárház fő ágában.

> [!div class="nextstepaction"]
> [GitHub-műveletek & Azure Kubernetes Service-ben][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download