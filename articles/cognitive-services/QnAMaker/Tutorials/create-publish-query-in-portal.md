---
title: Hozzon létre, közzététel, válaszoljon a QnA Maker
titleSuffix: Azure Cognitive Services
description: Ez a portálalapú oktatóanyag végigvezeti egy tudásbázis programozott módon történő létrehozásának és közzétételének, majd egy kérdés a tudásbázisból való megválaszolásának folyamatán.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: 71745ee73c71aba9c6c5cd5aea3972055fbec01d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087541"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>Oktatóanyag: Tudásbázis létrehozása, és kérdés megválaszolása a QnA Maker portálján

Ez az oktatóanyag végigvezeti egy tudásbázis létrehozásának és közzétételének, majd egy kérdés tudásbázisból való megválaszolásának folyamatán.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
* Tudásbázis létrehozása, és kérdés megválaszolása a QnA Maker portálján
* A tudásbázis ellenőrzése, mentése és betanítása
* A tudásbázis közzététele
* CURL használata a tudásbázis lekérdezéséhez

> [!NOTE] 
> A jelen oktatóanyag programozott verziója teljes körű megoldással együtt elérhető az [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-adattárból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz egy meglévő [QnA Maker-szolgáltatásra](../How-To/set-up-qnamaker-service-azure.md) lesz szükség. 

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása 

1. Jelentkezzen be a QnA Maker [portáljára](https://www.qnamaker.ai). 

1. A felső menüben válassza a **Create a knowledge base** (Tudásbázis létrehozása) lehetőséget.

    ![A tudásbázis létrehozásának 1. lépése](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Hagyja ki az első lépést, mivel a meglévő QnA Maker-szolgáltatást fogja használni. 

1. A következő lépésben válassza ki a meglévő beállításokat:  

    |Beállítás|Cél|
    |--|--|
    |Microsoft Azure Directory-azonosító|A _Microsoft Azure Directory-azonosító_ ugyanahhoz a fiókhoz van társítva, amelyet az Azure Portalra és a QnA Maker portálra való bejelentkezéshez használ. |
    |Azure-előfizetés neve|Az a számlázási fiók, amelyben létrehozta a QnA Maker-erőforrást.|
    |Azure QnA-szolgáltatás|A meglévő QnA Maker-erőforrás.|

    ![A tudásbázis létrehozásának 2. lépése](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. A következő lépésben adja meg a tudásbázis nevét: `My Tutorial kb`.

    ![A tudásbázis létrehozásának 3. lépése](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. A következő lépésben töltse fel a tudásbázist az alábbi beállítások használatával:  

    |Beállítás neve|Beállítás értéke|Cél|
    |--|--|--|
    |URL-cím|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |A megadott URL-címen található GYIK tartalma kérdés-válasz formátumú. A QnA Maker ebből a formátumból tudja kinyerni a kérdéseket és az azokhoz tartozó válaszokat.|
    |Fájl |_ebben az oktatóanyagban nincs használatban_|Itt tölthetők fel a kérdéseket és válaszokat tartalmazó fájlok. |
    |Chit-chat personality (Csevegő személyisége)|The friend (A barát)|Ezzel barátságos, laza stílust kölcsönözhet a gyakori kérdéseknek és válaszoknak. A kérdéseket és válaszokat később szerkesztheti. |

    ![A tudásbázis létrehozásának 4. lépése](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. A létrehozási folyamat befejezéséhez válassza a **Create your KB** (Tudásbázis létrehozása) lehetőséget.

    ![A tudásbázis létrehozásának 5. lépése](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>A tudásbázis ellenőrzése, mentése és betanítása

1. Ellenőrizze a kérdéseket és a válaszokat. Az első oldalon az URL-címről származó kérdések és válaszok szerepelnek. 

    ![Mentés és betanítás](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. A táblázat alján válassza ki a kérdések és válaszok utolsó oldalát. Az oldalon a Chit-chat personality (Csevegő személyisége) kérdései és válaszai szerepelnek. 

1. A kérdések és válaszok fölött található eszköztáron kattintson a fogaskerék ikonra. Ekkor megjelennek az egyes kérdésekhez és válaszokhoz tartozó szűrők. A Chit-chat questions (Csevegési kérdésekhez) már be van állítva az **editorial: chit-chat** (szerkesztői: csevegő) szűrő. A rendszer a kiválasztott válasszal együtt adja vissza a szűrőt az ügyfélalkalmazásban. Az ügyfélalkalmazás, például egy csevegőrobot ennek a szűrőnek a használatával további feldolgozási műveleteket vagy interakciókat határoz meg a felhasználóhoz.

    ![Szűrők megtekintése](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. A felső menüsávon válassza a **Save and train** (Mentés és betanítás) elemet.

## <a name="publish-to-get-kb-endpoints"></a>Közzététel a tudásbázis végpontjainak lekéréséhez

Kattintson a felső menüben található **Publish** (Közzététel) gombra. A közzétételi oldalon kattintson a **Cancel** (Mégse) gomb mellett található **Publish** (Közzététel) gombra.

![Közzététel](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

A tudásbázis közzététele után megjelenik a végpont

![Oldal végpont nastavení publikování](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>CURL használata a GYIK egyik válaszának lekérdezéséhez

1. Válassza ki a **Curl** lapot. 

    ![Curl parancs](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Másolja ki a **Curl** lap szövegét, és futtassa egy Curl-kompatibilis terminálon vagy parancssorban. A hitelesítés fejlécének értéke tartalmazza az `Endpoint ` szöveget, amelyet egy szóköz után a kulcs követ.

1. Cserélje le a `<Your question>` elemet a `How large can my KB be?` kérdésre. Ez hasonlít a `How large a knowledge base can I create?` kérdéshez, de nem pontosan ugyanaz. A QnA Maker természetes nyelvi feldolgozás segítségével határozza meg, hogy a két kérdés azonos-e.     

1. Hajtsa végre a CURL parancsot, és a rendszer válaszként egy pontszámot és választ tartalmazó JSON-fájlt ad vissza. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    A QnA Maker 42,81%-os pontszámmal megközelítőleg biztos a válaszban.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>CURL használata egy csevegési válasz lekérdezéséhez

1. A CURL-kompatibilis terminálon cserélje le a `How large can my KB be?` kérdést egy robotbeszélgetés-befejező felhasználói utasítással, például: `Thank you`.   

1. Hajtsa végre a CURL parancsot, és a rendszer válaszként egy pontszámot és választ tartalmazó JSON-fájlt ad vissza. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Mivel a `Thank you` utasításhoz tartozó kérdés pontosan megegyezik egy csevegési kérdéssel, a QnA Maker 100-as pontszámmal teljesen biztos a válaszban. A QnA Maker ezen kívül az összes kapcsolódó kérdést, valamint a csevegési szűrő információit tartalmazó metaadat-tulajdonságot is visszaadta.  

## <a name="use-curl-to-query-for-the-default-answer"></a>A CURL használata az alapértelmezett válasz lekérdezéséhez

A rendszer az alapértelmezett választ adja bármely olyan kérdésre, amelynek válaszában a QnA Maker nem biztos. Ezt a választ az Azure Portalon kell konfigurálni. 

1. A Curl-kompatibilis terminálon cserélje le a `Thank you` kérdést egy `x` karakterre. 

1. Hajtsa végre a CURL parancsot, és a rendszer válaszként egy pontszámot és választ tartalmazó JSON-fájlt ad vissza. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    A QnA Maker 0 pontot adott vissza, ami azt jelenti, hogy nem biztos a válaszban, emellett azonban az alapértelmezett választ is visszaadta. 

## <a name="next-steps"></a>További lépések

A támogatott fájlformátumokról további információ a [támogatott adatforrásokat](../Concepts/data-sources-supported.md) ismertető szakaszban talál. 

Tudjon meg többet a csevegő [személyiségeiről](../Concepts/best-practices.md#chit-chat).

Az alapértelmezett válasszal kapcsolatos további információkat lásd a [Nincs találat](../Concepts/confidence-score.md#no-match-found) című szakaszban. 

> [!div class="nextstepaction"]
> [A tudásbázissal kapcsolatos alapfogalmak](../Concepts/knowledge-base.md)