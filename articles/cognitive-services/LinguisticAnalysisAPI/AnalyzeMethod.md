---
title: Elemzési módszer – nyelvi elemzési API
titlesuffix: Azure Cognitive Services
description: Hogyan használható az elemzés módszer nyelvi elemzési API bizonyos természetes nyelvű bemeneti adatok elemzéséhez.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 2b02b048719dd7707db7e97df3641a314b512177
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861680"
---
# <a name="analyze-method"></a>Elemzési módszer

> [!IMPORTANT]
> A Linguistic Analysis előzetes verzióját 2018. augusztus 9-én visszavontuk. A szövegek feldolgozásához és elemzéséhet az [Azure Machine Learning szövegelemzési moduljait](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) javasoljuk használni.

A **elemzése** REST API egy adott természetes nyelvű bemeneti elemzésére szolgál.
Amely is járhat, csak keresése a [mondatokra és lexikális](Sentences-and-Tokens.md) belül, amelyek bemeneti, keresés, a [része a beszéd, a címkék](POS-tagging.md), vagy kereséséhez a [constitutency fa](Constituency-Parsing.md).
Megadhatja, hogy melyik a megfelelő elemzők kiválasztásával a kívánt eredményt.
Az összes rendelkezésre álló elemzők listázásához, tekintse meg a  **[elemzők](AnalyzersMethod.md)**.

Vegye figyelembe, hogy meg kell adnia a bemeneti karakterlánc nyelvét.

**REST-végpont:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>A kérés paraméterei

Name (Név) | Typo | Kötelező | Leírás
-----|-------|----------|------------
**language**    | Karakterlánc | Igen | A kétbetűs ISO nyelv kódja elemzéshez használható. Például angol nyelven az "en".
**analyzerIds** | karakterláncok | Igen | Az elemzők a alkalmazni GUID azonosítók listája. További információ az elemzők dokumentációjában talál.
**text**        | Karakterlánc | Igen | Nyers bemeneti elemezni. Ez lehet például egy szót vagy kifejezést, egy teljes mondatot, vagy egy teljes bekezdés vagy discourse rövid karakterlánc.

## <a name="response-json"></a>Válasz (JSON)

Elemzés tömbjét adja vissza, egy minden attribútum a kérelemben megadott.

Az eredményeket a hasonlítania:

Name (Név) | Típus | Leírás
-----|------|--------------
analyzerId | Karakterlánc | A megadott elemző GUID-ja
Eredmény | objektum | elemző eredménye

Vegye figyelembe, hogy az eredmény típusát a bemeneti analyzer típusától függ.

### <a name="tokens-response-json"></a>Jogkivonatok válasz (JSON)

Name (Név) | Típus | Leírás
-----|------|-------------
Eredmény | mondat objektumok listája | a szövegben azonosított mondat határok |
[x] eredményt. Eltolás | int | minden mondat ofszetet karakter |
[x] eredményt. Len | int | minden mondat karakterszámát |
[x] eredményt. Jogkivonatok | jogkivonat-objektumok listája | az adott mondaton belül azonosított token határok |
[x] eredményt. Token [y]. Eltolás | int | a jogkivonat ofszetet karakter |
[x] eredményt. Token [y]. Len | int | a jogkivonat karakterszámát |
[x] eredményt. Token [y]. RawToken | Karakterlánc | Ezt a jogkivonatot, mielőtt normalizálási belül a karakterek |
[x] eredményt. Token [y]. NormalizedToken | Karakterlánc | a karakter, biztonságosan használható legyen a normalizált űrlap egy [elemzési fa](Constituency-Parsing.md); például egy nyitó zárójel karakter "(" - LRB - válik |

Példabemenet: "Ez egy tesztművelet. Hello.'
Példa JSON-választ:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>POS-címkék válasz (JSON)

Ez listák karakterláncok listáját.
Minden mondat POS-címkék listája egy POS címke minden tokenhez nincs.
Minden egyes POS-címkék a token megfelelő megkereséséhez érdemes kérje meg, valamint a jogkivonatok objektum.

### <a name="constituency-tree-response-json"></a>Vevőkör fa válasz (JSON)

Az eredmény az lesz, a karakterláncok listájának, egy elemzési fa minden a bemenetben található mondat helyett szerepel.
Az elemzési fa zárójelek között szereplő formában jelennek meg.

## <a name="example"></a>Példa

`POST /analyze`

Kérelem törzse: JSON-adattartalmat
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Válasz: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
