---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325904"
---
Az ügyfélalkalmazásnak tudnia kell, hogy a Kimondás nem értelmezhető vagy nem megfelelő-e az alkalmazáshoz. A rendszer a létrehozási folyamat részeként hozzáadja az egyes alkalmazásokhoz a **nincs** szándékot annak megállapításához, hogy az ügyfélalkalmazás nem válaszol-e.

Ha LUIS a **none** szándékot adja vissza, az ügyfélalkalmazás megkérdezheti, hogy a felhasználó szeretné-e lemondani a beszélgetést, vagy további útmutatást ad a beszélgetés folytatásához. 

Ha üresen hagyja a **none** leképezést, a rendszer a tulajdonos tartományon kívül várhatóan megjósolja, hogy az egyik meglévő tartományhoz tartozik-e. Ennek az az oka, hogy az ügyfélalkalmazás, például a csevegési robot helytelen előrejelzés alapján helytelen műveleteket hajt végre. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza ki a **None** szándékot. Vegyen fel három hosszúságú kimondott szöveg, amelyet a felhasználó megadhat, de nem vonatkozik a pizza-rendezési alkalmazásra:

    |`None` példa hosszúságú kimondott szöveg|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    Ezek a példák nem használnak olyan szavakat, amelyeket elvár a tulajdonos tartományában, például `pizza`, `cheese`, `crust`, `pickup` `deliver`.