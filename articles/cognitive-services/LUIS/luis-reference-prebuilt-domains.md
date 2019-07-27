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
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: ce043e23a0384a74fd5d2c9dd514045578ef836d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563465"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Előre elkészített tartományi referenciák a LUIS-alkalmazáshoz
Ez az útmutató információkkal szolgál a [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md), amely gyűjteményei előre összeállított szándékok és entitások, amelyek a LUIS kínál.

[Egyéni tartományok](luis-how-to-start-new-app.md), ezzel szemben az elindításához nincs leképezések és a modellek. Egy egyéni modell bármilyen előre összeállított tartományban szándékok és entitások adhat hozzá.

# <a name="supported-domains-across-cultures"></a>Támogatott tartományok kultúrák között

Az egyetlen támogatott kulturális környezet angol. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Entitástípus|description|
|--|--|
|Naptár|A naptár a személyes találkozókról és a találkozókról szól, _nem_ nyilvános eseményekről (például a VB-ütemtervekről, a Seattle-i eseménynaptárról) vagy az általános naptárakról (például hogy mi a mai nap, mi a kezdete, ha a Labor Day).|
|Kommunikáció|Hívások kezdeményezésére, szövegek és azonnali üzenetek küldésére, névjegyek és más, a kommunikációval kapcsolatos egyéb kérések (általában kimenő) keresésére és hozzáadására irányuló kérések. A kapcsolattartó neve csak lekérdezések nem tartoznak a kommunikációs tartományhoz.|
|Email|Az e-mail a kommunikációs tartomány altartománya. Elsősorban az e-mailek küldésére és fogadására vonatkozó kérelmeket tartalmaz.|
|HomeAutomation|A HomeAutomation-tartomány a Smart Home-eszközök vezérléséhez kapcsolódó leképezéseket és entitásokat biztosít. Ez elsősorban a fények és a légkondicionáló vezérlési parancsát támogatja, de az egyéb elektromos készülékek esetében valamilyen általánosítási képességgel rendelkezik.|
|Megjegyzések|Megjegyzés a tartomány a jegyzetek létrehozására és a felhasználókra vonatkozó elemek leírására szolgáló leképezéseket és entitásokat biztosít.|
|Helyek|A helyek közé tartoznak a vállalatok, intézmények, éttermek, nyilvános terek és címek. A tartomány támogatja a helyek megkeresését és a nyilvános hely, például a tartózkodási hely, az üzemóra és a távolság adatait.|
|RestaurantReservation|Az éttermi foglalási tartomány támogatja a foglalások az éttermek számára való kezelésére szolgáló leképezéseket.|
|ToDo|A ToDo-tartomány feladatlistákat biztosít a felhasználók számára a teendők hozzáadásához, megjelöléséhez és törléséhez.|
|Közművek|A segédprogramok tartománya az összes LUIS előre összeépített modell egyik általános tartománya, amely közös szándék-és hosszúságú kimondott szöveg tartalmaz a különbségi helyzetekben.|
|Időjárás|Az időjárási tartomány az időjárási feltételek és az olyan tanácsadók ellenőrzésére összpontosít, amelyekben a hely és az idő, illetve az idő időjárási körülmények között van.|
|Web|A webes tartomány biztosítja a szándékot és az entitásokat a webhelyek kereséséhez.|
