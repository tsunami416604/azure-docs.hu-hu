---
title: Academic Graph Entitásattribútumok – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: További információ az Academic Graph az Academic Knowledge API-val használható tevékenységentitás-attribútumok.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f227cc03578adcfbf73fec3ae8941045e8352513
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182999"
---
# <a name="entity-attributes"></a>Entitásattribútumok

A academic graph 7 típusú entitás tevődik össze. Minden entitás egy entitás azonosítója és a egy entitás típusa lesz.

## <a name="common-entity-attributes"></a>Közös tevékenységentitás-attribútumok
Name (Név)    |Leírás                |Typo       | Műveletek
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

