---
title: Adattárolás
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: a382c3b3f9949e95ebc09f9db5072a123c59f970
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223204"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Az adattárolás és eltávolítása a Cognitive Services Language Understanding (LUIS)
A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja. Ezek az adatok 30 napig tárolja. 

## <a name="export-and-delete-app"></a>Exportálás és az alkalmazás törlése
Felhasználók teljes hozzáféréssel rendelkeznek a keresztül [exportálása](luis-how-to-start-new-app.md#export-app) és [törlése](luis-how-to-start-new-app.md#delete-app) az alkalmazást. 

## <a name="utterances-in-an-intent"></a>A megjelölésű kimondott szöveg
Példa utterances képzéshez használt törlése [LUIS](luis-reference-regions.md). Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazás, törlődik a LUIS webszolgáltatás és az exportálás nem érhető el.

## <a name="utterances-in-review"></a>Tekintse át a kimondott szöveg
Beszédmódok törölheti, hogy a LUIS javasol a felhasználó megcímkézzen listájából a  **[áttekintés végpont kimondott szöveg lap](luis-how-to-review-endoint-utt.md)**. Beszédmódok törlése a listáról a továbbiakban nem javasolt, de nem törli azokat a naplókat.

## <a name="accounts"></a>Fiókok
Ha töröl egy fiókot, az összes alkalmazás törlődnek, azokhoz példa kimondott szöveg és a naplókat. Az adatok a fiók előtt 60 napig megőrződnek, és adatok véglegesen törlődnek.

Fiók törlése folyamatban van a érhető el a **beállítások** lapot. Válassza ki a fiók nevét a jobb felső navigációs sávon való a **beállítások** lapot.

## <a name="data-inactivity-as-an-expired-subscription"></a>Lejárt az előfizetésem, adatok inaktív
Adatok megőrzése vagy törlése céljából, inaktív LUIS alkalmazás bármikor _a Microsoft saját belátása szerint értékeli_ lejárt az előfizetésem kell kezelni. Egy alkalmazás akkor számít inaktívnak, ha az utolsó 90 napra vonatkozó megfelel a következő feltételeknek: 

* Volt-e **nincs** intézett hívások.
* Nem lett módosítva.
* Nem nem a jelenlegi kulcs rendelt hozzá.
* Nem volt olyan felhasználó bejelentkezni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg exportálása és a egy alkalmazás törlése](luis-how-to-start-new-app.md)
