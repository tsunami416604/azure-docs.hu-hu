---
title: A szolgáltatás nyilvános előzetes verziójának korlátozásai
titleSuffix: Azure Digital Twins
description: A nyilvános előzetes verzióban az Azure Digital Twins szolgáltatás korlátozásait bemutató diagram.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: article
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 7847964d77e5dc09d2e09f207c47f9504c48e1db
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84729200"
---
# <a name="service-limits-in-public-preview"></a>Nyilvános előzetes verzióban elérhető a szolgáltatás korlátai

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Ezek a nyilvános előzetes verzióban elérhető Azure Digital Twins szolgáltatásokra vonatkozó korlátozások.

> [!NOTE]
> Egyes szolgáltatások állítható korlátokkal rendelkeznek, amelyek az alábbi táblázatban láthatók az *állítható?* oszlop szerint. Ha a korlát módosítható, az *állítható?* érték *Igen*.
>
> Ha vállalata megköveteli, hogy az alapértelmezett korlát fölé állítson be egy állítható korlátot vagy kvótát, további erőforrásokat kérhet le [egy támogatási jegy megnyitásával](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="functional-limits"></a>Működési korlátok

Az alábbi táblázat felsorolja az Azure Digital Twins működési korlátait az aktuális előzetes verzióban.

| Terület | Képesség | Korlát | Módosítható? |
| --- | --- | --- | --- |
| Azure-erőforrás | Azure digitális Twins-példányok száma egy adott régióban, előfizetés szerint | 10 | Yes |
| Digitális Twins | Az ikrek száma egy Azure-beli digitális Twins-példányban | 200,000 | Yes |
| Útválasztás | Végpontok száma egyetlen Azure Digital Twins-példányhoz | 6 | No |
| Útválasztás | Egyetlen Azure Digital Twins-példány útvonalai száma | 6 | Yes |
| Modellek | Egyetlen Azure Digital Twins-példányon belüli modellek száma | 10,000 | Yes |
| Modellek | Egyetlen API-hívásban feltölthethető modellek száma | 250 | No |
| Modellek | Egyetlen oldalon visszaadott elemek száma | 100 | No |
| Lekérdezés | Egyetlen oldalon visszaadott elemek száma | 100 | No |
| Lekérdezés | Kifejezések száma `AND`  /  `OR` egy lekérdezésben | 50 | Igen |
| Lekérdezés | Egy `IN`  /  `NOT IN` záradékban található tömb elemeinek száma | 50 | Igen |
| Lekérdezés | A lekérdezésben szereplő karakterek száma | 8,000 | Igen |
| Lekérdezés | `JOINS`Lekérdezésben szereplő szám | 1 | Yes |

## <a name="rate-limits"></a>Díjszabási korlátok

Ez a táblázat a különböző API-k díjszabását tükrözi.

| API | Képesség | Korlát | Módosítható? |
| --- | --- | --- | --- |
| Modellek API | Kérelmek másodpercenkénti száma | 100 | Yes |
| Digitális Twins API | Kérelmek másodpercenkénti száma | 1,000 | Yes |
| Lekérdezési API | Kérelmek másodpercenkénti száma | 500 | Yes |
| Lekérdezési API | Lekérdezési egység másodpercenként | 4,000 | Yes |
| Event Routes API | Kérelmek másodpercenkénti száma | 100 | Yes |

## <a name="other-limits"></a>Egyéb korlátok

Az Azure digitális Twins-modellekhez tartozó DTDL-dokumentumokban lévő adattípusokra és mezőkre vonatkozó korlátozások a GitHub dokumentációjában találhatók: [Digital Twins Definition Language (DTDL) – 2. verzió](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).
 
A lekérdezés késésének részletei és az előzetes verzióra vonatkozó egyéb irányelvek a következő [útmutatóban találhatók: a kettős gráf lekérdezése](how-to-query-graph.md).

## <a name="next-steps"></a>További lépések

További információ az Azure Digital Twins aktuális előzetes kiadásáról a szolgáltatás áttekintésében:
* [Áttekintés: Mi az Azure Digital Twins?](overview.md)
