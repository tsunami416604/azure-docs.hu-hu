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
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 581c47d84466f37d7c7c3ad3e98ae1749f8d9524
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608647"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>Oktatóanyag: Hozzon létre egy Tudásbázis, majd a QnA Maker portálján kérdés megválaszolása

Ez az oktatóanyag végigvezeti egy tudásbázis létrehozásának és közzétételének, majd egy kérdés tudásbázisból való megválaszolásának folyamatán.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
* Tudásbázis létrehozása, és kérdés megválaszolása a QnA Maker portálján
* A tudásbázis ellenőrzése, mentése és betanítása
* A tudásbázis közzététele
* CURL használata a tudásbázis lekérdezéséhez

> [!NOTE] 
> Ebben az oktatóanyagban programozott verziója érhető el a teljes körű megoldást a [ **Azure-Samples/cognitive-services-qnamaker-csharp** GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

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

1. Kérdések és válaszok listáját fölötti eszköztárból a metaadatok ikonnal. A metaadat-címkéket az egyes kérdés és válasz tartalmazza. A csevegési Chit kérdése van a **Szerkesztői: chit csevegési** metaadatok már be van állítva. Ezeket a metaadatokat az ügyfélalkalmazás és a kiválasztott választ küld vissza. Az ügyfélalkalmazás egy csevegőrobot, például a szűrt metaadat segítségével határozza meg, további feldolgozási vagy a felhasználói interakció.

    ![Metaadat-címkéket megtekintése](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

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

    Mivel a `Thank you` utasításhoz tartozó kérdés pontosan megegyezik egy csevegési kérdéssel, a QnA Maker 100-as pontszámmal teljesen biztos a válaszban. A QnA Maker is a kapcsolódó kérdések, valamint a csevegési Chit metaadatok címke adatokat tartalmazó metaadat-tulajdonságot adott vissza.  

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