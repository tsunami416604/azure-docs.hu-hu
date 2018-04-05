---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
Tárolási korlátozza szabad lemezterület, vagy rögzített korlátját a a *maximális* indexek, dokumentum vagy más magas szintű erőforrások, amelyik előbb következik be. A következő táblázat a tárolási korlátokat dokumentumokat. Indexek, dokumentumok és egyéb objektumok felső határai, lásd: [korlátok erőforrás](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Erőforrás | Ingyenes | Basic&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Szolgáltatásiszint-szerződés (SLA) szolgáltatás <sup>3</sup>  |Nem |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partíciók szolgáltatásonként |– |1 |12 |12 |12 |3 |
| Partíció mérete |– |2 GB |25 GB |100 GB |200 GB |200 GB |
| Replikák |– |3 |12 |12 |12 |12 |

<sup>1</sup> basic egy rögzített partíciót tartalmaz. A réteg a további SUs vannak használja több replikák lefoglalásával megnövekedett lekérdezés munkaterhelések.

<sup>2</sup> Az S3 HD három partíciós rögzített korláttal rendelkezik, amely alacsonyabb, mint az S3 partíciókorlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

<sup>3</sup> számlázható szolgáltatásokat a dedikált erőforrások szolgáltatásszint-szerződések (SLA) kínálják. Ingyenes szolgáltatásokat és az előzetes funkciók nem SLA rendelkezik. Számlázható szolgáltatások SLA-k érvénybe, ha a szolgáltatás megfelelő redundancia kiépítése. Két vagy több replikák szükségesek (olvasás) lekérdezés SLA-t. Három vagy több replikák lekérdezés és az indexelés (írásra) SLA szükségesek. A partíciók számának nincs egy SLA-t szempont. 