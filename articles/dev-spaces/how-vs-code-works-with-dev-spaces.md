---
title: Hogyan működik a Visual Studio Code az Azure dev Spaces-szel
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Hogyan működik a Visual Studio Code az Azure dev Spaces-szel
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 7809f0215137b0c473e13143404a031497906ebf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725818"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hogyan működik a Visual Studio Code az Azure dev Spaces-szel

Használhatja a Visual Studio Code és az [Azure dev Spaces bővítményt][azds-extension] a szolgáltatások előkészítéséhez, futtatásához és hibakereséséhez az Azure dev Spaces használatával. A Visual Studio Code és az Azure dev Spaces bővítménnyel a következőket teheti:

* Eszközök előállítása a szolgáltatások futtatásához és hibakereséséhez az AK-ban
* Java-, Node. js-és .NET Core-szolgáltatások futtatása egy fejlesztői térben
* A Java-, Node. js-és .NET Core-szolgáltatások közvetlen hibakeresése egy fejlesztői térben

## <a name="generate-assets"></a>Eszközök előállítása

A Visual Studio Code és az Azure dev Spaces bővítmény a következő eszközöket állítja elő a projekthez:

* Dockerfiles a Maven, Node. js-alkalmazásokat és .NET Core-alkalmazásokat használó Java-alkalmazásokhoz
* Helm-diagramok szinte bármilyen nyelvhez egy Docker
* Egy `azds.yaml` fájl, amely a projekthez tartozó [Azure dev Spaces konfigurációs fájl][azds-yaml]
* Egy `.vscode` olyan mappa, amelyen a Visual Studio Code elindítja a projekt Java-alkalmazásokhoz készült konfigurációját a Maven, a Node. js-alkalmazások és a .net Core-alkalmazások használatával

A Docker, a Helm diagram és `azds.yaml` a fájlok ugyanazokat az eszközöket generálják `azds prep`, amikor futnak. Ezek a fájlok a Visual Studio code-on kívül is használhatók a projekt AK-ban való futtatására `azds up`, például a futtatásra. A `.vscode` mappát csak a Visual Studio Code használja, hogy a projektet a Visual Studio Code-ból futtassa az AK-ban.

## <a name="run-your-service-in-aks"></a>A szolgáltatás futtatása AK-ban

Miután létrehozta a projekthez tartozó eszközöket, futtathatja Java-, Node. js-és .NET Core-szolgáltatásait egy meglévő fejlesztői térben a Visual Studio Code-ból. A Visual Studio Code *hibakeresés* lapján megnyithatja a konfiguráció `.vscode` elindítása a címtárból a projekt futtatásához.

Létre kell hoznia az AK-fürtöt, és engedélyeznie kell az Azure dev-helyeket a fürtön a Visual Studio code-on kívül. Használhatja például az Azure CLI-t vagy a Azure Portalt a beállítás elvégzéséhez. Újra felhasználhatja a meglévő Dockerfiles, Helm- `azds.yaml` diagramokat és a Visual Studio code-on kívül létrehozott fájlokat, például a `azds prep`futtatásával létrehozott eszközöket. Ha nem a Visual Studio code-on kívül generált eszközöket használ fel, akkor továbbra is `.vscode` rendelkeznie kell egy könyvtárral. Ezt `.vscode` a könyvtárat a Visual Studio Code és az Azure dev Spaces bővítmény újra létrehozhatja, és nem írja felül a meglévő eszközöket.

A .net Core-projektek esetében telepíteni kell a [ C# bővítményt][csharp-extension] a .net-szolgáltatás Visual Studio Code-ból való futtatásához. A Mavent használó Java-projektekhez is rendelkeznie kell a Java-hibakeresővel az [Azure dev Spaces bővítménnyel][java-extension] , valamint a [telepített és konfigurált Maven][maven] használatával, hogy a Java-szolgáltatás a Visual Studio Code-ból fusson.

## <a name="debug-your-service-in-aks"></a>Szolgáltatás hibakeresése az AK-ban

A projekt elindítása után a Java-, Node. js-és .NET Core-szolgáltatásait közvetlenül a Visual Studio Code-ból futtathatja a fejlesztői térben. A `.vscode` könyvtár indítási konfigurációja további hibakeresési információkat biztosít a szolgáltatás futtatásához, ha a hibakeresés engedélyezve van egy fejlesztői térben. A Visual Studio Code a dev Spaces-ben futó tárolóban is kapcsolódik a hibakeresési folyamathoz, amely lehetővé teszi a töréspontok beállítását, a változók vizsgálatát és más hibakeresési műveletek elvégzését.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>A Visual Studio Code használata az Azure dev Spaces használatával

A Visual Studio Code és az Azure dev Spaces bővítmény az Azure dev Spaces szolgáltatással használható az alábbi rövid útmutatókban:

* [Gyors iteráció és hibakeresés a Visual Studio Code és a Java révén][quickstart-java]
* [Gyors iteráció és hibakeresés a Visual Studio Code és a .NET használatával][quickstart-netcore]
* [Gyors iteráció és hibakeresés a Visual Studio Code és a Node. js-sel][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
