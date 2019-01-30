---
title: A QnA Maker Tudásbázis chit csevegési hozzáadása
titleSuffix: Azure Cognitive Services
description: Személyes chit csevegési ad hozzá a robot megkönnyíti több természetes nyelvi és vonzóbbá tehetik a KB-os létrehozásakor. A QnA Maker egyszerűen adja hozzá a felső chit csevegési, előre megadott készlete a KB-os teszi lehetővé.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 051c92ad6598934e13568e61c17840e136e9a57f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216319"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Csevegési Chit Tudásbázis hozzáadása

A robot chit csevegési hozzáadása megkönnyíti több természetes nyelvi és vonzóbbá tehetik. A QnA Maker chit csevegési funkció segítségével könnyedén adhat hozzá a felső chit csevegési, előre megadott készlete a Tudásbázis (KB). Ez a robot felhőszerepkör felel a kiindulási pont lehet, és az időt és rögzíti őket előzmények költséget takaríthat meg.  

Ez az adatkészlet chit csevegési körülbelül 100 forgatókönyvek három személyekre szabott – a Professional és az ismerősök a Comic hanggal tartalmaz. Válassza ki a személy, amely leginkább megfelel a robot hangalapú. Adja meg a felhasználó lekérdezése, QnA Maker megpróbálja egyezik, a legközelebbi ismert chit csevegési QnA. 

A különböző személyiséghez néhány példát: <!-- added quotes so acrolinx doesn't score these sentences -->
|Felhasználói lekérdezés|A Professional|A typu Friend|A Comic|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Aw, that's sweet.`|`Sure. Take me to city hall. See what happens.`|


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

## <a name="add-additional-chit-chat-questions-and-answers"></a>Adjon hozzá további chit csevegési kérdések és válaszok
Új chit csevegési QnA, amely a készlet nem szerepel az előre meghatározott adhat hozzá. Győződjön meg arról, hogy nem másolat létrehozása egy kérdés-válasz pár chit csevegési csoportban már jelez. Minden olyan új chit csevegési QnA hozzáadásakor lekérdezi hozzáadódik a **Szerkesztői** forrás. Győződjön meg arról, hogy ez a csevegési chit a rangsorolás tudomásul veszi, adja hozzá a metaadatokat a kulcs/érték pár "Szerkesztői: chit csevegési", a következő képen látható módon:
   
![A QnA-tudásbázisok chit csevegési hozzáadása](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Csevegési chit töröl egy meglévő KB
Válassza ki a KB, és keresse meg a **beállítások** lapot. Az adott chit csevegési forrás szerepel, a kiválasztott személy nevű fájl. A forrásfájl törölheti.

![A KB-os chit csevegési törlése](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
