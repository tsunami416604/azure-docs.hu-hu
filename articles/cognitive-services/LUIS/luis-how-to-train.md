---
title: Betanítási alkalmazás – LUIS
titleSuffix: Azure Cognitive Services
description: A képzés a nyelvismertetési (LUIS) alkalmazásverziójának tanítási folyamata a természetes nyelvi megértés javítása érdekében. A LUIS-alkalmazás betanítása a modell frissítései után, például entitások, szándékok vagy kimondott szöveg hozzáadása, szerkesztése, címkézése vagy törlése után.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219862"
---
# <a name="train-your-active-version-of-the-luis-app"></a>A LUIS alkalmazás aktív verziójának betanítása 

A képzés a language understanding (LUIS) alkalmazás tanításának folyamata a természetes nyelvi megértés javítása érdekében. A LUIS-alkalmazás betanítása a modell frissítései után, például entitások, szándékok vagy kimondott szöveg hozzáadása, szerkesztése, címkézése vagy törlése után. 

Az alkalmazás betanítása és [tesztelése](luis-concept-test.md) iteratív folyamat. A LUIS-alkalmazás betanítása után tesztelje azt a minta utterances, hogy lássa, a szándékok és az entitások megfelelően ismeri fel. Ha nem, a LUIS-alkalmazás frissítései, a vonat és a tesztelés újra. 

A betanítás a LUIS-portál on aktív verziójára vonatkozik. 

## <a name="how-to-train-interactively"></a>Hogyan kell a vonat interaktívan

Az iteratív folyamat elindításához a [LUIS-portálon](https://www.luis.ai)először be kell képeznie a LUIS-alkalmazást legalább egyszer. Győződjön meg arról, hogy minden szándék legalább egy utterance (kifejezés) betanítás előtt.

1. Az alkalmazás eléréséhez válassza ki a nevét a **Saját alkalmazások** lapon. 

1. Az alkalmazásban válassza a **Betanítás** lehetőséget a felső panelen. 

1. Amikor a betanítás befejeződött, egy értesítés jelenik meg a böngésző tetején.

## <a name="training-date-and-time"></a>Edzés dátuma és időpontja

Az edzés dátuma és időpontja GMT + 2. 

## <a name="train-with-all-data"></a>Betanítás az összes adattal

Képzés használ egy kis százaléka negatív mintavétel. Ha a kis negatív mintavételhelyett az összes adatot szeretné használni, használja az [API-t.](#version-settings-api-use-of-usealltrainingdata)

### <a name="version-settings-api-use-of-usealltrainingdata"></a>A UseAllTrainingData verzióbeállítások API-használata

Használja a [Verzióbeállítások API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) a `UseAllTrainingData` true beállítással a szolgáltatás kikapcsolására. 

## <a name="unnecessary-training"></a>Szükségtelen képzés

Nem kell minden egyes változás után edzeni. A képzést a modellre alkalmazott módosítások egy csoportja után kell elvégezni, és a következő lépés a teszt vagy a közzététel. Ha nem kell tesztelnie vagy közzétennie, nincs szükség képzésre. 

## <a name="training-with-the-rest-apis"></a>Oktatás a REST API-kban

A LUIS portálon való képzés egyetlen lépés a **Vonat** gomb megnyomásával. A REST API-kkal való képzés két lépésből áll. Az első az, hogy [kérjen képzést](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) HTTP POST. Ezután kérje a [betanítási állapothttp](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) Get. 

Annak érdekében, hogy tudja, ha a betanítás befejeződött, le kell töltenie az állapotot, amíg az összes modell sikeresen be van tanítva. 

## <a name="next-steps"></a>További lépések

* [Interaktív tesztelés](luis-interactive-test.md)
* [Kötegelt tesztelés](luis-how-to-batch-test.md)
