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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279501"
---
Az ügyfélalkalmazás tudnia kell, ha egy utterance (kifejezés) nem értelmes vagy megfelelő az alkalmazás számára. A **Nincs** szándék minden alkalmazáshoz hozzáadódik a létrehozási folyamat részeként annak meghatározásához, hogy az ügyfélalkalmazás nem válaszol-e egy utterance (kifejezés) kérdésre.

Ha a LUIS visszaadja a **Nincs** szándék egy utterance (kifejezés) az ügyfélalkalmazás kérheti, ha a felhasználó szeretné befejezni a beszélgetést, vagy adjon több útvonalat a beszélgetés folytatásához.

Ha üresen hagyja a **Nincs** szándékot, a tulajdonos tartományon kívül előre jelezendő utterance (kifejezés) előre jelzi a meglévő tulajdonostartomány-leképezések egyikét. Az eredmény az, hogy az ügyfélalkalmazás, például egy csevegőrobot helytelen előrejelzés alapján helytelen műveleteket hajt végre.

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget.

1. Válassza ki a **None** szándékot. Adjon hozzá három kimondott szöveget, amelyet a felhasználó megadhat, de nem releváns a Pizza rendelési alkalmazáshoz:

    |`None`példa kimondott szöveg|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Ezek a példák nem használhatók a tárgytartományban `cheese` `crust`várt `pickup` `deliver`szavakkal, például `pizza`, , , .