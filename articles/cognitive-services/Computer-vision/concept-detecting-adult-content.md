---
title: Felnőtt és zamatos tartalom – Computer Vision
titleSuffix: Azure Cognitive Services
description: A felnőtt és a zamatos tartalom észlelésével kapcsolatos fogalmak a Computer Vision APi használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ca5f35ab47822d74de556671c38886942d23d9ff
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946286"
---
# <a name="detect-adult-and-racy-content"></a>A felnőtt és a zamatos tartalom észlelése

A Computer Vision képes felderíteni a képeken a felnőtt anyagokat, hogy a fejlesztők a szoftverben is korlátozzák az ilyen rendszerképek megjelenítését. A tartalom jelzői a nulla és az egyik pontszám között lesznek alkalmazva, így a fejlesztők a saját preferenciáiknak megfelelően tudják értelmezni az eredményeket. 

> [!NOTE]
> Ezt a funkciót az [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) szolgáltatás is felkínálja. Tekintse meg ezt az alternatívát a megoldásokhoz a szigorúbb tartalomkezelési forgatókönyvek, például a szöveges moderálás és az emberi felülvizsgálati munkafolyamatok terén.

## <a name="content-flag-definitions"></a>Content Flag-definíciók

A **felnőtt** képek olyanok, amelyek pornográf jellegűek, és gyakran ábrázolják a meztelen és a szexuális műveleteket. 

A zamatos képek olyan képként vannak definiálva, amelyek szexuálisan szuggesztív jellegűek, és gyakran kevésbé szexuálisan explicit tartalmú tartalmat tartalmaznak, mint a **felnőttként**megjelölt képek. 

## <a name="identify-adult-and-racy-content"></a>A felnőtt és a zamatos tartalom azonosítása

Az [elemzés](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API.

A rendszerkép elemzése módszer két logikai tulajdonságot ad `isAdultContent` vissza `isRacyContent`, a metódus JSON-válaszában pedig a felnőtt és a zamatos tartalmat. A metódus a két tulajdonságot `adultScore` `racyScore`is adja vissza, amelyek a felnőtt és a zamatos tartalom azonosításának megbízhatósági pontszámait jelölik.

## <a name="next-steps"></a>További lépések

A tartományra [jellemző tartalom észlelésével](concept-detecting-domain-content.md) és az [arcok észlelésével](concept-detecting-faces.md)kapcsolatos fogalmak megismerése.
