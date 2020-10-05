---
title: 'Első lépések: az első Bing Custom Search-példány létrehozása'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval létrehozhat egy egyéni Bing-példányt, amely képes a definiált tartományokra és weblapokra.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b8287250df4e278d4904e31121ed7d2df208e1c9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80238852"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rövid útmutató: az első Bing Custom Search-példány létrehozása

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

6.  Kattintson a **termelés** lapra a **végpontok**területen, és másolja az **egyéni konfigurációs azonosítót**. Erre az AZONOSÍTÓra szüksége lesz a Custom Search API meghívásához, hogy hozzáfűzi a `customconfig=` lekérdezési paraméterhez a hívásokban.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: az Bing Custom Search-végpont meghívása](./call-endpoint-csharp.md)
