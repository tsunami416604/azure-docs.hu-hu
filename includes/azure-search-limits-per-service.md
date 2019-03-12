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
ms.openlocfilehash: fc0451aa89da9b84a5a01a0762425f7533dded3c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553766"
---
Tárhelyet a lemezterület vagy rögzített korlátja korlátozza a a *maximális* indexek, a dokumentum vagy egyéb magas szintű erőforrásokat, amelyik előbb bekövetkezik. Az alábbi táblázat a tárhelykorlátok dokumentumok. Indexek, dokumentumok és egyéb objektumok maximális korlátozásairól lásd: [erőforrás korlátok](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Erőforrás | Ingyenes | Alapszintű<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Szolgáltatásszint-szerződésen (SLA)<sup>3</sup>  |Nem |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partíciók szolgáltatásonként |– |1 |12 |12 |12 |3 |
| Partíció mérete |– |2 GB |25 GB |100 GB |200 GB |200 GB |
| Replikák |– |3 |12 |12 |12 |12 |

<sup>1</sup>alapszintű egy rögzített partícióval rendelkezik. Ez a szint a további keresési egységek vannak használja több replika lefoglalása nagyobb lekérdezési számítási feladatok esetében.

<sup>2</sup>korábbi, mint a partíciós korlát, az S3 rendelkezik S3 HD három partíció rögzített korlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

<sup>3</sup>szolgáltatásiszint-szerződések dedikált erőforrásokkal a számlázható szolgáltatások érhetők el. Ingyenes szolgáltatások és az előzetes funkciók nem biztosítunk szolgáltatói szerződést rendelkezik. Számlázható Services SLA-k a szolgáltatás üzembe helyezésekor megfelelő redundanciát érvénybe léptetéséhez. Két vagy több replikát is szükséges a lekérdezés (olvasás) SLA-kat. Három vagy több replikát is szükséges a lekérdezési és indexelési SLA-k (olvasás / írás). A partíciók számát, nem egy SLA-t veszi figyelembe. 