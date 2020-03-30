---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272718"
---
| Adattípus | Lambda-kifejezésekben engedélyezett funkciók`any` | Lambda-kifejezésekben engedélyezett funkciók`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Mindent, `search.ismatch` kivéve és`search.ismatchscoring` | Ugyanaz |
| `Collection(Edm.String)` | Összehasonlítások `eq` a vagy`search.in` <br/><br/> Alkifejezések kombinálása`or` | Összehasonlítások `ne` a vagy`not search.in()` <br/><br/> Alkifejezések kombinálása`and` |
| `Collection(Edm.Boolean)` | Összehasonlítások `eq` a vagy`ne` | Ugyanaz |
| `Collection(Edm.GeographyPoint)` | Használata `geo.distance` `lt` a vagy`le` <br/><br/> `geo.intersects` <br/><br/> Alkifejezések kombinálása`or` | Használata `geo.distance` `gt` a vagy`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Alkifejezések kombinálása`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Összehasonlítások `eq`a `ne` `lt`, `gt` `le`, , , vagy`ge` <br/><br/> Összehasonlítások kombinálása más alkifejezésekkel`or` <br/><br/> Összehasonlítások kombinálása más `ne` részkifejezésekkel`and` <br/><br/> Kifejezések, amelyek a `and` `or` normál alak kombinációit és a [disjunctive (DNF) alakot használják](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Összehasonlítások `eq`a `ne` `lt`, `gt` `le`, , , vagy`ge` <br/><br/> Összehasonlítások kombinálása más alkifejezésekkel`and` <br/><br/> Összehasonlítások kombinálása más `eq` részkifejezésekkel`or` <br/><br/> Kifejezések `and` konf-féle `or` kombinációkat használva [és konjunctive normál alakban (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
