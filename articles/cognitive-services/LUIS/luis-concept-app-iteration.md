---
title: Megismerheti a LUIS-alkalmazásokon iteratív megtervezése – Azure |} A Microsoft Docs
description: A LUIS-alkalmazások tervezési ismétlések beolvasni a legjobb adatkinyerés LUIS betanításához szükséges.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: v-geberr
ms.openlocfilehash: e0467e4c41209c937f548edc0c40c05cae588f4c
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888248"
---
# <a name="authoring-cycle"></a>Tartalomkészítési ciklus
LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések. 

![Tartalomkészítési ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>A LUIS-modell létrehozása
A modell célja döntse el, mi a felhasználó kéri (a blokkolni vagy leképezés) és a kérdés mely részeit adja meg az adatokat (entitások), amely segít meghatározni, hogy a válasz. 

A modell kell lennie az app-tartomány adott szavak, hogy megállapítsuk, és, hogy van megfelelő, valamint a tipikus word rendezés kifejezések. 

A modell szándékkal, entitásokat is tartalmaz. 

## <a name="add-training-examples"></a>Példák hozzáadása
A LUIS példa utterances a szándék fog vonatkozni a kell. A példák kell elegendő változata, a word választási lehetőség és a szórendjét meg tudja határozni, melyik a cél az utterance (kifejezés) tervezték. Valamennyi példa utterance (kifejezés) rendelkeznie kell címkével ellátott entitásokként szükséges adatokat. 

A LUIS figyelmen kívül hagyja, hogy nem vonatkoznak az alkalmazás tartományhoz az utterance (kifejezés) való hozzárendelésével megcímkézzen utasította a **nincs** szándékot. Minden olyan szavak vagy kifejezések nem kell az utterance (kifejezés) kihúzott nem kell lesz. Nincs címke szót vagy kifejezést figyelmen kívül van. 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>Betanítása és az alkalmazás közzététele
Miután 10 – 15 különböző utterances az egyes szándékkal, a szükséges entitásokkal címkével ellátott, LUIS betanítását, majd beolvasni a végpontok közzététele. Ügyeljen arra, hogy az alkalmazás létrehozása, és tegye közzé az alkalmazást, hogy elérhető legyen a [végpont régiók](luis-reference-regions.md) van szüksége. 

## <a name="https-endpoint-testing"></a>HTTPS-végpont tesztelése
A HTTPS-végpont található, a LUIS-alkalmazás teszteléséhez a **[közzététel](luis-how-to-publish-app.md)** lapot. A végpont a tesztelés lehetővé teszi, hogy a LUIS felülvizsgálatra alacsony – magabiztosan megszólalásokat kiválasztása.  

## <a name="recycle"></a>Lomtár
Amikor elkészült, a szerzői műveletek a ciklust, elkezdheti újra. Indítsa el a LUIS megjelölve alacsony magabiztosan végpont utterances áttekintése. Ellenőrizze ezeket mind a szándék entitás kimondott szöveg. Miután utterances tekintse át, a felülvizsgálat lista üresnek kell lennie.  

## <a name="batch-testing"></a>Kötegelt tesztelés
A Batch vizsgálat nem láthatják az hány példa utterances LUIS szerint kell kiértékelni. A példák a LUIS új kell lennie, és megfelelően feliratú szándékot, és azt szeretné, hogy a LUIS található entitások. A teszteredmények azt jelzik, LUIS végre arról, hogy az adott készletét kimondott szöveg. 

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [együttműködési](luis-concept-collaborator.md).