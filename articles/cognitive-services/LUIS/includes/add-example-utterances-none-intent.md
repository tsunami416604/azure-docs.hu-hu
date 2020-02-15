---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279501"
---
Az ügyfélalkalmazásnak tudnia kell, hogy a Kimondás nem értelmezhető vagy nem megfelelő-e az alkalmazáshoz. A rendszer a létrehozási folyamat részeként hozzáadja az egyes alkalmazásokhoz a **nincs** szándékot annak megállapításához, hogy az ügyfélalkalmazás nem válaszol-e.

Ha LUIS a **none** szándékot adja vissza, az ügyfélalkalmazás megkérdezheti, hogy a felhasználó szeretné-e lemondani a beszélgetést, vagy további útmutatást ad a beszélgetés folytatásához.

Ha üresen hagyja a **none** leképezést, a rendszer a tulajdonos tartományon kívül várhatóan megjósolja, hogy az egyik meglévő tartományhoz tartozik-e. Ennek az az oka, hogy az ügyfélalkalmazás, például a csevegési robot helytelen előrejelzés alapján helytelen műveleteket hajt végre.

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza ki a **None** szándékot. Vegyen fel három hosszúságú kimondott szöveg, amelyet a felhasználó megadhat, de nem vonatkozik a pizza-rendezési alkalmazásra:

    |`None` példa hosszúságú kimondott szöveg|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Ezek a példák nem használnak olyan szavakat, amelyeket elvár a tulajdonos tartományában, például `pizza`, `cheese`, `crust`, `pickup` `deliver`.