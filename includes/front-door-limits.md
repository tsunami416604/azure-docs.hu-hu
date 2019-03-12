---
title: fájl belefoglalása
description: fájl belefoglalása
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e3fa5616518675d8475937ec63afdd8e1742e8c6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553521"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Az Azure szolgáltatás bejárati ajtajának erőforrásokra előfizetésenként | 100 |
| Előtér-gazdagépeken, beleértve az egyéni tartományok erőforrásonként | 100 |
| Erőforrásonként útválasztási szabályok | 100 |
| Háttérkészlet erőforrásonként | 50 |
| A háttérrendszerek háttér-készletenként | 100 |
| Útválasztási szabályainak megfelelő elérésiút-minták | 25 |
| Egyéni webalkalmazási tűzfalszabályok házirend szerint | 10 |
| Webes alkalmazás-erőforrásonként tűzfalházirend | 100 |

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
| **Feltöltés** |  Mindaddig, amíg minden irányuló feltöltött 28.6 MB-nál kevesebb, nincs korlátozva. | A méret nem lehet nagyobb, mint 28.6. MB-OT. |
