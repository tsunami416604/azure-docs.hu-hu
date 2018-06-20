---
title: Adattároló szolgáltatásáról LUIS - Azure megértése |} Microsoft Docs
description: Ismerje meg, hogyan tárolja a nyelvi ismertetése (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: f235c787e7d2064696e5421219a297d914b5882d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266005"
---
# <a name="data-storage-and-removal"></a>Adattárolás és eltávolítása
LUIS tárolja az Azure-tárolóban a régió van megadva. a kulcs megfelelő titkosított adatokat. Ezeket az adatokat 30 napig tárolja. 

## <a name="export-and-delete-app"></a>Exportálás és az alkalmazás törlése
Felhasználók teljes hozzáféréssel rendelkeznek a keresztül [exportálása](create-new-app.md#export-app) és [törlése](create-new-app.md#delete-app) az alkalmazást. 

## <a name="utterances-in-an-intent"></a>A megjelölésű utterances
Példa utterances képzési használt törlése [LUIS][LUIS]. Ha törli egy példa utterance a LUIS alkalmazás, a LUIS webszolgáltatás törlődik, és nem érhető el az exportált.

## <a name="utterances-in-review"></a>Utterances felülvizsgálat alatt
A listája, amely LUIS javasol, a felhasználó utterances utterances törölheti a  **[áttekintés végpont utterances lap](label-suggested-utterances.md)**. Utterances törlése a listáról megakadályozza, hogy a javasolt, de nem törli a naplókat.

## <a name="accounts"></a>Fiókok
Ha töröl egy fiókot, akkor minden alkalmazás törlődnek, együtt a példa utterances és a naplókat. Az adatok a fiók előtt 60 napig megőrződnek, és adat véglegesen törlődik.

A fiók törlése érhető a **beállítások** lap. Válassza ki a fiók nevére való regisztrálásáról és a jobb felső navigációs sávon a **beállítások** lap.

## <a name="data-inactivity-as-an-expired-subscription"></a>Adatok tétlen, mint egy lejárt előfizetés
Az adatok megőrzése és törlését alkalmazásában, inaktív LUIS alkalmazás bármikor _Microsoft megítélése_ egy lejárt előfizetés kell kezelni. Az alkalmazások akkor számít inaktívnak, ha az elmúlt 90 napban megfelel a következő feltételeknek: 

* Volt-e **nem** intézett hívások.
* Nem lett módosítva.
* Nem nem rendelkeznek aktuális kulcs rendelve.
* Jelentkezzen be azt a felhasználó nem volt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudnivalók az exportálás és alkalmazás törlése](create-new-app.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions