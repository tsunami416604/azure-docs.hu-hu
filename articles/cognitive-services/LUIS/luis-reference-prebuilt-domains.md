---
title: Előre elkészített tartományi hivatkozás – LUIS
titleSuffix: Azure Cognitive Services
description: Az előre elkészített tartományokra mutató hivatkozásokat, amelyek az Language Understanding intelligens szolgáltatásokból (LUIS) származó szándékok és entitások előre összeépített gyűjteményei.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270615"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Előre elkészített tartományi referenciák a LUIS-alkalmazáshoz
Ez a hivatkozás az [előre elkészített tartományokra](luis-how-to-use-prebuilt-domains.md)vonatkozó információkat tartalmaz, amelyek a Luis által kínált szándékok és entitások előre összeépített gyűjteményei.

Az [Egyéni tartományok](luis-how-to-start-new-app.md)ezzel szemben a nem szándékok és modellek használatával kezdődnek. Hozzáadhat bármilyen előre elkészített tartományi leképezéseket és entitásokat egy egyéni modellhez.

## <a name="custom-domains-per-language"></a>Egyéni tartományok nyelv szerint

Az alábbi táblázat összefoglalja a jelenleg támogatott tartományokat. Az angol nyelvű támogatás általában teljesebb, mint mások.

| Entitás típusa       | EN-US      | ZH-CN   | DE    | JK     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:|
| Naptár  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Kommunikáció  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| E-mail     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Megjegyzések     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Helyek   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Segédprogramok      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Időjárás        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Az előre elkészített tartományok **nem támogatottak** a ben:

* Francia kanadai
* Hindi
* Spanyol mexikói

## <a name="next-steps"></a>További lépések

Ismerje meg az [egyszerű entitást](reference-entity-simple.md).