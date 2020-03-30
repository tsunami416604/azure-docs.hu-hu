---
title: Hogyan működik a kód távoli hibakeresési funkciója az Azure Dev Spaces segítségével?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Az Azure Kubernetes szolgáltatás és az Azure Dev Spaces távoli hibakeresési folyamatainak ismertetése
keywords: Azure dev spaces, fejlesztői terek, Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241399"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Hogyan működik a kód távoli hibakeresési funkciója az Azure Dev Spaces segítségével?

Az Azure Dev Spaces többféle lehetőséget kínál a Kubernetes-alkalmazások gyors iterálására és hibakeresésére, valamint a csapatával való együttműködésre egy Azure Kubernetes-szolgáltatás (AKS) fürtön. Miután a projekt egy fejlesztői térben fut, az Azure Dev Spaces lehetővé teszi, hogy csatolja és hibakeresés egy futó alkalmazás a KS-ben.

Ez a cikk ismerteti, hogyan működik a távoli hibakeresés a fejlesztői szóközök kel.

## <a name="debug-your-code"></a>Hibakeresés a kód

Java, .NET Core és Node.js alkalmazások esetén a Visual Studio Code vagy a Visual Studio segítségével közvetlenül a fejlesztői térben futó alkalmazás hibakeresésével is futtathatja az alkalmazást. A Visual Studio Code és a Visual Studio eszközként szolgál a fejlesztési területhez való csatlakozáshoz, az alkalmazás elindításához és a hibakereső csatolásához. A `azds prep`futás után megnyithatja a projektet a Visual Studio Kód vagy a Visual Studio alkalmazásban. A Visual Studio Code vagy a Visual Studio saját konfigurációs `azds prep`fájlokat hoz létre a csatlakozáshoz, amelyek elkülönülnek a futástól. A Visual Studio-kódból vagy a Visual Studio-ból töréspontokat állíthat be, és elindíthatja az alkalmazást a fejlesztői területre.

![A kód hibakeresése](media/get-started-node/debug-configuration-nodejs2.png)

Amikor elindítja az alkalmazást a Visual Studio Code vagy a Visual Studio használatával a hibakereséshez, `azds up`ugyanúgy kezelik a fejlesztői terület indítását és csatlakoztatását, mint a futást. Emellett a Visual Studio Code és a Visual Studio ügyféloldali eszközei további paramétert biztosítanak a hibakereséshez. A paraméter tartalmazza a hibakereső lemezképének nevét, a hibakereső helyét a hibakereső lemezképében, valamint az alkalmazás tárolójában lévő célhelyet a hibakereső mappának csatlakoztatásához.

A hibakereső-lemezképet automatikusan az ügyféloldali eszközbeállítás határozza meg. A Dockerfile és a Helm diagram során használtmódszerhez `azds prep`hasonló módszert használ, amelyet futáskor hoz létre. Miután a hibakereső csatlakoztatva van az alkalmazás lemezképéhez, a rendszer a használatával futtatja. `azds exec`

## <a name="next-steps"></a>További lépések

Az Azure Dev Spaces használatával a projekt távoli hibakereséséhez az alábbi rövid útmutatókat láthatja el:

* [Gyorsan iterálni és debug a Visual Studio kód és a Java][quickstart-java]
* [Gyorsan iterálni és hibakeresés a Visual Studio Code és a .NET][quickstart-netcore]
* [Gyorsan iterálni és hibakeresés a Visual Studio kód és Node.js][quickstart-node]
* [Gyorsan iterate és debug a Visual Studio és a .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
