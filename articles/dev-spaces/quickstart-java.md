---
title: 'Hibakeresés és iteráció a Kubernetes-on: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure dev Spaces és a Visual Studio Code egy Java-alkalmazás hibakereséséhez és gyors megismétléséhez az Azure Kubernetes Service-ben
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Java, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: c0fd035a0341e5e92b2706d833b24d5e5b87e018
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371566"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Gyors útmutató: Hibakeresés és iteráció a Kubernetes a Visual Studio Code és a Java-Azure dev Spaces-szel

Ebben a rövid útmutatóban egy felügyelt Kubernetes-fürttel állítja be az Azure dev Spaces-t, a Visual Studio Code-ban pedig egy Java-alkalmazást, hogy iteratív és hibakeresési kódot használjon a tárolókban. Az Azure dev Spaces lehetővé teszi az alkalmazás összes összetevőjének hibakeresését és tesztelését az Azure Kubernetes szolgáltatásban (ak) a minimális fejlesztői gép beállításával. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0 +](https://aka.ms/azure-jdks).
- [Maven 3.5.0 +](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Az [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és a [Java Debugger for Azure fejlesztői Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) bővítmények a Visual Studio Code-hoz.
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

Nyissa meg a Visual Studio Code-ot, válassza a **fájl** , majd a **Megnyitás**lehetőséget, navigáljon a *dev-Spaces/Samples/Java/Getting-Started/webfrontend* könyvtárba, és válassza a **Megnyitás**elemet.

Most már megnyitotta a *webfrontend* -projektet a Visual Studio Code-ban. Az alkalmazás fejlesztői tárhelyen való futtatásához a Docker és a Helm chart objektumokat a parancs paletta Azure dev Spaces bővítményének használatával hozhatja ki.

A Command paletta Visual Studio Code-ban való megnyitásához válassza a **nézet** , majd a **parancs paletta**lehetőséget. Kezdjen el gépelni `Azure Dev Spaces` , majd válassza **Az Azure dev Spaces: konfigurációs fájlok előkészítése az Azure dev Spaces szolgáltatáshoz**lehetőséget.

![Konfigurációs fájlok előkészítése az Azure dev Spaces szolgáltatáshoz](./media/common/command-palette.png)

Ha a Visual Studio Code azt is kéri, hogy konfigurálja az alaplemezképeket, az elérhető portot és a nyilvános végpontot, válassza ki `Azul Zulu OpenJDK for Azure (Free LTS)` az alaplemezképet a `8080` kitett porton, és `Yes` engedélyezze a nyilvános végpontot.

![Kiinduló rendszerkép kiválasztása](media/get-started-java/select-base-image.png)

![Kitett port kiválasztása](media/get-started-java/select-exposed-port.png)

![Nyilvános végpont kiválasztása](media/get-started-java/select-public-endpoint.png)

Ez a parancs előkészíti a projektet az Azure dev Spaces-ben való futtatásra egy Docker és egy Helm diagram létrehozásával. Létrehoz egy *. vscode* könyvtárat is, amely hibakeresési konfigurációval rendelkezik a projekt gyökerében.

> [!TIP]
> A projekthez tartozó [Docker és Helm diagramot](how-dev-spaces-works-prep.md#prepare-your-code) az Azure dev Spaces használja a kód összeállításához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Kód létrehozása és futtatása a Kubernetes a Visual Studio Code-ból

Válassza a bal oldali **hibakeresés** ikont, és válassza a felül a **Java program indítása (AZDS)** lehetőséget.

![Java-program elindítása](media/get-started-java/debug-configuration.png)

Ez a parancs az Azure dev Spaces szolgáltatásban hozza létre és futtatja a szolgáltatást. Az alján található **Terminálablak** megjeleníti az Azure dev Spaces szolgáltatást futtató szolgáltatáshoz tartozó Build kimenetet és URL-címeket. A **hibakeresési konzol** megjeleníti a napló kimenetét.

> [!Note]
> Ha nem látja az Azure dev Spaces-parancsokat a **parancssorban**, győződjön meg arról, hogy telepítette a [Visual Studio Code bővítményt az Azure dev Spaces szolgáltatáshoz](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Győződjön meg arról is, hogy megnyitotta a *dev-Spaces/Samples/Java/Getting-Started/webfrontend* könyvtárat a Visual Studio Code-ban.

A szolgáltatás futását a nyilvános URL-cím megnyitásával tekintheti meg.

Válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** a hibakereső leállításához.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez frissítheti a projektben lévő összes fájlt, és újra futtathatja a **Java program elindítása (AZDS)**. Például:

1. Ha az alkalmazás még fut, válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** .
1. [A 19. sor `src/main/java/com/ms/sample/webfrontend/Application.java` frissítése a következőre](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) :
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Mentse a módosításokat.
1. Futtassa újra a **Java program elindítása (AZDS)**.
1. Navigáljon a futó szolgáltatáshoz, és figyelje meg a módosításokat.
1. Válassza a **hibakeresés** lehetőséget, majd állítsa le a **hibakeresést** az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Töréspontok beállítása és használata hibakereséshez

Indítsa el a szolgáltatást a **Launch Java program (AZDS)** használatával. A szolgáltatás hibakeresési módban is fut.

A **nézet** , majd az **Intéző**lehetőség kiválasztásával térjen vissza a **tallózó** nézethez. Nyissa meg a *src/Main/Java/com/MS/Sample/webfrontend/Application. Java* -t, majd kattintson a 19. sorban a kurzorra. A Töréspont beállításához nyomja meg az **F9** billentyűt, vagy válassza a **hibakeresés** , majd a **töréspont váltása**lehetőséget.

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy nem jelenik meg üzenet. Térjen vissza a Visual Studio Code-ba, és figyelje meg, hogy a 19. sor ki van emelve. A beállított töréspont szüneteltette a szolgáltatást a 19. sorban. A szolgáltatás folytatásához nyomja le az **F5** billentyűt, vagy válassza a **hibakeresés** , majd a **Folytatás**lehetőséget. Térjen vissza a böngészőhöz, és figyelje meg, hogy az üzenet most megjelenik.

A szolgáltatás Kubernetes való futtatásakor a hibakeresőhöz csatolva teljes hozzáférése van a hibakeresési adatokhoz, például a hívási verem, a helyi változók és a kivételek adataihoz.

Távolítsa el a töréspontot úgy, hogy a kurzort a 19. sorban a *src/Main/Java/com/MS/Sample/webfrontend/Application. Java* és az **F9**billentyű lenyomásával helyezi el.

## <a name="update-code-from-visual-studio-code"></a>Kód frissítése a Visual Studio Code-ból

Amíg a szolgáltatás hibakeresési módban fut, frissítse a 19. sort a *src/Main/Java/com/MS/Sample/webfrontend/Application. Java*nyelven. Például:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Mentse a fájlt. Válassza **a hibakeresés lehetőséget, majd** **indítsa újra a hibakeresést** , vagy a hibakeresés **eszköztáron**válassza a **hibakeresés újraindítása** gombot.

![Hibakeresés frissítése](media/common/debug-action-refresh.png)

Nyissa meg a szolgáltatást egy böngészőben, és figyelje meg, hogy a frissített üzenet jelenik meg.

Az új Container-lemezképek újraépítése és újratelepítése helyett az Azure dev Spaces fokozatosan újrafordítja a kódot a meglévő tárolóban, így gyorsabb szerkesztési/hibakeresési hurkot biztosít.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy az Azure dev Spaces hogyan segíti az összetettebb alkalmazások fejlesztését több tárolóban, és hogyan egyszerűsítheti az együttműködésen alapuló fejlesztést, ha a kód különböző verzióival vagy ágaival dolgozik a különböző helyeken.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
