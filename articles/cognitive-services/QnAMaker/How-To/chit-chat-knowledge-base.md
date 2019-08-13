---
title: A QnA Maker Tudásbázis chit csevegési hozzáadása
titleSuffix: Azure Cognitive Services
description: Személyes chit csevegési ad hozzá a robot megkönnyíti több természetes nyelvi és vonzóbbá tehetik a KB-os létrehozásakor. A QnA Maker egyszerűen adja hozzá a felső chit csevegési, előre megadott készlete a KB-os teszi lehetővé.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a8ebd0b36c6ee8bf6762a70af9f4a7c09f6b118d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955150"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Csevegési Chit Tudásbázis hozzáadása

A robot chit csevegési hozzáadása megkönnyíti több természetes nyelvi és vonzóbbá tehetik. A QnA Maker chit csevegési funkció segítségével könnyedén adhat hozzá a felső chit csevegési, előre megadott készlete a Tudásbázis (KB). Ez a robot felhőszerepkör felel a kiindulási pont lehet, és az időt és rögzíti őket előzmények költséget takaríthat meg.  

Ez az adatkészlet körülbelül 100 forgatókönyvet kínál a több persona, például a Professional, a barátságos és a szellemes beszélgetés hangjaként. Válassza ki a személy, amely leginkább megfelel a robot hangalapú. Adja meg a felhasználó lekérdezése, QnA Maker megpróbálja egyezik, a legközelebbi ismert chit csevegési QnA.  

Néhány példa a különböző személyiségekre. A személyiségi adatkészleteket [](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) a személyes adatokkal együtt tekintheti meg.

A felhasználói lekérdezéséhez `When is your birthday?`minden személynek van egy stílusú válasza:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Személyiség|Példa|
|--|--|
|Professional|A kor nem igazán vonatkozik rám.|
|Rövid|Nem igazán van kora.|
|Szellemes|Ingyenes.|
|Gondoskodó|Nincs életkorom.|
|Lelkes|Robot vagyok, úgyhogy nem vagyok kora.|
||

> [!NOTE]
> Chit-csevegéses támogatás csak jelenleg angol nyelven érhető el. 

## <a name="add-chit-chat-during-kb-creation"></a>Csevegési chit hozzáadása létrehozáskor KB
Tudásbázis létrehozása során, a forrás URL-címét és a fájlokat, hozzáadása után chit csevegési hozzáadásának lehetősége van. Válassza ki a személy, amelyeket szeretne a csevegési chit alapjaként. Ha nem szeretné, hogy chit csevegési hozzáadása, vagy ha már rendelkezik chit csevegési támogatja az adatforrásokat, válassza a **None**. 
   
![Adja hozzá a csevegési chit létrehozása során](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Csevegési Chit hozzáadása egy meglévő KB
Válassza ki a KB, és keresse meg a **beállítások** lapot. Hivatkozás a megfelelő chit csevegési adatkészletekhez **.tsv** formátumban. Töltse le a személyiségelemző szeretne, majd töltse fel a fájlt adatforrásként. Ellenőrizze, hogy nem szerkesztése a formátum vagy a metaadatok letöltése, és töltse fel a fájlt. 
  
![Csevegési chit hozzáadása meglévő KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>A csevegési chit kérdések és válaszok szerkesztése
Ha szerkeszti a KB-os, látni fogja a chit csevegéshez, a kiválasztott személy alapján új forrásból. Most módosítva kérdések hozzáadása vagy szerkesztése a válaszokat, csakúgy, mint bármely más forrással. 

![A QnA-tudásbázisok chit csevegési szerkesztése](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

A metaadatok megtekintéséhez válassza az eszköztár **beállítások megtekintése** elemét, majd válassza a **metaadatok megjelenítése**lehetőséget.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adjon hozzá további chit csevegési kérdések és válaszok
Új chit csevegési QnA, amely a készlet nem szerepel az előre meghatározott adhat hozzá. Győződjön meg arról, hogy nem másolat létrehozása egy kérdés-válasz pár chit csevegési csoportban már jelez. Minden olyan új chit csevegési QnA hozzáadásakor lekérdezi hozzáadódik a **Szerkesztői** forrás. Győződjön meg arról, hogy ez a csevegési chit a rangsorolás tudomásul veszi, adja hozzá a metaadatokat a kulcs/érték pár "Szerkesztői: chit csevegési", a következő képen látható módon:
   
![! [A Chit-Chat QnAs hozzáadása] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Csevegési chit töröl egy meglévő KB
Válassza ki a KB, és keresse meg a **beállítások** lapot. Az adott chit csevegési forrás szerepel, a kiválasztott személy nevű fájl. A forrásfájl törölheti.

![A KB-os chit csevegési törlése](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
