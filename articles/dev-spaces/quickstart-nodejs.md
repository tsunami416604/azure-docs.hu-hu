---
title: 'Hibakeresés és iteráció a Kubernetes-on: Visual Studio Code & Node.js'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio Code egy Node.js-alkalmazás hibakereséséhez és gyors megismétléséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 7fcb60d3f020d911157735ce9632ed4157e2da4e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080419"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-nodejs---azure-dev-spaces"></a>Gyors útmutató: Hibakeresés és iteráció a Kubernetes a Visual Studio Code és a Node.js – Azure dev Spaces

Ebben a rövid útmutatóban egy felügyelt Kubernetes-fürttel állítja be az Azure dev Spaces-t, és a Visual Studio Code-ban egy Node.js alkalmazás használatával iteratív a kód fejlesztését és hibakeresését a tárolókban. Az Azure dev Spaces lehetővé teszi az alkalmazás összes összetevőjének hibakeresését és tesztelését az Azure Kubernetes szolgáltatásban (ak) a minimális fejlesztői gép beállításával. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [A Node.jslegújabb verziója ](https://nodejs.org/download/).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Az [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) bővítmény a Visual Studio Code-hoz.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-vel.
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Létre kell hoznia egy AK-fürtöt egy [támogatott régióban][supported-regions]. A következő parancsok létrehoznak egy *MyResourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AK-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev-helyek engedélyezése az AK-fürtön

A `use-dev-spaces` parancs használatával engedélyezze a fejlesztői szóközöket az AK-fürtön, és kövesse az utasításokat. A következő parancs engedélyezi a dev Spaces szolgáltatást a *MyAKS* -fürtön a *MyResourceGroup* csoportban, és létrehoz egy *alapértelmezett* fejlesztői helyet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure dev Spaces CLI-t is telepíti, ha még nincs telepítve. Nem telepítheti az Azure dev Spaces CLI-t a Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Minta alkalmazás kódjának beolvasása

Ebben a cikkben az [Azure dev Spaces minta alkalmazásával](https://github.com/Azure/dev-spaces) mutatjuk be az Azure dev Spaces használatát.

Az alkalmazás klónozása a GitHubról.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>A minta alkalmazás előkészítése a Visual Studio Code-ban

Nyissa meg a Visual Studio Code-ot, válassza a **fájl** , majd a **Megnyitás**lehetőséget, navigáljon a *dev-Spaces/Samples/NodeJS/Getting-Started/webfrontend* könyvtárba, és válassza a **Megnyitás**elemet.

Most már megnyitotta a *webfrontend* -projektet a Visual Studio Code-ban. Az alkalmazás fejlesztői tárhelyen való futtatásához a Docker és a Helm chart objektumokat a parancs paletta Azure dev Spaces bővítményének használatával hozhatja ki.

A Command paletta Visual Studio Code-ban való megnyitásához válassza a **nézet** , majd a **parancs paletta**lehetőséget. Kezdjen el gépelni `Azure Dev Spaces` , majd válassza **Az Azure dev Spaces: konfigurációs fájlok előkészítése az Azure dev Spaces szolgáltatáshoz**lehetőséget.

![Konfigurációs fájlok előkészítése az Azure dev Spaces szolgáltatáshoz](./media/common/command-palette.png)

Ha a Visual Studio Code azt is kéri, hogy konfigurálja a nyilvános végpontot, válassza a `Yes` nyilvános végpont engedélyezését.

![Nyilvános végpont kiválasztása](media/common/select-public-endpoint.png)

Ez a parancs előkészíti a projektet az Azure dev Spaces-ben való futtatásra egy Docker és egy Helm diagram létrehozásával. Létrehoz egy *. vscode* könyvtárat is, amely hibakeresési konfigurációval rendelkezik a projekt gyökerében.

> [!TIP]
> A projekthez tartozó [Docker és Helm diagramot](how-dev-spaces-works-prep.md#prepare-your-code) az Azure dev Spaces használja a kód összeállításához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kód létrehozása és futtatása a Kubernetes a Visual Studio Code-ból

Válassza a bal oldali **hibakeresés** ikont, és válassza a felül a **kiszolgáló indítása (AZDS)** lehetőséget.

![Kiszolgáló indítása](media/get-started-node/debug-configuration-nodejs.png)

Ez a parancs az Azure dev Spaces szolgáltatásban hozza létre és futtatja a szolgáltatást. Az alján található **Terminálablak** megjeleníti az Azure dev Spaces szolgáltatást futtató szolgáltatáshoz tartozó Build kimenetet és URL-címeket. A **hibakeresési konzol** megjeleníti a napló kimenetét.

> [!Note]
> Ha nem látja az Azure dev Spaces-parancsokat a **parancssorban**, győződjön meg arról, hogy telepítette a [Visual Studio Code bővítményt az Azure dev Spaces szolgáltatáshoz](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Győződjön meg arról is, hogy megnyitotta a *dev-Spaces/Samples/NodeJS/Getting-Started/webfrontend* könyvtárat a Visual Studio Code-ban.

A szolgáltatás futását a nyilvános URL-cím megnyitásával tekintheti meg.

Válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** a hibakereső leállításához.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez frissítheti a projektben lévő összes fájlt, majd újra futtathatja a **Launch Servert**. Például:

1. Ha az alkalmazás még fut, válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** .
1. [A 13. sor `server.js` frissítése a következőre](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) :
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Mentse a módosításokat.
1. Futtassa újra a **elindítja a kiszolgálót**.
1. Navigáljon a futó szolgáltatáshoz, és figyelje meg a módosításokat.
1. Válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Indítsa el a szolgáltatást a **Launch Server (AZDS)** használatával.

A **nézet** , majd az **Intéző**lehetőség kiválasztásával térjen vissza a tallózó nézethez. Nyissa meg *server.js* , majd a 13. sorban kattintson a kurzorra. A Töréspont beállításához nyomja meg az **F9** billentyűt, vagy válassza a **hibakeresés** , majd a **töréspont váltása**lehetőséget.

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy nem jelenik meg üzenet. Térjen vissza a Visual Studio Code-ba, és figyelje meg, hogy a 13. sor ki van emelve. A megadott töréspont szüneteltette a szolgáltatást a 13. sorban. A szolgáltatás folytatásához nyomja le az **F5** billentyűt, vagy válassza a **hibakeresés** , majd a **Folytatás**lehetőséget. Térjen vissza a böngészőhöz, és figyelje meg, hogy az üzenet most megjelenik.

A szolgáltatás Kubernetes való futtatásakor a hibakeresőhöz csatolva teljes hozzáférése van a hibakeresési adatokhoz, például a hívási verem, a helyi változók és a kivételek adataihoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a 13. sorban *server.js* és az **F9**billentyű lenyomásával.

Válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** a hibakereső leállításához.

## <a name="update-code-from-visual-studio-code"></a>Kód frissítése a Visual Studio Code-ból

Módosítsa a hibakeresési módot **egy kiszolgálóhoz (AZDS) való csatoláshoz** , és indítsa el a szolgáltatást:

![Képernyőkép a VS Code felhasználói felületéről, a Csatolás a kiszolgálóhoz (AZDS) lehetőség van kiválasztva.](media/get-started-node/attach-nodejs.png)

Ez a parancs az Azure dev Spaces szolgáltatásban hozza létre és futtatja a szolgáltatást. Emellett a szolgáltatás tárolójában is elindítja a nem [démoni](https://nodemon.io) folyamatot, és a vs Code-ot csatolja hozzá. A nem *démon* folyamat lehetővé teszi az automatikus újraindítást a forráskód módosításakor, ami lehetővé teszi a gyorsabb belső hurok-fejlesztést a helyi gépen való fejlesztéshez hasonló módon.

A szolgáltatás elindítása után navigáljon a böngészővel, és használja azt.

A szolgáltatás futása közben térjen vissza a VS Code-ra, és frissítse a 13. sort *server.js*. Például:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Mentse a fájlt, és térjen vissza a szolgáltatáshoz egy böngészőben. Lépjen kapcsolatba a szolgáltatással, és figyelje meg, hogy a frissített üzenet jelenik meg.

A nem *démon*futtatásakor a rendszer automatikusan újraindítja a csomópont-folyamatot, amint a kód változása észlelhető. Ez az automatikus újraindítási folyamat hasonló a szolgáltatás helyi gépen való szerkesztésének és újraindításának élményéhez, ami egy belső hurok-fejlesztési élményt biztosít.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
