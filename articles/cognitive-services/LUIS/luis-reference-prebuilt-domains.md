---
title: Előre összeállított tartományhivatkozás - LUIS
titleSuffix: Azure Cognitive Services
description: Hivatkozás az előre összeállított tartományok, amelyek előre összeállított gyűjtemények szándékok és entitások a nyelv-ismeretek megértése intelligens szolgáltatások (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 05ad340b3856291832ba0521c7da70ad55260384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270615"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Előre összeállított tartományhivatkozás a LUIS alkalmazáshoz
Ez a hivatkozás az [előre összeállított tartományokról](luis-how-to-use-prebuilt-domains.md)nyújt információt, amelyek a LUIS által biztosított leképezések és entitások előre összeállított gyűjteményei.

[Az egyéni tartományok](luis-how-to-start-new-app.md)ezzel szemben szándékok és modellek nélkül kezdődnek. Az egyéni modellhez hozzáadhat ja az előre összeállított tartományi leképezéseket és entitásokat.

## <a name="custom-domains-per-language"></a>Egyéni tartományok nyelvenként

Az alábbi táblázat összefoglalja a jelenleg támogatott tartományokat. Az angol nyelv támogatása általában teljesebb, mint mások.

| Entitás típusa       | EN-US      | ZH-CN   | DE    | JK     | ES    | IT      | PT-BR |  JP  |      Ko |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Naptár  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Kommunikáció  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-mail     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Otthoni automatizálás          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Megjegyzések     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Helyek   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Todo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Segédprogramok      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Időjárás        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Az előre összeállított tartományok **nem támogatottak:**

* Francia kanadai
* Hindi
* Spanyol mexikói

## <a name="next-steps"></a>További lépések

Ismerje meg az [egyszerű entitást.](reference-entity-simple.md)