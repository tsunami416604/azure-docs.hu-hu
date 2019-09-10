---
title: fájl belefoglalása
description: fájl belefoglalása
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67179499"
---
A tárterületet a lemezterület korlátozza, vagy az indexek, dokumentumok vagy más magas szintű erőforrások *maximális számának* korlátozásával, attól függően, hogy melyik következik be először. A következő táblázat a dokumentumok tárolási korlátait tartalmazza. Az indexek, dokumentumok és egyéb objektumok maximális korlátaihoz lásd: [erőforrások korlátozása](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resource | Free | <sup>1</sup> . alapszintű | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | 2\. |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Szolgáltatói szerződés (SLA)<sup>3</sup>  |Nem |Igen |Igen |Igen |Igen |Igen |Igen |Igen |
| Tárterület partíciónként |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partíciók szolgáltatásonként |– |1 |12 |12 |12 |3 |12 |12 |
| Partíció mérete |– |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replikák |– |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> az alapszintű partíció egy rögzített partíciót tartalmaz. Ezen a szinten további keresési egységek vannak használatban több replika kiosztásához a lekérdezési feladatok megnövekedéséhez.

<sup>2</sup> az S3 HD rögzített korlátja három partíció, ami alacsonyabb, mint az S3 partíciós korlátja. Azért alacsonyabb a partíciókorlát, mert az S3 HD esetében az indexek száma jelentősen magasabb. Ha szolgáltatási korlátok vonatkoznak a számítási erőforrásokra (tárolás és feldolgozás) és a tartalomra (indexek és dokumentumok) is, a rendszer először a tartalmi korlátot éri el.

<sup>3</sup> a dedikált erőforrásokon lévő számlázható szolgáltatások esetében 3 szolgáltatói szerződés is rendelkezésre áll. Az ingyenes szolgáltatások és az előzetes verziójú funkciók nem rendelkeznek SLA-val. Számlázható szolgáltatások esetén a SLA-kat akkor kell végrehajtani, ha elegendő redundancia van kiépítve a szolgáltatáshoz. Legalább két replika szükséges a lekérdezés (olvasási) SLA-hoz. Három vagy több replika szükséges a lekérdezéshez és az indexeléshez (írási/olvasási) SLA-hoz. A partíciók száma nem SLA-megfontolás. 