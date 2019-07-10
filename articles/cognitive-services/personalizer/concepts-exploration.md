---
title: Feltárás - Personalizer
titleSuffix: Azure Cognitive Services
description: Feltárás Personalizer továbbra is jó eredményesség, még akkor is, ahogy a felhasználók viselkedésének módosítása. Egy üzleti döntés kapcsolatos felhasználói interakció érdekében az időarány, amíg a megismeréséhez, annak érdekében, hogy a modell javításán-feltárás beállítás választása.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: ebb59b6bb7c36f4558b2bd63d2d55fa95823c4c3
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722479"
---
# <a name="exploration-and-exploitation"></a>Adatáttekintési és hasznosítása

Feltárás Personalizer továbbra is jó eredményesség, még akkor is, ahogy a felhasználók viselkedésének módosítása.

Amikor Personalizer rangsorolják hívás kap, adja vissza egy RewardActionID, vagy:
* Kiaknázása felel meg az a legvalószínűbb felhasználói viselkedés alapján a jelenlegi gépi tanulási modellt használ.
* Feltárás, amely nem felel meg a műveletet, amely rendelkezik a legnagyobb valószínűség belüli rangjának használja.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Personalizer jelenleg nevű algoritmust használ *mohó epszilon* megismeréséhez. 

## <a name="choosing-an-exploration-setting"></a>Egy feltárás beállítás kiválasztása

Konfigurálja a használata az Azure Portalon feltárási forgalom százalékaránya **beállítások** Personalizer lapját. Ez a beállítás határozza meg a feltárás végző rangsorolják hívások százalékban. 

Personalizer határozza meg, hogy ismerje meg, vagy biztonsági rés kiaknázása elleni az egyes rangsorolják hívásakor a valószínűség. Ez eltér attól a viselkedést bizonyos a / B-keretrendszereket, amelyek zárolják a kezelést az adott felhasználói azonosítók.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Egy feltárás beállítás kiválasztására vonatkozó ajánlott eljárások

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

Egy üzleti döntés kapcsolatos felhasználói interakció érdekében az időarány, amíg a megismeréséhez, annak érdekében, hogy a modell javításán-feltárás beállítás választása. 

A beállítás értéke nulla fog negálandó számos Personalizer előnye. Ezzel a beállítással a Personalizer nincs felhasználói interakció érdekében használja a jobb felhasználói interakció érdekében felderítéséhez. Ez a modell stagnálásához, eltéréseket, és végső soron kisebb teljesítményt vezet.

Egy beállítás, amely túl nagy lesz negálandó a felhasználói viselkedés tanulás előnyeit. Állandó véletlenszerűsítést értékre állítaná a 100 %-os azt jelenti, és minden megismert viselkedés, a felhasználók lenne befolyásolja az eredményt.

Fontos, hogy nem módosítható az alkalmazás viselkedése alapján e láthatja Ha Personalizer felfedezése vagy ártó szándékkal használja fel. Milyen előítéletek befolyásolják, amely végső soron a lehetséges teljesítményének csökkenne tanulási vezetne.

## <a name="next-steps"></a>További lépések

[Megerősítő tanulást](concepts-reinforcement-learning.md) 