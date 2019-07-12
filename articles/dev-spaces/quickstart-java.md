---
title: Fejlesztés Java nyelven, a Kubernetes Azure fejlesztési tárolóhelyek használatával
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Gyors, tárolók, mikroszolgáltatások és a Java Azure-beli Kubernetes-fejlesztés
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Java, Helm, szolgáltatás-háló, szolgáltatás háló az Útválasztás, a kubectl, k8s
manager: gwallace
ms.openlocfilehash: b3e199f38f6f57cf10991f7e03757b8b603f74ad
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706864"
---
# <a name="quickstart-develop-with-java-on-kubernetes-using-azure-dev-spaces"></a>Gyors útmutató: Fejlesztés Java nyelven, a Kubernetes Azure fejlesztési tárolóhelyek használatával

Ebből az útmutatóból a következőket tudhatja meg:

- Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
- Iteratív fejlesztés kód a Visual Studio Code-tárolókban.
- A Visual Studio Code-ból a fejlesztési tárhely kódok hibakeresése.


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free).
- [A Visual Studio Code telepítése](https://code.visualstudio.com/download).
- A [Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) és [Java hibakereső az Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) telepítve a Visual Studio Code-bővítmények.
- [Telepített Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Telepítse és konfigurálja a mavent](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Az Azure Kubernetes Service-fürt létrehozása

Az AKS-fürt létrehozásához szükséges egy [támogatott régió][supported-regions]. Az alábbi parancsokkal nevű erőforráscsoport létrehozása *MyResourceGroup* és a egy AKS-fürt nevű *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Az AKS-fürt az Azure fejlesztési tárolóhelyek engedélyezése

Használja a `use-dev-spaces` parancs fejlesztési szóközt engedélyezni az AKS-fürt, és kövesse az utasításokat. Az alábbi parancs segítségével fejlesztési tárolóhelyek a *MyAKS* a fürt a *MyResourceGroup* csoportból, és létrehoz egy *alapértelmezett* fejlesztési terület.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Minta az alkalmazáskód letöltése

Ebben a cikkben fogja használni a [Azure fejlesztési tárolóhelyek mintaalkalmazás](https://github.com/Azure/dev-spaces) bemutatása, Azure-fejlesztési szóközzel.

Klónozza az alkalmazást a Githubról.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>A mintaalkalmazás a Visual Studio Code előkészítése

Nyissa meg a Visual Studio Code, kattintson a *fájl* majd *megnyitása...* , keresse meg a *fejlesztési-tárolóhelyek/samples/java/első-lépések/webfrontend* könyvtárra, és kattintson *nyílt*.

Most már a *webfrontend* projektben nyissa meg a Visual Studio Code-ban. Az alkalmazás futtatásához a fejlesztési tárhelyre, hozzon létre a Docker és a Helm diagram eszközök, az Azure fejlesztési tárolóhelyek bővítmény használatával a parancs kívánt színére.

A Visual Studio Code-ban a Parancskatalógus megnyitásához kattintson a *nézet* majd *Parancskatalógus*. Kezdje el beírni `Azure Dev Spaces` , majd kattintson a `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Konfigurációs fájlok előkészítése Azure-fejlesztési tárolóhelyek](./media/common/command-palette.png)

Ha a Visual Studio Code-ot is megkéri, hogy konfigurálja az alaprendszerképek lekéréshez, elérhetővé tett port és a nyilvános végponthoz, válassza `Azul Zulu OpenJDK for Azure (Free LTS)` az alaprendszerképet a `8080` a elérhetővé tett port és `Yes` engedélyezése egy nyilvános végpontot.

![Az alap-rendszerkép kiválasztása](media/get-started-java/select-base-image.png)

![Válassza ki az elérhetővé tett port](media/get-started-java/select-exposed-port.png)

![Select public endpoint](media/get-started-java/select-public-endpoint.png)

Ez a parancs előkészíti a projekthez, hogy egy docker-fájl és a Helm-diagram létrehozása az Azure fejlesztési tárolóhelyek futtatásához. Is előállít egy *.vscode* könyvtárat, amely a projekt gyökerében konfigurációs hibakeresés.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Létrehozása és kód futtatása a Kubernetesben a Visual Studióból

Kattintson a *hibakeresése* ikonra a bal oldalon, majd a *indítsa el a Java-Program (AZDS)* tetején.

![Indítsa el a Java-Program](media/get-started-java/debug-configuration.png)

A parancs épít, és a szolgáltatás fut az Azure fejlesztési szóközöket. A *terminálon* ablak alján látható a felépítési művelet kimenetében és URL-címek az Azure fejlesztési tárolóhelyek futó szolgáltatás. A *hibakeresési konzolt* napló kimenetét mutatja be.

> [!Note]
> Ha nem látja az Azure fejlesztési tárolóhelyek parancsok a *Parancskatalógus*, győződjön meg arról, hogy telepítette a [Visual Studio Code-bővítmény az Azure fejlesztési tárolóhelyek](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Ellenőrizze azt is nyitotta meg a *fejlesztési-tárolóhelyek/samples/java/első-lépések/webfrontend* könyvtárat a Visual Studio Code-ot.

A nyilvános URL-cím megnyitásával fut a szolgáltatás megjelenik.

Kattintson a *Debug* majd *hibakeresés leállításához* a hibakereső leállítása.

## <a name="update-code"></a>Kód frissítése

A szolgáltatás frissített verziójának üzembe helyezéséhez bármilyen fájl frissítése a projekt és futtassa újra a *indítsa el a Java-Program (AZDS)* . Példa:

1. Ha az alkalmazás továbbra is fut, kattintson a *Debug* majd *hibakeresés leállításához* leállításához.
1. Frissítés [a 19. sor `src/main/java/com/ms/sample/webfrontend/Application.java` ](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) való:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Mentse a módosításokat.
1. Ismétlés *indítsa el a Java-programot (AZDS)* .
1. Keresse meg a futó szolgáltatás, és vizsgálja meg a módosításokat.
1. Kattintson a *Debug* majd *hibakeresés leállításához* az alkalmazás leállításához.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Beállítás és hibakeresési töréspontok használatával

A szolgáltatás használatának megkezdéséhez *indítsa el a Java-Program (AZDS)* . Ez is fut a szolgáltatás a hibakeresési mód.

Lépjen vissza a *Explorer* nézet kattintva *nézet* majd *Explorer*. Nyissa meg `src/main/java/com/ms/sample/webfrontend/Application.java` kattintson valahol van helyezze a kurzort a 19. sorban. Állítson be egy töréspontot, nyomja le az *F9* , vagy kattintson *Debug* majd *töréspont*.

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy nem jelenik meg. Térjen vissza a Visual Studio Code-ot, és tekintse meg az 19 sor van kijelölve. A töréspont beállított 19. sorban a szolgáltatás szünetel. A szolgáltatás folytatásához nyomja le az *F5* , vagy kattintson *Debug* majd *Folytatás*. Térjen vissza a böngészőben, és figyelje meg, hogy az üzenet jelenik meg.

Miközben fut a szolgáltatás a Kubernetes egy hibakeresővel, akkor a hibakeresési információkat, például a hívási verem, a helyi változókat és a kivétel adatai teljes hozzáféréssel rendelkezik.

A töréspont eltávolítása ehhez vigye a kurzort 19. sorban a `src/main/java/com/ms/sample/webfrontend/Application.java` és lenyomásával *F9*.

## <a name="update-code-from-visual-studio-code"></a>A Visual Studio Code-ból kódjának frissítése

A szolgáltatás futása hibakeresési módban, frissítse a 19. sorban található `src/main/java/com/ms/sample/webfrontend/Application.java`. Példa:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Mentse a fájlt. Kattintson a *Debug* majd *indítsa újra a hibakeresés* vagy a a *hibakeresési eszköztár*, kattintson a *indítsa újra a hibakeresés* gombra.

![Frissítse a hibakeresés](media/common/debug-action-refresh.png)

Egy böngészőben nyissa meg a szolgáltatást, és figyelje meg, hogy a frissített üzenet jelenik meg.

Újraépítése és a egy új tárolórendszerképet minden alkalommal, amikor a kód módosításokat végzett, az Azure fejlesztési tárolóhelyek növekményes újbóli üzembe helyezés helyett újrafordítja a kódot a Szerkesztés/debug gyorsabb hurkot biztosít a meglévő tárolón belül.

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások törlése

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ismerje meg, Azure fejlesztési tárolóhelyek segítségével miként több tárolóra kiterjedő összetettebb alkalmazásokat fejleszthet, és egyszerűsítését által biztosított együttműködési környezettel fejlesztési különböző verziói vagy az ágak a kódot a különböző tárolóhelyek segítségével.

> [!div class="nextstepaction"]
> [Több tároló használata és fejlesztés csapatban](multi-service-java.md)


[supported-regions]: about.md#supported-regions-and-configurations