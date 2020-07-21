---
author: baanders
description: fájl belefoglalása az Azure-beli digitális ikrek korlátaira
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 21d910fb0e0992b35aa19ce65fc216734e30265c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515874"
---
### <a name="functional-limits"></a>Működési korlátok

Az alábbi táblázat felsorolja az Azure Digital Twins működési korlátait az aktuális előzetes verzióban.

| Terület | Képesség | Alapértelmezett korlát | Módosítható? |
| --- | --- | --- | --- |
| Azure-erőforrás | Azure digitális Twins-példányok száma egy adott régióban, előfizetés szerint | 10 | Igen |
| Digitális Twins | Az ikrek száma egy Azure-beli digitális Twins-példányban | 200,000 | Igen |
| Útválasztás | Végpontok száma egyetlen Azure Digital Twins-példányhoz | 6 | Nem |
| Útválasztás | Egyetlen Azure Digital Twins-példány útvonalai száma | 6 | Igen |
| Modellek | Egyetlen Azure Digital Twins-példányon belüli modellek száma | 10,000 | Igen |
| Modellek | Egyetlen API-hívásban feltölthethető modellek száma | 250 | Nem |
| Modellek | Egyetlen oldalon visszaadott elemek száma | 100 | Nem |
| Lekérdezés | Egyetlen oldalon visszaadott elemek száma | 100 | Nem |
| Lekérdezés | Kifejezések száma `AND`  /  `OR` egy lekérdezésben | 50 | Igen |
| Lekérdezés | Egy `IN`  /  `NOT IN` záradékban található tömb elemeinek száma | 50 | Igen |
| Lekérdezés | A lekérdezésben szereplő karakterek száma | 8,000 | Igen |
| Lekérdezés | `JOINS`Lekérdezésben szereplő szám | 1 | Igen |

### <a name="rate-limits"></a>Sebességkorlátok

Ez a táblázat a különböző API-k díjszabását tükrözi.

| API | Képesség | Alapértelmezett korlát | Módosítható? |
| --- | --- | --- | --- |
| Modellek API | Kérelmek másodpercenkénti száma | 100 | Igen |
| Digitális Twins API | Kérelmek másodpercenkénti száma | 1,000 | Igen |
| Lekérdezési API | Kérelmek másodpercenkénti száma | 500 | Igen |
| Lekérdezési API | Lekérdezési egység másodpercenként | 4,000 | Igen |
| Event Routes API | Kérelmek másodpercenkénti száma | 100 | Igen |

### <a name="other-limits"></a>Egyéb korlátok

Az Azure digitális Twins-modellekhez tartozó DTDL-dokumentumokban lévő adattípusokra és mezőkre vonatkozó korlátozások a GitHub dokumentációjában találhatók: [Digital Twins Definition Language (DTDL) – 2. verzió](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
A lekérdezés késésének részletei és az előzetes verzióra vonatkozó egyéb irányelvek a következő [*útmutatóban találhatók: a kettős gráf lekérdezése*](../articles/digital-twins/how-to-query-graph.md).