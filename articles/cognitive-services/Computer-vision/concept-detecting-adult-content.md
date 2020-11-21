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
ms.openlocfilehash: 5cc8a4508ceeda245fbc10a81e16f3ecf05284c7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013620"
---
# <a name="detect-adult-content"></a>Felnőtt tartalom észlelése

A Computer Vision képes felderíteni a képeken elérhető felnőtt anyagokat, hogy a fejlesztők a szoftverben korlátozzák a képek megjelenítését. A tartalom jelzői a nulla és az egyik pontszám között lesznek alkalmazva, így a fejlesztők a saját preferenciáiknak megfelelően tudják értelmezni az eredményeket.

> [!NOTE]
> A funkció nagy részét az [Azure Content moderator](../content-moderator/overview.md) szolgáltatás kínálja. Tekintse meg ezt az alternatívát a megoldásokhoz a szigorúbb tartalomkezelési forgatókönyvek, például a szöveges moderálás és az emberi felülvizsgálati munkafolyamatok terén.

## <a name="content-flag-definitions"></a>Content Flag-definíciók

A "felnőtt" besorolás több különböző kategóriába esik:

- A **felnőtt** képeket a kifejezetten szexuális természetű képek határozzák meg, és gyakran a meztelenség és a szexuális tevékenységek láthatók.
- A **zamatos** képek olyan képként vannak definiálva, amelyek szexuálisan szuggesztív jellegűek, és gyakran kevésbé szexuálisan explicit tartalmú tartalmat tartalmaznak, mint a **felnőttként** megjelölt képek.
- A **véres** képeket a Gore-t ábrázoló képek határozzák meg.

## <a name="use-the-api"></a>Az API használata

A felnőtt tartalmat a [képek elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API-val ismerheti meg. Amikor hozzáadja a értéket `Adult` a **visualFeatures** lekérdezési paraméterhez, az API három logikai tulajdonságot ad vissza, &mdash; `isAdultContent` és a `isRacyContent` `isGoryContent` &mdash; JSON-válaszában. A metódus a megfelelő tulajdonságokat is adja vissza, &mdash; `adultScore` `racyScore` `goreScore` &mdash; amelyek a nulla és az egyik kategóriába tartozó megbízhatósági pontszámokat jelölik.

- [Gyors útmutató: rendszerkép elemzése (.NET SDK)](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)