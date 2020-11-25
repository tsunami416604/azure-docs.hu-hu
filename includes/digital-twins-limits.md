---
author: baanders
description: fájl belefoglalása az Azure-beli digitális ikrek korlátaira
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 183d12b5e9d32c777c8acf01177c8cbbe1b6ca00
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026623"
---
### <a name="functional-limits"></a>Működési korlátok

Az alábbi táblázat felsorolja az Azure digitális ikrek működési korlátait.

| Terület | Képesség | Alapértelmezett korlát | Módosítható? |
| --- | --- | --- | --- |
| Azure-erőforrás | Azure digitális Twins-példányok száma egy adott régióban, előfizetés szerint | 10 | Yes |
| Digital Twins | Az ikrek száma egy Azure-beli digitális Twins-példányban | 200,000 | Yes |
| Digital Twins | Egyetlen Twin típusú bejövő kapcsolatok száma | 5000 | No |
| Digital Twins | Egyetlen Twin kimenő kapcsolatainak száma | 5000 | No |
| Digital Twins | Egyetlen dupla méret maximális mérete | 32 KB | No |
| Digital Twins | Kérelmek maximális hasznos mérete | 32 KB | No | 
| Útválasztás | Végpontok száma egyetlen Azure Digital Twins-példányhoz | 6 | No |
| Útválasztás | Egyetlen Azure Digital Twins-példány útvonalai száma | 6 | Yes |
| Modellek | Egyetlen Azure Digital Twins-példányon belüli modellek száma | 10,000 | Yes |
| Modellek | Egyetlen API-hívásban feltölthethető modellek száma | 250 | No |
| Modellek | Egyetlen oldalon visszaadott elemek száma | 100 | No |
| Lekérdezés | Egyetlen oldalon visszaadott elemek száma | 100 | Igen |
| Lekérdezés | Kifejezések száma `AND`  /  `OR` egy lekérdezésben | 50 | Igen |
| Lekérdezés | Egy `IN`  /  `NOT IN` záradékban található tömb elemeinek száma | 50 | Igen |
| Lekérdezés | A lekérdezésben szereplő karakterek száma | 8,000 | Igen |
| Lekérdezés | `JOINS`Lekérdezésben szereplő szám | 5 | Yes |

### <a name="rate-limits"></a>Sebességkorlátok

Az alábbi táblázat a különböző API-k díjszabását mutatja be.

| API | Képesség | Alapértelmezett korlát | Módosítható? |
| --- | --- | --- | --- |
| Modellek API | Kérelmek másodpercenkénti száma | 100 | Yes |
| Digitális Twins API | Kérelmek másodpercenkénti száma | 2000 | Yes |
| Digitális Twins API | A létrehozási/törlési műveletek másodpercenkénti száma az **összes ikrek és kapcsolat** között | 50 | Yes |
| Digitális Twins API | A létrehozási/frissítési/törlési műveletek másodpercenkénti száma **egyetlen dupla** vagy a kapcsolatain | 10 | No |
| Lekérdezési API | Kérelmek másodpercenkénti száma | 500 | Yes |
| Lekérdezési API | [Lekérdezési egység](../articles/digital-twins/concepts-query-units.md) másodpercenként | 4,000 | Yes |
| Event Routes API | Kérelmek másodpercenkénti száma | 100 | Yes |

### <a name="other-limits"></a>Egyéb korlátok

Az Azure digitális Twins-modellekhez tartozó DTDL-dokumentumokban lévő adattípusokra és mezőkre vonatkozó korlátozások a GitHub dokumentációjában találhatók: [*Digital Twins Definition Language (DTDL) – 2. verzió*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
A lekérdezés késésének részletei és más lekérdezési korlátozások a következő [*útmutatóban találhatók: a Twin gráf lekérdezése*](../articles/digital-twins/how-to-query-graph.md).
