---
title: Felnőtt, pikáns, véres tartalom – Computer Vision
titleSuffix: Azure Cognitive Services
description: Az Computer Vision APi-val a képeken a felnőtt tartalom észlelésével kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718517"
---
# <a name="detect-adult-content"></a>Felnőtt tartalom észlelése

A Computer Vision képes felderíteni a képeken elérhető felnőtt anyagokat, hogy a fejlesztők a szoftverben korlátozzák a képek megjelenítését. A tartalom jelzői a nulla és az egyik pontszám között lesznek alkalmazva, így a fejlesztők a saját preferenciáiknak megfelelően tudják értelmezni az eredményeket.

> [!NOTE]
> A funkció nagy részét az [Azure Content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) szolgáltatás kínálja. Tekintse meg ezt az alternatívát a megoldásokhoz a szigorúbb tartalomkezelési forgatókönyvek, például a szöveges moderálás és az emberi felülvizsgálati munkafolyamatok terén.

## <a name="content-flag-definitions"></a>Content Flag-definíciók

A "felnőtt" besorolás több különböző kategóriába esik:

- A **felnőtt** képeket a kifejezetten szexuális természetű képek határozzák meg, és gyakran a meztelenség és a szexuális tevékenységek láthatók.
- A **zamatos** képek olyan képként vannak definiálva, amelyek szexuálisan szuggesztív jellegűek, és gyakran kevésbé szexuálisan explicit tartalmú tartalmat tartalmaznak, mint a **felnőttként**megjelölt képek.
- A **véres** képeket a Gore-t ábrázoló képek határozzák meg.

## <a name="use-the-api"></a>Az API használata

A felnőtt tartalmat a [képek elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-val ismerheti meg. Ha `Adult` értéket ad hozzá a **visualFeatures** lekérdezési paraméterhez, az API három logikai tulajdonságot ad vissza&mdash;`isAdultContent`, `isRacyContent`és `isGoryContent`&mdash;a JSON-válaszában. A metódus a megfelelő tulajdonságokat&mdash;`adultScore`, `racyScore`és `goreScore`&mdash;is visszaadja, amely a nulla és az egyik kategóriába tartozó megbízhatósági pontszámokat jelöli.

- [Gyors útmutató: rendszerkép elemzése (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
