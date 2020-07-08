---
title: Helyi folyamat és Kubernetes használata a Visual Studióval (előzetes verzió)
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Ismerje meg, hogyan használható a helyi folyamat a Kubernetes a Visual Studióval a fejlesztői számítógép Kubernetes-fürthöz való összekapcsolásához az Azure dev Spaces használatával
keywords: Helyi folyamat a Kubernetes, az Azure dev Spaces, a dev Spaces, a Docker, a Kubernetes, az Azure, az AK, az Azure Kubernetes Service, a containers
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316802"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>Helyi folyamat és Kubernetes használata a Visual Studióval (előzetes verzió)

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
* A [Visual Studio 2019][visual-studio] Version 16,7 Preview 2 vagy újabb verziója fut a Windows 10-es verzióban a *ASP.net, a webfejlesztéssel* és az *Azure-fejlesztési* számítási feladatokkal.
* Az [Azure dev Spaces CLI telepítve van][azds-cli].

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>A helyi folyamat engedélyezése a Kubernetes előzetes verziójának szolgáltatásával a Visual Studióban

Ha engedélyezni szeretné a helyi folyamatot a Kubernetes a Visual Studióban, kattintson az *eszközök*  >  *beállításai*  >  *környezet*  >  *előzetes*verziójának funkciók elemre. Jelölje be *a Kubernetes-szolgáltatások helyi hibakeresésének engedélyezése*jelölőnégyzetet.

A .NET-konzol alkalmazásai esetében telepítse a *Microsoft. VisualStudio. Azure. Kubernetes. Tools. Targets* NuGet-csomagot.

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

Nyissa meg a *dev-Spaces/Samples/BikeSharingApp/ReservationEngine/app. csproj* elemet az [Azure dev Spaces Bike Sharing minta alkalmazásban][bike-sharing-github] a Visual Studióban.

A projektben válassza a *helyi folyamat a Kubernetes* lehetőséget az indítási beállítások legördülő menüből az alábbi ábrán látható módon.

![Helyi folyamat kiválasztása a Kubernetes](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

Kattintson a Start gombra a *helyi folyamat*mellett a Kubernetes használatával. A *helyi folyamat Kubernetes* párbeszédpanelen:

* Válassza ki az előfizetését.
* Válassza ki a fürthöz tartozó *MyAKS* .
* Válassza a *fejlesztői* lehetőséget a névtérhez.
* Válassza ki az átirányítani kívánt szolgáltatás *reservationengine* .
* Válassza az *alkalmazás* lehetőséget az indítási profilhoz.
* Válassza ki `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` az URL-címet a böngésző indításához.

![Helyi folyamat kiválasztása a Kubernetes-fürttel](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> Csak egyetlen Pod-vel rendelkező szolgáltatásokat lehet átirányítani.

Kattintson *a Mentés gombra, és indítsa el a hibakeresést*.

A rendszer a Kubernetes-fürtben lévő összes forgalmat átirányítja a *reservationengine* szolgáltatáshoz a fejlesztői számítógépen futó alkalmazás verziójára. A Kubernetes helyi folyamata az alkalmazásból érkező összes kimenő forgalmat is a Kubernetes-fürtre irányítja át.

> [!NOTE]
> A rendszer felszólítja, hogy engedélyezze a *KubernetesDNSManager* futtatását, és módosítsa a gazdagépek fájlját.

A fejlesztői számítógép akkor csatlakozik, ha az állapotsorban látható, hogy csatlakozik a *reservationengine* szolgáltatáshoz.

![Fejlesztői számítógép csatlakoztatva](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> A subesquent indításakor a rendszer nem fogja megkérdezni a *helyi folyamatot a Kubernetes* párbeszédpanellel. Ezeket a beállításokat a projekt tulajdonságai között található *hibakeresés* ablaktáblán frissítheti.

A fejlesztői számítógép csatlakoztatása után a forgalom a cserélni kívánt szolgáltatáshoz irányítja át a fejlesztési számítógépét.

## <a name="set-a-break-point"></a>Töréspont beállítása

Nyissa meg a [BikesHelper.cs][bikeshelper-cs-breakpoint] , és kattintson a 26. sorban a kurzorra. Állítsa be a töréspontot az *F9 billentyű* lenyomásával, vagy kattintson a *hibakeresés* elemre, majd a *töréspontot*

Nyissa meg a minta alkalmazást a nyilvános URL-cím megnyitásával. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Kattintson a *kerékpár kölcsönzése*lehetőségre. Térjen vissza a Visual studióhoz, és figyelje meg, hogy a 26. sor ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 26. sorban. A szolgáltatás folytatásához nyomja meg az *F5 billentyűt* , vagy kattintson a *hibakeresés* gombra, és *folytassa a művelettel*. Térjen vissza a böngészőhöz, és ellenőrizze, hogy a lap tartalmazza-e a kerékpár kölcsönzését.

Távolítsa el a töréspontot úgy, hogy a kurzort a 26. sorban helyezi el `BikesHelper.cs` , és az *F9 billentyűt*

> [!NOTE]
> Alapértelmezés szerint a hibakeresési feladat leállítása a Kubernetes-fürtről is leválasztja a fejlesztői számítógépet. Ennek a viselkedésnek a megváltoztatásához módosítsa a *leválasztást* a hibakeresési beállítások *Kubernetes hibakeresési eszközök* szakaszában, a hibák *hamis értékre* állításával. A beállítás frissítése után a fejlesztői számítógép továbbra is csatlakoztatva marad, amikor leállítja és elindítja a hibakeresést. A fejlesztői számítógép a fürtből való leválasztásához kattintson a *Leválasztás* gombra az eszköztáron.
>
> Ha a Visual Studio hirtelen leállítja a kapcsolatot a fürttel, vagy leáll, előfordulhat, hogy az átirányított szolgáltatás nem állítható vissza az eredeti állapotába, mielőtt a helyi folyamattal csatlakozik a Kubernetes. A probléma megoldásához tekintse meg a [hibaelhárítási útmutatót][troubleshooting].

## <a name="using-logging-and-diagnostics"></a>A naplózás és a diagnosztika használata

A diagnosztikai naplók a `Azure Dev Spaces` címtárban találhatók a [fejlesztői számítógép *temp* könyvtárában][azds-tmp-dir].

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
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/