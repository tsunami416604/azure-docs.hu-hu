---
title: Előre elkészített tartományi hivatkozás – LUIS
titleSuffix: Azure Cognitive Services
description: Az előre összeállított tartományok, amelyek előre elkészített gyűjteménye szándékokat és entitásokat a Language Understanding Intelligent Services (LUIS) referenciája.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 17d72ad51974bb0fb741ae19ebb19f313e646c62
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677638"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Előre elkészített tartományi referenciák a LUIS-alkalmazáshoz
Ez az útmutató információkkal szolgál a [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md), amely gyűjteményei előre összeállított szándékok és entitások, amelyek a LUIS kínál.

[Egyéni tartományok](luis-how-to-start-new-app.md), ezzel szemben az elindításához nincs leképezések és a modellek. Egy egyéni modell bármilyen előre összeállított tartományban szándékok és entitások adhat hozzá.

## <a name="custom-domains-per-language"></a>Egyéni tartományok nyelv szerint

Az alábbi táblázat összefoglalja a jelenleg támogatott tartományokat. Az angol nyelvű támogatás általában teljesebb, mint mások. 

| Entitás típusa       | EN-US      | ZH-CN   | DE    | JK     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Naptár  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Kommunikáció  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Email     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Megjegyzések     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Helyek   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Közművek      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Időjárás        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Az előre elkészített tartományok **nem támogatottak** a ben:

* Francia kanadai
* hindi
* Spanyol mexikói

## <a name="next-steps"></a>További lépések

Ismerje meg az [egyszerű entitást](reference-entity-simple.md). 