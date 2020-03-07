---
title: Adattárolás – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390089"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Az adattárolás és eltávolítása a Cognitive Services Language Understanding (LUIS)
A LUIS egy Azure data store, a kulcs által megadott a régióhoz tartozó, a titkosított adatokat tárolja. Ezek az adatok 30 napig tárolja. 

## <a name="export-and-delete-app"></a>Exportálás és az alkalmazás törlése
A felhasználók teljes körű hozzáféréssel rendelkeznek az alkalmazás [exportálásához](luis-how-to-start-new-app.md#export-app) és [törléséhez](luis-how-to-start-new-app.md#delete-app) . 

## <a name="utterances"></a>Beszédmódok

A hosszúságú kimondott szöveg két különböző helyen is tárolhatók. 

* **A szerzői folyamat**során a rendszer létrehozza és tárolja a hosszúságú kimondott szöveg a szándékban. A sikeres LUIS-alkalmazáshoz hosszúságú kimondott szöveg szükséges. Miután közzétette az alkalmazást, és lekérdezéseket fogad a végponton, a végponti kérelem querystring (`log=false`) meghatározza, hogy a végpontok kimondása tárolódik-e. Ha a rendszer tárolja a végpontot, az a portál **összeállítás** szakaszában található aktív tanulási hosszúságú kimondott szöveg részévé válik az **Endpoint hosszúságú kimondott szöveg áttekintése** szakaszban. 
* Ha **áttekinti a végpont hosszúságú kimondott szöveg**, és felvesz egy leképezést, a rendszer már nem tárolja a megjelölést az áttekinthető végponti hosszúságú kimondott szöveg részeként. A rendszer hozzáadja az alkalmazás szándékához. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Példa hosszúságú kimondott szöveg törlése egy szándékból

A [Luis](luis-reference-regions.md)-képzéshez használt példa hosszúságú kimondott szöveg törlése. Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazás, törlődik a LUIS webszolgáltatás és az exportálás nem érhető el.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Hosszúságú kimondott szöveg törlése az Active learningből

A hosszúságú kimondott szöveg törölheti a felhasználói hosszúságú kimondott szöveg listájáról, amelyet a LUIS az **[Endpoint hosszúságú kimondott szöveg áttekintése lapon](luis-how-to-review-endpoint-utterances.md)** javasol. Beszédmódok törlése a listáról a továbbiakban nem javasolt, de nem törli azokat a naplókat.

Ha nem szeretné, hogy az aktív tanulás hosszúságú kimondott szöveg, [Tiltsa le az aktív tanulást](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Az aktív tanulás letiltása szintén letiltja a naplózást.

### <a name="disable-logging-utterances"></a>Hosszúságú kimondott szöveg naplózásának letiltása
Az [aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning) letiltja a naplózást.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Fiók eltávolítása
Ha töröl egy fiókot, az összes alkalmazás törlődnek, azokhoz példa kimondott szöveg és a naplókat. Az adatok a fiók előtt 60 napig megőrződnek, és adatok véglegesen törlődnek.

A fiók törlése a **Beállítások** lapon érhető el. Válassza ki a fiók nevét a jobb felső navigációs sávon a **Beállítások** lapra való lekéréshez.

## <a name="data-inactivity-as-an-expired-subscription"></a>Lejárt az előfizetésem, adatok inaktív
Az adatmegőrzés és a törlés céljából az inaktív LUIS alkalmazás a _Microsoft belátása_ szerint lejárt előfizetésként kezelhető. Egy alkalmazás akkor számít inaktívnak, ha az utolsó 90 napra vonatkozó megfelel a következő feltételeknek: 

* **Nem** volt hívása.
* Nem lett módosítva.
* Nem nem a jelenlegi kulcs rendelt hozzá.
* Nem volt olyan felhasználó bejelentkezni.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók az alkalmazások exportálásáról és törléséről](luis-how-to-start-new-app.md)
