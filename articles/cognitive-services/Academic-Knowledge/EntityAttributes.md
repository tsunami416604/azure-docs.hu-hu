---
title: Academic Graph Entitásattribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: További információ az Academic Graph az Academic Knowledge API-val használható tevékenységentitás-attribútumok.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902790"
---
# <a name="entity-attributes"></a>Entitásattribútumok

A academic graph 7 típusú entitás tevődik össze. Minden entitás egy entitás azonosítója és a egy entitás típusa lesz.

## <a name="common-entity-attributes"></a>Közös tevékenységentitás-attribútumok
Name (Név)    |Leírás                |Típus       | Műveletek
------- | ------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                  |Int64      |Egyenlő
Folyó évi      |Entitástípus                |Enum   |Egyenlő

## <a name="entity-type-enum"></a>Entitás típusa enum
Name (Név)                                                            |érték
----------------------------------------------------------------|-----
[Tanulmány](PaperEntityAttributes.md)                               |0
[Szerző](AuthorEntityAttributes.md)                             |1
[Napló](JournalEntityAttributes.md)                           |2
[Konferenciasorozat](JournalEntityAttributes.md)                 |3
[Konferenciaesemény](ConferenceInstanceEntityAttributes.md)    |4
[Tagság](AffiliationEntityAttributes.md)                   |5
[Szakterület](FieldsOfStudyEntityAttributes.md)                      |6

