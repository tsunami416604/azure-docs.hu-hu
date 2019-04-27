---
title: Academic Graph Entitásattribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: További információ az Academic Graph az Academic Knowledge API-val használható tevékenységentitás-attribútumok.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340179"
---
# <a name="entity-attributes"></a>Entitásattribútumok

A academic graph 7 típusú entitás tevődik össze. Minden entitás kap egy entitás azonosítója és a egy entitástípust.

## <a name="common-entity-attributes"></a>Közös tevékenységentitás-attribútumok
Name (Név)    |Leírás                |Típus       | Műveletek
------- | ------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                  |Int64      |Egyenlő
Folyó évi      |Entitástípus                |Enum   |Egyenlő

## <a name="entity-type-enum"></a>Entitás típusa enum
Name (Név)                                                            |value
----------------------------------------------------------------|-----
[Tanulmány](PaperEntityAttributes.md)                               |0
[Szerző](AuthorEntityAttributes.md)                             |1
[Napló](JournalEntityAttributes.md)                           |2
[Konferenciasorozat](JournalEntityAttributes.md)                 |3
[Konferenciaesemény](ConferenceInstanceEntityAttributes.md)    |4
[Tagság](AffiliationEntityAttributes.md)                   |5
[Szakterület](FieldsOfStudyEntityAttributes.md)                      |6

