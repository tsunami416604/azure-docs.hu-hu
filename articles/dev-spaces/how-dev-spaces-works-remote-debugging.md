---
title: A kód távoli hibakeresése az Azure dev Spaces működésével
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Ismerteti az Azure Kubernetes Service-ben az Azure dev Spaces szolgáltatással végzett távoli hibakeresés folyamatait
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80241399"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>A kód távoli hibakeresése az Azure dev Spaces működésével

Az Azure dev Spaces lehetővé teszi a Kubernetes-alkalmazások gyors megismétlését és hibakeresését, valamint a csapattal való együttműködését egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön. Miután a projekt egy fejlesztői térben fut, az Azure dev Spaces lehetővé teszi, hogy egy futó alkalmazást az AK-ban csatoljon és hibakeresést végezzen.

Ez a cikk bemutatja, hogyan működik a távoli hibakeresés a dev Spaces szolgáltatással.

## <a name="debug-your-code"></a>A kód hibakeresése

A Java, a .NET Core és a Node.js alkalmazások esetében a Visual Studio Code vagy a Visual Studio használatával közvetlenül a fejlesztői térben futtathatja alkalmazásait. A Visual Studio Code és a Visual Studio eszközöket biztosít a fejlesztői területhez való kapcsolódáshoz, az alkalmazás elindításához és egy hibakereső csatolásához. A Futtatás után `azds prep` megnyithatja a projektet a Visual Studio Code vagy a Visual Studio alkalmazásban. A Visual Studio Code vagy a Visual Studio létrehozza a saját konfigurációs fájljait a csatlakozáshoz, amely elkülönül a futtatástól `azds prep` . A Visual Studio Code-ból vagy a Visual studióból töréspontokat állíthat be, és elindíthatja az alkalmazást a fejlesztői területére.

![A kód hibakeresése](media/get-started-node/debug-configuration-nodejs2.png)

Ha a Visual Studio Code vagy a Visual Studio használatával indítja el az alkalmazást a hibakereséshez, a rendszer a futtatással megegyező módon kezeli az indítást és a csatlakozást a fejlesztői területhez `azds up` . Emellett a Visual Studio Code-ban és a Visual Studióban található ügyféloldali eszközök mindegyike egy további, a hibakereséshez szükséges adatokat tartalmazó paramétert biztosít. A paraméter tartalmazza a hibakereső rendszerképének nevét, a hibakereső lemezképében található hibakereső helyét, valamint az alkalmazás tárolójában lévő célhelyet a hibakereső mappa csatlakoztatásához.

A hibakereső rendszerképét a rendszer automatikusan meghatározza az ügyféloldali eszközkészlet alapján. A futtatáskor a Docker és a Helm diagram létrehozásakor használt metódushoz hasonló módszert használ `azds prep` . Miután a hibakereső csatlakoztatva van az alkalmazás rendszerképéhez, az a használatával fut `azds exec` .

## <a name="next-steps"></a>További lépések

Az Azure dev Spaces használatának megkezdéséhez a projekt távoli hibakereséséhez tekintse meg az alábbi rövid útmutatókat:

* [Gyors iteráció és hibakeresés a Visual Studio Code és a Java révén][quickstart-java]
* [Gyors iteráció és hibakeresés a Visual Studio Code és a .NET használatával][quickstart-netcore]
* [Gyors iteráció és hibakeresés a Visual Studio Code és Node.js][quickstart-node]
* [Gyors iteráció és hibakeresés a Visual Studióval és a .NET Core-val][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
