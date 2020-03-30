---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272851"
---
A tárhelyet a lemezterület vagy az indexek, dokumentumok vagy más magas szintű erőforrások *maximális számának* szigorú korlátozása korlátozza , attól függően, hogy melyik következik be előbb. A következő táblázat korlátozza a tárolási korlátokat. Az indexek, dokumentumok és egyéb objektumok maximális korlátát az [Erőforrásonkénti korlátok (Korlátok erőforrásonként) (Korlátok erőforrásonként) (Korlátok erőforrásonként) (Korlátok erőforrásonként) (Korlátok erőforrásonként) (Korlátok erőforrásonként) (Korlátok erőforrás](../articles/search/search-limits-quotas-capacity.md#index-limits)

| Erőforrás | Ingyenes | <sup>1.</sup> alap | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | 1. | 2. |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Szolgáltatásiszint-szerződés (SLA)<sup>3</sup>  |Nem |Igen |Igen |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partíciók szolgáltatásonként |N/A |1 |12 |12 |12 |3 |12 |12 |
| Partíció mérete |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replikák |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic egy rögzített partícióval rendelkezik. Ebben a rétegben további keresési egységek et használnak a megnövekedett lekérdezési számítási feladatok további replikák lefoglalásához.

<sup>2</sup> Az S3 HD három partícióból álló korláttal rendelkezik, ami alacsonyabb, mint az S3 partíciós korlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

<sup>3</sup> A szolgáltatásiszint-szerződések et a számlázható szolgáltatásokhoz dedikált erőforrásokon kínáljuk. Az ingyenes szolgáltatások és az előnézeti funkciók nem rendelkeznek SLA-val. Számlázható szolgáltatások esetén az SLA-k akkor lépnek érvénybe, amikor elegendő redundanciát biztosít a szolgáltatáshoz. Két vagy több kópia szükséges a lekérdezési (olvasási) SLA-khoz. Három vagy több kópia szükséges a lekérdezési és indexelési (írási és írási) SLA-k. A partíciók száma nem SLA szempont. 