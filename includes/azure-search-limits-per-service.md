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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755934"
---
Tárhelyet a lemezterület vagy rögzített korlátja korlátozza a a *maximális* indexek, a dokumentum vagy egyéb magas szintű erőforrásokat, amelyik előbb bekövetkezik. Az alábbi táblázat a tárhelykorlátok dokumentumok. Indexek, dokumentumok és egyéb objektumok maximális korlátozásairól lásd: [erőforrás korlátok](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Erőforrás | Ingyenes | Basic&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Szolgáltatásszint-szerződésen (SLA) <sup>3</sup>  |Nem |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partíciók szolgáltatásonként |– |1 |12 |12 |12 |3 |
| Partíció mérete |– |2 GB |25 GB |100 GB |200 GB |200 GB |
| Replikák |– |3 |12 |12 |12 |12 |

<sup>1</sup> alapszintű egy rögzített partícióval rendelkezik. Ez a szint a további SUs vannak használja több replika lefoglalása nagyobb lekérdezési számítási feladatok esetében.

<sup>2</sup> Az S3 HD három partíciós rögzített korláttal rendelkezik, amely alacsonyabb, mint az S3 partíciókorlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

<sup>3</sup> dedikált erőforrásokkal a számlázható szolgáltatásokhoz érhető el szolgáltatásiszint-szerződések (SLA). Ingyenes szolgáltatások és az előzetes funkciók nem biztosítunk szolgáltatói szerződést rendelkezik. Számlázható Services SLA-k a szolgáltatás üzembe helyezésekor megfelelő redundanciát érvénybe léptetéséhez. Két vagy több replikát is szükséges a lekérdezés (olvasás) SLA-t. Három vagy több replikát is szükséges a lekérdezési és indexelési (olvasás / írás) SLA-t. A partíciók számát, nem egy SLA-t veszi figyelembe. 