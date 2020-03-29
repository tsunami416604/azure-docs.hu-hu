---
title: Feltárás - Personalizer
titleSuffix: Azure Cognitive Services
description: A feltárás, Personalizer képes folytatni a jó eredményeket, még akkor is, ha a felhasználói viselkedés megváltozik. A feltárási beállítás kiválasztása üzleti döntés a felhasználói interakciók arányáról, amelyekkel a modell javítása érdekében.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490817"
---
# <a name="exploration-and-exploitation"></a>Feltárás és kitermelés

A feltárás, Personalizer képes folytatni a jó eredményeket, még akkor is, ha a felhasználói viselkedés megváltozik.

Amikor a Personalizer ranghívást kap, egy RewardActionID-t ad vissza, amely a következőket adja:
* A használat az aktuális gépi tanulási modell alapján a legvalószínűbb felhasználói viselkedéshez használja.
* Feltárást használ, amely nem egyezik meg azzal a művelettel, amelynek a legnagyobb valószínűsége van a rangsorban.

Personalizer jelenleg használ nevű algoritmus *epsilon kapzsi* felfedezni. 

## <a name="choosing-an-exploration-setting"></a>Feltárási beállítás kiválasztása

Az Azure Portal Configuration page for Personalizer **konfigurációs** lapján konfigurálja a feltáráshoz használandó forgalom százalékos arányát. Ez a beállítás határozza meg a lekutatást végző ranghívások százalékos arányát. 

A Personalizer határozza meg, hogy ezzel a valószínűséggel fedezze-e fel vagy használja-e ki ezt a valószínűséget minden rangsorolási hívásnál. Ez eltér a viselkedés egyes A/B keretrendszerek, amelyek zár a kezelés adott felhasználói azonosítók.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Gyakorlati tanácsok a feltárási beállítások kiválasztásához

A feltárási beállítás kiválasztása üzleti döntés a felhasználói interakciók arányáról, amelyekkel a modell javítása érdekében. 

A nulla beállítás a Personalizer számos előnyét tagadja. Ezzel a beállítással a Personalizer nem használ felhasználói beavatkozást a jobb felhasználói interakciók felderítéséhez. Ez vezet a modell stagnálás, sodródás, és végső soron alacsonyabb teljesítményt.

A túl magas beállítás tagadja a felhasználói viselkedésből való tanulás előnyeit. 100%-ra állítása állandó véletlenszerűséget jelent, és a felhasználók tól megtanult viselkedés nem befolyásolná az eredményt.

Fontos, hogy ne változtassa meg az alkalmazás viselkedését attól függően, hogy látja-e, hogy a Personalizer felfedezi vagy kihasználja.It is important to not to change the application behavior based on whether you see if Personalizer is exploring or exploiting. Ez tanulási elfogultsághoz vezetne, ami végső soron csökkentené a potenciális teljesítményt.

## <a name="next-steps"></a>További lépések

[Megerősítő tanulás](concepts-reinforcement-learning.md) 