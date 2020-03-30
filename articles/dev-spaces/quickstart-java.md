---
title: 'Hibakeresés és iterálni a Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Dev Spaces és a Visual Studio Code segítségével a Java-alkalmazások hibakeresését és gyors iterálni az Azure Kubernetes-szolgáltatásban
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, Java, Helm, szolgáltatásháló, szolgáltatásháló útválasztás, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ac7a1b37b565f3589b7c049a3c1ed2a84972ded0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239738"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Rövid útmutató: Hibakeresés és műveletlebonyolítás a Kubernetes en visual studio-kóddal és Java – Azure dev spaces

Ebben a rövid útmutatóban az Azure Dev Spaces egy felügyelt Kubernetes-fürttel van beállítva, és a Visual Studio-kódban egy Java-alkalmazást használ a tárolókban lévő kód iteratív módon történő fejlesztéséhez és hibakereséséhez. Az Azure Dev Spaces segítségével hibakeresést és tesztelést az alkalmazás az Azure Kubernetes szolgáltatás (AKS) minimális fejlesztői gép beállítása. 

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Visual Studio kód](https://code.visualstudio.com/download).
- Az [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és a Java [debugger az Azure Dev Spaces-bővítmények](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) a Visual Studio-kódhoz.
- [Az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

AKS-fürtöt támogatott régióban kell [létrehoznia.][supported-regions] A következő parancsok létrehoznak egy *SamiresourceGroup* nevű erőforráscsoportot és egy *MyAKS*nevű AKS-fürtöt.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az Azure dev spaces engedélyezése az AKS-fürtön

A `use-dev-spaces` paranccsal engedélyezheti a fejlesztői szóközöket az AKS-fürtön, és kövesse az utasításokat. A következő parancs engedélyezi a fejlesztői szóközöket a *MyResourceGroup* csoport *MyAKS* fürtén, és létrehoz egy *alapértelmezett* fejlesztői területet.

> [!NOTE]
> A `use-dev-spaces` parancs az Azure Dev Spaces CLI-t is telepíti, ha még nincs telepítve. Az Azure Dev Spaces CLI nem telepíthető az Azure Cloud Shellben.

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

## <a name="get-sample-application-code"></a>Mintaalkalmazás kódjának beszereznie

Ebben a cikkben az [Azure Dev Spaces mintaalkalmazás](https://github.com/Azure/dev-spaces) használatával bemutatja az Azure Dev Spaces használatával.

Klónozza az alkalmazást a GitHubról.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>A mintaalkalmazás előkészítése a Visual Studio-kódban

Nyissa meg a Visual Studio-kódot, válassza a **Fájl,** majd **a Megnyitás**lehetőséget, keresse meg a *fejlesztői tereket/mintákat/java/getting-started/webfrontend* könyvtárat, és válassza a **Megnyitás**lehetőséget.

Most már meg van nyitva a *webfrontend* projekt a Visual Studio-kódban. Az alkalmazás futtatásához a fejlesztői térben, hozza létre a Docker és a Helm diagram eszközök az Azure Dev Spaces bővítmény a parancspaletta használatával.

A Parancspaletta Visual Studio-kódban való megnyitásához válassza **a Nézet,** majd **a Parancspaletta**lehetőséget. Kezdje el `Azure Dev Spaces` a gépelést, és válassza az **Azure Dev Spaces: Konfigurációs fájlok előkészítése az Azure Dev Spaces**számára lehetőséget.

![Konfigurációs fájlok előkészítése az Azure dev spaces-hez](./media/common/command-palette.png)

Amikor a Visual Studio-kód az alaplemezképek, a kitett port `Azul Zulu OpenJDK for Azure (Free LTS)` és a `8080` nyilvános végpont konfigurálását `Yes` is kéri, válassza ki az alapképet, a kitett portot, és engedélyezze a nyilvános végpontot.

![Alapkép kijelölése](media/get-started-java/select-base-image.png)

![A szabadon elérhető port kijelölése](media/get-started-java/select-exposed-port.png)

![Nyilvános végpont kiválasztása](media/get-started-java/select-public-endpoint.png)

Ez a parancs előkészíti a projekt futtatását az Azure Dev Spaces egy Dockerfile és Helm diagram létrehozásával. Ez is létrehoz egy *.vscode* könyvtár hibakeresési konfiguráció a gyökere a projekt.

> [!TIP]
> A [Dockerfile és helm diagram](how-dev-spaces-works-prep.md#prepare-your-code) a projekt az Azure Dev Spaces segítségével hozza létre és futtatja a kódot, de módosíthatja ezeket a fájlokat, ha módosítani szeretné, hogyan a projekt épül és fuss.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kód létrehozása és futtatása a Kubernetes alkalmazásban a Visual Studio-kódból

A bal oldalon válassza a **Hibakeresés** ikont, és felül válassza a **Java program indítása (AZDS)** lehetőséget.

![Java program indítása](media/get-started-java/debug-configuration.png)

Ez a parancs létrehozza és futtatja a szolgáltatást az Azure Dev Spaces.This command builds and runs your service in Azure Dev Spaces. Az alsó **terminálablak** az Azure Dev Spaces szolgáltatást futtató szolgáltatás buildkimenetét és URL-címeit jeleníti meg. A **Hibakeresési konzol** megjeleníti a napló kimenetét.

> [!Note]
> Ha nem látja az Azure Dev Spaces parancsokat a **parancspalettán,** győződjön meg arról, hogy telepítette a [Visual Studio kódbővítményét](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)az Azure Dev Spaces számára. Ellenőrizze azt is, hogy megnyitotta-e a *dev-spaces/samples/java/getting-started/webfrontend* könyvtárat a Visual Studio Code-ban.

A szolgáltatás a nyilvános URL megnyitásával láthatja.

Válassza **a Debug,** majd a **Debugging leállítása** lehetőséget a hibakereső leállításához.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának telepítéséhez frissítheti a projekt bármely fájlját, és újrafuttathatja **a Launch Java Program (AZDS) programot.** Példa:

1. Ha az alkalmazás továbbra is fut, válassza **a Hibakeresés** lehetőséget, majd a **Hibakeresés leállítása** lehetőséget a leállításához.
1. A [19-es `src/main/java/com/ms/sample/webfrontend/Application.java` vonal](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) frissítése a következőhöz:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Mentse a módosításokat.
1. Futtassa **a Java program (AZDS) elindítását.**
1. Nyissa meg a futó szolgáltatást, és figyelje meg a módosításokat.
1. Válassza **a Debug,** majd **a Hibakeresés leállítása** lehetőséget az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Indítsa el a szolgáltatást **a Launch Java Program (AZDS)** segítségével. Ez hibakeresési módban is futtatja a szolgáltatást.

Navigáljon vissza az **Intéző** nézetbe a **Nézet,** majd **az Intéző**lehetőség kiválasztásával. *Nyisd meg az src/main/java/com/com/sample/webfrontend/Application.java-t,* és kattints valahol a 19-es vonalon, hogy a kurzorodat oda helyezd. A töréspont beállításához nyomja le **az F9** billentyűt, vagy válassza **a Hibakeresés** lehetőséget, majd **kapcsolja be a Töréspontot**.

Nyissa meg a szolgáltatást a böngészőben, és észreveszi, hogy nem jelenik meg üzenet. Visszatérés a Visual Studio-kódhoz, és a 19-es sor figyelése ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 19-es sorban. A szolgáltatás folytatásához nyomja le az **F5 billentyűt,** vagy válassza **a Hibakeresés** lehetőséget, majd folytassa a **munkát.** Térjen vissza a böngészőbe, és figyelje meg, hogy az üzenet megjelenik.

A szolgáltatás Kubernetes-ben való futtatása közben egy hibakeresőcsatlakoztatva teljes hozzáféréssel rendelkezik a hibakeresési információkhoz, például a hívásveremhez, a helyi változókhoz és a kivételadatokhoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a 19-es vonalon *az src/main/java/com/com/ms/sample/webfrontend/Application.java* billentyűlenyomással, majd az F9 billentyű lenyomásával a **19-es**vonalon helyezi el.

## <a name="update-code-from-visual-studio-code"></a>Kód frissítése a Visual Studio kódjából

Amíg a szolgáltatás hibakeresési módban fut, frissítse a 19-es sort *az src/main/java/com/com/ms/sample/webfrontend/Application.java nyelven.* Példa:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Mentse a fájlt. Válassza **a Hibakeresés** lehetőséget, majd **a Hibakeresés újraindítása** lehetőséget, vagy a **Hibakeresés eszköztáron**válassza a **Hibakeresés újraindítása** gombot.

![Hibakeresés frissítése](media/common/debug-action-refresh.png)

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy megjelenik a frissített üzenet.

Ahelyett, hogy újralétre, és újraüzembe egy új tárolórendszerkép minden egyes alkalommal kód szerkesztések készülnek, az Azure Dev Spaces növekményesen újrafordítja a kódot a meglévő tárolón belül, hogy egy gyorsabb szerkesztési/hibakeresési hurok.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure Dev Spaces hogyan segít összetettebb alkalmazások fejlesztésében több tárolóközött, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést a kód különböző verzióival vagy ágaival való együttműködéssel különböző helyeken.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
