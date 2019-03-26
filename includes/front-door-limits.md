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
ms.openlocfilehash: a3a43c56a49c243390eac964d31988b7d30fbb56
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407941"
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
| **Feltöltés** |  Mindaddig, amíg minden irányuló feltöltött 2 GB-nál kevesebb, nincs korlátozva. | A mérete nem lehet nagyobb 2 GB-nál. |
