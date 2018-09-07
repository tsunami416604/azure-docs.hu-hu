---
title: A LUIS iteratív Alkalmazástervezés – beszédfelismerés ismertetése
description: LUIS legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.  A LUIS-alkalmazások tervezési ismétlések beolvasni a legjobb adatkinyerés LUIS betanításához szükséges.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: diberry
ms.openlocfilehash: 7c267d53c9057ac05427ff14a7e3c25d56ab1f62
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025427"
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
## <a name="train-and-publish-the-app"></a>Az alkalmazás betanítása és közzététele
Miután 10 – 15 különböző utterances az egyes szándékkal, a szükséges entitásokkal címkével ellátott, LUIS betanítását, majd beolvasni a végpontok közzététele. Ügyeljen arra, hogy az alkalmazás létrehozása, és tegye közzé az alkalmazást, hogy elérhető legyen a [végpont régiók](luis-reference-regions.md) van szüksége. 

## <a name="https-endpoint-testing"></a>HTTPS-végpont tesztelése
A HTTPS-végpont található, a LUIS-alkalmazás teszteléséhez a **[közzététel](luis-how-to-publish-app.md)** lapot. A végpont a tesztelés lehetővé teszi, hogy a LUIS felülvizsgálatra alacsony – magabiztosan megszólalásokat kiválasztása.  

## <a name="recycle"></a>Lomtár
Amikor elkészült, a szerzői műveletek a ciklust, elkezdheti újra. Indítsa el a LUIS megjelölve alacsony magabiztosan végpont utterances áttekintése. Ellenőrizze ezeket mind a szándék entitás kimondott szöveg. Miután utterances tekintse át, a felülvizsgálat lista üresnek kell lennie.  

## <a name="batch-testing"></a>Kötegelt tesztelés
A Batch vizsgálat nem láthatják az hány példa utterances LUIS szerint kell kiértékelni. A példák a LUIS új kell lennie, és megfelelően feliratú szándékot, és azt szeretné, hogy a LUIS található entitások. A teszteredmények azt jelzik, LUIS végre arról, hogy az adott készletét kimondott szöveg. 

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [együttműködési](luis-concept-collaborator.md).