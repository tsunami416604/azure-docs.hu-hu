---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079638"
---
| Adattípus | A lambda-kifejezésekkel engedélyezett szolgáltatások `any` | A lambda-kifejezésekkel engedélyezett szolgáltatások `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Kivételével mindent `search.ismatch` és `search.ismatchscoring` | Azonos |
| `Collection(Edm.String)` | Az összehasonlítás `eq` vagy `search.in` <br/><br/> Az alkifejezések kombinálása `or` | Az összehasonlítás `ne` vagy `not search.in()` <br/><br/> Az alkifejezések kombinálása `and` |
| `Collection(Edm.Boolean)` | Az összehasonlítás `eq` vagy `ne` | Azonos |
| `Collection(Edm.GeographyPoint)` | Használatával `geo.distance` a `lt` vagy `le` <br/><br/> `geo.intersects` <br/><br/> Az alkifejezések kombinálása `or` | Használatával `geo.distance` a `gt` vagy `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Az alkifejezések kombinálása `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Összehasonlítások használatával `eq`, `ne`, `lt`, `gt`, `le`, vagy `ge` <br/><br/> Összehasonlítások egyesül más alkifejezések használatával `or` <br/><br/> Kivéve összehasonlítások kombinálásával `ne` az egyéb az alkifejezések használatával `and` <br/><br/> Kifejezések, kombinációjával `and` és `or` a [diszjunkt normál űrlapot (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Összehasonlítások használatával `eq`, `ne`, `lt`, `gt`, `le`, vagy `ge` <br/><br/> Összehasonlítások egyesül más alkifejezések használatával `and` <br/><br/> Kivéve összehasonlítások kombinálásával `eq` az egyéb az alkifejezések használatával `or` <br/><br/> Kifejezések, kombinációjával `and` és `or` a [Conjunctive normál űrlapot (használható a cnf Paraméterrel)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
