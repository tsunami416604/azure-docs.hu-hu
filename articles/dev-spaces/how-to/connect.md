---
title: A fejlesztői gép csatlakoztatása egy AKS-fürthöz
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Ismerje meg, hogyan csatlakoztatható a fejlesztői gép egy AK-fürthöz az Azure dev Spaces használatával
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: c26d159d6f883e1c368b09a82bc53c621c70e281
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482243"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>A fejlesztői gép csatlakoztatása egy AKS-fürthöz (előzetes verzió)

Az Azure dev Spaces lehetővé teszi, hogy kódot futtasson vagy anélkül a fejlesztői gépen lévő tárolóval, és a Kubernetes-fürthöz a többi alkalmazással vagy szolgáltatással is kapcsolódjon. A fejlesztői gép fürthöz csatlakoztatásával gyorsan fejlesztheti alkalmazását, és teljes körű tesztelést hajthat végre anélkül, hogy Docker-vagy Kubernetes-konfigurációt kellene létrehoznia. Az AK-fürthöz úgy is csatlakozhat, hogy nem érinti a többi munkaterhelést, vagy olyan felhasználókat, akik ugyanazt a fürtöt használják.

Az Azure dev Spaces átirányítja a forgalmat a csatlakoztatott AK-fürt és a fejlesztői számítógép között. Ez a forgalom átirányítása lehetővé teszi, hogy az AK-fürtön futó fejlesztési gépen és szolgáltatásain a kód ugyanúgy kommunikáljon, mintha ugyanabban az AK-fürtben vannak. Mivel a kód a fejlesztői gépen fut, rugalmassággal rendelkezik a kód futtatásához és hibakereséséhez használt fejlesztői eszközökhöz is. Az Azure dev Spaces lehetővé teszi a környezeti változók és a csatlakoztatott fájlok a hüvelyek számára elérhetővé tételét is a fejlesztői gépen.

Ebből az útmutatóból a következőket tudhatja meg:

* Azure fejlesztői tárhelyek beállítása egy felügyelt Kubernetes-fürtön az Azure-ban.
* Helyezzen üzembe egy nagyméretű alkalmazást több szolgáltatással egy fejlesztői tárhelyen.
* Az Azure dev Spaces használatával átirányíthatja a forgalmat az AK-fürt és a fejlesztői gépen futó kód között.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

Ez az útmutató az [Azure dev Spaces Bike Sharing Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használatával mutatja be, hogyan csatlakoztathatja a fejlesztői GÉPET egy AK-fürthöz. A minta alkalmazás futtatásához kövesse az [Azure dev Spaces Bike Sharing minta alkalmazáshoz tartozó tudnivalókat](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) ismertető témakör utasításait. Ha egy AK-fürtön saját alkalmazása van, akkor továbbra is követheti az alábbi lépéseket, és használhatja a saját szolgáltatásainak és hüvelyének nevét.

### <a name="limitations"></a>Korlátozások

* Az UDP jelenleg nem támogatott.

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Telepített Azure CLI][azure-cli].
* A [Visual Studio Code][vs-code] az [Azure dev Spaces][azds-vs-code] bővítménnyel van telepítve, és MacOS vagy Windows 10 rendszeren fut.
* Az [Azure dev Spaces Bike Sharing Sample Application](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) vagy a saját alkalmazása egy AK-fürtön fut.

## <a name="connect-your-development-machine"></a>A fejlesztői gép összekötése

Nyissa meg a *dev-Spaces/Samples/BikeSharingApp/Bikes* -t a Visual Studio Code-ban, és az Azure dev Spaces bővítménnyel kapcsolja össze a fejlesztői GÉPET az AK-fürthöz.

Az Azure dev Spaces bővítmény használatához nyissa meg a parancssort a Visual Studio Code-ban a *nézet* , majd a *parancs paletta*elemre kattintva. Kezdje el begépelni `Azure Dev Spaces: Redirect` és kattintson a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, a `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`vagy a `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`elemre.

![Parancsok](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Átirányítási lehetőség kiválasztása

Ha `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`futtatja, a rendszer arra kéri, hogy válasszon ki egy meglévő Kubernetes-szolgáltatást:

![Szolgáltatás kiválasztása](../media/how-to-connect/connect-choose-service.png)

Ez a beállítás a szolgáltatáshoz tartozó AK-fürt összes forgalmát átirányítja a fejlesztői gépen futó alkalmazás verziójára. Ha a szolgáltatásban több hüvely fut az AK-fürtben, akkor a szolgáltatáshoz tartozó összes forgalom csak a fejlesztői géphez lesz irányítva. Az Azure dev Spaces az alkalmazásból érkező összes kimenő forgalmat az AK-fürtre irányítja át.

Ha `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`futtatja, a rendszer arra kéri, hogy válasszon ki egy adott Pod-t:

![Válassza a pod lehetőséget](../media/how-to-connect/connect-choose-pod.png)

Ez a beállítás egy adott Pod-hoz csatlakozik. Ez a lehetőség olyan hüvelyek esetében hasznos, amelyek nem küldenek vagy fogadnak forgalmat, és leállított hüvelyeket replikálnak. Ha a pod elküldi és fogadja a forgalmat, ez a beállítás hasonló módon viselkedik `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` és átirányítja az AK-fürt összes forgalmát a kiválasztott Pod szolgáltatáshoz kapcsolódó összes hüvelyre.

Ha `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`futtatja, a rendszer nem kéri egy meglévő Pod vagy szolgáltatás kiválasztását. Ez a beállítás átirányítja az összes kimenő forgalmat a fejlesztői gépen futó alkalmazásból az AK-fürtre.

Ebben a példában válassza a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` lehetőséget, és válassza ki a *Bikes* szolgáltatást.

### <a name="select-a-connection-mode"></a>Válassza ki a kapcsolatok módját

Miután kiválasztotta az átirányítási lehetőséget, a rendszer kéri, hogy válassza ki a *Csere* vagy a *klónozás* kapcsolódási módját.

![Csere vagy klónozás](../media/how-to-connect/connect-replace-clone.png)

A *replace (csere* ) lehetőség lecseréli az aktuális Pod vagy szolgáltatást az AK-fürtben, és átirányítja az adott szolgáltatáshoz tartozó összes forgalmat a fejlesztői gépre. Ez a beállítás zavaró lehet az AK-fürt más, az átirányított szolgáltatással kommunikáló szolgáltatásai esetében, amíg el nem indítja az alkalmazást a fejlesztői gépen. A *klónozott* lehetőség lehetővé teszi, hogy kiválasszon egy meglévő gyermek fejlesztési területet, vagy új gyermek-fejlesztési helyet hozzon létre a pod vagy szolgáltatáshoz tartozó forgalom átirányításához a fejlesztői gépre. Ez a beállítás lehetővé teszi, hogy elszigetelten működjön, és ne zavarja meg a többi szolgáltatást, mivel csak a gyermek-fejlesztési terület felé irányuló forgalom lesz átirányítva a fejlesztői gépre. A *klónozási* beállításhoz az AK-fürtnek engedélyezve kell lennie az Azure dev Spaces használatához.

Ehhez a példához válassza a *replace (csere*) lehetőséget.

> [!NOTE]
> Ha a meglévő szolgáltatás Pod több tárolóval rendelkezik, a rendszer felszólítja az alkalmazás tárolójának kiválasztására is.

### <a name="select-a-port-for-your-application"></a>Válasszon portot az alkalmazáshoz

A kapcsolódási mód kiválasztása után a rendszer felszólítja, hogy adja meg a helyi alkalmazás TCP-portját. Ha az alkalmazás több portot nyit meg, vesszővel válassza el őket, például *80, 81*. Ha az alkalmazás nem fogad el hálózati kérelmeket, adja meg a *0*értéket. Ehhez a példához adja meg a *3000*értéket.

![Csatlakozás a porthoz](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Erősítse meg, hogy csatlakozik

Az alkalmazás TCP-portjának kiválasztása után az Azure dev Spaces létrehoz egy kapcsolatot az AK-fürthöz. Az Azure dev Spaces egy ügynököt fecskendez be az AK-fürtbe az AK-fürt és a fejlesztői gép közötti forgalom átirányításához. A kapcsolatok létrehozása eltarthat néhány percig. Az Azure dev Spaces emellett rendszergazdai hozzáférést is kér, hogy módosítsa a *gazdagépek* fájlját a fejlesztői gépen.

> [!IMPORTANT]
> Ha az Azure dev-helyek kapcsolatot létesít az AK-fürttel, előfordulhat, hogy az AK-fürt többi szolgáltatása nem működik megfelelően, amíg el nem indítja a szolgáltatást a fejlesztői gépen, ha a kapcsolat *cseréje* módot választja. Kiválaszthatja a *klónozott* kapcsolódási módot ahelyett, hogy az átirányításhoz hozzon létre egy gyermek fejlesztési helyet, és elkerülje a szülő terület megszakadását. Továbbá, ha a szolgáltatás olyan függőséggel rendelkezik, amely nem érhető el a fejlesztői gépen, előfordulhat, hogy módosítania kell az alkalmazást, vagy [további konfigurációt](#additional-configuration) kell megadnia.

Az Azure dev Spaces megnyit egy, a *AZDS csatlakoztatása-Bikes* nevű terminál ablakot, miután kapcsolatot LÉTESÍT az AK-fürttel. Ez a terminálablak az AK-fürtből konfigurált összes környezeti változót és DNS-bejegyzést tartalmazza. Az ebben a terminál ablakban vagy a Visual Studio Code Debugger használatával futtatott kód az AK-fürthöz csatlakozik.

![Terminál](../media/how-to-connect/connect-terminal.png)

Emellett az Azure dev Spaces egy, a *dev Spaces* -hez csatlakozó ablakot hoz létre az összes kimenetével.

![Kimenet](../media/how-to-connect/connect-output.png)

Az Azure dev Spaces egy állapotsor-elemmel is rendelkezik, amely a kapcsolatok állapotát mutatja.

![status](../media/how-to-connect/connect-status.png)

Győződjön meg arról, hogy az állapotsoron a *dev Spaces: Connected to dev/Bikes on local port 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Az alkalmazás konfigurálása a fejlesztői gépen

Nyissa meg a *AZDS-összekötő-Bikes* terminál ablakot, és futtassa a `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Kattintson a *hibakeresés* , majd a *konfigurációk megnyitása*lehetőségre. Ha a rendszer kéri, hogy válasszon ki egy környezetet, válassza a *Node. js*elemet. Ezzel létrehoz egy `.vscode/launch.json` fájlt. Cserélje le a fájl tartalmát a következőre:

```json
{
    "configurations": [
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

Az alkalmazás elindul, és az Azure dev Spaces átirányítja a forgalmat az AK-fürt és a fejlesztői számítógép között. Az alábbihoz hasonló üzenetek jelennek meg a *hibakeresési konzolon*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Navigáljon a *bikesharingweb* szolgáltatáshoz az Azure dev Spaces állapotsorra kattintva, és válassza ki az alkalmazás nyilvános URL-címét. A nyilvános URL-címet a korábban futtatott `azds list-uris` parancsból is megtalálhatja. Ha nem használja az Azure dev Spaces szolgáltatást a fürtön, használja az alkalmazás IP-címét vagy URL-címét a használt névtérhez. A fenti példában a *bikesharingweb* szolgáltatás nyilvános URL-címe `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt.

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

A hibakereső leállításához kattintson a *hibakeresés* gombra, majd *állítsa le a hibakeresést* . Kattintson az Azure dev Spaces állapotjelző sávjára az AK-fürtről való leválasztáshoz.

## <a name="additional-configuration"></a>További konfiguráció

Az Azure dev Spaces képes az útválasztási forgalom kezelésére és a környezeti változók replikálására további konfiguráció nélkül. Ha olyan fájlokat kell letöltenie, amelyek az AK-fürtben lévő tárolóhoz vannak csatlakoztatva, például egy ConfigMap-fájlt, létrehozhat egy `azds-local.env`, amellyel letöltheti ezeket a fájlokat a fejlesztői gépére.

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

## <a name="using-logging-and-diagnostics"></a>A naplózás és a diagnosztika használata

A naplózási kimenet a *dev Spaces kapcsolódási* ablakba kerül, miután a fejlesztési GÉPET az AK-fürthöz csatlakoztatná.

![Kimenet](../media/how-to-connect/connect-output.png)

Kattintson az Azure dev Spaces állapotsorra, és válassza a *diagnosztikai adatok megjelenítése*lehetőséget. Ez a parancs kinyomtatja az aktuális környezeti változókat és a DNS teljes értékét a naplózási kimenetben.

![Kimenet diagnosztika](../media/how-to-connect/connect-output-diagnostics.png)

Ezen kívül megtalálhatja a diagnosztikai naplókat `Azure Dev Spaces` könyvtárban a [fejlesztői számítógép *temp* könyvtárában][azds-tmp-dir].

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használható az Azure dev Spaces és a GitHub-műveletek a lekéréses kérelemben szereplő módosítások tesztelésére közvetlenül az AK-ban, mielőtt a lekéréses kérelem beolvad a tárház fő ágában.

> [!div class="nextstepaction"]
> [GitHub-műveletek & Azure Kubernetes Service-ben][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download