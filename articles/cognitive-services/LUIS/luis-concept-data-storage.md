---
title: Adattárolás
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893197"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Az adattárolás és eltávolítása a Cognitive Services Language Understanding (LUIS)
A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja. Ezek az adatok 30 napig tárolja. 

## <a name="export-and-delete-app"></a>Exportálás és az alkalmazás törlése
Felhasználók teljes hozzáféréssel rendelkeznek a keresztül [exportálása](luis-how-to-start-new-app.md#export-app) és [törlése](luis-how-to-start-new-app.md#delete-app) az alkalmazást. 

## <a name="utterances"></a>Beszédmódok

Beszédmódok két különböző helyen tárolhatók. 

* Során **az Authoring Tool folyamat**, utterances létrehozása és tárolása a SZÁNDÉKTÓL. A leképezések kimondott szöveg sikeres LUIS-alkalmazásokon szükségesek. Miután az alkalmazás közzé van téve, és megkapja a végponton, a végpont-kérelem lekérdezési karakterlánc, a lekérdezések `log=false`, határozza meg, ha a végpont utterance (kifejezés) tárolja. Ha a végpont, aktív tanulás megcímkézzen található a része lesz a **hozhat létre** szakaszban a portál, a a **tekintse át a végpont utterances** szakaszban. 
* Ha Ön **tekintse át a végpont utterances**, és -leképezést ad hozzá az utterance (kifejezés), az utterance (kifejezés) már nem található végponti megcímkézzen vizsgálni a részeként. Felvettük a alkalmazásszándékkal. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Példa utterances törlése rendeltetésből
Példa utterances képzéshez használt törlése [LUIS](luis-reference-regions.md). Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazás, törlődik a LUIS webszolgáltatás és az exportálás nem érhető el.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Aktív tanulás, tekintse át a kimondott szöveg törlése

Beszédmódok törölheti, hogy a LUIS javasol a felhasználó megcímkézzen listájából a  **[áttekintés végpont kimondott szöveg lap](luis-how-to-review-endpoint-utterances.md)**. Beszédmódok törlése a listáról a továbbiakban nem javasolt, de nem törli azokat a naplókat.

Ha nem szeretné, aktív tanulás utterances, [tiltsa le az aktív tanulás](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Aktív tanulás letiltását is letiltja a naplózást.

### <a name="disable-logging-utterances"></a>Tiltsa le a naplózást kimondott szöveg
[Aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning) letiltja a naplózást.


<a name="accounts"></a>

## <a name="delete-an-account"></a>-Fiók törlése
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
