---
title: LUIS iteratív alkalmazás megismeréséhez tervezési - Azure |} Microsoft Docs
description: LUIS alkalmazásokhoz szükséges tervezési ismétlési képzése LUIS lekérni a legjobb adatok kinyerése érdekében.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/12/2018
ms.author: v-geberr
ms.openlocfilehash: b7f8dd46dc8289322726934f330761b0f1ab94bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265937"
---
# <a name="authoring-cycle"></a>Szerzői ciklus
LUIS megtanulja a legjobb az ismétlődő ciklusának modellmódosításokkal, utterance példák, közzétételét és adatgyűjtés végpont lekérdezésből. 

![Szerzői ciklus](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Egy LUIS modell létrehozása
A modell célja, hogy megtudja, mi a felhasználó által kért (a szándék vagy leképezés) és a kérdés részeinek részletesen (entitás), amely a meghatározásához, hogy az fogadja a hívást. 

A modell kell lennie az alkalmazástartomány jellemző szavak meghatározásához és, hogy van megfelelő, valamint az olyan tipikus word rendezési kifejezéseket. 

A modell leképezés, entitásokat tartalmaz. 

## <a name="add-training-examples"></a>Példák hozzáadása
LUIS a leképezések az példa utterances kell. A példák word kiválasztása és a word sorrendben kell tudni határoznia azt, milyen célt a utterance vállalatán elég változata szükséges. Minden egyes példa utterance kell entitások címkézve szükséges adatokat. 

Figyelmen kívül hagyásához, amelyek a utterance való hozzárendelésével nem kapcsolódnak az alkalmazástartomány utterances LUIS utasította a **nincs** leképezés. Bármely szavak vagy kifejezések nem szükséges egy utterance kihúzott nem kell neve. Nincs címke szót vagy kifejezést figyelmen kívül van. 
<!--
## Not just yet
Do not add features such as a [phrase list](luis-concept-feature.md) feature in your first cycle. Phrase lists are phrases that would be specific to your app's subject area.  
-->
## <a name="train-and-publish-the-app"></a>A vonat és az alkalmazás közzététele
Minden szándéka is megegyezik, 10 – 15 különböző utterances szerepel a szükséges entitásokkal feliratú, akkor LUIS betanítása, majd a közzétételére az beszerzése a végpontokat. Ügyeljen arra, hogy az alkalmazás létrehozása és az alkalmazás közzétételéhez, így a érhető el a [végpont régiók](luis-reference-regions.md) van szüksége. 

## <a name="https-endpoint-testing"></a>HTTPS-végpont tesztelése
A HTTPS-végpont szerepel az LUIS alkalmazását tesztelheti a **[közzététel](publishapp.md)** lap. A végpont a tesztelés lehetővé teszi, hogy a LUIS bármely utterances felülvizsgálati alacsony-megbízhatósággal kiválasztásához.  

## <a name="recycle"></a>Lomtár
Amikor elkészült, a szerzői ciklusára, elkezdheti újra. Első lépésként LUIS alacsony megbízhatósággal jelölésű végpont utterances áttekintése. Ezek a célt és a entitás utterances ellenőrzése. Miután utterances tekintse át, a felülvizsgálati lista üresnek kell lennie.  

## <a name="batch-testing"></a>Kötegelt tesztelése
Kötegelt vizsgálat nem láthatják, például hogy hány utterances LUIS alapján kell kiértékelni. A példák LUIS új kell lennie, és megfelelően feliratú leképezés és azt szeretné, hogy LUIS található entitások. A vizsgálati eredmények jelzik, hogy mennyire LUIS utterances adott csoportján hajtaná végre. 

## <a name="next-steps"></a>További lépések

Fogalmak megismerése [együttműködés](luis-concept-collaborator.md).

[luis-reference-prebuilt-domains]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-domains