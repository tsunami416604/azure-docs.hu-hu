---
title: Az elemző módszer - Lingistic Analysis API
titlesuffix: Azure Cognitive Services
description: Az elemzők számát REST API-t a nyelvi elemzési API által jelenleg támogatott elemzők listáját tartalmazza.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 8bf13bffe763b88e95da94f885e30d271e36da42
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221683"
---
# <a name="analyzers-method"></a>Elemző módszer

> [!IMPORTANT]
> A Linguistic Analysis előzetes verzióját 2018. augusztus 9-én visszavontuk. A szövegek feldolgozásához és elemzéséhet az [Azure Machine Learning szövegelemzési moduljait](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) javasoljuk használni.

A **elemzők** REST API-t a szolgáltatás által jelenleg támogatott elemzők listáját tartalmazza.
A válasz tartalmazza a [nevek](Analyzer-Names.md) és egyes (például "hu" angol nyelven) által támogatott nyelveket.

## <a name="request-parameters"></a>A kérés paraméterei
None

<br>

## <a name="response-parameters"></a>Válasz paraméterek
Name (Név) | Típus | Leírás
-----|------|--------------
Nyelvek | karakterláncok | két betű ISO nyelvi kód, amelynek segítségével a analyzer listája.
id   | sztring | az elemző eszköz egyedi azonosítója
típusa | sztring | Itt analyzer széles körű típusa
Specifikáció | sztring | a meghatározás az elemző nevét
Megvalósítása | sztring | a modell és/vagy az elemző eszköz mögött algoritmus leírása

<br>
## <a name="example"></a>Példa
/Analyzers beolvasása

Válasz: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
