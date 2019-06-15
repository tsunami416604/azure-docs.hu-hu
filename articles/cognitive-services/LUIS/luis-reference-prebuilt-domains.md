---
title: Előre összeállított tartományhivatkozás
titleSuffix: Azure
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
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233890"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>A LUIS-alkalmazás az előre összeállított útmutatója
Ez az útmutató információkkal szolgál a [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md), amely gyűjteményei előre összeállított szándékok és entitások, amelyek a LUIS kínál.

[Egyéni tartományok](luis-how-to-start-new-app.md), ezzel szemben az elindításához nincs leképezések és a modellek. Egy egyéni modell bármilyen előre összeállított tartományban szándékok és entitások adhat hozzá.

# <a name="supported-domains-across-cultures"></a>Támogatott tartományokat kulturális környezetek között

Az egyetlen támogatott kulturális környezet az angol. 

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

|Entitás típusa|description|
|--|--|
|Naptár|Naptár egy személyes értekezletek és találkozókat, _nem_ nyilvános események (például a világ cup ütemezéseket, Seattle események naptárak vagy általános naptárak (például milyen nap, még ma, mi does fall kezdődik, ha a Labor Day van).|
|Kommunikáció|Hívásokat, kérelmek küldése a szövegek vagy a Csevegőüzenetek, keresse meg, és adja hozzá a névjegyek és számos más kommunikációs ügyekben (általában kimenő). Kapcsolattartó neve csak lekérdezéseket kommunikációs tartományhoz nem tartozik.|
|E-mail|E-mail-cím a kommunikációs tartományhoz tartozó altartományban. E-mailek üzeneteket küldhet és fogadhat kéréseket főként tartalmazza.|
|HomeAutomation|A HomeAutomation tartományban szándékok és entitások szabályozása otthoni intelligens eszközökkel kapcsolatos biztosít. Főleg támogatja a vezérlő parancs lámpa és légkondicionálóját kapcsolódó, de néhány más electric készülékek általánossá képességekkel rendelkezik.|
|Megjegyzések|Megjegyzés tartományban szándékok és entitások jegyzeteket létrehozni, és írásra le elemeket a felhasználók számára biztosít.|
|Helyek|Forráshelyek közé tartozik a vállalkozások, intézmények, éttermek, nyilvános szóközöket és címek. A tartomány támogatja a hely megkeresésével és nyilvános helyen – például tartózkodási hely, a üzemeltetési óra és távolság hatásával kapcsolatos.|
|RestaurantReservation|Éttermek foglalás tartománya támogatja a leképezések az éttermek foglalások kezelése.|
|Teendőlista|Teendőlista-kezelő tartományi felhasználók hozzáadása, jelölje meg és törölje a saját todo elemeket a feladatlisták biztosít.|
|Közművek|Segédprogramok tartományban egy általános tartomány közötti összes LUIS közös leképezések és a különbség a forgatókönyvekben utterances tartalmazó előre összeállított modellek.|
|Időjárás|Időjárás-tartomány időjárási és tanácsok a hely és idő ellenőrzése vagy idő ellenőrzése az időjárási feltételek szerint összpontosít.|
|Web|A webes tartomány a leképezés és entitásokat biztosít egy webhely keresése.|
