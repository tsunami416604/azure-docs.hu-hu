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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67179427"
---
Az ügyfélalkalmazásnak tudnia kell, hogy a Kimondás nem értelmezhető vagy nem megfelelő-e az alkalmazáshoz. A rendszer a létrehozási folyamat részeként hozzáadja az egyes alkalmazásokhoz a **nincs** szándékot annak megállapításához, hogy az ügyfélalkalmazás nem válaszol-e.

Ha LUIS a **none** szándékot adja vissza, az ügyfélalkalmazás megkérdezheti, hogy a felhasználó szeretné-e lemondani a beszélgetést, vagy további útmutatást ad a beszélgetés folytatásához. 

> [!CAUTION] 
> Ne hagyja üresen a **nincs** leképezést. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

2. Válassza ki a **None** szándékot. Vegyen fel három hosszúságú kimondott szöveg, amelyet a felhasználó megadhat, de nem vonatkozik az emberi erőforrások alkalmazására:

    | Példák kimondott szövegekre|
    |--|
    |Az ugató kutyák idegesítőek|
    |Rendeljen nekem egy pizzát|
    |Pingvinek az óceánban|
