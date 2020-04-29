---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272718"
---
| Adattípus | Lambda kifejezésekben engedélyezett funkciók`any` | Lambda kifejezésekben engedélyezett funkciók`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Minden, `search.ismatch` kivéve és`search.ismatchscoring` | Ugyanaz |
| `Collection(Edm.String)` | Összehasonlítás a vagy `eq` a`search.in` <br/><br/> Alkifejezések kombinálása a`or` | Összehasonlítás a vagy `ne` a`not search.in()` <br/><br/> Alkifejezések kombinálása a`and` |
| `Collection(Edm.Boolean)` | Összehasonlítás a vagy `eq` a`ne` | Ugyanaz |
| `Collection(Edm.GeographyPoint)` | A `geo.distance` vagy `lt` a használatával`le` <br/><br/> `geo.intersects` <br/><br/> Alkifejezések kombinálása a`or` | A `geo.distance` vagy `gt` a használatával`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Alkifejezések kombinálása a`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Összehasonlítások a `eq`, `ne`, `lt`, `gt` `le`,, vagy`ge` <br/><br/> Összehasonlítások kombinálása más alkifejezésekkel a használatával`or` <br/><br/> Összehasonlítások kombinálása `ne` , kivéve más alkifejezésekkel`and` <br/><br/> A `and` és `or` a [diszjunkt kötelezőségi normál formában (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) használt kifejezések | Összehasonlítások a `eq`, `ne`, `lt`, `gt` `le`,, vagy`ge` <br/><br/> Összehasonlítások kombinálása más alkifejezésekkel a használatával`and` <br/><br/> Összehasonlítások kombinálása `eq` , kivéve más alkifejezésekkel`or` <br/><br/> A `and` és `or` a [Conjunctive normál formában (cnf)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) használt kifejezések |
