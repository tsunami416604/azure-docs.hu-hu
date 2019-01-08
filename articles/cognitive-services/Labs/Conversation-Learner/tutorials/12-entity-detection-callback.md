---
title: Entitás észlelési visszahívási használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: 'Útmutató: entitás észlelési visszahívást Beszélgetéstanuló modell.'
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a96cfcb07252dc4a9089eab2decaec9efb01a67b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064990"
---
# <a name="how-to-use-entity-detection-callback"></a>Entitás észlelési visszahívási használata

Ez az oktatóanyag bemutatja az entitás észlelési visszahívási, és entitások feloldására vonatkozó gyakori minta mutatja be.

## <a name="video"></a>Videó

[![Entitás észlelési visszahívási oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, amely a `tutorialEntityDetectionCallback` bot fut-e.

    npm run tutorial-entity-detection

## <a name="details"></a>Részletek
Entitás észlelési visszahívások az entitások felismerése viselkedésének és entitás adatkezelési kód módosításának engedélyezése. Bemutatjuk ezt a funkciót, ajánljuk figyelmébe a tipikus entitás észlelési visszahívási tervezési minta alapján. Például az "a big Data típusú apple", "Győr" feloldása.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "EntityDetectionCallback" és az enter.
3. A "Létrehozás" gombra.

Ebben a példában három entitások szükségesek, így most már létrehozhatja a három.

### <a name="create-the-custom-trained-entity"></a>A betanított egyéni entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza ki a "Custom betanított" a "entitástípus."
3. Írja be "City" a "entitás neve."
4. A "Létrehozás" gombra.

### <a name="create-the-first-programmatic-entity"></a>Az első programozott entitás létrehozása

1. Az "Új entitás" gombra.
2. Válassza ki a "Szoftveres" a "entitástípus."
3. Írja be a "CityUnknown" a "entitás neve."
4. A "Létrehozás" gombra.

### <a name="create-the-first-programmatic-entity"></a>Az első programozott entitás létrehozása

1. Az "Új entitás" gombra.
2. Válassza ki a "Szoftveres" a "entitástípus."
3. Írja be a "CityResolved" a "entitás neve."
4. A "Létrehozás" gombra.

Most hozzon létre három műveletet.

### <a name="action-creation"></a>Művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "melyik városban szeretne?"
3. A "Várt entitás felhasználói válasz..." mezőbe írja be a "City".
4. A "Kizárásának entitások" mezőben írja be a "City".
5. A "Létrehozás" gombra.
6. Az "Új Action" gombra.
7. A "Bot a válaszban..." mezőbe írja be a "melyik városban szeretne?"
8. A "Várt entitás felhasználói válasz..." mezőbe írja be a "Nem tudom az városa."
9. A "Létrehozás" gombra.
10. Az "Új Action" gombra.
11. A "Bot a válaszban..." mezőbe írja be a "$CityResolved nagyon hasznos."
12. A "Létrehozás" gombra.

A visszahívási kód itt látható:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hi"
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "melyik városban szeretne?"
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "big Data típusú apple" típusra
6. A "Score műveletek" gombra.
    - A gombra kattintva az entitás észlelési visszahívási aktiválása
    - A visszahívási kód megfelelően a "Győr" CityResolved entitás értékét állítja be.
7. Válassza ki a választ, a "new York-i nagyon hasznos."

Ez a minta akkor bot számos forgatókönyv jellemző. Felhasználói utterances és kinyert entitások végzik az üzleti logikát, és logikát a kanonikus formájában, amely későbbi bekapcsolja a párbeszédpanelen az programozott entitásokkal, majd menti az utterance (kifejezés) alakítja át.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Munkamenet-visszahívások](./13-session-callbacks.md)
