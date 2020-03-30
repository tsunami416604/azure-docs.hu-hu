---
title: A fejlesztői számítógép csatlakoztatása AKS-fürthöz (előzetes verzió)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Ismerje meg, hogyan csatlakoztathatja fejlesztői számítógépét egy AKS-fürthöz az Azure Dev Spaces segítségével
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235003"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>A fejlesztői számítógép csatlakoztatása AKS-fürthöz (előzetes verzió)

Az Azure Dev Spaces lehetővé teszi, hogy futtassa és hibakeresés kódot egy tárolóval vagy anélkül a fejlesztői számítógépen, miközben továbbra is csatlakozik a Kubernetes-fürthöz az alkalmazás vagy szolgáltatások többi részével. A fejlesztői számítógép fürthöz való csatlakoztatása segítségével gyorsan fejlesztheti az alkalmazást, és végpontok között végezheti el a tesztelést anélkül, hogy docker- vagy Kubernetes-konfigurációt kellene létrehoznia. Az AKS-fürthöz anélkül is csatlakozhat, hogy ez nem befolyásolná a többi számítási feladatot vagy olyan felhasználót, akik ugyanazt a fürtöt használják.

Az Azure Dev Spaces átirányítja a forgalmat a csatlakoztatott AKS-fürt és a fejlesztői számítógép között. Ez a forgalomátirányítás lehetővé teszi, hogy a fejlesztői számítógépen és az AKS-fürtön futó szolgáltatások úgy kommunikáljanak, mintha ugyanabban az AKS-fürtben lennének. Mivel a kód fut a fejlesztői számítógépen, akkor is rugalmasak a fejlesztői eszközöket használ a kód futtatásához és hibakereséséhez. Az Azure Dev Spaces lehetővé teszi a környezeti változók és a csatlakoztatott fájlok replikálását is az AKS-fürtben a fejlesztői számítógépben.

Ebből az útmutatóból a következőket tudhatja meg:

* Állítsa be az Azure Dev Spaces egy felügyelt Kubernetes-fürtön az Azure-ban.
* Üzembe helyezhet egy több mikroszolgáltatásokat tartalmazó nagy alkalmazást egy fejlesztői térbe.
* Az Azure Dev Spaces használatával átirányíthatja az AKS-fürt és a fejlesztői számítógépen futó kód közötti forgalmat.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

Ez az útmutató az [Azure Dev Spaces-ek kerékpármegosztásminta-alkalmazást](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) használja a fejlesztési számítógép AKS-fürthöz való csatlakoztatásának bemutatásához. Kövesse az [Azure Dev Spaces-i kerékpármegosztási mintaalkalmazás README című útmutatójában](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) található utasításokat a mintaalkalmazás futtatásához. Másik lehetőségként, ha saját alkalmazással rendelkezik egy AKS-fürtön, továbbra is követheti az alábbi lépéseket, és használhatja a saját szolgáltatások és podok nevét.

### <a name="limitations"></a>Korlátozások

* Az UDP jelenleg nem támogatott.

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Az Azure CLI telepítve van.][azure-cli]
* [Visual Studio-kód][vs-code] az [Azure Dev Spaces][azds-vs-code] bővítmény macOS-en vagy Windows 10-en telepített és futó használatával.
* Az [Azure Dev Spaces bike sharing mintaalkalmazás](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) vagy az AKS-fürtön futó saját alkalmazás.

## <a name="connect-your-development-computer"></a>A fejlesztői számítógép csatlakoztatása

Nyissa meg *a fejlesztői helyeket/mintákat/BikeSharingApp/Bikes* alkalmazást a Visual Studio-kódban, és az Azure Dev Spaces bővítmény segítségével csatlakoztassa a fejlesztői számítógépet az AKS-fürthöz.

Az Azure Dev Spaces bővítmény használatához nyissa meg a Parancspalettát a Visual Studio-kódban a *Nézet,* majd a *Parancspaletta*elemre kattintva. Kezdjen `Azure Dev Spaces: Redirect` el gépelni, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`és `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`kattintson a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, vagy a lehetőségre.

![Parancsok](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Átirányítási beállítás kiválasztása

A futtatáskor `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`a rendszer egy meglévő Kubernetes-szolgáltatás kiválasztását kéri:

![Szolgáltatás kiválasztása](../media/how-to-connect/connect-choose-service.png)

Ez a beállítás átirányítja a szolgáltatás AKS-fürtjének összes forgalmát a fejlesztői számítógépen futó alkalmazás verziójára. Ha ez a szolgáltatás több podok fut az AKS-fürtben, a szolgáltatás összes forgalma csak a fejlesztői számítógéphez. Az Azure Dev Spaces is irányítja az összes kimenő forgalmat az alkalmazásból vissza az AKS-fürthöz.

A futtatáskor `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`a rendszer egy adott pod kiválasztását kéri:

![Pod kiválasztása](../media/how-to-connect/connect-choose-pod.png)

Ez a beállítás egy adott podhoz csatlakozik. Ez a beállítás akkor hasznos, ha olyan podokkal kommunikál, amelyek nem küldenek vagy fogadnak forgalmat, és replikálják a megszakított podokat. Ha a pod nem küld és fogad forgalmat, ez `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` a beállítás hasonlóan viselkedik, és átirányítja az Összes forgalmat az AKS-fürtben a kiválasztott pod szolgáltatásához kapcsolódó összes podok.

A futtatásakor `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`a rendszer nem kéri, hogy válasszon ki egy meglévő podot vagy szolgáltatást. Ez a beállítás átirányítja az összes kimenő forgalmat a fejlesztői számítógépen futó alkalmazásból az AKS-fürtbe.

Ebben a példában válassza ki `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` és válassza ki a *kerékpárszolgáltatást.*

### <a name="select-a-connection-mode"></a>Csatlakozási mód kiválasztása

Miután kiválasztotta az átirányítási beállítást, a program kéri, hogy válassza a *Csere* vagy a *Klónozó* kapcsolat módot.

![Csere vagy klónozás](../media/how-to-connect/connect-replace-clone.png)

A *Csere* beállítás lecseréli az Aktuális podot vagy szolgáltatást az AKS-fürtben, és átirányítja az adott szolgáltatás összes forgalmát a fejlesztői számítógépre. Ez a beállítás zavaró lehet az AKS-fürt más szolgáltatásai számára, amelyek az átirányított szolgáltatással kommunikálnak, nem működnek, amíg el nem indítja az alkalmazást a fejlesztői számítógépen. A *Klónozás* beállítás lehetővé teszi, hogy válasszon egy meglévő gyermek fejlesztői terület, vagy hozzon létre egy új gyermek fejlesztési terület átirányításához forgalmat egy pod vagy szolgáltatás a fejlesztői számítógépre. Ez a beállítás lehetővé teszi, hogy elszigetelten dolgozzon, és ne zavarja meg az egyéb szolgáltatásokat, mivel csak az adott gyermek fejlesztői területre irányuló forgalmat irányítja át a fejlesztői számítógépre. A *Klónozás* beállítás megköveteli, hogy az AKS-fürt engedélyezve legyen az Azure Dev Spaces.

Ebben a példában válassza a *Csere*lehetőséget.

> [!NOTE]
> Ha a meglévő szolgáltatás pod több tárolók, a rendszer arra is kéri, hogy válassza ki az alkalmazás tárolóját.

### <a name="select-a-port-for-your-application"></a>Az alkalmazás portjának kiválasztása

Miután kiválasztotta a csatlakozási módot, a rendszer kéri, hogy adja meg a helyi alkalmazás TCP-portját. Ha az alkalmazás több portot nyit meg, vesszővel válassza el őket, például *80,81.* Ha az alkalmazás nem fogad el hálózati kéréseket, írja be a *0*értéket. Ebben a példában írja be a *3000*értéket.

![Csatlakozás port kiválasztása](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>A csatlakozás megerősítése

Miután kiválasztotta az alkalmazás TCP-portját, az Azure dev Spaces kapcsolatot létesít az AKS-fürttel. Az Azure Dev Spaces egy ügynököt injektál az AKS-fürtbe az AKS-fürt és a fejlesztői számítógép közötti forgalom átirányításához. A kapcsolat létrehozása eltarthat néhány percig. Az Azure Dev Spaces is kér rendszergazdai hozzáférést annak érdekében, hogy módosítsa a *gazdagép* fájl a fejlesztői számítógépen.

> [!IMPORTANT]
> Miután az Azure Dev Spaces kapcsolatot létesít az AKS-fürttel, előfordulhat, hogy az AKS-fürt többi szolgáltatása nem fog megfelelően működni, amíg el nem indítja a szolgáltatást a fejlesztői számítógépen, ha a Kapcsolat cseréje módot *választja.* A *Klónozási* kapcsolat mód kiválasztásával gyermekfejlesztési területet hozhat létre az átirányításhoz, és elkerülheti a szülőtér megzavarását. Ha a szolgáltatás olyan függőségben áll, amely nem érhető el a fejlesztői számítógépen, előfordulhat, hogy módosítania kell az alkalmazást, vagy [további konfigurációt](#additional-configuration) kell megadnia

Az Azure Dev Spaces megnyit egy terminálablak című *AZDS Connect - Bikes,* miután kapcsolatot létesít az AKS-fürt. Ez a terminálablak az AKS-fürtből konfigurált összes környezeti változót és DNS-bejegyzést tartalmazza. Az ebben a terminálablakban vagy a Visual Studio kódhiba-keresőjét használó kódok az AKS-fürthöz kapcsolódnak.

![Terminál](../media/how-to-connect/connect-terminal.png)

Emellett az Azure Dev Spaces létrehoz egy ablakot nevű *Fejlesztői terek connect* annak összes kimenetét.

![Kimenet](../media/how-to-connect/connect-output.png)

Az Azure Dev Spaces is rendelkezik egy állapotsor-elem, amely a kapcsolat állapotát mutatja.

![status](../media/how-to-connect/connect-status.png)

Ellenőrizze, hogy az állapotsorban a *Fejlesztői terek: Csatlakozás a 3000-es helyi porton csatlakozik a fejlesztői/kerékpárkapcsolati beállításokhoz.*

### <a name="configure-your-application-on-your-development-computer"></a>Az alkalmazás konfigurálása a fejlesztői számítógépen

Nyissa meg az *AZDS Connect* `npm install`- Bikes terminál ablakot, és fusson:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Kattintson *a Debug,* majd *a Configurations (Konfigurációk) megnyitása gombra.* Ha a rendszer kéri a környezet kiválasztását, válassza a *Node.js lehetőséget.* Ezzel létrehoz `.vscode/launch.json` egy fájlt. Cserélje le a fájl tartalmát a következőkre:

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

Nyissa [meg a package.json t,](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) és adjon hozzá egy hibakeresési parancsfájlt:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Az alkalmazás indítása a fejlesztői számítógépen

Kattintson a bal oldali *Debug* ikonra, és kattintson a Start gombra az *Indítás npm-en keresztül* a tetején.

![Indítás az NPM-en keresztül](../media/how-to-connect/launch-npm.png)

Az alkalmazás elindul, és az Azure Dev Spaces átirányítja a forgalmat az AKS-fürt és a fejlesztői számítógép között. Az alábbihoz hasonló üzeneteket fog látni a *Debug Konzolon:*

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Keresse meg a *bikesharingweb-szolgáltatást* az Azure Dev Spaces állapotsorra kattintva, és válassza ki az alkalmazás nyilvános URL-címét. A nyilvános URL-címet a `azds list-uris` korábban futtatott parancsból is megtalálhatja. Ha nem azure dev terek a fürtön, használja az IP-címet vagy az URL-címet az alkalmazás a használt névtér. A fenti példában a *bikesharingweb* szolgáltatás `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`nyilvános URL-címe . Válassza az *Aurelia Briggs (ügyfél) lehetőséget* felhasználóként, majd válassza ki a bérelhető kerékpárt.

### <a name="set-a-break-point"></a>Töréspont beállítása

Nyissa meg [a server.js-t,](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) és kattintson valahol a 233-as vonalon, hogy a kurzort oda tegye. Állítsa be a töréspontot az *F9* gombra kattintva, vagy kattintson a *Hibakeresés* gombra, majd *a Töréspont váltása gombra.*

A nyilvános URL megnyitásával keresse meg a *bikesharingweb* szolgáltatást. Válassza az *Aurelia Briggs (ügyfél) lehetőséget* felhasználóként, majd válassza ki a bérelhető kerékpárt. Figyelje meg, hogy a kerékpár képe nem töltődik be. Visszatérés a Visual Studio-kódhoz, és a 233-as vonal figyelése ki emelve. A beállított töréspont szüneteltette a szolgáltatást a 233-as vonalon. A szolgáltatás folytatásához nyomja le az *F5* gombot, vagy kattintson *a Hibakeresés gombra,* majd *a Folytatás gombra.* Térjen vissza a böngészőbe, és ellenőrizze, hogy lát-e helyőrző képet a kerékpárhoz.

Távolítsa el a töréspontot azáltal, hogy a `server.js` kurzort a 233-as vonalon helyezi el, és az *F9-et*üti.

### <a name="update-your-application"></a>Az alkalmazás frissítése

A `server.js` 232-es és a 233-as sorok eltávolításának szerkesztése:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A szakasznak most így kell kinéznie:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Mentse a módosításokat, majd kattintson *a Hibakeresés,* majd *a Hibakeresés újraindítása parancsra.* Frissítse a böngészőt, és ellenőrizze, hogy már nem lát-e helyőrző képet a kerékpárhoz.

Kattintson a *Hibakeresés,* majd a *Hibakeresés leállítása gombra* a hibakereső leállításához. Kattintson az Azure Dev Spaces állapotsorra az AKS-fürtről való leválasztáshoz.

## <a name="additional-configuration"></a>További konfigurálás

Az Azure Dev Spaces képes kezelni az útválasztási forgalom és a környezeti változók replikálása további konfiguráció nélkül. Ha le kell töltenie az AKS-fürt tárolójához csatlakoztatott fájlokat, például egy ConfigMap-fájlt, létrehozhat egy `azds-local.env` fájlt a fájlok fejlesztési számítógépre való letöltéséhez.

Íme egy `azds-local.env`példa:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Naplózás és diagnosztika használata

A naplózási kimenet a *fejlesztői helyek összekapcsolása* ablakba kerül, miután csatlakoztatta a fejlesztői számítógépet az AKS-fürthöz.

![Kimenet](../media/how-to-connect/connect-output.png)

Kattintson az Azure Dev Spaces állapotsorára, és válassza *a Diagnosztikai adatok megjelenítése*lehetőséget. Ez a parancs az aktuális környezeti változókat és a DNS-részeket nyomtatja ki a naplózási kimenetben.

![Kimenet diagnosztikával](../media/how-to-connect/connect-output-diagnostics.png)

Ezenkívül a diagnosztikai naplók a `Azure Dev Spaces` könyvtárban találhatók a [fejlesztői számítógép *TEMP* könyvtárában.][azds-tmp-dir]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja az Azure Dev Spaces és a GitHub-műveletek a lekéréses kérelem módosításait közvetlenül az AKS-ben, mielőtt a lekéréses kérelem beolvadna a tárház fő ágába.

> [!div class="nextstepaction"]
> [GitHub-műveletek & Az Azure Kubernetes szolgáltatás][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download