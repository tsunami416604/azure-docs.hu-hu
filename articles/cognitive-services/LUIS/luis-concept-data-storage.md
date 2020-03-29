---
title: Adattárolás - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS a kulcs által megadott régiónak megfelelő Azure-adattárban titkosított adatokat tárol.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220016"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Adattárolás és -eltávolítás a nyelvi ismeretek (LUIS) kognitív szolgáltatásokban
A LUIS a kulcs által megadott régiónak megfelelő Azure-adattárban titkosított adatokat tárol. Ezeket az adatokat 30 napig tároljuk. 

## <a name="export-and-delete-app"></a>Alkalmazás exportálása és törlése
A felhasználók teljes mértékben szabályozhatják az alkalmazás [exportálását](luis-how-to-start-new-app.md#export-app) és [törlését.](luis-how-to-start-new-app.md#delete-app) 

## <a name="utterances"></a>Beszédmódok

Utterances tárolható két különböző helyen. 

* A szerzői folyamat során **utterances**jönnek létre, és a szándék tárolja. A leképezések kimondott szöveg szükséges egy sikeres LUIS-alkalmazás. Az alkalmazás közzététele után, és lekérdezéseket fogad a végponton, a `log=false`végponti kérelem lekérdezési karakterlánca, határozza meg, hogy a végpont utterance (endpoint utterance) tárolja.Once the app is published and receives queries at the endpoint request's querystring, , determines if the endpoint utterance is stored. Ha a végpont tárolja, az aktív tanulási utterances a portál **buildelési** szakaszában található, a **végpont kimondott szöveg áttekintése** szakasz részében található aktív tanulási utterances részévé válik. 
* Amikor **áttekinti a végpont utterances,** és adja hozzá egy utterance (kifejezés) egy szándékot, az utterance (kifejezés) már nem tárolja a végpont utterances felülkell vizsgálni. Hozzáadja az alkalmazás szándékait. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Példakimondott szöveg törlése egy szándékból

A [LUIS](luis-reference-regions.md)betanításasorán használt példakimondott szöveg törlése. Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazásból, törlődik a LUIS webszolgáltatásból, és nem érhető el az exportáláshoz.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Az aktív tanulásból származó véleményben lévő kimondott szövegtörlések törlése

Törölheti az utterances a felhasználói utterances listából, hogy a LUIS javasolja a **[felülvizsgálat végpont utterances oldalon.](luis-how-to-review-endpoint-utterances.md)** Utterance s utterances erről a listából megakadályozza, hogy a javasolt, de nem törli őket a naplókból.

Ha nem szeretné, hogy az aktív tanulási kimondott szöveg, [letilthatja az aktív tanulás.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) Az aktív tanulás letiltása letiltja a naplózást is.

### <a name="disable-logging-utterances"></a>Naplózási kimondott szöveg letiltása
[Az aktív tanulás letiltása letiltja](luis-how-to-review-endpoint-utterances.md#disable-active-learning) a naplózást.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Fiók eltávolítása
Ha töröl egy fiókot, az összes alkalmazás törlődik, valamint a példa utterances és naplók. Az adatokat a fiók és az adatok végleges törlése előtt 60 napig őrizzük meg.

A fiók törlése a **Beállítások** lapon érhető el. A **Beállítások** lap hoz.

## <a name="data-inactivity-as-an-expired-subscription"></a>Adatinaktivitás lejárt előfizetésként
Az adatmegőrzés és -törlés céljából az inaktív LUIS-alkalmazások a _Microsoft belátása szerint_ lejárt előfizetésként kezelhetők. Egy alkalmazás akkor minősül inaktívnak, ha megfelel az alábbi feltételeknek az elmúlt 90 napban: 

* **Nem** hívtak rá.
* Nem módosult.
* Nincs hozzá rendelve aktuális kulcs.
* Nem volt felhasználó bejelentkezése.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudnivalók az alkalmazások exportálásáról és törléséről](luis-how-to-start-new-app.md)
