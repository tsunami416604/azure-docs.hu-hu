---
author: baanders
description: fájl belefoglalása az Azure-beli digitális ikrek korlátaira
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 22b00b41c7fce0af57fd9f92b0f42bbd9412afda
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87771072"
---
### <a name="functional-limits"></a>Működési korlátok

Az alábbi táblázat felsorolja az Azure Digital Twins működési korlátait az aktuális előzetes verzióban.

| Terület | Képesség | Alapértelmezett korlát | Módosítható? |
| --- | --- | --- | --- |
| Azure-erőforrás | Azure digitális Twins-példányok száma egy adott régióban, előfizetés szerint | 10 | Igen |
| Digital Twins | Az ikrek száma egy Azure-beli digitális Twins-példányban | 200,000 | Igen |
| Digital Twins | Egyetlen Twin típusú bejövő kapcsolatok száma | 5000 | Nem |
| Digital Twins | Egyetlen Twin kimenő kapcsolatainak száma | 5000 | Nem |
| Útválasztás | Végpontok száma egyetlen Azure Digital Twins-példányhoz | 6 | Nem |
| Útválasztás | Egyetlen Azure Digital Twins-példány útvonalai száma | 6 | Igen |
| Modellek | Egyetlen Azure Digital Twins-példányon belüli modellek száma | 10,000 | Igen |
| Modellek | Egyetlen API-hívásban feltölthethető modellek száma | 250 | Nem |
| Modellek | Egyetlen oldalon visszaadott elemek száma | 100 | Nem |
| Lekérdezés | Egyetlen oldalon visszaadott elemek száma | 100 | Nem |
| Lekérdezés | Kifejezések száma `AND`  /  `OR` egy lekérdezésben | 50 | Igen |
| Lekérdezés | Egy `IN`  /  `NOT IN` záradékban található tömb elemeinek száma | 50 | Igen |
| Lekérdezés | A lekérdezésben szereplő karakterek száma | 8,000 | Igen |
| Lekérdezés | `JOINS`Lekérdezésben szereplő szám | 5 | Igen |

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

Az Azure digitális Twins-modellekhez tartozó DTDL-dokumentumokban lévő adattípusokra és mezőkre vonatkozó korlátozások a GitHub dokumentációjában találhatók: [*Digital Twins Definition Language (DTDL) – 2. verzió*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
A lekérdezés késésének részletei és az előzetes verzióra vonatkozó egyéb irányelvek a következő [*útmutatóban találhatók: a kettős gráf lekérdezése*](../articles/digital-twins/how-to-query-graph.md).