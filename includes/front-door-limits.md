---
title: fájl belefoglalása
description: fájl belefoglalása
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333378"
---
| Resource | Alapértelmezett/maximális korlát |
| --- | --- |
| Az Azure szolgáltatás bejárati ajtajának erőforrásokra előfizetésenként | 100 |
| Előtér-gazdagépeken, beleértve az egyéni tartományok erőforrásonként | 100 |
| Erőforrásonként útválasztási szabályok | 100 |
| Háttérkészlet erőforrásonként | 50 |
| A háttérrendszerek háttér-készletenként | 100 |
| Útválasztási szabályainak megfelelő elérésiút-minták | 25 |
| Egyéni webalkalmazási tűzfalszabályok házirend szerint | 10 |
| Webes alkalmazás-erőforrásonként tűzfalházirend | 100 |
| Web application firewall egyezési feltételei egy egyéni szabály | 10 |
| Webes alkalmazás tűzfal IP-címtartományok száma feltételnek megfelelő | 600 |
| Webes alkalmazás tűzfal karakterlánc egyeztetése értéket, az egyezési feltétellel | 10 |
| Webes alkalmazás tűzfal karakterlánc egyeztetés értékének hossza | 256 |
| Webalkalmazási tűzfal bejegyzés törzse a paraméter nevének hossza | 256 |
| Webalkalmazási tűzfal HTTP-fejléc nevének hossza | 256 |
| Web application firewall cookie-k nevének hossza | 256 |
| Webes alkalmazás tűzfal HTTP kérés törzsének mérete megvizsgálni | 128 KB |
| Webes alkalmazás tűzfal egyéni válasz törzse hossza | 2 KB |

### <a name="timeout-values"></a>Időtúllépési értékek
#### <a name="client-to-front-door"></a>Ügyfél számára a bejárati ajtó
- Bejárati ajtajának rendelkezik TCP kapcsolat üresjárati időkorlátot 61 másodpercben.

#### <a name="front-door-to-application-back-end"></a>Az alkalmazás háttér-bejárati ajtajának
- Ha a válasz darabolt választ, ha, vagy amikor az első adatrészletben érkezik, 200-as adja vissza.
- A HTTP-kérelem továbbíthatja a rendszer a háttérben, miután bejárati ajtajának az első csomag a háttéralkalmazásból 30 másodpercet várakozik. Ezután visszaküldi 503-as hiba az ügyfélnek.
- Az első csomag a háttéralkalmazásból fogadását követően bejárati ajtajának megvárja, amíg az üresjárati időkorlátot 30 másodperc. Ezután visszaküldi 503-as hiba az ügyfélnek.
- A háttér-TCP-munkamenet időkorlátja bejárati ajtajának érték 30 perc.

### <a name="upload-and-download-data-limit"></a>Adatkorlát le- és feltöltése

|  | A darabolásos átvitel kódolási (irányuló) | HTTP-darabolás nélkül |
| ---- | ------- | ------- |
| **Letöltés** | Letöltési mérete nincs korlátozva van. | Letöltési mérete nincs korlátozva van. |
| **Feltöltés** |  Mindaddig, amíg minden irányuló feltöltött 2 GB-nál kevesebb, nincs korlátozva. | A mérete nem lehet nagyobb 2 GB-nál. |

### <a name="other-limits"></a>Egyéb korlátok
- Maximális URL-cím mérete – 8192 bájt – Itt adhatja meg a nyers URL-cím maximális hossza (séma + állomásnév + port + elérési út + lekérdezési karakterláncot az URL-cím) – lekérdezés-karakterlánc maximális mérete – 4096 bájtos - meghatározza a lekérdezési karakterlánc maximális hossza (bájt).