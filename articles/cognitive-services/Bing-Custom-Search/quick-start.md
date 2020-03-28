---
title: 'Első lépések: az első Bing Custom Search-példány létrehozása'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval egyéni Bing-példányt hozhat létre, amely képes a megadott tartományokban és weblapokon keresni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b8287250df4e278d4904e31121ed7d2df208e1c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238852"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rövid útmutató: az első Bing Custom Search-példány létrehozása

A Bing Custom Search használatához hozzon létre egy Custom Search-példányt, amelyben meghatározza a nézetet vagy a web egy részletét. Ez a példány tartalmazza a keresni kívánt nyilvános domaineket, webhelyeket és weblapokat, valamint a kívánt rangsorolási módosításokat. 

A példány létrehozásához használja a [Bing egyéni keresési portált.](https://customsearch.ai) 

![A Bing egyéni keresési portálja](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Egyéni keresési példány létrehozása

Bing Custom Search-példány létrehozása:

1. Kattintson az **Első lépések gombra** a [Bing egyéni keresési portálwebhelyének](https://customsearch.ai) webhelyén, és jelentkezzen be Microsoft-fiókjával.

2. Kattintson **az Új példány gombra,** és adjon meg egy leíró nevet. A példány nevét bármikor módosíthatja.
 
3. Az **Active** (Aktív) lap **Search Experience** (Keresési felület) területén adja meg a keresésbe belefoglalni kívánt egy vagy több webhely URL-címét. 

    > [!NOTE]
    > A Bing egyéni keresési példányai csak a nyilvános és a Bing által indexelt tartományok és weblapok eredményeit adják vissza.

4. A Bing egyéni keresési portál jobb oldalán lekérdezést írhat be, és megvizsgálhatja a keresési példány által visszaadott keresési eredményeket. Ha nem ad eredmény, próbáljon meg másik URL-címet megadni.  

5. Kattintson **a Közzététel gombra** az éles környezet módosításai közzétételéhez és a példány végpontjainak frissítéséhez.

6.  Kattintson a **Termelés** fülre **a Végpontok csoportban,** és másolja az egyéni **konfigurációs azonosítót.** Ehhez az azonosítóhoz szüksége van az egyéni keresési `customconfig=` API hívásához, ha hozzáfűzi a lekérdezési paraméterhez a hívásokban.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: A Bing egyéni keresési végpontjának hívása](./call-endpoint-csharp.md)
