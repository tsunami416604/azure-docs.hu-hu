---
title: A Visual Studio Code az Azure fejlesztési tárolóhelyek működése
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: A Visual Studio Code az Azure fejlesztési tárolóhelyek működése
keywords: Az Azure fejlesztési tárolóhelyek, fejlesztői, szóközök, Docker, Kubernetes, Azure, az AKS, Azure Kubernetes Service, tárolók
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712149"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>A Visual Studio Code az Azure fejlesztési tárolóhelyek működése

Használhatja a Visual Studio Code és a [Azure fejlesztési tárolóhelyek bővítmény][azds-extension] előkészítése, futtatása és a szolgáltatások az Azure fejlesztési tárolóhelyek hibakeresés. A Visual Studio Code és az Azure fejlesztési tárolóhelyek bővítmény segítségével:

* Hozzon létre eszközök futtatására és az aks-ben szolgáltatások hibakeresésében
* A .NET Core, Java és Node.js szolgáltatások futtatása egy fejlesztési terület
* A fejlesztési szóközzel futó .NET Core, Java és Node.js szolgáltatások közvetlen hibakeresése

## <a name="generate-assets"></a>Adategységek létrehozása

A Visual Studio Code és az Azure fejlesztési tárolóhelyek-bővítmény létrehozása a következő eszközöket a projekthez:

* Docker-fájlok a Maven használata Java-alkalmazások, a Node.js-alkalmazások és a .NET Core-alkalmazások
* Szinte bármilyen nyelven, egy docker-fájlban a Helm-diagramok
* Egy `azds.yaml` fájlt, amely a [Azure fejlesztési tárolóhelyek konfigurációs fájl][azds-yaml] a projekthez
* A `.vscode` mappát a Visual Studio Code indítási konfigurációval a projektet Maven használata Java-alkalmazások, a Node.js-alkalmazások és a .NET Core-alkalmazások

A docker-fájlban, Helm-diagramot, és `azds.yaml` fájlok jönnek létre, ha a futó ugyanazokat az eszközöket `azds prep`. Ezeket a fájlokat is használható a Visual Studio code-en kívül a projekt futtatása az aks-ben, például a futó `azds up`. A `.vscode` mappa csak használják a Visual Studio Code-ot a projekt futtatása az aks-ben a Visual Studio Code-ból.

## <a name="run-your-service-in-aks"></a>A szolgáltatás futtatásához az aks-ben

Után az eszközök hozza létre a projektet, a Visual Studio Code-ból a .NET Core, Java és Node.js szolgáltatások futtathatja egy már létező fejlesztési területen. Az a *Debug* lap Visual Studio Code-ban, az indítási konfiguráció hívhat a `.vscode` könyvtárban futtassa a projektet.

Az AKS-fürt létrehozása és a fürtön a Visual Studio Code-en kívül Azure fejlesztési szóközt engedélyezni kell. Például használhatja az Azure CLI vagy az Azure Portalon ezt a beállítást. Használhat meglévő docker-fájlok, Helm-diagramok és `azds.yaml` például az eszközök futtatásával generált kívül a Visual Studio Code-ban létrehozott fájlok `azds prep`. Ha fel szeretné használni az eszközök a Visual Studio Code-en kívül létrehozott, továbbra is szüksége lesz egy `.vscode` könyvtár. Ez `.vscode` directory helyreállíthatja a Visual Studio code és az Azure fejlesztési tárolóhelyek bővítmény által, és nem írja felül a meglévő eszközök.

.NET Core-projektek, rendelkeznie kell a [ C# bővítmény][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] telepített, valamint [telepítse és konfigurálja a Mavent][maven] futtatásához a Java a szolgáltatás a Visual Studio Code-ból.

## <a name="debug-your-service-in-aks"></a>Az aks-ben a szolgáltatás hibakeresése

Után a projekt indításához, a Java, Node.js és a .NET Core szolgáltatások közvetlenül a Visual Studio Code egy fejlesztési térben futó hibakeresési is. A launch-konfigurációt a `.vscode` directory szolgáltatást futtató a hibakeresést a fejlesztési szóközt engedélyezni a további hibakeresési információkat szolgáltat. A Visual Studio Code-ot is csatolja a hibakeresési folyamatot a futó tárolót a fejlesztői szóközöket a break pontok beállítása, és megvizsgálhatja a változókat, más hibakeresési műveletek végrehajtása lehetővé teszi.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>A Visual Studio Code használata az Azure fejlesztői, szóközök

Visual Studio Code és az Azure fejlesztési tárolóhelyek bővítmény használata az Azure fejlesztési tárolóhelyek az alábbi rövid útmutatókat a tekintheti meg:

* [Fejlesztés Java nyelven][quickstart-java]
* [Fejlesztés a .NET használatával][quickstart-netcore]
* [Fejlesztés a Node.js használatával][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md