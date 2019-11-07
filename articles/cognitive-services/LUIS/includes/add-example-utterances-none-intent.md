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
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648299"
---
Az ügyfélalkalmazásnak tudnia kell, hogy a Kimondás nem értelmezhető vagy nem megfelelő-e az alkalmazáshoz. A rendszer a létrehozási folyamat részeként hozzáadja az egyes alkalmazásokhoz a **nincs** szándékot annak megállapításához, hogy az ügyfélalkalmazás nem válaszol-e.

Ha LUIS a **none** szándékot adja vissza, az ügyfélalkalmazás megkérdezheti, hogy a felhasználó szeretné-e lemondani a beszélgetést, vagy további útmutatást ad a beszélgetés folytatásához. 

Ha üresen hagyja a **none** leképezést, a rendszer a tulajdonos tartományon kívül várhatóan megjósolja, hogy az egyik meglévő tartományhoz tartozik-e. Ennek az az oka, hogy az ügyfélalkalmazás, például a csevegési robot helytelen előrejelzés alapján helytelen műveleteket hajt végre. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza ki a **None** szándékot. Adjon meg három olyan kimondott szöveget, amelyet a felhasználó megadhat, de az alkalmazás nem fog foglalkozni velük:

    |`None` példa hosszúságú kimondott szöveg|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
