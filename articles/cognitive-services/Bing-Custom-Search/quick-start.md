---
title: 'Gyors útmutató: Első Bing Custom Search-példány létrehozása | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan hozhat létre olyan egyéni Bing-példányt, amely képes a definiált tartományokra és weblapokra.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 2806ca6f7079ffac3d2222363cd5b3839ef8f97d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405014"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Gyors útmutató: Az első Bing Custom Search példány létrehozása

A Bing Custom Search használatához hozzon létre egy Custom Search-példányt, amelyben meghatározza a nézetet vagy a web egy részletét. Ez a példány tartalmazza a keresni kívánt nyilvános tartományokat, webhelyeket és weblapokat, valamint az esetlegesen kívánt rangsorolási beállításokat. 

A példány létrehozásához használja a [Bing Custom Search portált](https://customsearch.ai). 

![A Bing Custom Search portál képe](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Egyéni keresési példány létrehozása

Bing Custom Search-példány létrehozása:

1. Kattintson az első **lépések** elemre a [Bing Custom Search portál](https://customsearch.ai) weboldalán, és jelentkezzen be a Microsoft-fiók.

2. Kattintson az **új példány**elemre, és adjon meg egy leíró nevet. A példány neve bármikor módosítható.
 
3. Az **Active** (Aktív) lap **Search Experience** (Keresési felület) területén adja meg a keresésbe belefoglalni kívánt egy vagy több webhely URL-címét. 

    > [!NOTE]
    > Bing Custom Search példányok csak a tartományok és a nyilvános és a Bing által indexelt weblapok eredményét adják vissza.

4. A Bing Custom Search portál jobb oldalán megadhat egy lekérdezést, és megvizsgálhatja a keresési példány által visszaadott keresési eredményeket. Ha nem ad vissza eredményt, próbáljon meg egy másik URL-címet beírni.  

5. Kattintson a **Közzététel** elemre a módosítások éles környezetben való közzétételéhez, és frissítse a példány végpontját.

6.  Kattintson a **termelés** lapra a **végpontok**területen, és másolja az **egyéni konfigurációs azonosítót**. Erre az azonosítóra szüksége lesz a Custom Search API meghívásához, hogy hozzáfűzi `customconfig=` a lekérdezési paraméterhez a hívásokban.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Bing Custom Search végpont meghívása](./call-endpoint-csharp.md)
