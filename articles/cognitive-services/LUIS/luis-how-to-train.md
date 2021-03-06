---
title: Alkalmazás betanítása – LUIS
titleSuffix: Azure Cognitive Services
description: A képzés a Language Understanding (LUIS) alkalmazás verziójának tanítási folyamata, amely a természetes nyelvi megértést javítja. A modell frissítéseinek elvégzése, például entitások, szándékok vagy hosszúságú kimondott szöveg hozzáadása, szerkesztése, címkézése vagy törlése után a LUIS-alkalmazás betanítása.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: a53857061110d5a77ac3d166277e7076f4f7f9c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541373"
---
# <a name="train-your-active-version-of-the-luis-app"></a>A LUIS-alkalmazás aktív verziójának betanítása

A képzés a Language Understanding (LUIS) alkalmazás tanításának folyamata, amely a természetes nyelvi megértést javítja. A modell frissítéseinek elvégzése, például entitások, szándékok vagy hosszúságú kimondott szöveg hozzáadása, szerkesztése, címkézése vagy törlése után a LUIS-alkalmazás betanítása.

Az alkalmazások betanítása és [tesztelése](luis-concept-test.md) egy iterációs folyamat. A betanítást követően mintabeszédelemekkel tesztelje a LUIS-alkalmazást annak megállapításához, hogy az alkalmazás helyesen ismeri-e fel a szándékokat és az entitásokat. Ha nem, akkor frissítse a LUIS alkalmazást, a betanítást és a tesztelést.

A betanítás a LUIS-portál aktív verziójára vonatkozik.

## <a name="how-to-train-interactively"></a>Interaktív képzés

Az iterációs folyamat a [Luis-portálon](https://www.luis.ai)való elindításához először legalább egyszer be kell tanítania a Luis-alkalmazást. A betanítás előtt győződjön meg arról, hogy minden szándéknak legalább egy kimondottnak kell lennie.

1. Az alkalmazás eléréséhez válassza ki a saját **alkalmazások** lapján a saját nevét.

1. Az alkalmazásban válassza a **betanítás** lehetőséget a felső panelen.

1. A képzés befejezésekor a böngésző tetején megjelenik egy értesítés.

## <a name="training-date-and-time"></a>Képzés dátuma és időpontja

A betanítás dátuma és időpontja GMT + 2.

## <a name="train-with-all-data"></a>Tanítás az összes adattal

A képzések a negatív mintavételezés kis hányadát használják. Ha a kis negatív mintavételezés helyett az összes adatát szeretné használni, használja az [API](#version-settings-api-use-of-usealltrainingdata)-t.

### <a name="version-settings-api-use-of-usealltrainingdata"></a>UseAllTrainingData API-k használata

A szolgáltatás kikapcsolásához használja a [Version Settings API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -t az `UseAllTrainingData` igaz értékre.

## <a name="unnecessary-training"></a>Szükségtelen képzés

Minden egyes módosítás után nem kell betanítania. A betanítást a modellre vonatkozó módosítások egy csoportjának alkalmazása után kell elvégezni, a következő lépésként pedig tesztelni vagy közzétenni. Ha nem kell tesztelni vagy közzétennie, a képzés nem szükséges.

## <a name="training-with-the-rest-apis"></a>Képzés a REST API-kkal

A LUIS-portálon betanítás egyetlen lépés a **vonat** gomb megnyomására. A REST API-k betanítása egy kétlépéses folyamat. Az első a HTTP POST használatával történő [betanítást kéri](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) . Ezután kérje a [betanítási állapotot](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) a HTTP Get lekérdezéssel.

Ha tudni szeretné, hogy a betanítás sikeres-e, le kell kérdezni az állapotot, amíg az összes modellt nem sikerült betanítani.

## <a name="next-steps"></a>Következő lépések

* [Interaktív tesztelés](luis-interactive-test.md)
* [Kötegelt tesztelés](luis-how-to-batch-test.md)
