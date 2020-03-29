---
title: Felnőtt, pikáns, véres tartalom - Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek felnőtt tartalmának a Computer Vision APi használatával történő észlelésével kapcsolatos fogalmak.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718517"
---
# <a name="detect-adult-content"></a>Felnőtt tartalom észlelése

A Computer Vision képes felismerni a képeken lévő felnőtt anyagokat, így a fejlesztők korlátozhatják ezeknek a képeknek a megjelenítését a szoftverükben. A tartalomjelzők nulla és egy közötti pontszámmal kerülnek alkalmazásra, így a fejlesztők saját preferenciáik szerint értelmezhetik az eredményeket.

> [!NOTE]
> A funkció nagy részét az [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) szolgáltatás kínálja. Tekintse meg ezt az alternatívát a szigorúbb tartalommoderálási forgatókönyvek, például a szövegmoderálás és az emberi ellenőrzés munkafolyamatainak megoldásaihoz.

## <a name="content-flag-definitions"></a>Tartalomjelző-definíciók

A "felnőtt" besoroláson belül több különböző kategória található:

- **A felnőtt** képek azok, amelyek kifejezetten szexuális jellegűek, és gyakran meztelenséget és szexuális aktusokat ábrázolnak.
- **Pikáns** képek meghatározása olyan képek, amelyek szexuálisan szuggesztív jellegű, és gyakran kevesebb szexuálisan explicit tartalom, mint a képek címkézett **Felnőtt**.
- **A véres** képek azok, amelyek gore-t ábrázolnak.

## <a name="use-the-api"></a>Az API használata

A felnőtteknek szóló tartalom észlelése a [Kép elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-val. Amikor hozzáadja a `Adult` **visualFeatures** lekérdezési paraméter értékét, az&mdash;`isAdultContent`API `isRacyContent`három `isGoryContent` &mdash;logikai tulajdonságot ad vissza, és a JSON-válaszában. A metódus a&mdash;`adultScore`megfelelő `racyScore`tulajdonságokat is visszaadja , és `goreScore` &mdash;az egyes kategóriákhoz 0 és egy közötti megbízhatósági pontszámokat jelöli.

- [Rövid útmutató: Lemezkép elemzése (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
