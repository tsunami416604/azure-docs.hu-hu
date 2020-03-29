---
title: Csevegés hozzáadása a QnA Maker tudásbázisához
titleSuffix: Azure Cognitive Services
description: Ha személyes csevegést ad hozzá a robothoz, az társalgásibb és vonzóbbá teszi, amikor létrehoz egy KB-t. QnA Maker lehetővé teszi, hogy könnyen hozzá egy előre kitöltött készlet a felső chit-chat, a KB.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220709"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Chit-chat hozzáadása tudásbázishoz

Ha a csevegést a robothoz adja, az társalgásibb és megnyerőbb. A QnA készítő chit-chat funkciója lehetővé teszi, hogy könnyedén hozzáadhategy előre kitöltött készletet a felső chit-chatből a tudásbázisodba (KB). Ez lehet a kiindulási pont a bot személyiségét, és ez takarít meg az időt és költséget az írás őket a semmiből.  

Ez az adatkészlet körülbelül 100 forgatókönyvek chit-chat a hangja több personas, mint a szakmai, barátságos és szellemes. Válassza ki azt a személyiséget, amely leginkább hasonlít a robot hangjára. Adott egy felhasználói lekérdezés, QnA Maker megpróbálja egyeztetni azt a legközelebbi ismert chit-chat QnA.  

Néhány példa a különböző személyiségek alább. Láthatjuk az összes személyiség [adatkészletek](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) mellett részleteket a személyiségek.

A felhasználói lekérdezésa esetén minden `When is your birthday?`személyiségnek van egy stílusú válasza:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Személyiség|Példa|
|--|--|
|Professional|A kor rám nem igazán vonatkozik.|
|Barátságos|Nem igazán vagyok annyi idős.|
|Szellemes|Nem vagyok megkormentes.|
|Gondoskodó|Nincs korom.|
|Lelkes|Robot vagyok, szóval nincs korom.|
||


## <a name="language-support"></a>Nyelvi támogatás

A chit-chat adatkészletek a következő nyelveken támogatottak:

|Nyelv|
|--|
|Kínai|
|Angol|
|Francia|
|Németország|
|Olasz|
|Japán|
|Koreai|
|Portugál|
|Spanyol|


## <a name="add-chit-chat-during-kb-creation"></a>Chit-chat hozzáadása a TUDÁSBÁZIS létrehozása során
A tudásbázis létrehozása során a forrás URL-címek és fájlok hozzáadása után lehetőség van a csevegés hozzáadására. Válassza ki a kívánt személyiséget, mint a chit-chat bázis. Ha nem szeretne csevegést hozzáadni, vagy ha már rendelkezik csevegési támogatással az adatforrásaiban, válassza a **Nincs**lehetőséget. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Chit-chat hozzáadása meglévő KB-hoz
Jelölje ki a tudásbázist, és keresse meg a **Beállítások** lapot. Van egy link az összes chit-chat adatkészlethez a megfelelő **.tsv** formátumban. Töltse le a kívánt személyiséget, majd töltse fel fájlforrásként. Ügyeljen arra, hogy a fájl letöltésekor és feltöltésekor ne szerkeszthesse a formátumot vagy a metaadatokat. 
  
![Chit-chat hozzáadása a meglévő KB-hoz](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>A csevegéskérdéseinek és válaszainak szerkesztése
Amikor szerkeszti a KB, megjelenik egy új forrás a chit-chat, alapján a személyiség kiválasztott. Most már hozzáadhat módosított kérdéseket, vagy szerkesztheti a válaszokat, mint bármely más forrásból. 

![Chit-chat qna szerkesztése](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

A metaadatok megtekintéséhez válassza az eszköztár **Nézetbeállításai parancsát,** majd a **Metaadatok megjelenítése**lehetőséget.

## <a name="add-additional-chit-chat-questions-and-answers"></a>További kérdések és válaszok hozzáadása csevegéshez
Hozzáadhat új chit-chat QnA-t, amely nem szerepel az előre definiált készletben. Győződjön meg arról, hogy nem duplikált a QnA pár, amely már szerepel a chit-chat készlet. Amikor új chit-chat QnA-t ad hozzá, az hozzáadódik a **szerkesztői** forráshoz. Annak érdekében, hogy a ranker megértse, hogy ez chit-chat, adja hozzá a metaadat-kulcs / érték pár "Szerkesztői: csevegés", ahogy az a következő képen látható:
   
![! [Add chit-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Csevegés törlése meglévő KB-ból
Jelölje ki a tudásbázist, és keresse meg a **Beállítások** lapot. Az ön konkrét csevegési forrása fájlként jelenik meg, a kiválasztott személyiségnévvel. Ezt törölheti forrásfájlként.

![Csevegés törlése a KB-ból](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Lásd még 

[A QnA Maker áttekintése](../Overview/overview.md)
