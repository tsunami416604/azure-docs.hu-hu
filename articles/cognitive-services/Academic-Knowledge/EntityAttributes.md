---
title: Akadémiai gráf entitás attribútumai – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a Academic Knowledge API Academic Graph használatával használható entitás-attribútumokat.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705022"
---
# <a name="entity-attributes"></a>Entitás attribútumai

Az akadémiai gráf 7 típusú entitásból áll. Minden entitáshoz tartozik egy entitás azonosítója és egy entitás típusa.

## <a name="common-entity-attributes"></a>Általános entitás attribútumai
Name (Név)    |Leírás                |Type       | Műveletek
------- | ------------------------- | --------- | ----------------------------
Id      |Entitásazonosító                  |Int64      |Egyenlő
Úlyossága      |Entitástípus                |Enum   |Egyenlő

## <a name="entity-type-enum"></a>Entitás típusa Enum
Name (Név)                                                            |value
----------------------------------------------------------------|-----
[Tanulmány](PaperEntityAttributes.md)                               |0
[Szerző](AuthorEntityAttributes.md)                             |1
[Napló](JournalEntityAttributes.md)                           |2
[Konferencia sorozat](JournalEntityAttributes.md)                 |3
[Konferencia-példány](ConferenceInstanceEntityAttributes.md)    |4
[Tagság](AffiliationEntityAttributes.md)                   |5
[Tanulmányi mező](FieldsOfStudyEntityAttributes.md)                      |6

