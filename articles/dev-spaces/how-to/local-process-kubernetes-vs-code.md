---
title: Helyi folyamat és Kubernetes használata a Visual Studio Code-dal (előzetes verzió)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Ismerje meg, hogyan használható a helyi folyamat a Kubernetes a fejlesztői számítógép Kubernetes-fürthöz való összekapcsolásához az Azure dev Spaces használatával
keywords: Helyi folyamat a Kubernetes, az Azure dev Spaces, a dev Spaces, a Docker, a Kubernetes, az Azure, az AK, az Azure Kubernetes Service, a containers
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316739"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>Helyi folyamat és Kubernetes használata a Visual Studio Code-dal (előzetes verzió)

A Kubernetes helyi folyamata lehetővé teszi a kód futtatását és hibakeresését a fejlesztői számítógépen, miközben továbbra is csatlakozik a Kubernetes-fürthöz a többi alkalmazással vagy szolgáltatással. Ha például nagyméretű, több függő szolgáltatásokkal és adatbázisokkal rendelkező, nagy üzemi architektúrával rendelkezik, a függőségek replikálása a fejlesztői számítógépen nehéz lehet. Emellett a belső hurkok fejlesztése során a kód a Kubernetes-fürthöz való létrehozása és üzembe helyezése is lassú, időigényes és nehéz a hibakeresővel való használatra.

A Kubernetes helyi folyamata elkerüli a kód felépítését és üzembe helyezését a fürtön ahelyett, hogy közvetlenül a fejlesztői számítógép és a fürt között hozza létre a kapcsolatot. A fejlesztői számítógép a fürthöz való csatlakoztatása során a hibakeresés lehetővé teszi, hogy a teljes alkalmazás kontextusában gyorsan tesztelje és fejlessze a szolgáltatást, anélkül, hogy Docker-vagy Kubernetes-konfigurációt hozna létre.

A Kubernetes helyi folyamata átirányítja a forgalmat a csatlakoztatott Kubernetes-fürt és a fejlesztői számítógép között. Ez a forgalom átirányítása lehetővé teszi a Kubernetes-fürtön futó fejlesztői számítógép és szolgáltatások programkódjának használatát úgy, mintha ugyanazon a Kubernetes-fürtön belül legyenek. A Kubernetes helyi folyamata lehetővé teszi a környezeti változók és a csatlakoztatott kötetek elérhetővé tételét a Kubernetes-fürtön lévő hüvelyek számára a fejlesztői számítógépén. A fejlesztői számítógép környezeti változóinak és csatlakoztatott köteteinek hozzáférésének biztosításával gyorsan dolgozhat a kódban anélkül, hogy ezeket a függőségeket manuálisan replikálja.

Ebből az útmutatóból megtudhatja, hogyan használhatja a helyi folyamatot a Kubernetes a Kubernetes-fürt és a fejlesztői számítógépen futó kód közötti adatforgalom átirányításához. Ez az útmutató egy olyan parancsfájlt is biztosít, amely egy Kubernetes-fürtön található több szolgáltatással rendelkező nagyméretű alkalmazás üzembe helyezését is lehetővé teszi.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][preview-terms]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

Ez az útmutató az [Azure dev Spaces Bike Sharing Sample Application][bike-sharing-github] használatával mutatja be a fejlesztői számítógép Kubernetes-fürthöz való csatlakoztatását. Ha már rendelkezik egy Kubernetes-fürtön futó saját alkalmazással, továbbra is követheti az alábbi lépéseket, és használhatja a saját szolgáltatásainak nevét.

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* Az [Azure CLI telepítve van][azure-cli].
* MacOS vagy Windows 10 rendszeren futó [Visual Studio Code][vs-code] .
* Az [Azure dev Spaces][azds-vs-code] bővítmény 2.0.220200601 vagy újabb verziója telepítve van a Visual Studio Code-ban.
* Az [Azure dev Spaces CLI telepítve van][azds-cli].

## <a name="create-a-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Hozzon létre egy AK-fürtöt egy [támogatott régióban][supported-regions]. Az alábbi parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>A minta alkalmazás telepítése

Telepítse a minta alkalmazást a fürtön a megadott parancsfájl használatával. Ezt a szkriptet futtathatja a fejlesztői számítógépen, vagy használhatja a [Azure Cloud Shell][azure-cloud-shell].

> [!IMPORTANT]
> A parancsfájl futtatásához *tulajdonosi* vagy *közreműködői* hozzáféréssel kell rendelkeznie a fürthöz.

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

Nyissa meg a fürtöt futtató minta alkalmazást a nyilvános URL-cím megnyitásával, amely a telepítési parancsfájl kimenetében jelenik meg.

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

A fenti példában a nyilvános URL-cím: `dev.bikesharingweb.EXTERNAL_IP.nip.io` .

## <a name="connect-to-your-cluster-and-debug-a-service"></a>A fürthöz való kapcsolódás és a szolgáltatás hibakeresése

A fejlesztői számítógépen töltse le és konfigurálja a Kubernetes CLI-t a Kubernetes-fürthöz való kapcsolódáshoz az [az AK Get-hitelesítő adatok][az-aks-get-credentials]használatával.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Nyissa meg a *dev-Spaces/Samples/BikeSharingApp/Bikes* elemet az [Azure dev Spaces Bike Sharing minta alkalmazásban][bike-sharing-github] a Visual Studio Code-ban. Nyissa meg az Azure Kubernetes Service bővítményt, és válassza ki a *fejlesztői* névteret a *MyAKS* -fürtben.

![Névtér kiválasztása](../media/local-process-kubernetes-vs-code/select-namespace.png)

Használja az `npm install` parancsot az alkalmazás függőségeinek telepítéséhez.

```console
npm install
```

Válassza a bal oldali *hibakeresés* ikont, és válassza a *helyi folyamat elemet a Kubernetes (előzetes verzió)* lehetőséggel a tetején.

![Helyi folyamat kiválasztása a Kubernetes](../media/local-process-kubernetes-vs-code/choose-local-process.png)

Kattintson a Start gombra a *helyi folyamat mellett a Kubernetes (előzetes verzió)* lehetőségre. Amikor először futtatja ezt az indítási konfigurációt, a rendszer felszólítja a lecserélni kívánt szolgáltatás konfigurálására, a fejlesztési számítógépről továbbítandó portra, valamint az indítási feladat használatára.

Válassza a *Bikes* szolgáltatást.

![Szolgáltatás kiválasztása](../media/local-process-kubernetes-vs-code/choose-service.png)

A Kubernetes-fürtben lévő összes forgalom átirányítva lesz a *Bikes* szolgáltatásnak a fejlesztői számítógépén futó alkalmazás verziójára. A Kubernetes helyi folyamata az alkalmazásból érkező összes kimenő forgalmat is a Kubernetes-fürtre irányítja át.

> [!IMPORTANT]
> Csak egyetlen Pod-vel rendelkező szolgáltatásokat lehet átirányítani.

A szolgáltatás kiválasztása után a rendszer felszólítja, hogy adja meg a helyi alkalmazás TCP-portját. Ehhez a példához adja meg a *3000*értéket.

![Csatlakozás a porthoz](../media/local-process-kubernetes-vs-code/choose-port.png)

A Launch (indítás) művelettel válassza a *NPM keresztül* lehetőséget.

![A kapcsolat elindítása feladat kiválasztása](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> A rendszer felszólítja, hogy engedélyezze a *KubernetesDNSManager* futtatását, és módosítsa a gazdagépek fájlját.

A fejlesztői számítógép akkor csatlakozik, ha az állapotsor narancssárga színűre változik, és a dev Spaces bővítmény azt mutatja, hogy csatlakozik.

![Fejlesztői számítógép csatlakoztatva](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> A subesquent indításakor nem fog megjelenni a szolgáltatás neve, portja vagy indítási feladata. Ezeket az értékeket a *. vscode/tasks.json tárolja a*rendszer.

A fejlesztői számítógép csatlakoztatása után a forgalom a cserélni kívánt szolgáltatáshoz irányítja át a fejlesztési számítógépét.

## <a name="set-a-break-point"></a>Töréspont beállítása

Nyissa meg [server.js][server-js-breakpoint] , és kattintson a 233. sorban a kurzorra. Állítsa be a töréspontot az *F9 billentyű* lenyomásával, vagy kattintson a *Futtatás* , majd a *töréspont váltása*gombra

Nyissa meg a minta alkalmazást a nyilvános URL-cím megnyitásával. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Figyelje meg, hogy a kerékpár képe nem töltődik be. Térjen vissza a Visual Studio Code-ba, és figyelje meg, hogy ki van emelve a 233. sor. A megadott töréspont szüneteltette a szolgáltatást a 233. sorban. A szolgáltatás folytatásához nyomja le az *F5 billentyűt* , vagy kattintson a *Futtatás* , majd a *Folytatás*gombra. Térjen vissza a böngészőhöz, és ellenőrizze, hogy megjelenik-e a bike helyőrző képe.

Távolítsa el a töréspontot úgy, hogy a kurzort az 233-es sorra helyezi, `server.js` és az *F9 billentyűt*

### <a name="update-your-application"></a>Az alkalmazás frissítése

Szerkesztés `server.js` a 234 és a 235 sorok eltávolításához:

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

Mentse a módosításokat, majd kattintson a *Futtatás* , majd a *hibakeresés újraindítása*elemre. Az újrakapcsolást követően frissítse a böngészőt, és győződjön meg arról, hogy már nem jelenik meg a bike helyőrző képe.

Kattintson a *Futtatás* parancsra, majd *állítsa le a hibakeresést* a hibakereső leállításához.

> [!NOTE]
> Alapértelmezés szerint a hibakeresési feladat leállítása a Kubernetes-fürtről is leválasztja a fejlesztői számítógépet. Ezt a viselkedést megváltoztathatja a *helyi folyamat keresésével a Kubernetes: válassza le a hibakeresést* a Visual Studio Code-beállításokban, és távolítsa el az *automatikus leválasztást a hibakeresés befejeződése után.* A beállítás frissítése után a fejlesztői számítógép továbbra is csatlakoztatva marad, amikor leállítja és elindítja a hibakeresést. A fejlesztői számítógép a fürtről való leválasztásához kattintson az Azure dev Spaces bővítményre az állapotsorban, majd válassza az *aktuális munkamenet leválasztása*lehetőséget.
>
> Ha a Visual Studio Code hirtelen megszakítja a kapcsolatot a fürttel, vagy leáll, előfordulhat, hogy az átirányított szolgáltatás nem állítható vissza az eredeti állapotába, mielőtt a helyi folyamattal csatlakozik a Kubernetes. A probléma megoldásához tekintse meg a [hibaelhárítási útmutatót][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>A naplózás és a diagnosztika használata

A naplózási kimenet a fejlesztői *helyek* ablakba íródik, miután a fejlesztési számítógép csatlakozik a Kubernetes-fürthöz.

![Kimenet](../media/local-process-kubernetes-vs-code/output.png)

Kattintson az Azure dev Spaces állapotsorra, és válassza a *kapcsolati diagnosztika adatainak megjelenítése*lehetőséget. Ez a parancs kinyomtatja az aktuális környezeti változókat és a DNS teljes értékét a naplózási kimenetben.

![Kimenet diagnosztika](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

Emellett a `Azure Dev Spaces` [fejlesztői számítógép *temp* könyvtárában][azds-tmp-dir]található diagnosztikai naplókat is megtalálhatja a címtárban.

## <a name="remove-the-sample-application-from-your-cluster"></a>A minta alkalmazás eltávolítása a fürtből

A megadott parancsfájl használatával távolítsa el a minta alkalmazást a fürtből.

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható az Azure dev Spaces és a GitHub-műveletek a lekéréses kérelemben szereplő módosítások tesztelésére közvetlenül az AK-ban, mielőtt a lekéréses kérelem beolvad a tárház fő ágában.

> [!div class="nextstepaction"]
> [GitHub-műveletek & Azure Kubernetes Service-ben][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download