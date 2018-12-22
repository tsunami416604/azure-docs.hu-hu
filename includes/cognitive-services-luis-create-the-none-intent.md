---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0579b1e21e714e25e197cb5abf46793a38978d06
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53755674"
---
Az ügyfélalkalmazás tudnia kell, ha az utterance (kifejezés), nem értelmezhető, vagy az alkalmazás megfelelő. A **nincs** szándékot bekerül a létrehozási folyamat részeként mindegyik alkalmazás határozza meg, ha az utterance (kifejezés) nem lehet válaszolni az ügyfélalkalmazás által.

A LUIS adja vissza. Ha a **nincs** szándék az utterance (kifejezés) számára, az ügyfélalkalmazás feltehető Ha a felhasználó szeretne-e a beszélgetés vagy a további utasításokat a beszélgetés további ad. 

> [!CAUTION] 
> Ne hagyja a **nincs** üres szándékot. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

2. Válassza ki a **None** szándékot. Adjon hozzá három utterances, hogy a felhasználó előfordulhat, hogy adja meg, de nem vonatkoznak az emberi erőforrások alkalmazását:

    | Példák kimondott szövegekre|
    |--|
    |Az ugató kutyák idegesítőek|
    |Rendeljen nekem egy pizzát|
    |Pingvinek az óceánban|