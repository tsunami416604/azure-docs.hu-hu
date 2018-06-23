---
title: Academic Graph entitásattribútumhoz a Academic Knowledge API-hoz |} Microsoft Docs
description: További tudnivalók az entitás attribútumai a Academic Knowledge API Academic Graph használatával.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346850"
---
# <a name="entity-attributes"></a>Entitás attribútumai

A academic graph 7 típusú entitás tevődik össze. Minden entitás egy entitás azonosítója és entitástípus lesz.

## <a name="common-entity-attributes"></a>Közös Entitásattribútumhoz
Name (Név)    |Leírás                |Típus       | Műveletek
------- | ------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                  |Int64      |Egyenlő
Most      |Entitástípus                |Enum   |Egyenlő

## <a name="entity-type-enum"></a>Entitás típusa enum
Name (Név)                                                            |érték
----------------------------------------------------------------|-----
[Tanulmány](PaperEntityAttributes.md)                               |0
[Szerző](AuthorEntityAttributes.md)                             |1
[Napló](JournalEntityAttributes.md)                           |2
[Konferencia sorozat](JournalEntityAttributes.md)                 |3
[Konferencia példány](ConferenceInstanceEntityAttributes.md)    |4
[Tagság](AffiliationEntityAttributes.md)                   |5
[Vizsgálat mező](FieldsOfStudyEntityAttributes.md)                      |6

