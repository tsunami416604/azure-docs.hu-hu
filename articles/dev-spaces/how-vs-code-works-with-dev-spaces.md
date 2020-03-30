---
title: A Visual Studio-kód és az Azure Dev Spaces szolgáltatás
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Megtudhatja, hogy a Visual Studio-kód és az Azure Dev Spaces hogyan segít a Kubernetes-alkalmazások hibakeresésében és gyors itertálásában
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240449"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>A Visual Studio-kód és az Azure Dev Spaces szolgáltatás

A Visual Studio-kód és az [Azure Dev Spaces bővítmény][azds-extension] segítségével előkészítheti, futtathatja és debugálhatja a szolgáltatásokat az Azure Dev Spaces segítségével. A Visual Studio-kód és az Azure Dev Spaces bővítmény segítségével a következőket teheti:

* Eszközök létrehozása az AKS-ben futó és hibakeresési szolgáltatásokhoz
* A Java, node.js és .NET Core szolgáltatások futtatása fejlesztési területen
* Közvetlenül hibakeresés a jáde térben futó Java, Node.js és .NET Core szolgáltatások között

## <a name="generate-assets"></a>Eszközök létrehozása

A Visual Studio Code és az Azure Dev Spaces bővítmény a következő eszközöket hozza létre a projekthez:

* Dockerfiles For Java alkalmazások Maven, Node.js alkalmazások és .NET Core alkalmazások
* Helm diagramok szinte bármilyen nyelvhez egy Dockerfile-fájllal
* Egy `azds.yaml` fájl, amely a projekt [Azure Dev Spaces konfigurációs fájlja][azds-yaml]
* A `.vscode` Project Java-alkalmazásokhoz készült, Maven, Node.js alkalmazásokat és .NET Core alkalmazásokat használó Visual Studio Code indítási konfigurációjával rendelkező mappa

A Dockerfile, a `azds.yaml` Helm diagram és a fájlok `azds prep`ugyanazok az eszközök, amelyek a futás során jönnek létre. Ezek a fájlok a Visual Studio kódján kívül is használhatók a `azds up`projekt AKS-ben való futtatásához, például a futtatáshoz. A `.vscode` mappát a Visual Studio kódja csak a Visual Studio-kódból használja a projekt AKS-ben való futtatásához.

## <a name="run-your-service-in-aks"></a>A szolgáltatás futtatása az AKS-ben

Miután létrehozta a projekt hez készült eszközöket, futtathatja a Java, Node.js és .NET Core szolgáltatásokat egy meglévő fejlesztői térben a Visual Studio-kódból. A Visual Studio-kód *Hibakeresési* lapján meghívhatja `.vscode` a projekt futtatásához a könyvtárból származó indítási konfigurációt.

Létre kell hoznia az AKS-fürtöt, és engedélyeznie kell az Azure Dev Spaces-t a fürtben a Visual Studio-kódon kívül. Például használhatja az Azure CLI vagy az Azure Portalon ezt a beállítást. A meglévő Docker-fájlokat, Helm-diagramokat és `azds.yaml` a Visual Studio-kódon kívül létrehozott `azds prep`fájlokat, például a futás által létrehozott eszközöket újra felhasználhatja. Ha a Visual Studio-kódon kívül létrehozott eszközöket újra felhasználja, továbbra is rendelkeznie kell egy `.vscode` könyvtárral. Ezt `.vscode` a könyvtárat a Visual Studio-kód és az Azure Dev Spaces bővítmény újragenerálhatja, és nem írja felül a meglévő eszközöket.

A .NET Core projektek esetében telepítve kell lennie a [C# kiterjesztésnek][csharp-extension] ahhoz, hogy a .NET szolgáltatást a Visual Studio-kódból futtatja. A Maven-t használó Java-projektek esetében is telepítve kell lennie a [Java Debugger for Azure Dev Spaces bővítménynek,][java-extension] valamint a [Maven telepítve és konfigurálva][maven] a Java szolgáltatás Visual Studio-kódból való futtatásához.

## <a name="debug-your-service-in-aks"></a>A szolgáltatás hibakeresése az AKS-ben

A projekt elindítása után közvetlenül a Visual Studio-kódból debugálhatja a jáde-térben futó Java, Node.js és .NET Core szolgáltatásokat. A címtárban `.vscode` lévő indítási konfiguráció további hibakeresési információkat biztosít egy olyan szolgáltatás futtatásához, amelyen a hibakeresés engedélyezve van a fejlesztési területen. A Visual Studio Code a fejlesztői terekben futó tárolóban is kapcsolódik a hibakeresési folyamathoz, lehetővé téve a töréspontok beállítását, a változók vizsgálatát és más hibakeresési műveletek elvégzését.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>A Visual Studio-kód használata az Azure dev spaces használatával

A Visual Studio-kód és az Azure Dev Spaces bővítmény az Azure Dev Spaces szolgáltatással együttműködve a következő rövid útmutatókban látható:

* [Gyorsan iterálni és debug a Visual Studio kód és a Java][quickstart-java]
* [Gyorsan iterálni és hibakeresés a Visual Studio Code és a .NET][quickstart-netcore]
* [Gyorsan iterálni és hibakeresés a Visual Studio kód és Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
