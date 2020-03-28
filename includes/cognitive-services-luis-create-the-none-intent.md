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
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179427"
---
Az ügyfélalkalmazás tudnia kell, ha egy utterance (kifejezés) nem értelmes vagy megfelelő az alkalmazás számára. A **Nincs** szándék minden alkalmazáshoz hozzáadódik a létrehozási folyamat részeként annak megállapítására, hogy az ügyfélalkalmazás nem tud-e megválaszolni egy utterance (kifejezés) lehetőséget.

Ha a LUIS visszaadja a **Nincs** szándék egy utterance (kifejezés) az ügyfélalkalmazás kérheti, ha a felhasználó szeretné befejezni a beszélgetést, vagy adjon több útvonalat a beszélgetés folytatásához. 

> [!CAUTION] 
> Ne hagyja üresen a **Nincs** szándékot. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

2. Válassza ki a **None** szándékot. Adjon hozzá három kimondott szöveget, amelyet a felhasználó megadhat, de nem releváns a Humán erőforrás alkalmazás szempontjából:

    | Példák kimondott szövegekre|
    |--|
    |Az ugató kutyák idegesítőek|
    |Rendeljen nekem egy pizzát|
    |Pingvinek az óceánban|
