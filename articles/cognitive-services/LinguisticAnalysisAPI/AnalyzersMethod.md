---
title: A lekérdezések metódus Lingistic elemzés API-ban |} Microsoft Docs
description: A lekérdezések REST API-t a jelenleg a szolgáltatás a Microsoft kognitív Services által támogatott elemzőkkel listáját tartalmazza.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346926"
---
# <a name="analyzers-method"></a>Elemzőkkel módszer

A **elemzőkkel** REST API-t a szolgáltatás által támogatott lekérdezések listáját tartalmazza.
A válasz tartalmazza a [nevek](Analyzer-Names.md) és egyes (például az angol nyelvű tájékoztatáshoz "hu") által támogatott nyelveket.

## <a name="request-parameters"></a>A kérés paraméterei
None

<br>

## <a name="response-parameters"></a>Válasz paraméterek
Name (Név) | Típus | Leírás
-----|------|--------------
nyelvek | karakterlánc-listával | két betű ISO nyelvkódjait, amelynek a analyzer használható listája.
id   | sztring | az elemző eszköz egyedi azonosítója
Típusa | sztring | Itt analyzer széleskörű típusa
Meghatározása | sztring | az elemző a meghatározás neve
Megvalósítása | sztring | a modell és/vagy az elemző eszköz mögött algoritmus leírása

<br>
## <a name="example"></a>Példa
/Analyzers beolvasása

Válasz: JSON-ban
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
