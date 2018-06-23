---
title: A nyelvi elemzés API metódus elemzése |} Microsoft Docs
description: Hogyan nyelvi elemzés API elemzés metódus segítségével bizonyos természetes nyelvű bemeneti adatok elemzéséhez.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 12/13/2016
ms.author: lesun
ms.openlocfilehash: b17a00f31845bfa05572dff7ca94e9a1ffd69586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347107"
---
# <a name="analyze-method"></a>Metódus elemzése

A **elemzése** REST API-t egy adott természetes nyelvű bemeneti elemzésére használatos.
Amely vonatkozhat csak keresése a [mondatokat és a jogkivonatok](Sentences-and-Tokens.md) belül, amely bemeneti, keresése a [része a beszéd címkék](POS-tagging.md), vagy kereséséhez a [constitutency fa](Constituency-Parsing.md).
Megadhatja, hogy mely ki a megfelelő elemzőkkel kívánt eredményt.
Az összes rendelkezésre álló elemzőkkel listázásához, tekintse meg a  **[elemzőkkel](AnalyzersMethod.md)**.

Vegye figyelembe, hogy meg kell adnia a bemeneti karakterlánc nyelvét.

**REST-végpont:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>A kérés paraméterei

Name (Név) | Típus | Szükséges | Leírás
-----|-------|----------|------------
**nyelv**    | sztring | Igen | A két betű elemzési célokra ISO nyelvi kódot. Angol, például "hu".
**analyzerIds** | karakterlánc-listával | Igen | Alkalmazandó elemzőkkel azonosítóját listája. További információ a lekérdezések dokumentációjában talál.
**szöveg**        | sztring | Igen | A vizsgálandó nyers bemenet. Ez lehet például egy szót vagy kifejezést, teljes néhány mondatot, vagy egy teljes bekezdés vagy discourse rövid karakterlánc.

<br>
## <a name="response-json"></a>Válasz (JSON)
Elemzés tömbjét adja kimenetként, egy minden attribútum a kérelemben megadott.

Az eredmények a hasonlítania:

Name (Név) | Típus | Leírás
-----|------|--------------
analyzerId | sztring | A megadott elemző GUID azonosítója
eredménye | objektum | Analyzer eredménye

Vegye figyelembe, hogy a eredményének típusa a bemeneti analyzer típusától függ.

### <a name="tokens-response-json"></a>Jogkivonatok válasz (JSON)

Name (Név) | Típus | Leírás
-----|------|-------------
eredménye | mondat objektumok listája | szövegében azonosított mondat határok |
[x] eredménye. Eltolása | int | minden mondat kezdési karaktereltolás |
[x] eredménye. Hossz | int | hosszának karakterszáma minden mondat |
[x] eredménye. Jogkivonatok | token objektumok listája | token határain belül a mondatok azonosított |
[x] eredménye. [-Y] jogkivonatokat. Eltolása | int | a jogkivonat kezdési karaktereltolás |
[x] eredménye. [-Y] jogkivonatokat. Hossz | int | a jogkivonat karakter hosszúságú |
[x] eredménye. [-Y] jogkivonatokat. RawToken | sztring | a karakterek belül, hogy a jogkivonat normalizálási előtt |
[x] eredménye. [-Y] jogkivonatokat. NormalizedToken | sztring | a karakter, biztonságosnak normalizált formája egy [elemzési fa](Constituency-Parsing.md); például egy nyitó zárójel karakter "(" - LRB - válik |

Példa bemenet: "Ez egy tesztművelet. Hello. "
Példa egy válasz JSON:
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


### <a name="pos-tags-response-json"></a>POS címkék válasz (JSON)

Eredménye listák karakterláncok listáját.
Minden mondat nincs POS címkék listáját, a tokenek egy POS címkét.
Az egyes POS címkék token megfelelő megkereséséhez érdemes kérje meg, valamint a lexikális elemekké alakításának objektum.

### <a name="constituency-tree-response-json"></a>Választókerülete fa válasz (JSON)

A karakterlánc-listával, a minden mondat helyett szerepel a bemenetben található egy elemzési fa eredménye.
A parse fák zárójelezett formában jelennek meg.

<br>

## <a name="example"></a>Példa

`POST /analyze`

Kérelemtörzset: JSON-adattartalmat
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?" 
}
```

Válasz: JSON-ban
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

