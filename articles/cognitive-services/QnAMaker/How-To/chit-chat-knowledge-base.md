---
title: Csevegés hozzáadása QnA Maker tudásbázishoz
titleSuffix: Azure Cognitive Services
description: Ha személyes Chit-csevegést szeretne hozzáadni a robothoz, akkor még több beszélgetést és részvételt tesz lehetővé, amikor létrehoz egy KB-ot. A QnA Maker lehetővé teszi, hogy egyszerűen vegyen fel egy előre feltöltött készletet a KB-ra.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901209"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Chit-csevegés hozzáadása egy tudásbázishoz

A csevegést a robothoz való hozzáadásával több beszélgetést és bevonást vehet igénybe. A QnA Maker Chit-Chat funkciója lehetővé teszi, hogy könnyedén vegyen fel egy előre feltöltött készletet a tudásbázisba (KB). Ez lehet a bot személyiségének kiindulási pontja, és megtakarítja az időt és a költségeket a semmiből való írás során.  

Ez az adatkészlet körülbelül 100 forgatókönyvet kínál a több persona, például a Professional, a barátságos és a szellemes beszélgetés hangjaként. Válassza ki azt a Personát, amely a legszorosabban hasonlít a robotja hangjára. A felhasználó lekérdezése miatt QnA Maker megpróbálja egyeztetni a legközelebbi ismert Chit-Chat-QnA.  

Néhány példa a különböző személyiségekre. A személyiségi [adatkészleteket](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) a személyes adatokkal együtt tekintheti meg.

`When is your birthday?`felhasználói lekérdezéséhez minden személynek van egy stílusú válasza:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Személyiség|Példa|
|--|--|
|Professzionális|A kor nem igazán vonatkozik rám.|
|Rövid|Nem igazán van kora.|
|Szellemes|Ingyenes.|
|Gondoskodó|Nincs életkorom.|
|Lelkes|Robot vagyok, úgyhogy nem vagyok kora.|
||


## <a name="language-support"></a>Nyelvi támogatás

A Chit-Chat adatkészletek a következő nyelveken támogatottak:

|Nyelv|
|--|
|kínai|
|Angol|
|Francia|
|Németország|
|olasz|
|japán|
|koreai|
|Portugál|
|Spanyol|


## <a name="add-chit-chat-during-kb-creation"></a>Chit-Chat hozzáadása a KB létrehozásakor
A Tudásbázis létrehozása során a forrás URL-címek és fájlok hozzáadása után lehetőség van a Chit-Chat hozzáadására. Válassza ki a kívánt személyiséget a Chit-Chat alapjaként. Ha nem kívánja felvenni a Chit-chatet, vagy ha már rendelkezik az adatforrások támogatásával, válassza a **nincs lehetőséget**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Chit-csevegés hozzáadása meglévő KB-hoz
Válassza ki a KB-ot, és navigáljon a **Beállítások** lapra. A megfelelő **. TSV** formátumú összes Chit-Chat-adatkészletre mutató hivatkozás. Töltse le a kívánt személyiséget, majd töltse fel a fájlt forrásként. A fájl letöltésekor és feltöltésekor ügyeljen rá, hogy ne szerkessze a formátumot vagy a metaadatokat. 
  
![Chit-csevegés hozzáadása meglévő TUDÁSBÁZIShoz](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>A Chit-csevegéssel kapcsolatos kérdések és válaszok szerkesztése
Ha szerkeszti a KB-ot, egy új forrást fog látni a Chit-Chat számára a kiválasztott személyiség alapján. Mostantól bármilyen más forráshoz hasonlóan hozzáadhat megváltoztatott kérdéseket vagy szerkesztheti a válaszokat is. 

![A Chit-Chat QnAs szerkesztése](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

A metaadatok megtekintéséhez válassza az eszköztár **beállítások megtekintése** elemét, majd válassza a **metaadatok megjelenítése**lehetőséget.

## <a name="add-additional-chit-chat-questions-and-answers"></a>További Chit-csevegési kérdések és válaszok hozzáadása
Hozzáadhat olyan új Chit-Chat-QnA, amely nem szerepel az előre definiált készletben. Győződjön meg arról, hogy nem duplikál olyan QnA párt, amely már szerepel a Chit-Chat készletben. Új Chit-Chat-QnA hozzáadásakor a rendszer bekerül a **szerkesztői** forrásba. Annak érdekében, hogy a Ranger megértse, hogy ez a Chit-Chat, adja hozzá a "Vezércikk: chitchat" metaadat-kulcs/érték párokat, ahogy az alábbi képen látható:
   
![! [A Chit-Chat QnAs hozzáadása] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Chit-csevegés törlése meglévő KB-ból
Válassza ki a KB-ot, és navigáljon a **Beállítások** lapra. Az adott Chit-Chat-forrás fájlként van listázva a kiválasztott személyiség nevével. Ezt forrásfájlként is törölheti.

![A KB-ból származó chit-csevegés törlése](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Lásd még: 

[A QnA Maker áttekintése](../Overview/overview.md)
