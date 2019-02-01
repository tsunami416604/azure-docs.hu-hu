---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 08328b3c872ce9e8a25db6075b00e0cee2fdf4fd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480597"
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
