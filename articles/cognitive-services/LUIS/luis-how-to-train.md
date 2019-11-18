---
title: Alkalmazás betanítása – LUIS
titleSuffix: Azure Cognitive Services
description: Képzési az a folyamat, a Language Understanding (LUIS) Alkalmazásverzió javítása a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143694"
---
# <a name="train-your-active-version-of-the-luis-app"></a>A LUIS-alkalmazás aktív verziójának betanítása 

Képzési az a folyamat, a Language Understanding (LUIS) alkalmazásával a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után. 

Képzés és [tesztelés](luis-concept-test.md) az alkalmazás az iteratív folyamat. Miután a LUIS-alkalmazás betanításához, tesztelje azt az annak ellenőrzéséhez, hogy a szándékok és entitások helyesen ismeri a minta kimondott szöveg. Ha nem, a LUIS-alkalmazásokon, tanítási és tesztelési ellenőrizze újra frissítéseket. 

Képzési alkalmazza a rendszer az aktív verzió a LUIS-portálon. 

## <a name="how-to-train-interactively"></a>Hogyan interaktív módon betanítása

A iteratív folyamat a [LUIS portál](https://www.luis.ai), először létre kell legalább egy alkalommal betanítása a LUIS-alkalmazás. Ellenőrizze, hogy minden szándékot képzési előtt legalább egy utterance (kifejezés).

1. Az alkalmazás eléréséhez annak nevét választva a **saját alkalmazások** lapot. 

1. Jelölje be az alkalmazásba, **Train** a az ablak tetején. 

1. A képzés befejezésekor a böngésző tetején megjelenik egy értesítés.

## <a name="training-date-and-time"></a>Képzés dátuma és időpontja

A betanítás dátuma és időpontja GMT + 2. 

## <a name="train-with-all-data"></a>Az összes adat betanítása

Képzési egy kis csoportja negatív mintavételt használ. Ha a kis negatív mintavételezés helyett az összes adatát szeretné használni, használja az [API](#version-settings-api-use-of-usealltrainingdata)-t.

### <a name="version-settings-api-use-of-usealltrainingdata"></a>UseAllTrainingData API-k használata

a szolgáltatás kikapcsolásához használja a [Version Settings API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -t a `UseAllTrainingData` True értékre. 

## <a name="unnecessary-training"></a>A szükségtelen képzés

Nem kell minden egyetlen módosítás után betanításához. Képzési módosítások csoportja érvénybe lépnek a modellt, és szeretné a következő lépés az, hogy tesztelje, vagy közzéteheti után kell elvégezni. Ha nem szeretné tesztelni, vagy közzéteheti, a képzési nem szükséges. 

## <a name="training-with-the-rest-apis"></a>A REST API-kkal képzés

A LUIS portálon képzési nyomja le az egyetlen lépésből áll a **Train** gombra. A REST API-kkal képzési két lépésből áll. Az első [képzési kérelem](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) a HTTP POST. Majd kérik a [képzési állapot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) az HTTP Get. 

Annak érdekében, hogy ismeri a betanítási befejezésekor, hogy lekérdezik az állapot addig, amíg sikeresen képzett összes modellt. 

## <a name="next-steps"></a>További lépések

* [Interaktív tesztelés](luis-interactive-test.md)
* [Kötegelt tesztelés](luis-how-to-batch-test.md)
