---
title: 'Gyors útmutató: Első Bing Custom Search-példány létrehozása |} A Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Ez a cikk segítségével hozzon létre egy egyéni Bing-példányt, amely képes keresési tartomány és a weblapok, Ön által meghatározott.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: aahi
ms.openlocfilehash: ba1444b3115ffd2c73e1773aa926d11fed81f830
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556118"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Gyors útmutató: Az első Bing Custom Search-példány létrehozása

A Bing Custom Search használatához hozzon létre egy Custom Search-példányt, amelyben meghatározza a nézetet vagy a web egy részletét. Ez a példány tartalmazza a nyilvános tartományok, webhelyek és, amelyek szeretne keresni, érdemes lehet ennek a területnek előtesztelést együtt. 

A példány létrehozásához használja a [Bing Custom Search portál](https://customsearch.ai). 

![A Bing Custom Search portál képe](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Egyéni keresési példány létrehozása

Bing Custom Search-példány létrehozása:

1. Kattintson a **Ismerkedés** a a [Bing Custom Search portál](https://customsearch.ai) weblapon, és jelentkezzen be Microsoft-fiókjával.

2. Kattintson a **új példányt**, és adjon meg egy leíró nevet. Bármikor módosíthatja a példány nevét.
 
3. Az **Active** (Aktív) lap **Search Experience** (Keresési felület) területén adja meg a keresésbe belefoglalni kívánt egy vagy több webhely URL-címét. 

    > [!NOTE]
    > Bing Custom Search-példányok csak tartományok és a nyilvános és a Bing által indexelt weblapjait eredményeket ad vissza.

4. A jobb oldalon, a Bing Custom Search portál használatával adjon meg egy lekérdezést, és vizsgálja meg a keresési eredményeket a keresési példány által visszaadott. Ha nem jár eredménnyel, adjon egy másik URL-címet.  

5. Kattintson a **közzététel** a módosítások közzététele az éles környezetbe, és frissítse a példány végpontok.

6.  Kattintson a a **éles** alatt fülre **végpontok**, másolatot a **egyéni konfigurációs azonosító**. Ezt az Azonosítót az egyéni keresési API hívása úgy, hogy van szüksége a `customconfig=` lekérdezési paraméter az Ön hívásait.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: A Bing Custom Search-végpont meghívása](./call-endpoint-csharp.md)